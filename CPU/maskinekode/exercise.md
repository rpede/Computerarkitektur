# Machine code exercises

Solve the exercises using [CPU Visual Simulator](https://cpuvisualsimulator.github.io/).

The CPU in the simulator have some differences from the one you saw in the
presentation.

It only has one register, called ACC.
Therefore, there is no need to specify which register is being referred to as part of the instruction.

If you want to read a value from a RAM address, you use LOD to load the value into ACC. You can then store it at a RAM address using STO.

For example, to get the value “10” into RAM address 4, you should write:

![LOD STO](./LOD-STO.png)
If you instead want to specify the address with a label, it looks like this:

You can see a couple of examples of programs by clicking the “Examples” button.

![Load example](./examples.png)

See the [manual](https://cpuvisualsimulator.github.io/manual) for a description
of the complete instruction set and more examples.

“NOP” is short for no-operation and essentially just means that the CPU does nothing during the instruction cycle.
You can stop the program with the “HLT” instruction, which is short for halt
execution.
Without HLT, the CPU will attempt to execute the values stored in RAM as if
they were instructions.

Solve as many of the following tasks as you can. It’s OK if you don’t get
further than the first one, as they are difficult.
However, at a minimum, I would ask you to read through all the tasks and give
them a try.

## 1. Countdown

Write a program that counts down from 10.

## 2. Multiplication without MUL

Write a program that multiplies two numbers without using the MUL instruction.

## 3. Logic

Write a program that reads a person's age from RAM address 32, calculates
whether the person is old enough to have the right to vote in Denmark, and
stores the result at address 34.

You can use the value 00000001 to represent true, meaning that the person is
old enough.
And the value 00000000 to represent false, meaning that the person is not old
enough.

## 4. More Logic

The CPU simulator also has an instruction called ADD and one called OR. These
do the same thing as the ADD and OR gates we have discussed previously, but
with several bits at a time.

These instructions can be used to create more complex logic.

For example, in Denmark, you must be both at least 18 years old and a Danish
citizen to have the right to vote.
We can store whether a person is a Danish citizen as true/false in a RAM address.
If we reuse our previous calculation of whether a person is over 18 and combine
it with whether they are a citizen (using the AND instruction), we can
determine whether a person has the right to vote in a parliamentary election.

You do not need to write a program to calculate the above.
I just hope this gives you an understanding that simple instructions can be
used to describe complex logic on a computer.

This type of logic is based on a branch of mathematics called Boolean algebra.
You will learn more about this in the Programming course over the coming weeks.
