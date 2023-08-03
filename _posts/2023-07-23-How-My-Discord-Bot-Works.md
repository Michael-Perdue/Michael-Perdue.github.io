---
title: How to setup a Discord bot and how my Discord Helper Bot Works
date: 2023-07-23
categories: [Additional Projects, Discord Bot Project]
tags: [python, discord]  # TAG names should always be lowercase
pin: true
---

This page focuses on the design of the bot, how it achieves its functionality and the reasons behind some of the choices made while coding the bot. If you want to see gifs of the bot working or if you care more about just what the bot offers for the end user then go to [this page](https://michael-perdue.github.io/posts/My-Discord-Bots-Functions/). This page won't cover all the functionality as some of the commands are self-explanatory in how they work and are coded. [Click here for the GitHub repo for this project](https://github.com/Michael-Perdue/Discord-Helper-Bot)

## How to setup a basic discord bot 

### The Overall structure of the bot

Below is a diagram of what happens on the launch of my helper bot. You can ignore the banned_words.txt node as that isn't needed for every bot its just mine which implements a banned word list, the rest of the diagram is what every bot that uses cogs goes through:

![](https://michael-perdue.github.io/assets/Discord-Diagram-structure.PNG)

### How to get the discord bot to run 

To set up the bot to work you need to create a sub-class of Discord's preexisting [bot class](https://discordpy.readthedocs.io/en/stable/ext/commands/commands.html) and then setup in the init what intents (what you want the bot to do), command prefix (what you want commands to use - not important if you end up using slash/hybrid commands) and what cogs the bot will use. Then all you need to do is make an instance of your new bot and call the run function on it in a while loop to ensure the bot keeps running.

Below is code from my bot with some complexities removed to show the bare bones of what is needed for the bot to function (init, setup_hook etc) and what are functions of the bot you can use:

```python
import discord
from discord import Message
from discord.ext import commands
import os

class Bot(commands.Bot):
    def __init__(self):
        """
        This function sets the bot up to have the right intents and command prefixes
        along with defining a hardcoded set of cogs/extension to use
        """
        super().__init__(
            intents=discord.Intents.all(),
            command_prefix="!",
            allowed_mentions=discord.AllowedMentions(everyone=True,roles=True,replied_user=True,users=True)
        )
        self.init_extensions = ["messaging","clock","moderation","fun"] # can be changed to just loop through cogs folder instead of hard coding


    async def setup_hook(self):
        """
        This function is called upon the start of the bot and loads the cogs/extensions
        which lets the bot use all the commands of those cogs
        """
        for extension in self.init_extensions:
            await self.load_extension(extension)


    async def on_ready(self):
        """
        This function is called once the bot has been setup and it prints to the terminal that
        the bots name and all the servers that it has connected to.
        """
        botID = str(self.user)
        print(botID + " is online\n")
        print(botID + " has connected to:")
        for server in self.guilds:      # loops through all the servers that the bot is already setup to use
            print(" " + str(server))

    async def on_guild_join(self,guild):
        # do stuff when bot joins guild
        pass

    async def on_command_error(self, ctx , error):
        # change how error handling works
        raise error

    async def on_message(self, message: Message):
        # do stuff when a message is sent on a server
        pass


bot = Bot()

# IGNORE THIS FUNCTION IF YOU DON'T WANT TO CREATE /commands
@bot.command()
@commands.guild_only()
@commands.is_owner()
async def sync(ctx: commands.Context) -> None:
    """
    This function loads all the hybrid commands and sets it so they can be called with a slash command,
    so a user can now do / and start typing and the commands will auto fill and describe what they are
    """
    synced = await ctx.bot.tree.sync()
    await ctx.send("Synced "+str(len(synced))+" commands")

def main():
    while True:
        bot.run(os.environ['TOKEN'])

if __name__ == '__main__':
    main()
```
{: file='/main.py'}


### What is a cog in discord 

>When making a cog or anything ensure you are looking at Discord's 2.0 API documentation rather then the old 1.0 API as there is alot of program breaking differences in the new API!
{: .prompt-info }

The diagram above helps show just how the helper bot is structured and one of the key design features that is shown is the use of *cogs*. So *cogs* in Discord's API are how you can design an extremely scalable and object-oriented bot. The cogs are classes that inherit the preexisting discord [*Cog* class](https://discordpy.readthedocs.io/en/latest/ext/commands/cogs.html) and then you just add commands which you want that cog to provide. So this means that you can make as many cogs as you want, to provide whatever functionality you want and this allows you to break up your code into nice neat object-oriented cogs to ensure that your code doesn't end up as just one long cog with over 1k lines of code. **For the cog to be loaded by the bot you just have to get the bot to load the cog in setup hook** as anywhere else can lead to the commands not being loaded properly. Each command you make must be an async function as the discord bots only work when they are asynchronous meaning the task being executed can be switched to another task at almost any point. 

Here is an example code of Cog file:  
```python 
from discord.ext import commands

class Messaging(commands.Cog):

    def __init__(self,bot: commands.Bot) -> None:
        self.bot = bot

    @commands.hybrid_command(name="hey",description="Returns a greeting")
    async def hey(self,ctx: commands.Context) -> None:
        """
        When a user sends !hey the bot responds with a message saying 'Hello @user'.
        :param ctx: the context of the message
        """
        await ctx.send("Hello " + ctx.author.mention) # sends the message to the same channel the !hey was used

async def setup(bot: commands.Bot) -> None:
    await bot.add_cog(Messaging(bot))   # Adds the cog to the bot
```
{: file='/messaging.py'}

### The bot is complete

Provided you have both the files shown above (<span>messaging.py</span> and <span>main.py</span>), then the bot now should run with no issues running the code and having the bot work. If you don't have a discord token or you haven't added the bot to your server I suggest you look at this tutorial which takes you through the steps of the initial setup https://www.ionos.co.uk/digitalguide/server/know-how/creating-discord-bot/. Upon completing the step-by-step instructions in that guide you should then be able to run the bot.

## How to setup global slash commands

### What slash commands are in discord
By default, all commands in Discord you make are done through a command prefix like '!' followed by the command and the arguments e.g. *!roll 1 6*. The issue with this is there is no auto-complete and you don't get told the description or arguments needed for the command. Discord does automatically make a *!help* command which lets you see an exhaustive list of all commands, arguments and descriptions but continually cross-referencing this is tedious so that's where slash commands come in. 

Slash commands work by the user typing '/' then it lists all of the commands which you can use and their description. Upon typing one of the commands you will then get the arguments popping up and each of the arguments will have its description once you select an argument it will show you options if the argument is restrictive (i.e. True or Flase or you must give a user's @).

Below is an example of how my bots slash commands look like:

![](https://michael-perdue.github.io/assets/Discord-Slash-Command.PNG)

### How to implement slash commands

There are around 5 different ways in which you can implement slash commands, the way that this page implements it is through hybrid commands. The reason to use hybrid commands as the way to implement it is that it lets you keep using cogs and the code change in the cogs is very minimal compared to the other ways which require any code you previously wrote to be completely changed. Another nice feature of a hybrid command is that the command can be used with command prefix like "!" and a slash so doing */roll* and *!roll* performs the same command, this is nice as it means during testing if you have issues with syncing the slash command you can still test the command just without the slash.

To implement hybrid commands you must specify every argument type otherwise it will not work and the command won't pop up as an option. Below is a snippet of code from my bot showing an example of a hybrid command within a Cog:

```python 
from discord.ext import commands

class Clock(commands.Cog):

    @commands.hybrid_command(name="countdown", description="Counts down from the number given.")
    async def countdown(self, ctx: commands.Context, start: int) -> None:
        """
        This function deals with a user sending a !countdown or /countdown command, and a number the bot then
        will send back a message of the number after minusing one after every second to work like a countdown.
        Example command: '/countdown 3' and the bot will respond with a message saying '3' then '2' then '1' all
        with one second between and then send a message saying the countdown is done.
        :param ctx: the context of the message
        :param start: an int of the number you want the bot to countdown from
        """
        # Starting from the number given the code loops minusing 1 from the value each time till it hits zero
        await ctx.send(start)
        for x in range(start-1,0,-1):
            await asyncio.sleep(1)      # waits a second before sending the number
            await ctx.channel.send(x)   # sends the number the countdown is at to the channel
        # Waits a second before sending a notification and message stating the countdown is done
        await asyncio.sleep(1)  
        await ctx.channel.send(ctx.author.mention + " countdown done")

        # 'await' in the code above is used for asyncronous coding and means that at that point,
        #  the program is waiting for the result of command after the await and it can switch to another task

```

### How to get the slash commands synced and on the discord servers

So to use these commands on any server you must sync them which is something that you don't have to do with normal commands that don't use slashes. So when coding the bot one thing which can become a big issue is where you sync the commands and this is because your bot will be heavily throttled if you aren't careful as Discord's API does not like you syncing a lot. So using it in the on-ready hook wouldn't work well as you are likely to restart the bot quite often which would lead to your bot being throttled. The best way that I have found to implement this is to add a user command which only the owner of the bot can use that syncs the bot's commands. 

Below is the command which you need to add to the bot to allow just you the owner to sync commands:

```python 
@bot.command()
@commands.guild_only()  # Makes it so the command can only be used in a server/guild
@commands.is_owner()    # Makes it so only the OWNER OF THE BOT can use the command
async def sync(ctx: commands.Context) -> None:
    synced = await ctx.bot.tree.sync()
    await ctx.send("Synced "+str(len(synced))+" commands")
```
The code above can be called by the owner of the bot with *!sync* in any server the bot is in and it will sync all servers. You only ever need to use sync when you add a new command or change the details of a command (description, name or arguments), this is due to the bot keeping the knowledge of commands persistently so the commands you sync will still be there over multiple restarts/crashes.

### How to implement sub-commands to slash commands

You can make sub-commands for slash commands by using hybrid groups. A great way to understand this is with my stopwatch command for the helper bot, so what a hybrid group does is make it so you can't call */stopwatch* you must instead call */stopwatch* followed by a sub-command which in this case would be start, stop or time and that looks like */stopwatch start*. This isn't usable in every example as sometimes you want to call the default command with no sub-commands but a stopwatch is a perfect use case of it as you would never want the user to just call */stopwatch* as what would that do so this way the user can either start, stop or check the time of the stopwatch. What this also means is each command first goes through the stopwatch function meaning if they have a common line of code or such you could put it there and it allows you that scalability.

Below is code from my discord helper bot, it shows the hybrid group inside the clock cog. It may seem complex but please note the multiple line *ctx.send* is just for an appealing discord embedded message, it could very much be replaced with just *ctx.send(time_elapsed)* and the code would still work. The log_message() function is for local terminal logging and is not needed but is there for completeness of code:

```python 
class Clock(commands.Cog):
    stopwatches = {}

    @commands.hybrid_group(name="stopwatch", description="Simply a working stopwatch")
    async def stopwatch(self,ctx : commands.Context):
        """
        This function is called first upon the user sending a /stopwatch command, and has 3 options start, time and stop.
        Start will store the user and the time in a key value pair and stop will then retrieve the time and see
        how long has passed and return that to the user and time will retrieve the start time and how long has passed.
        Example command: '/stopwatch start' and '/stopwatch stop'
        :param ctx: the context of the message
        """

    @stopwatch.command(name="start",description="starts the stopwatch")
    async def stopwatch_start(self, ctx: commands.Context):
        """
        This function is called by /stopwatch start.
        It stores a key value pair into stopwatches with the current time,
        the bot then sends a message stating the stopwatch has started.
        It also locally to the terminal logs the use of the command with log_message().
        :param ctx: the context of the message
        """
        self.stopwatches[ctx.message.author] = time.time()
        log_message(ctx, "Stopwatch command started:\n")
        await ctx.send("Stopwatch started")

    @stopwatch.command(name="stop",description="stops the stopwatch")
    async def stopwatch_stop(self, ctx: commands.Context):
        """
        This function is called by /stopwatch stop.
        It removes the key value pair of the user who typed the command and,
        the bot then sends a message back stating the time started, stopped and seconds passed.
        It also locally to the terminal logs the use of the command with log_message().
        :param ctx: the context of the message
        """
        time_elapsed = time.time()-self.stopwatches[ctx.message.author]
        await ctx.send(embed=discord.Embed(title="Stopwatch",
                                           description=(ctx.message.author.mention + " You have stopped your stopwatch "),
                                           colour=discord.Colour.blue()
                                           ).add_field(name="Start time", value=str(time.strftime("%Y-%m-%d %H:%M:%S",time.localtime(self.stopwatches[ctx.message.author]))), inline=True
                                           ).add_field(name="Stop time", value=str(time.strftime("%Y-%m-%d %H:%M:%S",time.localtime(time.time()))), inline=True
                                           ).add_field(name="Time elapsed", value="{:.2f}".format(time_elapsed) + " seconds", inline=False))
        self.stopwatches.pop(ctx.message.author)
        log_message(ctx, "Stopwatch command stopped:\n   time elapsed: " + str(time_elapsed) + " seconds\n")

    @stopwatch.command(name="time",description="gets the stopwatches time")
    async def stopwatch_time(self, ctx: commands.Context):
        """
        This function is called by /stopwatch time.
        It gets the bot to sends a message back stating the time started and seconds passed.
        It also locally to the terminal logs the use of the command with log_message().
        :param ctx: the context of the message
        """
        time_elapsed = time.time()-self.stopwatches[ctx.message.author]
        await ctx.send(embed=discord.Embed(title="Stopwatch",
                                           description=(ctx.message.author.mention + " Information on your stopwatch "),
                                           colour=discord.Colour.blue()
                                           ).add_field(name="Start time", value=str(time.strftime("%Y-%m-%d %H:%M:%S",time.localtime(self.stopwatches[ctx.message.author]))), inline=True
                                           ).add_field(name="Time elapsed", value="{:.2f}".format(time_elapsed) + " seconds", inline=False))
        log_message(ctx, "Stopwatch command time checked:\n   time elapsed: " + str(time_elapsed) + " seconds\n")
```

## How the message filtering works

### Storing the banned words for the message filtering

So in order to filter messages, you need to store a list of banned words and this list needs to be persistent through crashes of the bot and such. I did think about using MySQL as I have done for previous projects such as the [Smart Environment Toolkit](https://michael-perdue.github.io/posts/Smart-Environment-Toolkit/) but it would be overkill for this problem as all I need to store is an identifier for each server and a list of words. 

So I instead decided to use just a plain text file and store the server's id followed by one space between each banned word. Upon starting the bot it will check if the *banned_words.txt* file exists and if not it will make it, and then it will add all the servers ids it has connected to, to the file with one server on each line. The bot then once it has connected to the servers, it reads the file and makes a dictionary consisting of an id as the key with a list of banned words so that when filtering messages it doesn't need to continually check the file. Below is the code for this:

```python 
class Bot(commands.Bot):
    banned_words = {}

    def read_banned_words(self):
        if not Path("./banned_words.txt").is_file():
            # creates a new file through opening it on write
            open("banned_words.txt", "a").close()
        file = open("banned_words.txt","r")
        for x in file: # reads each line in the file 
            split_line = list(x.replace("\n","").split(" ")) # splits the line into a list
            self.banned_words[int(split_line[0])] = split_line[1:] 
        file.close()

    def add_guilds(self):
        file = open("banned_words.txt","a")
        found_guilds = self.banned_words.keys()
        for guild in self.guilds:   # loops through all the servers/guilds that the bot is on
            if guild.id not in found_guilds: # adds the servers id to a new line on the txt file if its not on file
                file.write(str(guild.id) + "\n")
                self.banned_words[guild.id] = [] # adds the server id and an empty list to the banned words dict
        file.close()

    async def on_ready(self):
        # The on_ready function is called once the bot has gone through connecting to every server.
        self.read_banned_words()
        self.add_guilds()
```

### How the message filtering checks message

The bot as shown above inherits the commands.bot class which has a function that is called every time a message is received called on_message, so I overwrote that function and got it to check each message through regular expression to see if it contains any banned words from the server the message was sent. I also ensured the function still processes the message if it does not contain any banned words, if it does contain a banned word it does not process the message and it deletes the message. In addition, the filter checks that it's not the bot sending the message (as it writes what banned words were used in the mod channel) you the user are not unbanning a word through the !unbanword, the /unbanword does not need to be checked as slash commands act differently. Finally after deleting the message the bot will put in the moderation channel who tried to who what banned word in what channel. Below is the code that does this:

```python
async def on_message(self, message: Message):
    # Checks the message isn't from the bot or a user unbanning a word
    if(message.author.id != self.user.id and "!unbanword" not in message.content):
        # Creates the list of banned words used
        check_word = [word for word in self.banned_words[message.guild.id] if(re.search(("(^|\s)"+word+"($|\s)"),message.content))] 
        if bool(check_word):  # Checks if any banned words were used
            await message.delete()  # Deletes the message containing banned words
            # The bot then sends a message to the moderation channel stating who used banned words and what channel
            await discord.utils.get(message.guild.channels, name="moderation").send(message.author.mention + " just tried to use banned word/s \'" + "\' \'".join(check_word) + "\' in channel: " + message.channel.mention )
        else:
            await self.process_commands(message)
    else:
        await self.process_commands(message)
```
### Adding and removing words from the banned word list

When adding and removing a word from the list through the two commands */banword* and */unbanword* it not only adds/removes it from the text file but also from the bot's banned words list so the effect is instant.


