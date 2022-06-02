## Project Purpose and Components
This project was where students were asked to create their first server and practice using a code module.

## What I Learned
This project marked a really big step in this class. The point of this class was to gain experience with server-side javascript, and up until this point in the term it was something we had been slowly working towards, but had not quite dove into yet. It was really satisfying to do this project and feel like I was creating something. I felt like I had been well prepared by the preceding work, but it was still a nice challenge.

## Code
### p3-module.js
function validDenomination(coin) {<br>
    //responds true if input matches an item in the array<br>
    console.log([1,5,10,25,50,100].indexOf(coin) !== -1);<br>
}<br>

//coin objects have 2 properties - denom and count<br>
function valueFromCoinObject(obj) {<br>
    const { denom = 0, count = 0 } = obj;<br>
    return denom*count;<br>
}<br>

//gets valueFromCoinObject as it iterates through the array, adds to and returns "total"<br>
function valueFromArray(arr) {<br>
    getSum = (total, obj) => {<br>
        let num = valueFromCoinObject(obj)<br>
        return total + num;<br>
    }<br>
    return arr.reduce(getSum, 0);<br>
}<br>

function coinCount(...coinage) {<br>
    return valueFromArray(coinage)<br>
}<br>

module.exports = {<br>
    coinCount<br>
}<br>

### p3-server.js
const fs = require("fs");<br>
const fastify = require("fastify") ();<br>
const {coinCount} = require('./p3-module.js');<br>

fastify.get("/", (request, reply) => {<br>
    fs.readFile(`${__dirname}/index.html`, (err, data) => {<br>
        if (err) {<br>
            console.log(err);<br>
            reply<br>
            .code(500)<br>
            .header("Content-Type", "application/json; charset=utf-8")<br>
            .send("Error processing request")<br>
        } else {<br>
            reply<br>
            .code(200)<br>
            .header("Content-Type", "application/json; charset=utf-8")<br>
            .send(data)<br>
        }<br>
    });<br>
});<br>

fastify.get("/coin", (request, reply) => {<br>
    console.log(request.query);<br>
    const { denom = 0, count = 0 } = request.query;<br>
    const parsedDenom = parseInt(denom)<br>
    const parsedCount = parseInt(count)<br>
    const coinValue = coinCount(request.query)<br>
    reply<br>
    .code(200)<br>
    .header("Content-Type", "text/html; charset=utf-8")<br>
    .send(`<h2>Value of ${count} of ${denom} is ${coinValue}</h2><br /><a href="/">Home</a>`);<br>
});<br>

fastify.get("/coins", (request, reply) => {<br>
    console.log(request.query);<br>
    const { option } = request.query;<br>
    switch(option) {<br>
        case '1':<br>
            coinValue = coinCount({ denom: 5, count: 3 }, { denom: 10, count: 2 });<br>
            break;<br>
        case '2':<br>
            //put this test array because I think this is what the instructions were asking for? unclear<br>
            const coins = [{denom: 25, count: 2},{denom: 1, count: 7}];<br>
            coinValue = coinCount(...coins)<br>
            break;<br>
        default:<br>
            coinValue = 0<br>
    }<br>
    reply<br>
    .code(200)<br>
    .header("Content-Type", "text/html; charset=utf-8")<br>
    .send(`<h2>Option ${option} value is ${coinValue}</h2><br /><a href="/">Home</a>`);<br>
});<br>

const listenIP = "localhost";<br>
const listenPort = 8080;<br>
fastify.listen(listenPort, listenIP, (err, address) => {<br>
  if (err) {<br>
    console.log(err);<br>
    process.exit(1);<br>
  }<br>
  console.log(`Server listening on ${address}`);<br>
});<br>

//http://localhost:8080<br>

### [Home Page](https://slynsky.github.io)
