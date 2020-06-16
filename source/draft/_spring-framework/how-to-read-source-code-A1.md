# References of How to read source code

## Reference 1

In my time of reading other peoples’ code, I’ve come up with a process that only involves three steps.

1. Pick a specific point of interest

- I had tried to learn too much at one time. It’s the same mistake many beginners commit when they try to read source code for the first time.
- **Mental models are built piece by piece. Code should be read the same way.**
- Instead of trying to stomach 1000 lines of code in a herculean effort, focus on a single topic. If it can be parsed down to a single method, even better.

2. Find a loose thread

- Now that you have a specific method you want to learn about, what do you do next?
- Whether you want to know the class of an object, the ancestry of a class, the stack trace, or owner of a certain method, most languages offer these features. Once you begin to unravel the tapestry that is a code library, you will find plenty of loose ends to follow.

3. Follow the trail

- Now that you have the thread you want to follow, all that’s left is to trace it until you find your answer. This may seem like an obvious step, but this is where most beginners get tripped up.
- One reason for this is that repositories don’t have a table of contents. We are at the mercy of the maintainers to organize their files in readable fashion.
- On bigger projects that have many maintainers, that’s usually not an issue. On smaller projects, you may find yourself wading through monolithic files, deciphering single name variables, and asking yourself “Where did this come from?” too often to count.

## Reference 2

A1:

1. Study the theory about the code you are about to read (es c program: what is c, how it works, paradygms).
2. Get experience with the language.
3. Get experience with the language.
4. Get experience with the language.
5. If it uses frameworks (and libraries), do 2 3 4 but with framework.
6. If you can, get to know the owner’s way of thinking.
7. Get to know the project mission.
8. Analyze the standards he’s using.
9. Analyze the tests he hopefully wrote.
10. You’re set! You can write a summary with the key points and review them for safety.

A2:

1. **Read the code aloud**(or in your mind) similar to how you would read a book. Simultaneously, create a mental **summary** of what the code does ignoring the detailed specifics.

```java
// Example:
for(step=0;step<n-1;++step)
    for(i=0;i<n-step-1;++i)
    {
        if(data[i]>data[i+1])  
        {
            temp=data[i];
            data[i]=data[i+1];
            data[i+1]=temp;
        }
    }
```

**Helpful Summary**: The above [code snippet](https://www.programiz.com/dsa/bubble-sort) sorts the data integer array in ascending order.

2. **Do Onion reading - multiple passes,** analyze code the way you would peel onions.

A first quick pass to get the summary of the code and subsequent multiple passes to uncover specifics as you get deeper in to it. *One layer at a time.*

3. **Make small improvements to the code that you are reading** if practically feasible and do some testing. This will enable you to uncover dependencies and why the implementor thought the way they did.
4. **Use tools** like [doxygen](https://code.google.com/archive/p/doxygraph/), [UML diagramming](http://staruml.io/) to supplement your reading process with diagrams which will speed up the code reading and assimilation process.
5. **Talk to the author/module** expert to gain a deeper understanding, this will improve your perspective of the product in a short span of time.
6. **Never be afraid** to read any code, you can say a lot by just looking.
7. **Trust comments with a pinch of salt** because *“Code doesn’t lie comments do”*, or rather “*Comments may become outdated”.*

## Reference 3

- I try to find a high-level abstraction as a starting point and then gradually dig deeper into the internals. I like to make notes in OneNote as I study the code, describing the flow and how the code works. These are short-form notes.
- Once I have an overview formed the next step is to go deeper. At this point, I’m trying to understand why code has been written as it is. I like to think about how I’d have chosen to approach a problem and contrast it with the actual implementation. Is the author’s approach better or more efficient? Should I file it away for when I next face a similar requirement?
- This second stage also improves my skills of working with tools such as my IDE. I find tricks and shortcuts to save time when navigating the code. Something I do often is to pull out a class into a window on my second monitor so I can follow code flows more quickly. 
- Try to break things down into smaller chunks to work through in your head. What is the code doing and also why has it been written that way? Has the author used an approach to solving something you had not previously considered?





## References

[1] [How to read code without ripping your hair out](https://medium.com/launch-school/how-to-read-source-code-without-ripping-your-hair-out-e066472bbe8d)

[2] [What is the best way to read source code?](https://www.quora.com/What-is-the-best-way-to-read-source-code)

[3] [BECOME A BETTER DEVELOPER BY READING SOURCE CODE](https://www.stevejgordon.co.uk/become-a-better-developer-by-reading-source-code)