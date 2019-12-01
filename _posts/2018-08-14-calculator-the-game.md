---
title: "(DRAFT) Solving Calculator: The Game"
layout: post
categories: blog
tags: [solving, games, draft]
---
_Note: this is a draft of a post that I will probably never finish._

I was playing this nice puzzle game called "Calculator: The Game". It's really cool, you should check it out ([iOS](https://itunes.apple.com/us/app/calculator-the-game/id1243055750?mt=8), [Android](https://play.google.com/store/apps/details?id=com.sm.calculateme&hl=en)).

The interface is just a calculator with slightly modified buttons. In each level you are tasked with getting from starting number to the goal number, buttons change between every level and number of moves is limited.

It starts very easy but gets tricky with longer *paths*. I said *paths* because one can imagine all the possible states as vertex and all operations as edges connecting them. Then the goal of each level is finding the path (shorter than the move limit) from starting number to the target one. That's a book example of [breadth-first search](https://en.wikipedia.org/wiki/Breadth-first_search).

_TODO: graphviz example?_

Since I was playing on my phone I jumped to [Pythonista](http://omz-software.com/pythonista/) and implemented a quick BFS with some helpers to define buttons properly:
```python
from collections import deque

def make_maker(calc_fun, label_fun):
	def maker(*args):
		def operation(x):
			return calc_fun(x, *args)
		operation.label = label_fun(*args)
		return operation
	return maker

def sig(x):
	return -1 if x < 0 else 1

digit = make_maker(lambda x, n: x * (10 if n < 10 else 100) + sig(x) * n, lambda n: str(n))
mul = make_maker(lambda x, n: x * n, lambda n: '*' + str(n))
div = make_maker(lambda x, n: None if x % n else x // n, lambda n: '/' + str(n))
plus = make_maker(lambda x, *args: x + args[0], lambda *args: '+' + str(args[0]))
minus = make_maker(lambda x, n: x - n, lambda n: '-' + str(n))
power = make_maker(lambda x, n: x ** n, lambda n: '^' + str(n))
neg = make_maker(lambda x: -x, lambda: '+/-')()
shift = make_maker(lambda x: x // 10, lambda: '<<')()
rotr = make_maker(lambda x: int(str(x)[-1] + str(x)[:-1]), lambda: 'Shift>')()
rev = make_maker(lambda x: sig(x) * int(''.join(reversed(str(abs(x))))), lambda: 'Reverse')()
mirror = make_maker(lambda x: int(str(x) + ''.join(reversed(str(abs(x))))), lambda: 'Mirror')()
repl = make_maker(lambda x, *args: int(str(x).replace(str(args[0]), str(args[1]))), lambda *args: '{}=>{}'.format(*args))
sum_ = make_maker(lambda x: sum(int(i) for i in str(abs(x))), lambda: 'SUM')()

class Edge (object):
	def __init__(self, steps=0, source=None, button=None):
		self.steps = steps
		self.source = source
		self.button = button

	def __repr__(self):
		if self.steps:
			return '{} {} (steps: {})'.format(self.source, self.button.label, self.steps)
		else:
			return '{} (start)'.format(self.steps)

buttons = [mul(3), plus(8), plus(2)]
buttons.append(mirror)
buttons.append(rev)
start = -1
target = 2020

visited = {None: Edge(), start: Edge()}
to_visit = deque()
to_visit.append(start)
current = None
while to_visit and current != target:
	current = to_visit.popleft()
	source_edge = visited[current]
	steps = source_edge.steps + 1
	for b in buttons:
		reached = b(current)
		if reached not in visited:
			edge = Edge(steps, current, b)
			visited[reached] = edge
			to_visit.append(reached)

current = target
while current != start:
	source_edge = visited[current]
	print('{} = {}'.format(current, source_edge))
	current = source_edge.source
```

Output:
```
2020 = 2018 +2 (steps: 8)
2018 = 2010 +8 (steps: 7)
2010 = 2002 +8 (steps: 6)
2002 = 20 Mirror (steps: 5)
20 = 12 +8 (steps: 4)
12 = 21 Reverse (steps: 3)
21 = 7 *3 (steps: 2)
7 = -1 +8 (steps: 1)
```

The algorithm works really well and in some cases it found a path that was shorter than the number of moves.

I was happily using it to pass the levels I was stuck on until..

## First fuck up - button that changes other buttons

_TODO: graphviz for buttons in states?_

## Second fuck up - store button

_TODO_

## Another feature - teleports

_TODO_

## Conclusions

Some of the solutions are much better than the limits provided.

I've even found a few crashes. Reported them to the creators but they don't respond.

**Spoilers alert** The game has a really cool ending, [check it out](https://youtu.be/Bq8nZxurItk)!
