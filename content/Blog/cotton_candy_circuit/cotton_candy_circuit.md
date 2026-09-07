Title: What's Inside "The REAL Cotton Candy Maker"?
Date: Oct 1 2019
Category: Blog
Tags: electronics, reverse engineering, preservation
Summary: Tracing out the circuit of an old, constantly-breaking toy.

When I was a child, I once owned a toy called "The REAL Cotton Candy Maker". It made cotton candy a couple of times, in small quantities. It stopped working after a few uses, for a reason I do not recall. Perhaps I never knew.

Recently, I had the good fortune to find a used version of the same toy at a flea market for a couple dollars. I bought it, tried it out, and determined it did not work. I figured I would take it apart, and see what I could see.

![a hand-drawn circuit diagram]({attach}cotton_candy_circuit.png){:width=700px}

Above is the circuit diagram I reconstructed. Here are a few observations:

- The circuit uses a reed relay to determine whether the lid is closed and in place. This is hooked up at JE1, on pin 7 of U2.
- JE2 is connected to a hall effect sensor, which detects a magnet on the machine's rotating shaft.
- JE3 is just connected to a couple LEDs.
- I have no idea what pin 11 of U2 is for. It seems to go out to a pad where it can be accessed. Perhaps this is used for programming the chip.

What was wrong with my particular model? I will never know, because I got bored and threw it out. My hope is that  the diagram I worked out along the way will someday help someone in a similar situation to diagnose more quickly, and perhaps get an answer.