---
title: "Singletons"
date: 2022-11-18
last_modified_at: 2022-11-19
category: study
tags: [Node.js, 디자인 패턴, Singleton, 모듈 캐시]
---

# Singletons

- Singletons : Objects that can only have a single instance, with a single point of access. a single source of truth, with a single point of access. Singletons are useful for maintaining a central state, where operate on a common resource. 
- The Node.js module system, incidentally(by the way), provides a basic framework for creating a rudimentary(가장 기초적인, basic) singleton.
- Modules are cached when they’re accessed using the require() statement.
- The VaultModule.js here behaves like a singleton but is merely a cached instance.

> 캐시(cache)는 컴퓨터 과학에서 데이터나 값을 미리 복사해 놓는 임시 장소를 가리킨다. 캐시는 캐시의 접근 시간에 비해 원래 데이터를 접근하는 시간이 오래 걸리는 경우나 다시 계산하는 시간을 절약하고 싶은 경우에 사용한다. 캐시에 데이터를 미리 복사해 놓으면 계산이나 접근 시간 없이 빠른 속도로 데이터에 접근할 수 있다.

---

Let’s say you have a module that exports two functions. When this module is imported across multiple files or multiple times in an application, only a single instance is created and referred to. This is because the module system caches the module the moment it is accessed using a require statement for the very first time. There after, the instance is referred to everywhere. No matter how many times you import this module across you application, it will access the same cached and common instance. There are, of course, a few caveats to this policy, which we’ll discuss in a bit; and I must mention here that though this behaves like a singleton, it is technically just an instance of the module that is cached upon first access.

---

- CashRegister.js

```javascript
let cash = 0; // This is a private variable

const CashRegister = {

    // credit function allows to add funds.
    credit(amount) {
        cash = cash + amount;
        return cash;
    },

    // debit function allows us to take funds away.
    debit(amount) {
        if (amount <= cash) {
            cash = cash - amount;
            return true;
        } else {
            return false;
        }
    },

    // total function tells us how much cash we have.
    total() {
        return cash;
    }
}

// commonJS -> ES module로 변환 가능.
// these 3 methods are public because we are using 
// module.exports to export them out as an interface.
// so, variable 'cash' is private.
// we can only using the variable cash using the given interface functions.
module.exports = CashRegister;
```

- App.js

```javascript
const Till = require('./CashRegister');
const Till1 = require('./CashRegister');

Till.credit(25);
Till.credit(35);
Till1.credit(20);
console.log(`Total amount in Till-1 : ${Till1.total()}`);

Till.debit(5);
console.log(`Total amount in Till : ${Till.total()}`);
```

---

This behavior changes, however, if we use classes instead.

- App.js

```javascript
const Game = require('./Game');
const Player = require('./Player');

const Pete = new Player('Pete');
const Mike = new Player('Mike');
const Poker = new Game();

Poker.join(Pete);
Poker.join(Mike);

Pete.wins(20);

Mike.wins(30);
Mike.wins(40);
Mike.wins(40);
Mike.wins(40);

Pete.wins(1000);
Pete.loses(1000);

console.log('Scoreboard: ', Poker.scores());
console.log(`Winnder: ${Poker.getWinner().name} with ${Poker.getWinner().points} points`);
```

- Game.js

```javascript
const Scores = require('./Scoreboard');
// const Scores = new ScoreBoard();

class Game {
    join(player) {
        Scores.join(player.getName());
    }
    scores() {
        return Scores.getBoard();
    }
    getWinner() {
        return Scores.sort()[0];
    }
}

module.exports = Game;
```

- Player.js

```javascript
const Scores = require('./Scoreboard');
// const Scores = new ScoreBoard();

class Player {
    constructor(name) {
        this.name = name;
    }
    getName() {
        return this.name;
    }
    wins(points) {
        Scores.update(this.name, points);
    }
    loses(points) {
        Scores.update(this.name, -points);
    }
}

module.exports = Player;
```

- Scoreboard.js

```javascript
class Scoreboard {
    constructor() {
        this.board = [];
    }
    // let a player join this game.
    join(name) {
        this.board.push({
            name,
            points: 0
        });
    }
    // let a player leave this game.
    leave(name) {
        this.board = this.board.filter(player => player.name !== name);
    }
    // find a player who named as parameter 'name' and give him points as delivered on the parameter.
    update(name, points) {
        const player = this.board.findIndex(player => player.name === name);
        if (player > -1) {
            this.board[player].points += points;
        }
    }
    getBoard() {
        return this.board;
    }
    // sort in descending order
    sort() {
        return this.board.sort((x, y) => y.points - x.points);
    }
}

// to acheive singleton, we have to export as an class instance
module.exports = new Scoreboard();
```

---

- Summarize
- As a singleton, objects can only have a single instance, with a single point of access.
- Node’s module system caches modules, then accessed for the first time using a require statement.
- The module system offers a rudimentary implementation of a singleton.
- This behavior is dependent on filename consistency.
- Inconsistent filenames will result in multiple cached instances.
- When working with classes, instantiating a class before exporting it out of a module will result in a singleton.
