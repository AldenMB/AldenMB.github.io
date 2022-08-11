Title: Lessons from the Xanthippe circuit board	
Date: Aug 11 2022
Category: Blog
Tags: electronics, Xanthippe, circuit boards
Summary: Regrets and insights brought from the Xanthippe circuit board

> It is too bad! Always the old story! When a man has finished building his house, he finds that he has learnt unawares something which he OUGHT absolutely to have known before he began to build. The eternal, fatal "Too late!" The melancholia of everything COMPLETED—!

> -- Friedrich Nietsche, <cite>Beyond Good and Evil</cite>, section 277, translated by Helen Zimmern

It's here! The board I designed has arrived!

This is always an exciting time. Marvel over all the little details which went into a circuit board, which seemed so much larger on the computer screen. Patiently solder all the connections. Curse the clever choices you made only a week prior. Hold your breath to see if it works as you hoped.

We are always our own harshest critics. I have been often told not to point out the flaws of my own artistic works -- most people won't notice the flaws unless pointed out, and many wouldn't consider them flaws unless they are told. There is wisdom in that. We should embrace the imperfect, recognizing that we are also imperfect.

We should also be frank with ourselves. We can embrace the mistakes we made while simultaneously taking measures to prevent those mistakes in the future. This blog is mostly just for my own reference. With that in mind, this blog post lists some lessons which I hope will stick with me.

# Board size

The board manufacturer I used, JLCPCB, provides a significant discount for boards which fit within a 100X100mm square. I think I could have fit my design into this space if I had set out to do so from the beginning. Once I realized this, though, I had already done most of the fiddly layout and didn't want to change it.

Also, I had initially neglected to include mounting holes in my design. I put some in by expanding the outline, which led to large mostly-unused margins at the top and bottom. If I had included mounting holes from the beginning, I would have saved some space over all.

# Default behavior

Some of the chips in my circuit, notably the 74HC595 chips, have undefined behavior if their inputs are not driven. This is no good if the board receives power before the microcontroller is driving it. This could have been avoided by using some pulldown resistors on all the inputs.

# Resistor arrays

I got pretty bored soldering the 32 pullup resistors individually. It was much more pleasant than doing the same on prototype board, but still not great. I think I would happily accept the additional expense of using resistor arrays for pullups, since this almost halves the number of solder points and means dealing with larger (i.e. less fiddly) parts.

# TO-92 footprints

These gave me a lot of trouble during soldering. I used a footprint for standard, unbent leads. That made it easy to insert the parts, sure, but it meant the pads were too close for easy soldering. This led to a few solder bridges I had to fix. Next time I will definitely go for a bent-lead footprint, just to make the hand soldering easier.

# Breakouts

Now that I am powering up and debugging the circuit, it would be quite useful to have a header for each of the signals for testing. Even something as simple as duplicating the input/output headers, so I can access them while they are plugged. That would have been a cheap thing to add, which would make the current task easier.

# Disconnects

Since there is more than one part to the circuit, it would be a help to the debugging if I could isolate parts of the circuit from one another. I'm thinking something like a double row of headers, which I could join with jumpers. That would also help if I decide to keep half the circuit but replace the other half -- I could just leave some of the jumpers off to disconnect the portion I don't need, and even use the headers to move the signal to another board.

# Conclusion

I am in the midst of hunting down bugs and errors, finding out why the circuit does not do exactly what I want it to. It is easy then to forget all the things it is doing well. Aside from the trouble with the TO-92 footprints, the board was quite easy to assemble. The layout is fairly sensible, and by not packing parts too tight I was able to get the soldering iron everywhere it needed to go. It is a good design, even if it is not working right just yet. The goal of pointing out the errors that were made is not to diminish the usefulness of the board I have, or the work that went into it. Rather, I hope to make the next board I make even better. 