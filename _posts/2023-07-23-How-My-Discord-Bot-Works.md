---
title: How Discord Helper Bot Works
date: 2023-07-23
categories: [Additional Projects, Discord Bot Project]
tags: [python, discord]  # TAG names should always be lowercase
---

This page focuses on the design of the bot, how it achieves its functionality and the reasons behind some of the choices made while coding the bot. If you want to see gifs of the bot working or if you care more about just what the bot offers for the end user then go to [this page](https://michael-perdue.github.io/posts/My-Discord-Bots-Functions/). This page won't cover all the functionality as some of the commands are self-explanatory in how they work and are coded. [Click here for the GitHub repo for this project](https://github.com/Michael-Perdue/Discord-Helper-Bot)

## The Overall structure of the bot

Diagram of what happens on the launch of the bot:

![](https://michael-perdue.github.io/assets/Discord-Diagram-structure.png)

### How to setup the bot 

To set up the bot to work you need to create a sub-class of Discord's preexisting [bot class](https://discordpy.readthedocs.io/en/stable/ext/commands/commands.html) and then setup in the init what intents (what you want the bot do), command prefix (what you want commands to use - not important if you end up using slash/hybrid commands) and what cogs the bot will use. Then all you need to do is make an instance of your new bot and call the run function on it in a while loop to ensure the bot keeps running.

Below is code from my bot with some complexities removed to show the bare bones of what is needed for the bot to function (init, setup_hook etc) and what are functions of the bot you can use:

```python
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
        #do stuff when bot joins guild
        pass

    async def on_command_error(self, ctx , error):
        #change how error handling works
        raise error

    async def on_message(self, message: Message):
        #do stuff when a message is sent on a server
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



### What is a cog in discord 

>When making a cog or anything ensure you are looking at Discord's 2.0 API documentation rather then the old 1.0 API as there is alot of program breaking differences in the new API!
{: .prompt-info }

The diagram above helps show just how the helper bot is structured and one of the key design features that is shown is the use of *cogs*. So *cogs* in Discord's API are how you can design an extremely scalable and object-oriented bot. The cogs are classes that inherit the preexisting discord [*Cog* class](https://discordpy.readthedocs.io/en/latest/ext/commands/cogs.html) and then you just add commands which you want that cog to provide. So this means that you can make as many cogs as you want, to provide whatever functionality you want and this allows you to break up your code into nice neat object-oriented cogs to ensure that your code doesn't end up as just one long cog with over 1k lines of code. **For the cog to be loaded by the bot you just have to get the bot to load the cog in setup hook** as anywhere else can lead to the commands not being loaded properly. Each command you make must be an async function as the discord bots only work when they are asynchronous meaning the task being executed can be switched to another task at almost any point. 

Here is an example code of cog file:  
```python
class Messaging(commands.Cog):

    def __init__(self,bot: commands.Bot) -> None:
        self.bot = bot

    @commands.hybrid_command(name="hey",description="Returns a greeting")
    async def hey(self,ctx: commands.Context) -> None:
        """
        When a user sends !hey the bot responds with a message saying 'Hello @user'.
        :param ctx: the context of the message
        """
        await ctx.send("Hello " + ctx.author.mention)

async def setup(bot: commands.Bot) -> None:
    await bot.add_cog(Messaging(bot))   # Adds the cog to the bot
```

## The use of global slash commands

By default, all commands in Discord you make are done through a command prefix like ! followed by the command and the arguments e.g. *!roll 1 6*. The issue with this is there is no auto-complete and you don't get told the description or arguments needed for the command. Discord does automatically make a *!help* command which lets you see an exhaustive list of all commands, arguments and descriptions but continually cross-referencing this is tedious so that's where slash commands come in. 

## How the moderation cog works

