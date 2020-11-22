---
title: "Thoughts on Advent of Code 2018"
date: 2019-02-10T19:00:00+10:00
draft: false
slug: "advent-of-code-2018"
---

I recently participated in [Advent of Code 2018](https://adventofcode.com/2018/about). Overall it was a very positive experience and I thought I would share some thoughts about the whole thing (albeit a lot later than intended!).

I learnt about Advent of Code less than 24 hours before the 2018 event (the fourth of its kind) began, after it was shared with me by a friend who has participated in previous years. My first thought was of other programming challenges such as [Project Euler](https://projecteuler.net/) and [Kattis](https://open.kattis.com/), both of which I've trialled briefly in the past. After reading some of the puzzles from previous years I was also reminded of the "problem solving competitions" I participated in back in my school days - simple questions that tested basic logic, mathematics and reading comprehension. A such I was instantly hooked - not just for the puzzles themselves, but also the promise of a month long commitment that would allow me to procrastinate on all the things I _should_ have been doing to finish the year!

Many others use the event as an opportunity to learn and gain experience with a new programming language. While this is a great example of how to approach it, as a day-to-day "enterprise" developer I expected (and was correct) that I would have enough of a challenge completing the puzzles with a familiar programming language. As such I stuck with my staple C#. I used [LINQPad](https://www.linqpad.net/) for my IDE which was great for writing code quickly and creating quick and dirty visual output which I could compare to the samples provided.

Thanks to a brief look at earlier puzzles and a healthy dose of imposter syndrome I expected to struggle after the first couple of days, but with a little perseverance and the occasional bit of guidance from peers I was able to complete each puzzle on the same day it was released. Due to the imperfect start time and my choice of a fairly verbose language, I was never quite racing for the leaderboard. Points are awarded to the top 100 finishers of each puzzle, and the only ones I managed were, quite appropriately, for the very final puzzle which rewarded those who had completed the earlier 49 puzzles successfully. No doubt the distraction of Christmas Day played in to this, but nonetheless I was surprised and pleased with my final results.

{{< figure src="/img/advent-of-code_1.png" alt="My completed advent calendar." title="My completed advent calendar." >}}

At the end of each day I would often discuss the day's puzzle with others. It was great to see how others approached the problems, and more often than not I walked away with ideas for improvements I could make to my own solutions. I especially liked the various visualizations that people shared on the [subreddit](https://www.reddit.com/r/adventofcode/).

Looking back, there were a number of puzzles that stood out to me:

### Most memorable ###

[Day 9: Marble Mania](https://adventofcode.com/2018/day/9) - This puzzle, featuring a cyclical series of marbles, was the first one where my initial naive solution was completely unsuited to the scale of the problem. I was forced to brush off my linked list knowledge in order to solve it.

[Day 10: The Stars Align](https://adventofcode.com/2018/day/10) - This puzzle, featuring a collection of stars that are slowly coming together, required knowing exactly when to pause in time and read a particular message. While many I spoke to experimented by hand manually until identifying when the points met, I had the bright idea of reusing some knowledge I'd picked up from an earlier puzzle. By calculating the Manhattan distance between the most upper-left and bottom-right corners and halting when the distance was at its smallest, I was able to precisely identify when the message was presented.

{{< figure src="/img/advent-of-code_2.png" alt="The stars align with a message." title="The stars align with a message." >}}

[Day 15: Beverage Bandits](https://adventofcode.com/2018/day/15) - This puzzle, featuring a battlefield of elves and goblins fighting against each other, was memorable for a number of reasons. For starters, it took me the longest of any puzzle to complete. Despite my best efforts, I was never able to solve the pathfinding aspect of it alone and had to pull in a library featuring Dijkstra's algorithm to help. There were also a number of subtle bugs that were not evident from running the samples provided, so they took awhile to find and fix. Despite all this, it felt like coding a little video game simulation which really made me want to do more like it.

{{< figure src="/img/advent-of-code_3.png" alt="Elves vs Goblins." title="Elves vs Goblins." >}}

[Day 17: Reservoir Research](https://adventofcode.com/2018/day/17) - This puzzle featured a flow of water that would fill up various containers scattered around the grid. I loved everything about this puzzle. The theme, my solution (a single stack that slowly worked its way around the entire grid), the final output and the visualisations that others created.

{{< figure src="/img/advent-of-code_4.png" alt="A snippet of a much larger scene featuring flowing water." title="A snippet of a much larger scene featuring flowing water." >}}

### Most difficult ###

[Day 19: Go With The Flow](https://adventofcode.com/2018/day/19) - I don't know how best to explain this one, but it's basically one in a series that deals with a minature computer that can process a predefined set of instructions. Part of this specific puzzle required inspecting the instructions that were being executed to determine what their intent was. This was brutal and I really struggled with this kind of analysis. In the end I managed to solve it by looking at a particular memory register with a simpler input and... well, I got lucky!

[Day 23: Experimental Emergency Teleportation](https://adventofcode.com/2018/day/23) - This puzzle featured a number of objects in a very large 3D space, and finding the point in that space within range of the most objects. I never came close to finding a solid, completely automated solution for it. Instead I picked a point in space and moved it large leaps at a time to find the best point. Then I'd narrow the search and repeat it, over and over again, until I found the best point. I'm still surprised it worked!

---

As I would love to do this again in future, I've been thinking about what it would take to to improve the time it takes me to solve these problems. So far I've come up with the following:

### Get better at parsing solution inputs ###

For anything more complicated than string splitting, I wasted a lot of time writing the Regex I needed. I often resorted to piecing together bits & pieces from Google. For [one particular puzzle](https://adventofcode.com/2018/day/24) I didn't even bother trying to parse it and just coded the input manually. I could save a lot of time by improving my Regex skills and having a collection of common expressions ready to go.

### Learn more data structures and search algorithms ###

The nature of my day to day work is that I don't need much beyond the basics. Everything I get out of the box with .NET is usually sufficient for what I need, but that definitely wasn't the case here. Many of my solutions were suboptimal, while for others I relied on an external library (I was able to limit this to just one, [Dijkstra.NET](https://github.com/matiii/Dijkstra.NET)). I attempted to implement a similar variant of this myself which would allow for some further customisation, but it was orders of magnitude slower. This is where I will need to spend the most time, learning to create performant implementations of trees and graphs with appropriate search algorithms.

### Spend less time print debugging ###

Many of the puzzles are described and demonstrated in a visual manner, such as traversing a maze, exploring a mine or simulating minions fighting on a battlefield. To confirm and/or debug parts of my solutions I often found it useful to print out the objects in question after each step to help identify issues. While effective it is also time consuming, with most of this code not contributing to the final solution. 

### Reuse more code ###

As evidenced by some of the points above, I often found myself copying and pasting code from earlier puzzles (I lost count of how many 2D grids I needed). If I can have even the basics ready to go then I'll be more prepared for some likely puzzles.

### Slow down ###

Just a little. Not too much, of course, as it is a competition after all - but I found that trying to rush things often lead me down the wrong path that would often take several minutes to recover from. This makes sense when you consider that programming is usually a slower and more methodical activity, so I'm not used to thinking of suitable solutions at such a fast pace.

---

Though the event is over now, anyone can still attempt the puzzles from all previous years at any time. I would strongly recommend it to anyone who is interested in a challenge that's probably different than what you're used to working on each day.

Finally, I would like to thank two people: [Infilament](https://twitter.com/Infilament), with whom I had many great discussions about the puzzles and was a regular source of encouragement; and [Eric Wastl](https://twitter.com/ericwastl), for putting on such a great event.

One day I would like to join the [271 others](https://twitter.com/ericwastl/status/1082420178510667781) who have achieved all the current stars. I am currently at 87/200. In the meantime, you can find all my 2018 solutions on [GitHub](https://github.com/pjohannessen/adventofcode/).