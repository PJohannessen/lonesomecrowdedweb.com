+++
title = "Four In A Row with boardgame.io"
draft = false
date = "2018-01-03"
slug = "four-in-a-row-boardgameio"
+++

Google recently released a "not an official Google product" framework called [boardgame.io](https://github.com/google/boardgame.io). To quote the documentation:

> The goal of this framework is to allow a game author to essentially translate the rules of a game into a series of simple functions that describe how the game state changes when a particular move is made, and the framework takes care of the rest. You will not need to write any networking or backend code.

As I have a love of both board games and coding, I thought it would be a fun excercise to try building something simple with this new framework. The documentation already includes a great tutorial of Tic-Tac-Toe, so I have decided to expand on this example with the next easiest thing I could think of: Four In A Row.

I recommend reading over the [boardgame.io documentation](http://boardgame.io/) and tutorial first to familiarise yourself with the basic elements of the framework.


## Getting Started

Four In A Row involves two players taking turns dropping discs in to a seven-column, six-row grid. The first player to line up four of their discs horizontally, vertically or diagonally wins the game.

The boardgame.io tutorial is built using [create-react-app](https://github.com/facebookincubator/create-react-app), so I'll be sticking with that for this excercise. Getting started is as simple as a few commands:

``` text
$ npm install -g create-react-app
$ create-react-app four-in-a-row
$ cd four-in-a-row
$ npm install --save boardgame.io
```

This gives us our initial project:

![four-in-a-row initial project](/img/four-in-a-row-boardgameio_1.png)


## Defining the board state, available moves and victory condition

The framework requires that we define an initial `setup` function to describe our board state, a series of `moves` to modify it and return a new board state, and a `victory` condition so we know when to end the game.

Defining our board state is relatively straightforward. We want to create a grid of cells consisting of 7 columns and 6 rows. We will use the following identifiers for each cell:

* `0` indicates a cell with no disc
* `1` indicates a cell with a disc from Player 1
* `2` indicates a cell with a disc from Player 2

That's a lot of different numbers we'll be using, so let's set up some constants for them now.

``` javascript
const emptyCell = 0;
const p1disc = 1;
const p2disc = 2;
const numOfRows = 6;
const numOfColumns = 7;
const playerDiscLookup = {
  "0": p1disc,
  "1": p2disc,
};

export {
  emptyCell,
  p1disc,
  p2disc,
  numOfRows,
  numOfColumns,
  playerDiscLookup,
};
```

We'll use `playerDiscLookup` a bit later.

We will use the equivilent of a 2D array to represent our grid using an object with indexed properties for each row:

``` javascript
setup: () => {
  const grid = {};
  for (var rowIdx = 0; rowIdx < numOfRows; rowIdx++) {
    grid[rowIdx] = Array(numOfColumns).fill(emptyCell);
  }
  return ({ grid: grid });
}
```

For each of our rows we'll set up the appropriate number of columns each with an empty cell.

There is only a single move in our game; dropping a disc in to a column. The disc will always fall to fill the lowest available cell. We'll call it `selectColumn`.

``` javascript
moves: {
  selectColumn(G, ctx, columnIdx) {
    let grid = Object.assign({}, G.grid);
    for (var rowIdx = numOfRows - 1; rowIdx >= 0; rowIdx--) {
      if (grid[rowIdx][columnIdx] === emptyCell) {
        grid[rowIdx][columnIdx] = playerDiscLookup[ctx.currentPlayer];
        break;
      }
    }
    return {...G, grid};
  },
},
```

First we clone the existing board state so as to not mutate the existing state, a common pattern in React applications. Then we look at a single column starting with the bottom cell until we find an empty cell, which we then assign to the current player. Note that `ctx.currentPlayer` is a 0-based string representing the player number, which is why we use `playerDiscLookup' to determine the appropriate disc. Then we apply the new grid to our board state and return it.

Finally we need to define a victory condition, which should return `true` if the current player has now won after taking their move. In the interest of time I have adapted a suitable victory algorithm from [user ferdelOlmo on Stackoverflow](https://stackoverflow.com/a/38211417/129967).

``` js
victory: (G, ctx) => {
  return IsVictory(G.grid, ctx.currentPlayer) ? ctx.currentPlayer : null;
}
```

The final `App.js` file including the full victory check can be found [here](https://github.com/PJohannessen/four-in-a-row/blob/master/src/App.js).


## Rendering the board and accepting user input

The logic behind our game is complete, but we still need a UI.

React is a great library for such a UI, as many of the components we need will be similar to their real life counterparts. It also works well with our board state, where our declarative UI will be efficiently updated as our board state changes.

I'm going to start with the smaller components first and work our way up to the full board, starting with a single cell. Again, we will be sticking close to the Tic-Tac-Toe example from the boardgame.io tutorial.

A cell should render a single circle indicating either an empty cell, player 1's disc or player 2's disc. We'll use [fontawesome](http://fontawesome.io/icon/circle/) for our circles, with player 1 and 2 represented by red and blue discs respectively.

``` js
import React from 'react';
import { emptyCell, numOfColumns, numOfRows, p1disc, p2disc, playerDiscLookup } from './constants';
// Discs from http://fontawesome.io/icon/circle/
import WhiteDisc from './circular-shape-silhouette-white.svg';
import BlueDisc from './circular-shape-silhouette-blue.svg';
import RedDisc from './circular-shape-silhouette-red.svg';

const Cell = ({ cell }) => {
  let cellImage;
  switch (cell) {
    case p1disc:
      cellImage = RedDisc;
      break;
    case p2disc:
      cellImage = BlueDisc;
      break;
    default:
      cellImage = WhiteDisc;
      break;
  }
  return (
    <img alt="disc" src={cellImage} className="disc" />
  );
}
```

Next we will need a row or column component. The former will probably make the UI easy to lay out, while the latter would probably make for better user interaction (since a user will select a column to drop a disc in to, not a row). I've gone with a Row for the easier layout. A Row will be a simple collection of Cells. Like any React collection, each must have a unique key, and we must pass down the required props to each Cell.

``` js
const Row = ({ row }) => {
  const cells = row.map((c, idx) => <Cell cell={c} key={idx} />);
  return cells;
}
```

Next we need a grid, which will also be quite simple with just a collection of rows.

``` js
const Grid = ({ grid }) => {
  let rows = [];
  for (var rowIdx = 0; rowIdx < numOfRows; rowIdx++) {
    rows = rows.concat(
      <div key={rowIdx}>
        <Row row={grid[rowIdx]} />
      </div>
    );
  }
  return rows;
}
```

Before we tie it all together we need to think about how the user will select a column. To make things simple we'll include a button at the top of each column to select it. The button should only be active while the column is not full. We'll also need a callback to handle a player selecting the column.

``` js
const ColumnSelector = ({ active, handleClick }) => {
  return (
    <div className="columnSelectorContainer">
      <button disabled={!active} onClick={handleClick} className="columnSelector">Select</button>
    </div>
  );
}
```

Now we can tie it all together. We need several things, including:

* A way to determine whether a particular column is valid for a new disc
* A way to handle a player's selection of a valid column
* Rendering the board, the column selectors, and a message indicating either the current player or the winner

Here's what I've come up with:

``` js
import React from 'react';
import { emptyCell, numOfColumns, numOfRows, p1disc, p2disc, playerDiscLookup } from './constants';
// Discs from http://fontawesome.io/icon/circle/
import WhiteDisc from './circular-shape-silhouette-white.svg';
import BlueDisc from './circular-shape-silhouette-blue.svg';
import RedDisc from './circular-shape-silhouette-red.svg';

class FourInARowBoard extends React.Component {
  onClick(columnIdx) {
    if (this.isActive(columnIdx)) {
      this.props.moves.selectColumn(columnIdx);
      this.props.endTurn();
    }
  }

  isActive(columnIdx) {
    if (this.props.ctx.winner !== null) return false;
    // If the top row of a column is not empty, we shouldn't allow another disc.
    if (this.props.G.grid[0][columnIdx] !== emptyCell) return false;
    return true;
  }

  render() {
    let message = '';
    if (this.props.ctx.winner !== null) {
      message = <span>Winner: Player {playerDiscLookup[this.props.ctx.currentPlayer]}</span>;
    } else {
      message = <span>Current Player: Player {playerDiscLookup[this.props.ctx.currentPlayer]}</span>;
    }
    const selectors = Array(numOfColumns).fill().map((_, i) => i).map(idx =>
      <ColumnSelector
        active={this.isActive(idx)}
        handleClick={() => this.onClick(idx)}
        key={idx}
      />
    );
    return (
      <div>
        <h1>Four In A Row</h1>
        <div>
          {message}
        </div>
        {selectors}
        <Grid grid={this.props.G.grid} />
      </div>
    )
  }
}
```

Sprinkle on some [CSS](https://github.com/PJohannessen/four-in-a-row/blob/master/src/index.css). and we have a working game!

![four-in-a-row finished game](/img/four-in-a-row-boardgameio_2.png)

The final `FourInARowBoard.jsx` file can be found [here](https://github.com/PJohannessen/four-in-a-row/blob/master/src/FourInARowBoard.jsx). All the code is available in the repository, and you can give the game a try by cloning the repository and running:

``` text
npm install
npm start
```


## Conclusion

Implementing this game with [boardgame.io](https://github.com/google/boardgame.io) was quite straightforward but a lot of fun. I could definitely see myself using this again in future. The library offers more powerful functionality such as [multiplayer](http://boardgame.io/#/multiplayer) and [secret state](http://boardgame.io/#/secret-state), both of which I would be keen to include next time.