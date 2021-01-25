---
title: "A short recap of Advent of Code 2020"
date: 2021-01-25T11:50:00+10:00
draft: false
slug: "advent-of-code-2020"
---

Writing about my experiences with [Advent of Code](https://adventofcode.com/) has become something of a yearly tradition for me, and while 2020's entry has lapsed into 2021, I am here to continue the tradition all the same!

In a year otherwise marred by difficulties for so many people on so many fronts, it was nice to have the distraction of programming puzzles for an hour or so each December evening.

My only goal this year (in addition to completing all the puzzles) was to try and leverage some of the new C# 9.0 functionality such as [record types](https://docs.microsoft.com/en-us/dotnet/csharp/whats-new/csharp-9#record-types). These were useful but haven't quite made it into my "muscle memory" yet, so it's hard to reach for new features when you're racing against the clock.

{{< figure src="/img/advent-of-code-2020_1.png" alt="My completed advent calendar." title="My completed advent calendar." >}}

{{< figure src="/img/advent-of-code-2020_2.png" alt="All puzzles completed from every event." title="All puzzles completed from every event." >}}

Like last year I was fortunate enough to be participating in the event with several peers from my local developer community. We would often discuss each day's puzzle, share the most interesting solutions, and provide support to each other as needed. It proved to be the most memorable part of the event, which I'll touch more on later.

I will now share some thoughts on the puzzles themselves - if you have any interest in completing them yourself, you may want to turn away now!

*** **Spoilers ahead!** ***

For the most part, I found 2020's puzzles to be of excellent quality. The descriptions were clear and easy to follow and featured many interesting themes. The problems were challenging at times but not overly difficult. I expected more of a difficulty spike in the last week than there was. There were no recurring solutions like last year's IntCode, and the majority of puzzles felt somewhat familiar with those of past events, which is understandable when you consider the number of new ones that need to be written each year.

I was disappointed that there were no graph problems to solve, as those have become a particular favourite of mine - although it does give me another year to come up with my own implementations of a graph type and search algorithms.

I also enjoyed this year's ASCII art - in particular the animations, the puzzles presented out of numerical order the presence of the sea monster from Day 20, and - perhaps most of all - the alliterative puzzle names.

## The Puzzles ##

### Favourites ###

[Day 4: Passport Processing](https://adventofcode.com/2020/day/4) - I am not very skilled at parsing text cleanly, but this was a fun early challenge and I enjoyed having to write custom validation for each type of input - though my initial solution could certainly use some refactoring!

[Day 18: Operation Order](https://adventofcode.com/2020/day/18) - Changing the order of operations in a math equation - easy enough to explain, but more challenging to implement! Writing a custom parser was a lot of fun. My solution to part one was insufficient for part two, which I had largely anticipated, so I had to re-write most of it. My solution is an absolute mess and there remains a lot to be learned from learning to solve this in a better way.

[Day 22: Crab Combat](https://adventofcode.com/2020/day/22) & [Day 23: Crab Cups](https://adventofcode.com/2020/day/23) - While the relationship between these two puzzles was mostly thematic, I just love games and the solutions that these two required.

[Day 24: Lobby Layout](https://adventofcode.com/2020/day/24) - This was the second puzzle I can recall that required a hex grid. It's just enough of a twist on a traditional grid to make modeling and navigating it a lot more interesting and provides for fond recollections of the most recent [Civilization](https://en.wikipedia.org/wiki/Civilization_(series)) games.


### Most difficult ###

[Day 10: Adapter Array](https://adventofcode.com/2020/day/10) - I had a lot of trouble "visualising" the solution to this, and I had to brush up on the [dynamic programming](https://en.wikipedia.org/wiki/Dynamic_programming) I was introduced to in an earlier event.

[Day 13: Shuttle Search](https://adventofcode.com/2020/day/13) - Solving part one wasn't particularly difficult, but making it performant enough for 100000000000000+ iterations certainly was! I found a way of "shortcutting" it enough to solve in a reasonable time, but it is still by no means the most optimal solution.

---

I didn't make it onto any daily leaderboards for this event, which was not surprising given the considerable increase in the number of competitors. I think this is unlikely to change in the future, which has allowed me to question how important the competitive aspect of Advent of Code is to me. It's in my nature to treat it as a competition, but I don't think it makes the event more interesting for me. I had just as much fun on the days where I couldn't participate at the normal start time, and if anything it was easier to step away from the computer and think about the problem without the self-imposed pressure of a clock. For future events I plan to focus on coming up with interesting, clean code and on learning from my peers by comparing their solutions to my own - solutions I develop at my own pace, and in my own time.

As always, this blog would not be complete without a thank you to [Eric Wastl](https://twitter.com/ericwastl) and his team of beta testers and community managers for creating such a great event.

You can find my solutions for all 300 stars on [GitHub](https://github.com/pjohannessen/adventofcode/) - and if *you* enjoyed the event, please consider [supporting it](https://adventofcode.com/2020/support).