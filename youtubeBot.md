# YouTube Discord Bot
A small tutorial of how to make a discord bot that can notify when certain YouTube channels goes live.

## Requirements
- Node.js v12 or greater
- Discord.js v12
- node-fetch
- cheerio


## Let's get started 
First up all let's set up a simple discord bot using a discord.js.
```js
const {Client,MessageEmbed} = require("discord.js");
const bot = new Client();
bot.login("Your Token Goes Here");
bot.on("ready",=> console.log(ready));
bot.on("message",async message => {
});
```
Yeah ,it's done.Now create a `config.json` that holds all the credentials.
```js
{
TOKEN: "Your BOT Token",
PREFIX:"?",
channels:[],
interval:60000
}
```
Here channels is the YouTube channels id that u wish to get notified when they goes live, multiple channel id's are supported.Ex: `["UChdjiio3iriuez","Uchshzezez6589"]`.And the interval is in terms of ms.

So now our code looks like below.
```js
const {Client,MessageEmbed} = require("discord.js");
const bot = new Client();
const config = require ("./config.json");

bot.login(config.TOKEN);
bot.on("ready",=> console.log("Ready"));
bot.on("message",async message => {
if(message.author.bot || message.author.id == bot.user.id || message.channel.type == "dm") return;
if(!message.content.startsWith(config.PREFIX)) return;
    let args = message.content.slice(config.PREFIX.length).trim().split(/ +/g);
    let command = args.shift().toLowerCase();

if(command == "ping") {
message.channel.send("Pong")
};
```
So here we completed the bot stuff.Now we move to the youtube stuffs.Now create a `youtube.js` file.
Then fill these codes.

```js 
const fetch = require("node-fetch");
const cheerio = require("cheerio");
const config = require ("./config.json");
let IP = [];
let UserAgent = [];

module.exports = (bot) => {

function random() {
const ip = IP[Math.floor(Math.random() * IP.length)];
const useragent = UserAgent[Matt.floor(Math.random() * IP.length)];
return {ip,useragent};
};

async function live(id) {
const {ip,useragent} = random();
let options = {UserAgent:useragent};
if(ip) options.agent = new HttpProxy(ip);
const res = await fetch(`https://www.youtube.com/channel/${id}`,options);
const text = await res.text();
const dom = cheerio.load(text);

const text = dom(".yt-lockup-content").find(".yt-badge-live").text().toLowerCase();
if(!text.includes("live") return;
const videoID = dom(.yt-lockup-content").find(".spf-link").attr("href").split("=")[1];
if(!videoID) return; 
return videoID;
};

setInterval(async() => {
for(const id of config.channels) {
let videoID = await live(id);
if(!videoID) return;
bot.emit("live",videoID);
},config.interval);
};0

```
- Youtube may block you ,if u scrap the web constantly,so we have a setup to prevent it ,that's is nothing but this `random` function,it supppies random ip ,so that they cannot know we r scraping their web . So fill some user agents and working IPS in the respective array ,you can find it out [here](https://deviceatlas.com/blog/list-of-user-agent-strings#desktop) and [here](https://free-proxy-list.net/). Note ip should be in this format `https://<ip>:<port>`,for example `https://90.587.34.62:8080`.
> Note: You don't need to fill ips in the array if u are tracking only 4-5 channels.
 
- we have created function `live`,it fetches the respective channels dom(html) ,searchs `Live Now` word in the html,so we r using `cheerio` which is the best package to parse the html like jQuery.
- So one more method that is `setInterval` ,we r running `live` function in an interval.I do recommnad putting interval `1m(60000)` or more.Finnaly if we found the live keyword ,the function will returns id of the video ,then it emits the `live` event.

Now in our main file just listen for the `live` event.
```js
let lastvidoes = [];
bot.on("live",videoID => {
if(lastvidoes.includes(videoID)) return;
// Do whatever u want.
bot.channels.cach.get("channel id").send(`@eveyone https://www.youtube.com/watch?v=${videoID}`);
lastvideos.push(videoID);
});
```

So whenever live event emits ,it means one of your channel is streaming now.
> Note: Make sure to store the video id and check if it is exists or not, otherwise
it will spam the channel every interval with the same message.I just stored it in temporary memory ,I do recommand storing it in database.

