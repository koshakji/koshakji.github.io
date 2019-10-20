---
title: Sliding Tily
layout: post
date: 2019-10-20
---

Today, I'm launching my first app to the App Store.
It's a little game called Sliding Tily, or just Tily for short.

This game is a sliding tile puzzle game with a small number of levels, and a few search algorithms that can solve each level with varying levels of efficiency, including:

- Depth-First Search
- Breadth-First Search
- Uniform-Cost Search
- Hill Climbing (pure heuristic search)
- A-Star (A*)

The goal of the game is to move the main tile (colored in red) to the rightmost bottom position, in the lowest number of moves.

![Sliding Tily Screenshots](/assets/tily-announcement-screenshots.png)

The whole game is open source and you can check out the source code [here](https://github.com/koshakji/tily).

I know it's not a huge game, it started out as a university project, but I thought it would be fun to release to the App Store, alongside the code implementing some common search algorithms in a way that's generelizable to other [state space search](https://en.wikipedia.org/wiki/State_space_search) problems, which may be helpful to someone learning those algorithms.

You can check it out [here](https://apps.apple.com/us/app/sliding-tily/id1482540864). I hope you enjoy it!