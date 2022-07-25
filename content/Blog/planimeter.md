Title: A 3D-printed Planimeter For Use In Classrooms
Date: 7-24-2022
Category: Blog
Tags: electronics, 3D printing, CadQuery, teaching
Summary: A simple 3D-printable design for a planimeter, suitable for making a class set.

Over the past week I have been developing [a design for a 3D printed planimeter.](https://github.com/AldenMB/Planimeter) The idea was to make something so cheap and simple that a whole class of students could each have their own, either individually in groups. I also wanted the parameters of the planimeter (the lengths of the arms) to be configurable by the students, so they are adjustable with thumb screws.

Usually, the most intricate part of a planimeter is the system of gears which allows you to count the rotations of the wheel. Here, we side-step that issue by using a 10-turn potentiometer. I intend to have students read the values using a microcontroller, and write a program to compute the area. You could also power it from a battery and use a digital volt meter. For students who are interested in electronics, it could be a nice exercise to design a circuit which runs off a 9 volt battery and displays the swept-out area in appropriate units on a cheap [panel mount volt meter](https://www.amazon.com/bayite-Voltmeter-Motorcycle-Polarity-Protection/dp/B00YALV0NG/).

If you don't want to include electronics at all, the design could be modified to turn the wheel on a bearing instead of on a potentiometer, and markings could be added to the wheel to count rotations by eye.

I feel this is a good example of how 3D printing can change the way we teach. It would not be feasible to manufacture this type of thing for a reasonable cost using traditional methods, just because the setup time would be so great and the demand so small. 3D printing allows us to incorporate more physical activities in the classroom without breaking the bank.