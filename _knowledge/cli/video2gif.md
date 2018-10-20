---
title: Make a GIF out of a video
layout: knowledge
tags: [cli, gif]
---

```shell
$ ffmpeg -i in.mov -pix_fmt rgb8 -r 10 -f gif - | gifsicle --optimize=3 --delay=12 > out.gif
```
