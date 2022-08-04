Title: A 3D-printed Planimeter For Use In Classrooms
Category: Blog
Status: draft
Tags: electronics, Python
Summary: A simple 3D-printable design for a planimeter, suitable for making a class set.

I am learning how to use the PIO feature of the Raspberry Pi Pico. This gives access to eight independent state machines, which can run in parallel to your main program, manipulating the GPIO very fast and with precise timing. A program for a PIO state machine is tiny, consisting of at most 32 instructions. There are only 9 two-byte instructions to choose from in PIO assembly, all specialized to doing input/output tasks.

There are currently two interfaces provided for writing PIOASM code. There is an assembler bundled with the C/C++ SDK, which allows you to compile a program using the PIOASM assembly language. This is a fairly bare-bones assembly language, giving you labels for jump statements and some compile-time arithmetic, but not much else. For the most part the assembly commands correspond one-to-one with the binary instructions.

The second interface is a bit weirder. In Micropython, they provide a decorator, `@rp2.asm_pio`, which lets you write PIO assembly in a regular Python function. How does it do this? It monkey-patches the global namespace of the function. [You can see the code yourself here](https://github.com/micropython/micropython/blob/master/ports/rp2/modules/rp2.py). The result is that anything inside the function is called using a miniature language with all the syntax of Python, but none of the names. Each of the provided functions just adds the relevant instruction to an internally-stored PIO program.

This strategy explains some of the choices which were made for the functions in this mini-language. This is why a Python PIOASM program can have a function called `set` -- within this scope, it has overwritten the name of the standard python `set`. That's also why it uses the handle `in_` instead of `in`, since `in` is a reserved word in Python, part of the syntax, and not the name of an object.

A fun consequence is that we can take advantage of Python syntax when writing our code. For example, before I realized this I had the following lines in a program.

```python
# tell the calculator to release the buttons
set(pins, 0b000)  # empty the register
set(pins, 0b110)  # show the result
set(pins, 0b100)
```

We can make it easier on the eyes by defining local variables which explain what each bit is for. Here's what that same part of my code looks like now.

```python
show = 0b010
noblank = 0b100
# tell the calculator to release the buttons
set(pins, 0 | 0)  # empty the register
set(pins, show | noblank)  # show the result
set(pins, 0 | noblank)
```

It does not stop there. Since we are still writing a Python function, we can use Python control structures to make our program. For example, I had some code which looked like this.

```python
clock = 0b01
nsample = 0b10

wait(1, pin, 1)

set(x, 31).side(nsample | 0)
label(0)
in_(pins, 1).side(nsample | clock)
jmp(x_dec, 0).side(nsample | 0)
push(noblock).side(0 | 0)

wait(0, pin, 1)

set(x, 31).side(nsample | 0)
label(1)
in_(pins, 1).side(nsample | clock)
jmp(x_dec, 1).side(nsample | 0)
push(noblock).side(0 | 0)

wait(1, pin, 2)

set(x, 31).side(nsample | 0)
label(2)
in_(pins, 1).side(nsample | clock)
jmp(x_dec, 2).side(nsample | 0)
push(noblock).side(0 | 0)

wait(0, pin, 2)

set(x, 31).side(nsample | 0)
label(3)
in_(pins, 1).side(nsample | clock)
jmp(x_dec, 3).side(nsample | 0)
push(noblock).side(0 | 0)

irq(rel(0))
```

That's not too bad, really. The local variables defined at the top make it clear what the side-sets are doing, and while reading it you will quickly realize each of the loop blocks are doing the same thing. The program is 25 instructions long, since the local variable declaration doesn't get passed to the PIOSM compiler.

You may think that we could fit this in fewer instructions by making an assembly subroutine. There is no obvious way to set the arguments to the wait commands from variable, but we could make the bit-shift loop into a subroutine and use the spare scratch variable `y` to keep track of where we are jumping from. Indeed, you can do it that way, but since there aren't instructions in PIOASM for handling subroutines it ends up taking more instructions total to use that approach. Having the loop unrolled in this manner makes the program run faster, and makes it easier to read.

We can do better. The following code makes the same program -- produces the same 25 instructions for the PIO module -- but does it in a way that's easier to read.

```python
clock = 0b01
nsample = 0b10
for loop_number, (trigger_pin, condition) in enumerate(
	[(1, 1), (1, 0), (2, 1), (2, 0)]
):
	wait(condition, pin, trigger_pin)

	set(x, 31).side(nsample | 0)
	label(loop_number)
	in_(pins, 1).side(nsample | clock)
	jmp(x_dec, loop_number).side(nsample | 0)
	push(noblock).side(0 | 0)

irq(rel(0))
```

It's important to emphasize that the loop here is not part of the assembled code. In order to do loops in PIOASM you need to use jump instructions. Rather, we are looping over the process of creating the program. Essentially, we are using Python syntax to write an assembler macro.

I am fairly sure this is not the way we are intended to write PIOASM code. After all, a PIO program is at most 32 instructions, so listing out the instructions one by one will always be explicit and reasonable to read. A program with loops like the one I gave above has the potential to confuse people greatly, if they get the impression that the assembled program will have a loop in it.

For this coding style, the biggest nail in the coffin [is one line of Micropython source code](https://github.com/micropython/micropython/blob/963e599ec0d253534eb835ade7020e8ac3d7919b/ports/rp2/modules/rp2.py#L258). What is this doing here? One thing it will do is prevent you from making modular PIO functions. For example, to me the following code makes sense.

```python
def delay(loops=31, duration=31, loop_label = 'delay_loop'):
	set(y, loops)
	label(loop_label)
	nop[duration-2]
	jmp(y_dec, loop_label)
	

@rp2.asm_pio(autopush=True)
def alternating_slow_fast_read():
	in_(pins, 1)
	delay(2, 20)
	in_(pins, 1)
	delay(15, 30)
```

I think it is clear what this code intends, but it does not work because of that `__globals__.clear()` call in the Micropython source.
