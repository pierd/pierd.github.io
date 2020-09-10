---
title: (DRAFT) Background stars with parallax in Deasil
layout: post
categories: blog
tags: [rust, gamedev, deasil, draft]
---
_Note: this is a draft of a post that I will probably never finish._

After hearing about [ECS]() and [playing a bit with Rust]() I decided to try the both out and make a simple game. Something simple, realtime (as in not turn based) and fun. That is how [Deasil](https://github.com/deasilgame/deasil) was born.

It is supposed to be a 2D space shooter and as a 2D space game it needs [a parallax background with stars](https://www.youtube.com/watch?v=KyQdK56Qee0). I implemented a first version of it [yesterday](https://github.com/deasilgame/deasil/commit/13b9ab435f05da0126666c0b339326e0d2025738), I am quite happy with the result and here is how it works.

## Planes, visible sky and sectors

_TODO: Explain: check what's visible basing on transform matrix_

_TODO: Explain: divide into planes to achieve parallax_

_TODO: Explain: divide each plane into sectors to have stable stars between frames_

## Pseudo-random number generation

_TODO: Explain: to get the same stars on each frame_

_TODO: Explain: hashing coords to get the seed and using it for new PRNG_

## Conclusions

It gives nice feeling of the space and makes controlling the ship easier because now you can see your velocity.

![Parallax]({{ "/assets/deasil-parallax.gif" | relative_url }})

Deasil is more of a prototype and a learning project. For a real one this parallax would have to be implemented using shaders to offload the computation from CPU. You can find the current simple implementation [here](https://github.com/deasilgame/deasil/blob/2a9b80e66610191ea92cc2d4979bcdc2147aeed7/src/frontend_piston/rendering.rs#L128).
