Title: A Two Bit Analog-Digital Converter
Date: 2022-07-12
Category: Blog
Tags: Electronics
Slug: two_bit_adc
Summary: A surprisingly simple circuit gives a reliable two bit analog-digital converter using only one integrated circuit chip.

For a project I am working on I need to read the signal driving a multiplexed segment LCD. The signal switches between four voltage levels (0, 1.5, 3, and 4.5), across 32 different pins. Here I will describe a few ways to recover that signal onto a microcontroller.

# An off-the-shelf ADC chip

We only need two bits of information to distinguish between the four voltage levels. Even the cheapest ADCs give a whole eight bits -- [the cheapest ADC on Digikey](https://www.digikey.com/en/products/detail/touchstone-semiconductor/TS7003ITD833T/3645626) right now gives you eight bits and communicates over SPI, and would cost just under a dollar a piece. More realistically, there are eight-input ADCs which cost more like $4 each in small quantities, which would be a better deal over all.

## Advantages

- It's dead simple to implement, fairly easy to understand.

## Disadvantages

- It's power-hungry, both from the power rails and from the the data signals. An LCD driver usually has to put out basically zero current, and a low-end ADC might consume a noticeable amount, possibly more than the LCD driver is comfortable supplying.

- It's noisy, since it puts a time-varying load on the signal wires.

- It's expensive!

# A handfull of microcontrollers

Here is a cheaper solution, which would also need way fewer parts. I have a bunch of Arduino Nano microcontrollers around, and each one has eight ADC inputs. We can set four of them to sample the circuit.

## Advantages

- Very few additional components, since it has most of the circuitry we need included.

- Cheap: I already have them so it's basically free, but even if you didn't have them on hand the off-brand ones cost about $3 each.

## Disadvantages

- Still noisy and power-hungry

- We have to write code for the microcontrollers

# A resistance ladder (flash) ADC

A better solution is to wire up our own 2-bit ADC. A traditional way to do this would be to use three comparators and a resistance ladder. Something like this.

![a picture of a traditional flash ADC]({attach}traditional_flash_adc.svg){:width=300}

## Advantages

- Low power, low noise. If we use a good mosfet comparator this can consume basically no power except when the input value changes. The resistance ladder sets thresholds at 1.25, 2.5, and 3.75 which nicely divides the voltage levels we are comparing.

- Independent of component values. We can use any resistors we like, and any comparator chip. This makes it potentially very cheap.

- Solid-state. The outputs are simple digital high and low values, so we can hook them up to a shift register, or even use them for interrupts on our microcontroller. This also makes it very easy to debug, since all the logic is visible.

## Disadvantages

- Uses an odd number of components. Comparators are usually available in packages of two or four, and this uses three.

- The output requires processing. We could take a digital output straight from the comparators, but then we would be using three wires to send two bits. We need those some additional logic chips to bring it down to two, which adds to the complexity. A repeatable unit of these on a board would necessarily process multiple signals and include several ICs so as to make use of every available gate.

# A creative solution

Here is the design I have settled on.

![a compact 2-bit ADC]({attach}compact_adc.svg){:width=300}

This manages to get our output using just two comparators. The two resistors take the average from the high-bit and the 2.5 volt rail, and use that as the threshold for the other comparator.

## Advantages

This has all the benefits from the flash ADC, plus it only takes 2 resistors and half of a 4-comparator IC. This makes it easier to assemble, and way cheaper -- less than 22 cents per input, in small quantities.

## Disadvantages

Requires a low-impedance 2.5 volt rail. This is not a problem for me, since I have a handful of 2.5 volt regulators on hand. Of course, all the comparators for the whole project can share the same rail, so this doesn't add much to our part count. If you only had a couple of these to do, here is an alternative arrangement you could use which only needs the power rail, and a few more resistors.

![a version which does not need a 2.5 volt rail]({attach}no_reference_adc.svg){:height=250}

This also reduces the noise on the 2.5 volt reference line, which could reduce the chance of jittering on the output. In the previous version, if signal A changes its low bit while B is changing its high bit, the small leakage from the feedback circuit of B could prevent a clean transition on A. This circuit solves that.

# Generalizing

I think this is a neat topology! If you are familiar with R-2R ladders, it is probably clear to you how we could generalize this circuit to make an ADC of arbitrary precision, at least on paper. Bit n would be generated like this.

![how to form bit n of an ADC of this topology]({attach}r_2r_adc.svg){:height=200}

Essentially, we use an R-2R ladder as a DAC, giving the best approximation to the signal if bit n were high. We compare that to the actual signal to see if bit n should be high. Each bit only uses inputs from the higher-order bits, so I don't expect there is any danger of oscillation.

# Conclusion

This is actually a special case of a really common design called a "subranging" flash. Usually designers will use more bits in a stage before going to an ADC, which gets them decreased latency and better monotonicity. Since these are usually all integrated onto a single chip they can make the priority encoder cheaply on the chip without any messy routing.

In my design it makes sense to incorporate feedback after just one bit. I get to use an off-the-shelf part (the comparator) for a very specialized purpose, all with the addition of just two resistors. It's good to keep an open mind, try out several designs, even for an undemanding task like this. Doing it the traditional way with a full 8-bit ADC on each line, or even using standard 2-bit ADC design, would lead to a needlessly expensive and complicated circuit. This custom design will do more with less.