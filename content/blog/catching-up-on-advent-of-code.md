---
title: "Catching Up on Advent of Code"
date: 2019-08-22T13:30:00+10:00
draft: false
slug: "catching-up-on-advent-of-code"
---

Earlier this year I [posted about my experience](/blog/advent-of-code-2018/) participating in [Advent of Code 2018](https://adventofcode.com/2018). For those who aren't familiar, Advent of Code is (to quote the website):

> an Advent calendar of small programming puzzles for a variety of skill sets and skill levels that can be solved in any programming language you like.

On each December day leading up to Christmas all players are presented with an identical puzzle that requires programming to solve. Puzzles have two parts, the second of which is only provided after successfully completing the first. The second part compliments the first in a variety of ways, such as extending the program to deal with additional scenarios or by altering the input in a way that tests the performance limits of the code.

I thouroughly enjoyed the 2018 event, and to my surprise (at the time) I was able to complete all of the challenges. I liked getting to solve problems using solutions and patterns that are somewhat different from the ones I use day to day in my enterprise development work, and all without worrying too much about the quality or reusability of my code.

At the start of every year I like to set myself some goals for the following 12 months. I try to include at least one or two programming goals of some kind, like creating a small app for myself or learning a new language or framework. As a result of my positive experience with last year's event I decided to set myself the goal of completing the previous three events (2015, 2016 and 2017) as well, using the lessons I learnt from 2018.

The good news is - I did it! I have now joined the [362-odd people](https://twitter.com/ericwastl/status/1116561274379259904) who have achieved all 200 available stars.

{{< figure src="/img/catching-up-on-advent-of-code_2015.png" title="Advent of Code 2015" >}}


{{< figure src="/img/catching-up-on-advent-of-code_2016.png" title="Advent of Code 2016" >}}


{{< figure src="/img/catching-up-on-advent-of-code_2017.png" title="Advent of Code 2017" >}}


{{< figure src="/img/catching-up-on-advent-of-code_2018.png" title="Advent of Code 2018" >}}


Once again I thought it would be worthwhile to reflect on the experience and share my thoughts, starting with the things I set out to improve upon...

### Get better at parsing solution inputs

I didn't achieve the improvements I would have liked here. Instead of regex or another, more reliable means of input extraction, I just turned my string splitting up to 11 and this proved to be enough. It's ugly though, and I would consider limiting my options in future so I'm forced to improve here.

### Learn more data structures and search algorithms

I expected that I would need to know a lot more than what I currently do, but this wasn't really the case. I was able to implement a few tree-like structures and choose correctly between [breadth-first search](https://en.wikipedia.org/wiki/Breadth-first_search) and [depth-first search](https://en.wikipedia.org/wiki/Depth-first_search) when required, and this was sufficient. I continued to use [Dijkstra.NET](https://github.com/matiii/Dijkstra.NET) a few times, and I'd still like to get some experience implementing my own graph instead of relying on an external library.

### Reuse more code

I found very few opportunities for any notable code reuse, and most puzzles were different enough in some way that I found implementing independent solutions to be perfectly fine. I'm not so bothered about this one.

### Spend less time print debugging

I definitely improved here - very rarely did I resort to "drawing" my output to diagnose a problem, and I even spent less time debugging in general. I think this is largely because...

### Slow down ###

... I slowed down! I think this was the biggest change. In 2018 I was completing the puzzles each and every day at a competitive-like speed. I was prone to making silly errors or picking the wrong solution to the problem in front of me. In contrast, I solved the older challenges without the pressure of the clock, so I worked at a steadier pace that in many cases achieved a solution faster than I think I would have achieved otherwise. I think this is a good lesson that I can apply elsewhere as well.


Not knowing exactly what to expect from these earlier challenges, I think I slightly misjudged what areas I needed to improve; whether because I expected the puzzles to be even more difficult or because I was lacking in confidence, I'm not quite sure.

I'd now like to comment on some of the puzzles I completed and give out some pseudo awards. I have shared links to my respective solutions, but be warned - I made no changes after hacking together the the correct answer, and I do not consider them to be examples of readable or high quality code! If you are interested in trying these puzzles yourself you may want to skip ahead, as I may reveal information that will diminish your own experience.


### Most challenging puzzle ###

[2016 Day 11: Radioisotope Thermoelectric Generators](https://adventofcode.com/2016/day/11) | [My Solution](https://github.com/PJohannessen/AdventOfCode/blob/master/2016/11-2.linq) - I never expected a Day 11 puzzle to be the hardest, but it definitely was for me. I think there were a number of reasons for this. Firstly, the description of the problem is very verbose and it took me several reads to fully understand. Secondly, it wasn't obvious to me how best to represent the data. In the end I opted for a single array, with each element representing a generator or microchip with an integer value 0-3 representing the floor. Finally, the solution required many things: identifying valid states, identifying all possible states from a given state, breadth-first search of states, a heuristic for ranking states and trimming of "seen" states. I also struggled with the performance of my solution which wasn't completing even after several minutes because I was missing a `.Distinct()` that removed duplicate states when performing a search across a series of states. Even with all this my solution wasn't great, and had to be manually modified based on the total numbers of microchips and generators. This was the last puzzle I solved and I breathed a big sigh of relief upon completion.


### Hack-iest solution ###

[2015 Day 19: Medicine for Rudolph](https://adventofcode.com/2015/day/19) | [My Solution](https://github.com/PJohannessen/AdventOfCode/blob/master/2015/19-2.linq) - Part 1 of this solution, which involved applying replacement rules to generate new molecules, was easy enough. But Part 2 required finding the optimal series of replacements to apply in a specific order to reached a desire molecule in the shortest amount of steps. The difficulty was in knowing which replacement to apply at each step. I tried applying the replacements naively without success, then prioritizing the "biggest" replacements, which also didn't work. In the end I had to randomize the order of replacements at each step and run the program several times to find the desired result. I'm sure there was a proper, consistent way to do it, but I didn't stick around to find out!

### Most undeserved solution ###

[2016 Day 21: Scrambled Letters and Hash](https://adventofcode.com/2016/day/21) | [My Solution](https://github.com/PJohannessen/AdventOfCode/blob/master/2016/21.linq) - The first part of this solution was reasonable enough; apply a series of transformations to a "password" until the final scrambled password is identified. But part two involved applying the process in reverse and finding the original password from a pre-scrambled password. Rather than applying the steps in reverse, I simply scrambled all permutations of possible passwords until arriving at the given scrambled password. It worked (thanks to the limited range of valid inputs), but I still feel a little guilty!

### Favourite puzzles ###

[2015 Day 21: RPG Simulator 20XX](https://adventofcode.com/2015/day/21) | [My Solution](https://github.com/PJohannessen/AdventOfCode/blob/master/2015/21-2.linq) - This puzzle involved simulating a battle between a player (capable of equipping a weapon, armor and magic rings) and a powerful boss. While it wasn't particularly difficult, I'm a sucker for turn-based simulations!

[2015 Day 22: Wizard Simulator 20XX](https://adventofcode.com/2015/day/22) | [My Solution](https://github.com/PJohannessen/AdventOfCode/blob/master/2015/22-2.linq) - Like the above, but much more difficult! There were more permutations, more complicated effects that lasted for several turns, more edge cases and more performance considerations. I left the solution running for several hours at one stage because I wasn't abandoning certain, undesirable states. Although the final implementation was a bit all over the place, I think it remains my absolute favourite Advent of Code puzzle.


In hindsight I really regret not commenting the code of my solutions more, as after reviewing them for this blog post even I'm not 100% sure how some of them actually work!

It was also interesting getting to see how the puzzles have evolved over the years (albeit in reverse), and while I enjoyed all four years I definitely think the puzzles have improved over time, with the most memorable ones occurring in 2018. I'd like to thank [Eric Wastl](https://twitter.com/ericwastl) and the rest of the team once again for creating such a great series of events.

Completing all the puzzles took me a reasonable amount of time, and you might wonder whether it was worth it. For me, it was. For one thing it was a lot of fun, and that's a good enough reason on its own. But it also left me feeling just that little bit more confident in my programming abilities, which is a big bonus for me.

Looking ahead, I will certainly participate in future events, should they occur - I recognise that hosting them is a considerable effort and I don't take that for granted. I would also like to post in detail about any particularly interesting solutions I come up with in future, or even tackle some puzzles again in a language I'm less familiar with like F#.