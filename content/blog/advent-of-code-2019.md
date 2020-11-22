---
title: "Wrapping up Advent of Code 2019"
date: 2019-12-26T22:05:00+10:00
draft: false
slug: "advent-of-code-2019"
---

Another year, another [Advent of Code](https://adventofcode.com/) event completed! I've blogged before about [participating in the 2018 event](/blog/advent-of-code-2018/) and [catching up on the 2015, 2016 and 2017 events](/blog/catching-up-on-advent-of-code/), so I thought I would keep the tradition going by sharing my experience with the 2019 event that has recently finished. This blog assumes a certain level of familiarity with Advent of Code - if you don't know what it is, check out the website or one of my previous blog posts.

I had two goals for this year's event; completing it, and making a daily leaderboard at least once. I managed to do both!

{{< figure src="/img/advent-of-code-2019_1.png" alt="My completed advent calendar." title="My completed advent calendar." >}}

{{< figure src="/img/advent-of-code-2019_2.png" alt="233 points acquired from days 10, 19 and 25." title="233 points acquired from days 10, 19 and 25." >}}

I had been looking forward to this event all year. I was lucky enough to have a number of peers participating this year as well. At the end of each day we would often discuss the day's challenge, compare solutions and provide support to others in our group who were also particpating. This was a great addition that added a positive social component to an otherwise mostly solo affair, and I will certainly try and repeat this aspect in any possible future events.

From this point forth I will be sharing specific details about some of this year's puzzles (and solutions), so if you are interested in completing these puzzles yourself completely fresh, you may want to turn away now!

*** **Spoilers ahead!** ***

This year I was surprised to find myself a little disappointed at the end of the first week, for two notable reasons.

Firstly, I found a number of the early puzzles to be unnecessarily "word-y". Puzzles were described in such verbose detail that it often took me several reads to understand the puzzle and to find the many important bits of detail woven throughout. In some cases I found the descriptions to be quite ambiguous. I think one trait of a good puzzle is that the "what" of "what you need to do" is relatively clear, while the challenge is in the "how" of "how am I going to do it?". Some of the earlier puzzles felt like the reverse; figuring out the "what" from the description, then letting the "how" fall easily in to place. If I had to speculate I would guess this was intentional (at least in part), both to help the less experienced programmers/puzzlers and also to prevent the more seasoned veterans from rushing through the puzzle without taking the time to read it carefully. Still, I was worried this might persist throughout the event.

Secondly, the initial few "Intcode" puzzles - a collection of puzzles that required building an interpreter for a program defined only by a series of integers - defied my expectations around reuse of existing materials. I can't recall using any given program for more than two days in any previous event, but after just 7 days it had already been used 3 times. As someone who likes starting "fresh" each day, this initially felt somewhat annoying and I was worried about how long it would continue.

A true lesson in patience - I think both worries were unfounded. The quality of the puzzles only improved over time, and I found the descriptions became clearer and more succinct. And as for the Intcode puzzles, well, I really came to enjoy them. While somewhat atypical, their repetition meant that certain programming skills that would otherwise be ignored in a competition like this (such as the extensibility of a solution) came to be very important as we were required to extend and improve each previous iteration. It also meant that certain "boilerplate" activities (such as input parsing) were only really required on alternate days, which helped mix things up a bit.

After four previous years I can understand the desire to shake things up, and I think this proved to be a great way to do it.

Now, on to some of the most notable puzzles!

## The Puzzles ##

### Most disliked ###

[Day 22: Slam Shuffle](https://adventofcode.com/2019/day/22) - I want to get this one out of the way quickly as I don't recall ever really _disliking_ a puzzle before now, but this puzzle stood out in what was otherwise another great year of puzzles. I would describe myself as an average programmer who still has a reasonable chance of solving most AoC problems. Sometimes I'll need the occasional hint to steer me in the right direction or to catch a particularly frustrating bug, but I usually manage okay. But I can say confidently that I never would have solved part 2 of this puzzle on my own. No amount of clues of google searches would have provided me the (seemingly) advanced knowledge of math required to solve it. And maybe that's ok - not every puzzle has to be made for me, and I know many others liked it and that's a good thing. But it left me feeling frustrated and a little bit stupid in a way I hadn't experienced before. I shamelessly copied my solution and feel no remorse for doing so. Even after reading the explanation I still didn't understand the underlying math involved. I expect this may become more common as authoring new and unique puzzles becomes increasingly difficult each year.

With that out of the way, I will move on to the many more positive aspects.


### Most difficult ###

[Day 16: Flawed Frequency Transmission](https://adventofcode.com/2019/day/16) - Attempting to explain this puzzle and its solution would do it a great injustice. But it was tough, and I required a hint or two to lead me down the right path.

[Day 18: Many-Worlds Interpretation](https://adventofcode.com/2019/day/18) - I'm sure there's a "correct" and "performant" way to solve this puzzle, which required collecting a number of keys from a maze which would each allow you to pass through a specific door, but I found neither. I tried about 10 different solutions over the course of nearly a full day before finding a solution that worked, albeit requiring about half an hour of runtime. My second solution fared no better and required even longer to solve. As challenging as it was it didn't feel unfair at all, and I adore the "theme" of the puzzle. At some point I would like to sit down with someone more experienced and talk through what the ideal solution looks like.

[Day 21: Springdroid Adventure](https://adventofcode.com/2019/day/21) - Programming _inside_ my program? This puzzle required supplying a series of instructions to my existing Intcode machine to stop a "springdroid" from unnecessarily falling down a series of holes. A challenging puzzle in its own right made even more difficult by being inside another program entirely.


### Most memorable ###

[Day 13: Care Package](https://adventofcode.com/2019/day/13) - This was the first puzzle that showed me what Intcode is really capable of - a playable game of [Breakout](https://en.wikipedia.org/wiki/Breakout_(video_game))! While my automated solution was relatively trivial, the sheer simplicity and cleverness of this puzzle means it will stay with me for a long time.

[Day 20: Donut Maze](https://adventofcode.com/2019/day/20) - The most exciting thing about this puzzle (which involved a series of duplicate recursive mazes...) was that I had a genuine "lightbulb" moment. After solving the first part, I laid eyes on the second and had my mind blown. I actually told a friend that it looked so tough that I wasn't even going to attempt it. So I decided to walk the dog instead, and while doing so experienced a number of "Oh, but what if I..." moments, and by the time I had returned home I knew exactly how I wanted to try and solve it. And it worked! It was an incredibly rewarding feeling, and in a way represented what the best puzzles are all about.

[Day 25: Cryostasis](https://adventofcode.com/2019/day/25) - I can't imagine a more exciting or fitting puzzle to complete this year's event with. A brilliant mix of the expectated - another Intcode puzzle - with the unexpected _interactive text adventure game_. My Intcode machine only required minor adjustments in order to "play" the puzzle, and I quickly forgot I was in a competition at all as I moved from room to room collecting items, getting stuck to giant electromagnets or entering infinite loops. While somewhat non-traditional in that I didn't write code to find the final answer (though many others have), I think it was the perfect way to celebrate the end of the event and the achievement of carrying our Intcode machines all the way to Christmas.

That's it for the puzzles, but as for some more general lessons...

## Mistakes I made... again

* Rushing - For the first couple of puzzles, I tried to do what I have often tried to do - rush. Whether by jumping straight to the input before reading the puzzle, or by only skimming the puzzle description. This always proved to be a mistake as I missed critical information that cost me far more time than I would have spent reading it properly. I suspect some of the earlier puzzles were written in such a way to punish people for attempting that, and I'm honestly glad - I learnt my lesson quickly and proceeded more steadily after the first few days, calmer and more prepared.

* Not writing my own graph library - I use C# and .NET which can be a little light on certain data types that would be useful. I've mentioned before my use of [Dijkstra.NET](https://github.com/matiii/Dijkstra.NET) to provide me graph capabilities and my desire to reproduce this functionality myself. I haven't, and I really need to - there continue to be situations where I want to modify the "default" behaviour and am unable to do so. I'd also just love to be able to consistently write such an algorithm myself.

* Not having my own library of utilities - I've also mentioned my desire to create my own library of common utilities, which I still haven't done. This means I'm often hurrying to search through old solutions in search of memorable functions that I'd like to reuse, costing me valuable time.

## Suggestions for next year

As great as this year's event was, there are a couple of small changes I'd like to see in future. I'm sure that not everyone would agree with these, and in some cases they may not actually align with the goals of Advent of Code. Additionally, some suggestions may be subject to performance or architectural limitations that make them unrealistic. But, I thought I'd throw them out there anyway.

* An additional private leaderboard ordering of "time to complete since first viewing" - This is a bit of a mouthful, but let me explain. The timer that records how long someone takes to complete a puzzle starts at the same time every day, regardless of whether you've actually started solving the puzzle or not. This makes sense for the public leaderboard and any alternative would be too easily gamed (by reading the puzzle anonymously, for example). However, I think it would be a great addition for private leaderboards where I'm only competing with a small number of peers. For example, I live in Australia where the puzzles start at 3pm each day. This means everyone in my group will start at different times depending on when they finish work, attend to other commitments and so on. Having an ordering that takes in to account when you _actually_ "start" (view the puzzle for the first time) would level the playing field in our group and eliminate the pressure to start as quickly as possible. As the default order and all other means of ordering would still remain, I think this addition could only be beneficial.

* Increase the size of the daily leaderboard - The 100 fastest solvers of each puzzle part are awarded points. Over time, the number of people participating in each event has increased. As such, I'd love to see an increase in the number of solvers awarded points as well (for instance, to 200).

* Show me my place on the overall leaderboard - The overall leaderboard is currently limited to the top 100. Even without showing the full list, I would be interested to know exactly where I rank with the points I have. (It has since been pointed out to me that [Extra AoC Stats](https://betaveros.github.io/extra-aoc-stats/) exists, so this may be unnecessary after all.)

---

Overall it was a great event, one that I think was definitely up there with 2018 in terms of quality. There was lots of surprises that kept the event fresh well in to its fifth year. With all 250 stars in my pocket, I can now rest for another year!

As always, this blog would not be complete without a thank you to [Eric Wastl](https://twitter.com/ericwastl) and his team of beta testers and community managers for creating such a great event.

If *you* enjoyed the event, please consider [supporting it](https://adventofcode.com/2019/support)! You can participate in any of the puzzles from any year at any time, so I definitely recommend checking them out if you haven't already done so.

In the meantime, you can find all my solutions on [GitHub](https://github.com/pjohannessen/adventofcode/).
