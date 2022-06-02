## Project Purpose and Components
This project was where students were asked to create their first server and practice using a code module.

## What I Learned
This project marked a really big step in this class. The point of this class was to gain experience with server-side javascript, and up until this point in the term it was something we had been slowly working towards, but had not quite dove into yet. It was really satisfying to do this project and feel like I was creating something. I felt like I had been well prepared by the preceding work, but it was still a nice challenge.

## Code
### p3-module.js
function validDenomination(coin) {
    //responds true if input matches an item in the array
    console.log([1,5,10,25,50,100].indexOf(coin) !== -1);
}

//coin objects have 2 properties - denom and count
function valueFromCoinObject(obj) {
    const { denom = 0, count = 0 } = obj;
    return denom*count;
}

//gets valueFromCoinObject as it iterates through the array, adds to and returns "total"
function valueFromArray(arr) {
    getSum = (total, obj) => {
        let num = valueFromCoinObject(obj)
        return total + num;
    }
    return arr.reduce(getSum, 0);
}

function coinCount(...coinage) {
    return valueFromArray(coinage)
}

module.exports = {
    coinCount
}

### p3-server.js
const fs = require("fs");
const fastify = require("fastify") ();
const {coinCount} = require('./p3-module.js');

fastify.get("/", (request, reply) => {
    fs.readFile(`${__dirname}/index.html`, (err, data) => {
        if (err) {
            console.log(err);
            reply
            .code(500)
            .header("Content-Type", "application/json; charset=utf-8")
            .send("Error processing request")
        } else {
            reply
            .code(200)
            .header("Content-Type", "application/json; charset=utf-8")
            .send(data)
        }
    });
});

fastify.get("/coin", (request, reply) => {
    console.log(request.query);
    const { denom = 0, count = 0 } = request.query;
    const parsedDenom = parseInt(denom)
    const parsedCount = parseInt(count)
    const coinValue = coinCount(request.query)
    reply
    .code(200)
    .header("Content-Type", "text/html; charset=utf-8")
    .send(`<h2>Value of ${count} of ${denom} is ${coinValue}</h2><br /><a href="/">Home</a>`);
});

fastify.get("/coins", (request, reply) => {
    console.log(request.query);
    const { option } = request.query;
    switch(option) {
        case '1':
            coinValue = coinCount({ denom: 5, count: 3 }, { denom: 10, count: 2 });
            break;
        case '2':
            //put this test array because I think this is what the instructions were asking for? unclear
            const coins = [{denom: 25, count: 2},{denom: 1, count: 7}];
            coinValue = coinCount(...coins)
            break;
        default:
            coinValue = 0
    }
    reply
    .code(200)
    .header("Content-Type", "text/html; charset=utf-8")
    .send(`<h2>Option ${option} value is ${coinValue}</h2><br /><a href="/">Home</a>`);
});

const listenIP = "localhost";
const listenPort = 8080;
fastify.listen(listenPort, listenIP, (err, address) => {
  if (err) {
    console.log(err);
    process.exit(1);
  }
  console.log(`Server listening on ${address}`);
});

//http://localhost:8080

### [Home Page](https://slynsky.github.io)
