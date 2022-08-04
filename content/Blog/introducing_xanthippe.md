Title: Introducing Xanthippe
Date: Aug 4 2022
Category: Blog
Tags: electronics, Xanthippe, data structures
Summary: A formal introduction to my calculator validator

I have been going on about my project to [emulate a calculator in the browser](/calculator_emulator) for some time. A month or so ago I decided that, if I am going to make the more fiddly bits of the calculator work correctly, I should really find a way to generate tests automatically.

I have spoken previously about how an emulator is really the ideal venue for test-driven development. All the visible behaviors of the finished product are completely specified, in the sense that any feature not-yet-implemented corresponds to a class of inputs which produce an incorrect output. If you want to know what the emulator should do in a given situation, all you have to do is check against the real deal.

Up to now, I have been using a suite of tests I carefully chose based on pressing buttons by hand. That made sense when there were only a dozen or two tests. Because the examples are carefully chosen, I don't necessarily need many tests to cover a wide range of features. Now there are close to 50 tests, and the remaining functionality -- fractions, statistics registers, base 10 encoding, alternative display styles -- rely on hidden information. With more state involved, I expect the number of necessary test to explode.

# What is Xanthippe?

This is where Xanthippe comes in. Instead of pressing the buttons myself and documenting what the display shows, I will program a computer to press the buttons for me. This way all I have to do is specify a string of presses, and I can automatically get what the calculator shows at each stage. This way I can really "go ham" designing the emulator, and have real confidence that the emulator behaves like the genuine article.

