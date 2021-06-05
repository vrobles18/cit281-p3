# cit281-p3

Project 3 - p3-module.js
/*
    CIT 281 Project 3
    Name: Vanessa Robles
*/

module.exports = {
  coinCount,
};

function valueFromArray(arr) {
  return arr.reduce(
    (acc, val) =>
      Array.isArray(val) ? valueFromArray(val) : acc + valueFromCoinObject(val),
    0
  );
}

// Return coin values from object
function valueFromCoinObject(obj) {
  const { denom = 0, count = 0 } = obj;
  return validDenomination(denom) ? denom * count : 0;
}

// Validate coin denomination, note using type equality test
function validDenomination(coin) {
  return [1, 5, 10, 25, 50, 100].indexOf(coin) !== -1;
}

// Process coin objects, either as a single coin, or array of coins
// Coin object properties consist of:
// - denom: coin denomination (1, 5, 10, 25, 50, 100)
// - count: number of coins
function coinCount(...coinage) {
  return valueFromArray(coinage);
}

console.log("{}", coinCount({denom: 5, count: 3}));

/*
console.log("{}", coinCount({denom: 5, count: 3}));
console.log("{}s", coinCount({denom: 5, count: 3},{denom: 10, count: 2}));
const coins = [{denom: 25, count: 2},{denom: 1, count: 7}];
console.log("...[{}]", coinCount(...coins));
// Extra credit
console.log("[{}]", coinCount(coins));
*/
Project 3 - p3-server.js
/*
    CIT 281 Project 3
    Name: Your Name
*/

// Import module files
const fs = require("fs");
const fastify = require("fastify")();
const { coinCount } = require("./p3-module.js");

fastify.get("/", (request, reply) => {
  fs.readFile(`${__dirname}/index.html`, (err, data) => {
    if (err) {
      reply
        .code(500)
        .header("Content-Type", "text/html; charset=utf-8")
        .send("<h1>Server error.</h1>");
    } else {
      reply
        .code(200)
        .header("Content-Type", "text/html; charset=utf-8")
        .send(data);
    }
  });
});
fastify.get("/coin", (request, reply) => {
  const { denom = 0, count = 0 } = request.query;
  const coinValue = coinCount({
    denom: parseInt(denom),
    count: parseInt(count),
  });
  reply
    .code(200)
    .header("Content-Type", "text/html; charset=utf-8")
    .send(
      `<h2>Value of ${count} of ${denom} is ${coinValue}</h2><br /><a href="/">Home</a>`
    );
});
fastify.get("/coins", (request, reply) => {
  let coinValue = 0;
  const coins = [
    { denom: 25, count: 2 },
    { denom: 1, count: 7 },
  ];
const { option } = request.query;
  switch (option) {
    case "1":
      coinValue = coinCount({ denom: 5, count: 3 }, { denom: 10, count: 2 });
      break;
    case "2":
      coinValue = coinCount(...coins);
      break;
    case "3":
      coinValue = coinCount(coins);
      break;
  }
  reply
    .code(200)
    .header("Content-Type", "text/html; charset=utf-8")
    .send(
      `<h2>Option ${option} value is ${coinValue}</h2><br /><a href="/">Home</a>`
    );
});

// Start server and listen to requests using Fastify
const listenIP = "localhost";
const listenPort = 8080;
fastify.listen(listenPort, listenIP, (err, address) => {
  if (err) {
    console.log(err);
    process.exit(1);
  }
  console.log(`Server listening on ${address}`);
});
