Title: Decoding a multiplexed LCD
Date: Aug 12 2022
Category: Blog
Tags: electronics, Xanthippe, LCD
Summary: Figuring out which segments are wired where on the TI-30Xa

The button-pressing side of Xanthippe is currently working intermittently. I have ordered some more-appropriate parts which will hopefully improve matters. In the mean time, the LCD decoding functionality is working fine. Now is an ideal time to figure out the correspondence between the signals heading to the display and the segments which are activated.

Previously, when designing the display for my calculator emulator, I painstakingly traced every segment. I found that there are precisely 112 segments. There are 32 wires going into the LCD, making up 4 ground planes and 28 data lines, for a total of 4x28=112 segments. It's clear there is an alignment between the two, it's just a matter of figuring it out.

# Naming conventions

In order to describe the correspondence, it will be necessary to have names for the signals and names for the segments. I already have names I like for the segments, which I made up when creating this SVG file.

![a hand-made SVG of the TI-30Xa display]({attach}display.svg){:width=600}

I have edited the file here to overlay some of the segment labels. Each digit is numbered starting from the right at 0. Each segment within follows a traditional lettering scheme for 7 segment displays. For the orphan "-" sign at the left, it is numbered as though there were an entire digit around it. The exponent digits are numbered similarly, prefixed with E. The indicators above are text, so their labels are simply their text content.

When naming the signals I wish to refer back to the physical device as much as possible. I have numbered the wires leading to the display, starting with 1 closest to the battery. The first four wires carry the ground planes. I have also assigned each ground plane a letter, according to the order the segments cycle through when displaying. They repeat a pattern of 4-3-2-1, so I have assigned the backplane on pin 4 the letter A, the plane on pin 3 the letter B, and the plane on pin 2 the letter C.

I have put together a simple script which constantly polls the screen and produces the following output.
<pre>
   33322222222221111111111000000000
   21098765432109876543210987654321
A: ......##....................#...
B: #.....##.....................#..
C: #.....#.......................#.
D: ......##.......................#
</pre>
A "." indicates that the corresponding segment is not active, and "#" indicates it is active. In this way we end up with a table. All that remains is to fill it in with labels.

# The table

To avoid filling the whole screen with tables, I will include the full table here only once. Below I will describe how it was filled in.

| |  32| 31| 30| 29| 28| 27| 26|25| 24|23| 22|21| 20|19| 18|17
|-|----|---|---|---|---|---|---|--|---|--|---|--|---|--|---|--
|A|STAT|R  |K  |E0D|() |E1D|0DP|0D|1DP|1D|2DP|2D|3DP|3D|4DP|4D
|B|DE  |X  |E0C|E0E|E1C|E1E|0C |0E|1C |1E|2C |2E|3C |3E|4C |4E
|C|G   |RAD|E0B|E0G|E1B|E1G|0B |0G|1B |1G|2B |2G|3B |3G|4B |4G
|D|FIX |E2G|E0A|E0F|E1A|E1F|0A |0F|1A |1F|2A |2F|3A |3F|4A |4F

| | 16|15| 14|13| 12|11| 10| 9|  8| 7|  6|  5|4|3|2|1
|-|---|--|---|--|---|--|---|--|---|--|---|---|-|-|-|-
|A|5DP|5D|6DP|6D|7DP|7D|8DP|8D|9DP|9D| M3|2nd|A|.|.|.
|B|5C |5E|6C |6E|7C |7E|8C |8E|9C |9E|10G|HYP|.|B|.|.
|C|5B |5G|6B |6G|7B |7G|8B |8G|9B |9G| M2|ENG|.|.|C|.
|D|5A |5F|6A |6F|7A |7F|8A |8F|9A |9F| M1|SCI|.|.|.|D

## The backplanes

These are free spaces. Bingo!

## DEGRAD

The angle indication is actually composed of three segments, "DE", "G", and "RAD". By cycling through the angle modes, we can infer the values from each combination.

## Indicators

Most of the indicators can be toggled on or off individually. That saves us some effort, we can just inspect them one at a time.


## Digits

What about the digits? The calculator powers on showing "0.", which indicates that most of the first digit lies in columns 25 and 26. If we enter "8." this confirms it, and tells us segment G for free.

Pushing the digit to the left by hitting zero, we find columns for the other digits just the same. Hitting "-" at the end tells us 10G.

Now we must discern which segment is which. I don't like to assume, so let's test all of them. Starting with "G", since we can just push a negative sign through to see it. We can get DP by sending and 8 along without its decimal point. Next grab E by sending a 9 along. 6 gives us B in a similar way. Take away F from 9 and you get 3, so 3 will tell us F. 1 consists of B and C, and we know B, so 1 gives us C. Finally, 7 is the only digit which distinguishes between A and D, so we need it to get us those. Now we are done with the mantissa

## Exponent

A bit trickier since you can't have a blank digit in the exponent. We can have 8, the complement of blank. By comparing everything against 8, we get all our segments in the same way.

# Conclusions

We can see a definite pattern, with the digits all using the same configuration of segments on backplanes, just placed on different pins.We can see that the "K" and "()" indicators are filling the spots of the exponent's decimal points.

We can also see that a natural way to encode this signal would be to group adjacent pairs of wires into bytes, so that one digit with associated decimal point ends up occupying exactly one byte.

Now that I have confidence in the wiring of the LCD reader, all that remains is to get a stable solution to the button pressing problem. I have some ideas for how this will get done, but this will make up a future post. Stay tuned!