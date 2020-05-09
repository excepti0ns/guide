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
```js
const db = require("quick.db");
const prefix = "?";
bot.on("message",async message => {
if(message.author.bot || message.author.id == bot.user.id || message.channel.type == "dm") return;
if(!message.content.startsWith(prefix)) return;
    let args = message.content.slice(prefix.length).trim().split(/ +/g);
    let command = args.shift().toLowerCase();

if(command == "add") {
if(!args[0]) return message.channel.send("Please provide a YouTube channel id");
const res = await fetch(`https://www.youtube.com/channel/${args[0]}`);
if(res.status > 210) return message.channel.send("You mispelled the channel id, check the id once again.");
db.set(message.guild.id,[args[0]]);
message.channel.send("Added YouTube channel to the list");
};
```
Next ,we r doing `remove` command ,it removes YouTube channel id from the database.
Here I m not going to do the same stuff like ignoring bots ,prefix etc.
```js
if(command == "remove") {
if(!args[0]) return message.channel.send("Please provide a channel id to remove");
const list = db.fetch(message.guild.id);
if(list && list.channels.includes(args[0])) {
let newlist = list.channels.filter(id => id != args[0]);
db.set(message.guild.id,{channels:newlist});
message.channel.send("Removed channel from the list");
} else {
return message.channel.send("That channel doesn't exists in list");
};
```
})
