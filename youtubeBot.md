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
Yeah ,it's done.So now we r using quick.db to store guild data (i.e guild YouTube channels and configuration).You can use any remote database.
So firstly we will do a simple command `add` ,it will store the YouTube channel to track live streams ,So the example usage is `?add UCnxjyu999393ushszzh`.
Here,we go
``` 
const db = require("quick.db");
const prefix = "?";
bot.on("message",async message => {
if(message.author.bot || message.author.id == bot.user.id || message.channel.type == "dm") return;
c
})
