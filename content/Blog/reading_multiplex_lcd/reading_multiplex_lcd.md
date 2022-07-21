Title: Reading a Multiplexed LCD Signal
Date: 7-21-2022
Category: Blog
Tags: electronics, Xanthippe
Summary: I want to read the signal going to the display of a TI-30Xa calculator. The display has 112 segments, multiplexed in a 4x28 configuration. How can we recover the displayed values from just those 32 pins?

I want to read the signal going to the display of a TI-30Xa calculator. The display has 112 segments, multiplexed in a 4x28 configuration. How can we recover the displayed values from just those 32 pins?

# backplanes

First, we have to identify the signals going to the backplanes. Those don't change no matter what is displayed, always giving the same repeating signal. Using the pin assignments [I chose previously](/reflections-on-wiring-a-calculator), those are pins 1-4. Here is what the voltage at each of them is throughout one 24 millisecond cycle. To keep things tidy, the header row gives the time at the start of the period. The following table is in units of 1.5 volts, so that everything ends up an integer.

|pin| 0ms| 3ms| 6ms| 9ms|12ms|15ms|18ms|21ms|
|---|----|----|----|----|----|----|----|----|
|  3|0|2|2|2|3|1|1|1|
|  4|2|0|2|2|1|3|1|1|
|  2|2|2|0|2|1|1|3|1|
|  1|2|2|2|0|1|1|1|3|

We can see that in the second half of the cycle the same pattern of voltages is repeated. Treating each voltage as a function of time V(t), the signal obeys the rule V(t) = 3-V(t-12). In fact, every pin going to the display obeys this rule. This is done so that every LCD segment is guaranteed to see a balanced load. This leads to our first helpful observation for reading the signal.

**We only need to measure the pins four times per cycle, during the first 12 milliseconds.**

# data

That's the four backplanes identified, what about the data pins? To give an example, let's look at pin 32. Its signal changes depending on whether the "g" in the "de/g/rad" display is on. We can test this easily, because the display will cycle between saying "deg", "rad", and "grad". By looking during the transition between "rad" and "grad", we can be sure the "g" is the only change.

|pin     | 0ms| 3ms| 6ms| 9ms|12ms|15ms|18ms|21ms|
|--------|----|----|----|----|----|----|----|----|
|32(no g)|1|1|1|1|2|2|2|2|
|32(g)   |1|1|3|1|2|2|0|2|

We can see it obeys the same symmetry relation. The only difference between the two signals is in the 6 millisecond position, where it goes to 3 units when the "g" is showing. In other cases, it stays at 1 unit.

This makes sense. When a segment is meant to be off, this scheme keeps its voltage always within 1.5 volts of the four backplanes. When it is meant to be on, it goes all the way up to 4.5 volts making for a 1.5 volt difference with three of the backplanes and a 4.5 volt difference with the backplane it targets.

Assuming the others segments are driven in the same way, this tells us how to tell which are activated. **For each pin, its corresponding cells will be active when it reaches 4.5 volts during each of the four measurement windows.**

There are a few downsides to this scheme which we can certainly see in the final product. For one thing, every cell always has at least 1.5 volts across it, so it is very slightly on. If you hold the display at an angle, you can faintly see all the supposedly-turned-off cells. This effect goes away when the calculator is turned off for real, so it is clearly due to the multiplexing scheme.

Another downside is that cells are only activated for 1/4 the time. Presumably we could achieve deeper tone depth in the turned-on cells if they were driven the whole time.

Both of those pale in comparison to the big advantage of a multiplexed display: we got 112 segments with only 32 pins from the microcontroller, four of which are always giving the same signal.

# from measurement, to circuit

To begin, let's take all 32 pins and put them into comparators. [The comparators I have]({attach}KA339A-D.pdf) prefer to work near their lower voltage end, so let's use the 0 value during the second half of the cycle to read our data pins. We can get the start of our read by looking at groundplane 1. This way, all our comparators can share a reference value of 0.75 volts. This gives us a clean signal we can feed into any microcontroller, with the output voltage determined by the comparator chips.

After that, we just need a microcontroller to act as a timed shift register. 29 pins (28 for data and one for the timing pulse) is too many for any of the ones I have on hand -- the Arduino Nano has 22 and the Raspberry Pi Pico has 25. I am inclined to set two Arduino Nanos on this task, each reading 14 pins, and have them communicate their combined signal to a separate microcontroller which will handle the keypad. This way I can be sure that the timing of reading the display will be handled correctly. The Arduinos can take 5 volts on their data line, so I can give the comparators extra breathing room with a 5 volt input. This is perhaps a bit wasteful, leaving 7 pins on each Arduino untouched, though of course a couple of those will be needed to communicate. The benefits are significant however: it's a modular approach which uses parts I have on hand.