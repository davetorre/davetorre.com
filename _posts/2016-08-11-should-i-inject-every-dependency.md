---
layout: post
title: "Should I Inject Every Dependency?"
date: 2016-08-11
---

We're taught to inject dependencies.
It's the Dependency Inversion Principle, and gives you the Open-Closed Principle for free.
But it seems to favor configuration over convention, flexibility over ease-of-use.
Say I want to add the number two to a bunch of numbers.
I might write:

```javascript
var addTwo = function (x) {
    return x + 2;
};
```

But you could argue that the number 2 is a dependency.
Why not inject it?

```javascript
var add = function (x, y) {
    return x + y;
};
```

Now we have a much more flexible function but it requires more setup.
Which one is better?

## Backbone ##

In Backbone projects I've worked on, views are composed of smaller views.

```javascript
class SmallView extends Backbone.View
  render: -&gt;
    @$el.html('some text')
    @

class BigView extends Backbone.View
  render: -&gt;
    @$('[data-id=small-view-container]').html(new SmallView().render().$el)
    @

class App
  start: -&gt;
    $('[data-id=app]').html(new BigView().render().$el)

new App().start()
```

BigView depends on SmallView and App depends on BigView.
If we inject those dependencies we get:

```javascript
new App(new BigView(new SmallView())).start()
```

Not so bad at the moment.
But oftentimes the BigView will depend on several smaller views, and those on other smaller views.

```javascript
new App(new BigView(new SmallView1(), new SmallView2(new SmallerView1())).start()
```

Some views fetch data from servers and pass some of that data to child views.
This would make it impossible to initialize all views up front unless all data was fetched up front.
That's not really acceptable.

What if a library required this much setup?
And then you mixed it with your own dependency-injecting code?

What I'm saying is, sometimes it makes sense to depend on concretions.
It does mean that I can't reuse BigView with a different SmallView unfortunately.
But I can very easily use BigView or SmallView separately:

```javascript
new BigView()
new SmallView()
```

## Decisions ##

I started a tic tac toe game recently for fun and realized that I'm always asking this question about if something is easy to work with and also easy to extend.
I started with the players. Here were some ideas for how to create them:

```javascript
new MinimaxPlayer()
new ConsolePlayer()
new MinimaxPlayer('X')
new ConsolePlayer('O')
new AIPlayer('X', minimax, rules)
new HumanPlayer('O', consoleIO)
```

I really liked the idea of a player as being some object that took a token in its constructor, and had a move function that took a board and returned a new board with a move made with the token.
To get different types of players I'd pass in a `chooseMove` function that Player's move function could pass a board to for deciding which space to claim before returning the new board.

```javascript
new Player('X', chooseConsoleMove)
new Player('O', chooseMinimaxMove)
```

This actually forced `chooseConsoleMove` to depend directly on a console UI which was a trade-off I was willing to make.
If I wanted other types of UIs in the future, I'd be happy to create new `chooseMove` functions for them.

Here's what I ended up with for the game runner:

```javascript
var run = function (board, currentPlayer, otherPlayer) {
    if (isTttGameOver(board)) {
        return board;
    } else {
        var newBoard = currentPlayer.move(board);
        return run(newBoard, otherPlayer, currentPlayer);
    }
};
```

I passed the players in because I wanted their tokens and who goes first to be decided dynamically. I passed in the board because I liked having the function be recursive and needed to start the always-changing board off somewhere outside the function itself.

Then came the question of whether to pass in the `isTttGameOver` function. If I passed it in I'd allow the run function to be reused with different game rules. But I didn't think the rules would change any time soon and liked the simplicity of passing a board and two players to a `run` function too much to do that:

```javascript
var player1 = new Player('X', chooseConsoleMove);
var player2 = new Player('O', chooseMinimaxMove);
var emptyBoard = ['', '', '', '', '', '', '', '', ''];
var resultBoard = run(emptyBoard, player1, player2);
```
