# YouTube Discord Bot
A small tutorial of how to make a discord bot that can notify when certain YouTube channels goes live.

## Requirements
- Node.js v12 or greater
- Discord.js v12
- node-fetch
- cheerio
- http-proxy-agent

## Source Code
[GET SOURCE CODE HERE](https://github.com/excepti0ns/YouTubeBot)


## Let's get started 
First up all let's set up a simple discord bot using a discord.js.
```js
const {Client,MessageEmbed} = require("discord.js");
const bot = new Client();
bot.login("Your Token Goes Here");
bot.on("ready",=> console.log(ready));

```
Yeah ,it's done.Now create a `config.json` that holds all the credentials.
```js
{
"Token": "Your BOT Token",
"channelID":"Discord channel id",
"channels":[],
"interval":60000
}
```
Here channels is the YouTube channels id that u wish to get notified when they goes live, multiple channel id's are supported.Ex: `["UChdjiio3iriuez","Uchshzezez6589"]`.And `channelID` is the discord channel id where u want to send message.

So now our code looks like below.
```js
const {Client,MessageEmbed} = require("discord.js");
const bot = new Client();
const config = require ("./config.json");
require("./youtube.js")(bot);

bot.login(config.Token);
bot.on("ready",() => console.log("Bot is ready"));
```
Now create a `youtube.js` file ,then follow these codes.

```js 

const fetch = require("node-fetch");
const cheerio = require("cheerio");
const HttpProxy = require("http-proxy-agent");
const config = require ("./config.json");
let IP = [];
let UserAgent = [];

module.exports = (bot) => {

function random() {
const ip = IP[Math.floor(Math.random() * IP.length)];
const useragent = UserAgent[Math.floor(Math.random() * IP.length)];
return {ip,useragent};
};

async function live(id) {
try {
  
const { ip , useragent } = random();
let options = {headers:{"User-Agent":useragent,"referer":"https://www.google.com"}};
if(ip) options.agent = new HttpProxy(ip);
const res = await fetch(`https://www.youtube.com/channel/${id}`,options);
const text = await res.text();
const dom = cheerio.load(text);

const test = dom(".yt-lockup-content").find(".yt-badge-live").text().toLowerCase();
if(! test.includes("live"))return;
const videoID = dom(".yt-lockup-content").find(".spf-link").attr("href").split("=")[1];
if(!videoID) return; 
bot.emit("live",videoID);
} catch(r) {
console.log(r);

};
};

setInterval(() => {

for (const id of config.channels) {
   live(id);
};
  
},config.interval);
};


```
- Youtube may block you ,if u scrap the web constantly,so we have a setup to prevent it ,that's is nothing but this `random` function,it supppies random ip from the array ,so that they cannot know we are scraping their web . So fill some user agents and working IPS in the respective array ,you can get a list of ip's [here](https://deviceatlas.com/blog/list-of-user-agent-strings#desktop) and [here](https://free-proxy-list.net/). Note ip should be in this format `https://<ip>:<port>`,for example `https://90.587.34.62:8080`.
> Note: You don't need to fill ips in the array if u are tracking only 4-5 channels.
 
- we have created function `live`,it fetches the respective channels dom(html) , searches `Live now` word in the html,so we r using `cheerio` which is the best package to parse the html like jQuery.
- So one more method that is `setInterval` ,we r running `live` function in an interval.I do recommnad putting an interval of `1m(60000)` or more.Finnaly if the html contains live keyword ,the function will returns id of the video ,then it emits the `live` event.

Now in our main file just listen for the `live` event.
```js

let last = []
bot.on("live",videoID => {
if(last.includes(videoID)) return;
// Do whatever u want.
bot.channels.cache.get(config.channelID).send(`@eveyone https://www.youtube.com/watch?v=${videoID}`);
last.push(videoID);
});
```

So whenever live event emits ,it means one of your channel is streaming now.
> Warning:Make sure to store the video id and check if it is exists or not, otherwise
it will spam the channel every interval with the same message.I just stored it in temporary memory ,I do recommand storing it in a database.