I have already [soldered test points to a calculator](reflections-on-wiring-a-calculator) and figured out the electrical characteristics of [the buttons](reverse-engineering-a-matrix-keypad) and [the display](reading-a-multiplexed-lcd-signal). I have designed [a circuit board](https://github.com/AldenMB/Xanthippe/tree/main/calculator_interface) which reads the signals from the LCD to a shift register and writes button presses from a shift register. Assuming the circuit works as intended (the circuit board is still in the mail right now) I should be able to write code for any microcontroller which controls the calculator and reads its outputs.

Currently, my dream for Xanthippe is that it will be an art piece hanging on a wall in a glass box, constantly pressing buttons on the calculator and recording the results. It will send all the results onto Github, so I can test my emulator against them whenever I make a change. I also want to be able to make requests for specific sequences to check, so I can still design test cases explicitly.

# Why the name?

I am emulating the TI-30Xa, and there are not too many names which start with the letters "Xa". I think Xavier would be a better name for the emulator itself (easier to type). Xanthippe is the name of the wife of Socrates. I think that's somewhat fitting, for a character who sits behind the scenes and makes sure the famous one is keeping in line. Xanthippe is often portrayed as stubborn and unyielding, which fits the role of a testing suite. Though the emulator may disagree, we will know Xanthippe is right.

# What cases to test?

Once I have Xanthippe pushing buttons, the obvious question to ask is "which ones?". There are 40 buttons, and button press sequences often reach over 20 presses in length, so there is no hope of just trying every combination. Many of those combinations will reach an error state long before they reach the end, and calculator does not do much upon reaching an error state, so in reality we can cut lots of sequences short. There are still well too many for an exhaustive search.

A few combinations spring to mind as obvious additions. First, I have already specified many button sequences as good tests, may as well do those. Second, since my emulator runs in the browser, it is not too tricky to ask it to phone home. Whenever someone completes a session with the emulator I can have it send off the button presses, and Xanthippe can check them. That way I am sure to have coverage of the cases people actually use, at least the second time they try it. I could even do something silly like loading answers from Xanthippe when I have them instead of using the emulator, which could make people think I am very fast at fixing bugs. Probably unwise since that would lead to inconsistent behavior, which is the only thing harder to troubleshoot than incorrect behavior.

Another way I can choose button presses is to explore the area around existing sequences. For example, if there is something in memory, I could press buttons to read what it is. I could subtract the visible part of a mantissa to reveal the hidden part. This surely deserves its own blog post.

# How to save the answers?

The calculator does not give us a whole lot of information. Most of the computation happens within the epoxy blob, a "black box" as it were. Really, I just need a way of mapping each sequence of button presses to a display on the screen. Since there are 40 buttons a button press can fit in a single byte. There are 118=8x14 segments on the screen. Not all the combinations are possible of course, so I could fit the LCD into less than 14 bytes, but why bother? The premise of Xanthippe is that I don't know everything the calculator does at all times, so I may as well have a format which can store every result conceivable. This also will take less processing, since what I get from the screen are raw bits.

There is actually one more thing I can measure, which I may as well do. The calculator takes longer to process some presses than others. I have to monitor how long it takes to respond anyhow, since I can't give a new button press while I am waiting for the previous one to resolve. Why not record the time each button press takes? In some future version of the emulator I may wish to simulate the actual delay the calculator gives, so I may as well record that as well. That way I actually have every piece of information the calculator will give me.

I can be somewhat clever about the data structure I use. After each button press, the calculator gives me a result to store. However, multiple sequences of button presses can start with the same few buttons. Why repeat them in the storage? I can instead do a tree structure. In JSON, one node of the tree might look something like this.

```JSON
{
	"display": "dQw4w9WgXcQ",
	"time": 148,
	"a":{}
	"X":{}
	"7":{}
}
```

Here `dQw4w9WgXcQ` is an example base-64-encoded LCD reading. Time is how long the button press took, in some appropriate units. Each of those single-character keys would be a button press, and it would lead to a child node of the same type. We would only include those children which we have evaluated. A tree is a simple, sensible way to store the type of data we are creating.

# How to encode the keys?

The shift register in hardware which receives the key presses takes two bytes, switching 16 transistors. The most naive way of storing the presses would be to just record the two bytes which are sent.

That is quite wasteful, since far from every combination of transistors would ever be turned on. We know there are 40 buttons, and the next power of two up from there is 64=2^6. We should be able to store one button press in less than one byte, rather in one base-64 character.

You may recall this table from [a previous post](reverse-engineering-a-matrix-keypad).

|  | A0 | A1 | A2 | A3 | A4 | A5 | A6 | A7 |
|--|----|----|----|----|----|----|----|----|
|B0|+/- |2nd |+   |0   |.   |SIN |1/x |    |
|B1|y^x |HYP |-   |1   |5   |COS |x^2 |    |
|B2|OFF |pi  |X   |2   |6   |TAN |sqrt|    |
|B3|  |Sigma+|/   |3   |7   |DRG |EE  |    |
|B4|    |STO |=   |4   |8   |LOG |(   |    |
|B5|    |RCL |ab/c|<-  |9   |LN  |)   |    |
|B6|    |    |    |    |    | ||(reset all)|
|RESET|ON/C|ON/C|ON/C|ON/C|ON/C|ON/C|ON/C|ON/C|

Here I have added two rows and a column to reflect how the shift registers on Xanthippe are configured. When the A register puts A7 high and B6 high, for example, the pad on the reverse of the calculator is shorted, clearing all memory and putting the calculator in a fresh state. This will prove indispensable when I want to start a new session, as a reliable way to clear all memory of the previous run. There are only 7 pins on the calculator labeled B, so I have used the remaining bit there to trigger a transistor which sends an ON/C keypress.

In each byte, either none of the bits are on or one of the bits is on. None is the default state, when no button is being pushed. I never need to send an "empty press", that's just the default state in between presses. When the RESET bit is on, it doesn't really matter whether the A bits are on or off, since they can't make a full circuit without one of the B bits on as well. Therefore we can encode the keypress with just 6 bits, 3 for the A byte and 3 for the B+RESET byte. 6 bits is great, because it lets me use base 64 encoding. I like 64 bit encoding because it's standardized and human-readable.

The two numbers `0b111110` and `0b111111` have ambiguous encodings in base 64, since their original versions use the characters `+` and `/` which don't play nice in URLs. I would like to avoid them. Depending on whether I use the high bits for A or B, I could run into them or avoid them. By choosing to put B into the three high bits, I can put those in the last row of the table above. Any number of the form `0b111xxx` will give a RESET, so I never need to use the two offending characters at all.

The resulting encoding is as follows.

A | B | Binary | Base64 | label
--|---|--------|--------|------
0 | 0 | 000000 | A      | +/-
1 | 0 | 000001 | B      | 2nd
2 | 0 | 000010 | C      | +
3 | 0 | 000011 | D      | 0
4 | 0 | 000100 | E      | .
5 | 0 | 000101 | F      | SIN
6 | 0 | 000110 | G      | 1/x
0 | 1 | 001000 | I      | y^x
1 | 1 | 001001 | J      | HYP
2 | 1 | 001010 | K      | -
3 | 1 | 001011 | L      | 1
4 | 1 | 001100 | M      | 5
5 | 1 | 001101 | N      | COS
6 | 1 | 001110 | O      | x^2
0 | 2 | 010000 | Q      | OFF
1 | 2 | 010001 | R      | pi
2 | 2 | 010010 | S      | X
3 | 2 | 010011 | T      | 2
4 | 2 | 010100 | U      | 6
5 | 2 | 010101 | V      | TAN
6 | 2 | 010110 | W      | sqrt
1 | 3 | 011001 | Z      | Sigma+
2 | 3 | 011010 | a      | /
3 | 3 | 011011 | b      | 3
4 | 3 | 011100 | c      | 7
5 | 3 | 011101 | d      | DRG
6 | 3 | 011110 | e      | EE
1 | 4 | 100001 | h      | STO
2 | 4 | 100010 | i      | =
3 | 4 | 100011 | j      | 4
4 | 4 | 100100 | k      | 8
5 | 4 | 100101 | l      | LOG
6 | 4 | 100110 | m      | (
1 | 5 | 101001 | p      | RCL
2 | 5 | 101010 | q      | ab/c
3 | 5 | 101011 | r      | <-
4 | 5 | 101100 | s      | 9
5 | 5 | 101101 | t      | LN
6 | 5 | 101110 | u      | )
7 | 6 | 110111 | 3      | (reset all)
x | 7 | 111xxx | 4,5,...| ON/C

<!---
make the above table using the following Python code.
for i in range(64):
    letter = str(base64.b64encode(bytearray([0,0,i])))[-2]
    A = i % 8
    B = i //8
    symbol = table[B][A]
    if symbol:
        print(f'{A} | {B} | {i:06b} | {letter:<6} | {symbol}')
-->

Alas, there is no obvious rhyme or reason to how these are laid out on the calculator. Some times many buttons on the same row of the keypad will share an A pin, but this is a pattern and not a rule. I suspect it mostly has to do with whatever was convenient when the engineers at TI were laying out the traces on the keypad. I could have chosen an encoding which corresponds more to the physical layout than the electrical layout, but any such encoding would be completely ad-hoc. This encoding appeals to me because it is quite natural, and it will be easy for Xanthippe to interpret when connecting to the hardware.

The issue of how to encode the LCD still remains. There are four batches of 28 bits to store, coming from the four backplanes. For now it makes sense to defer this task, since I currently don't know what cells the majority of the bits correspond to. If, for example, they line up neatly with the 7-segment portion then it would make sense to take advantage of this. Regardless, we will certainly be able to fit the result into 14 bytes somehow, possibly padding it out to 15 bytes to make a 20-character base64 string.

# How to store the tree?

We need a way for Xanthippe to keep its records, so that it knows what it has done and other software can read it. I have two bad ideas and a good idea.

First, a bad idea: I could define my own binary file format. This would let me load it fast from memory, and store it efficiently. It would also be a lot of work, and it would make it hard for other people to understand the code. If something breaks, I would not have an easy way to recover.

A less bad, but still not good, idea is to use JSON. This has the advantage of being easy for humans to read, and easy to pass between Python and Javascript. It is also a natural way to express a tree structure. There are some major disadvantages, though. I want to have Xanthippe running constantly, so it will pile up lots of data. A JSON object really would rather stay in memory while it is being manipulated, and only be written to a string when manipulations are done. If I do it this way, Xanthippe will eventually run out of memory, which will be hard to fix. JSON is also not an economical way to store binary data, in terms of disk space.

Instead I will use an actual database. This is a bit tricky since most databases are built for tables not for trees, but others have solved this problem before me. For now this will take the form of an SQLite database, since that will be the easiest to work with in Python. For the Javascript side of things, I will either figure out how to read the SQLite file directly (likely with [sql.js](https://github.com/sql-js/sql.js)) or have Python export it to a file for Javascript consumption. It does feel a bit silly to use SQLite to represent a tree, because to do so I must represent my data as a table which SQLite will internally represent as a B-Tree. There may be a way to give SQLite some hints as to the data structure to regain some performance, but this may also just be the price we pay for compatibility.

# Next steps

Once the circuit board for Xanthippe arrives it will be time to test it out, make sure I can do the reads and writes I have designed. Exploration will probably happen with a microcontroller, but for the final result I'll probably use an old Raspberry Pi I have lying around, since I need the networking. Once I am sending keypresses and receiving raw LCD segments I will need to figure out exactly which LCD segment is controlled by which bit of the signal. After that I can get to writing the code which will run Xanthippe forever.