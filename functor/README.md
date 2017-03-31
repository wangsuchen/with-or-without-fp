Without Either Functor
---------------

```js
const players = require('../players');

function getLastNames(players) {
    try {
        const names = players.map(player => player.name);
        const lastNames = names.map(name => name.split(' ')[1]);
        return lastNames;
    } catch (e) {
        console.error('error:', ' players should be an array')
    }
}

console.log('players:', getLastNames(players)); // =>  ['Curry', 'James', 'Paul', 'Thompson', 'Wade' ]
getLastNames({}); // => error:  players should be an array
```

With Either Functor
-----------------

```js
const F = require('../../fp');
const players = require('../players');

const getPlayers = function (players) {
    if (!Array.isArray(players)) {
        return F.Left.of('players should be an array');
    } else {
        return F.Right.of(players);
    }
}

const getLastNames = F.compose(
    F.feither(F.log('error', 'error:'), F.log('debug', 'players:')),
    F.fmap(F.map(F.last)),
    F.fmap(F.map(F.split(' '))),
    F.fmap(F.map(F.property('name'))),
    getPlayers
);

getLastNames(players); // => players: [ 'Curry', 'James', 'Paul', 'Thompson', 'Wade' ]
getLastNames({}); // => error: players should be an array
```