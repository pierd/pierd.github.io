---
title: (DRAFT) Timing Attack in practice
layout: post
categories: blog
tags: [security, draft]
---
_Note: this is a very raw draft of a post that I will probably never finish. I tried to use a prepared timing attack against a dummy service of my creation - no luck. It's not as easy as it sounds. In the end I got bored._

_TODO: Explain: what is a timing attack?_

https://en.wikipedia.org/wiki/Timing_attack

_mentioned on pycon au, show example times_

basic idea with labyrinth of smells analogy

usual protections:
- number of tries - slow down
- number of tries - captcha/loytering
- number of tries - time ban
- password hashed anyway - labyrinth too complicated
- strictly for timing attacks - O(1) (constant) time functions - spreading smells

we are using normal compare in 2 places - it should be possible to attack us

```python
if signature == expected_signature.lower():
    valid_signature = True
```

```c
return !memcmp(digest.data(), hash, SHA256_DIGEST_LENGTH);
```

try python from the same process

[String comparison in Python](https://github.com/python/cpython/blob/1e596d3a20a1a9d1ef15218ef33795bc9e651b7a/Objects/unicodeobject.c#L10878)

try c++ from the same process

Blow the cache (allocate every time?)

Try distribution


win? fail?

in practice it might be different
- combined with other attacks (remote code execution)
- just checking if entity (user) exists - known for ssh

real world examples?

You can find all source code from this post [here](https://github.com/pierd/timing-attack-playground).

links:
https://www.blackhat.com/docs/us-15/materials/us-15-Morgan-Web-Timing-Attacks-Made-Practical.pdf

https://github.com/dmayer/time_trial

https://codahale.com/a-lesson-in-timing-attacks/?
