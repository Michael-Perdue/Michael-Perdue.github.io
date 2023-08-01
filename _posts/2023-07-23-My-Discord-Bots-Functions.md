---
title: What My Discord Helper Bot Does
date: 2023-07-23
categories: [Additional Projects, Discord Bot Project]
tags: [python, discord]  # TAG names should always be lowercase
---

This page focuses on what different functions/utility the bot provides to the user and the motivation behind the bot. If you want to see the innerworks of the bot explained then look at [this page](https://michael-perdue.github.io/posts/How-My-Discord-Bot-Works/), and if you want to just see the code then click [HERE](https://github.com/Michael-Perdue/Discord-bot) for the public github repo of the bot. 

# The motivation for this project

A discord bot is a bot that you can add to a discord server (a server is just a collection of users and a series of text and voice channels) which can then manage the server, provide commands, filter messages etc.

There exists many different discord bots already like [MEE6](https://mee6.xyz/en/) or [DYNO](https://dyno.gg/) and others which all have probably more functionality overall then my bot that I have made has. With that being said the issue with these bots is that they are mostly each specialized for one function so like [DYNO](https://dyno.gg/) is built for moderation and discord server management which means if I also want to be able to have a command to do a coin flip or roll a dice then I would need another discord bot like MEE6. This then leads to me having to search multiple sites for all the commands and you also have to hope they use unique identifies as 2 bots could use '!' to start there command which would then cause issues. So me and my friends both noticing this issue I decided to make a bot mainly for personal/friends use as I just wanted to have one bot which does everything I need.

So this page covers all the functionality of the bot without getting bogged down with the implementation side of things which is covered on a [seperate page](https://michael-perdue.github.io/posts/How-My-Discord-Bot-Works/). This page is also perfect for anyone using the bot and wanting to know all the commands it provides. 

# Helper bot commands

## Moderation functions and commands

### Automatic message filtering 

Note message filtering is per server so one server can have one word banned and another server could still say that word without their message being deleted and vice versa. So this means if the bot is in 100 servers it would have 100 unique banned words lists and depending on where the message comes from leads to what list it is checked against.

Upon any message getting typed and sent into a server it proccesed by the bot. The bot will check if the words used any words which are banned by the server that its on and if so that message will be deleted by the bot and the bot will send a message to the moderation channel. The message to the moderation channel states the user who tried to the use the banned word/s, the banned word/s and what channel it happened in. Upon joining the server a moderation channel will be made if one doesn't exist so that these messages can be safely logged to the server owner and admins.

Below is a gif of this happening:

![](https://michael-perdue.github.io/assets/Discord-Banwords-command.gif)

#### Adding a ban word to the message filtering

> Command: */banword word*  
*word* - The word you want banned from the server
{: .prompt-info }
>Example use: */banword 'badword'*  
{: .prompt-tip }

To ban a word it is very simple all you need to do is *!banword* then the word you want to ban. What this will do is store the word next to the server the command was executed on and it will persistantly store this so you don't have to worry about the bot crashing and losing your list of banned words. Upon executing the command the bot will delete the message of you wanting to ban the word and then create a new log in the moderation channel which will then state which user banned which word.

Below is a gif of this command in use:

![](https://michael-perdue.github.io/assets/Discord-Banword-command.gif)

#### Removing a banned word from the message filtering

>Command: */unbanword word*  
*word* - The word you want unbanned from the server
{: .prompt-info }
>Example use: */unbanword 'badword'*  
{: .prompt-tip }

To unban a word all you need to do is *!unbanword* followed by the word that you no longer want to be banned. The message filtering will not filter your message even though it includes a banned word as it checks if the message contains the unbanword command so this command will still pass through the filter and be executed. Upon executing the command the bot will delete the message of you wanting to unban the word and then create a new log in the moderation channel which will state which user unbanned which word.

Below is a gif of this command in use:

![](https://michael-perdue.github.io/assets/Discord-Unbanword-command.gif)

#### Checking what words are banned in the message filtering

>Command: */banwords*
{: .prompt-info }
>Example use: */banwords*  
{: .prompt-tip }

To see all the words which are automatically filtered you can use the *!banwords* command that will return a discord embed message with all the words that are banned on this server. 

The embed that is returned looks like this:

### Deleting Messages from a user or channel

So discord for some reason has no way on the user side to delete messages from a channel or a user other then manually deleting each message which is very tedious. They do let you delete up to the last 7 days of a users messages if you permanently ban a user but that is all it allows. So below are commands to delete all of a users message from a channel or server and delete messages from a channel.

### Deleting all user's messages in a channel

>Command: */delete member*  
*member* - The member whose messages you want to delete  
{: .prompt-info }
>Example use: */delete @michael*  
{: .prompt-tip }

Using the command *!delete* followed by the @user you want to delete will lead to all messages from that user being delete from just the channel !delete was typed in. Upon completion of the command the bot will delete the message with the command and log in the moderation channel the user who used the command the channel the deleted the messages from and the user who's message was deleted. It is important to note due to the throttling of discord API it is only possible to delete one message per second, so it will take x seconds to complete with x being the number of messages they have sent in the channel.

Below is a gif of this command in use:

![](https://michael-perdue.github.io/assets/Discord-Delete-command.gif)

### Deleting all a user's messages

>Command: */delete member all*  
*member* - The member whose messages you want to delete  
*all* - Optional by default False if set to True deletes the members messages from all channels not just this one
{: .prompt-info }
>Example use: */delete @michael True*  
{: .prompt-tip }

Using this command is the same as deleting a users message for just one channel *!delete* but you have to add *'True'* after the users name. This command works the same as the previous one but will delete all users message from the server. It will also similiar send a message to the moderation channel stating which user deleted the messages and the user which had their message deleted. Furthermore this command suffers the same throttling of 1 message being deleted per second.

Below is a gif of this comand in use:

![](https://michael-perdue.github.io/assets/Discord-Delete-all-command.gif)

### Deleting last x messages from a channel

>Command: */clear amount*   
*amount* - The number of messages you want deleted from the channel
{: .prompt-info }
>Example use: */clear 10*
{: .prompt-tip }

This command lets you delete a certain number of messages from a channel using *!clear* followed by the number you want deleted. The command will then delete the last x messages in the channel and this command works faster then delete with it being able to delete 100 messages every 3 seconds (more on why this is the case on the other page). Upon deleting the messages, the bot will send a message to the bot channel saying who deleted it, the amount they deleted and what channel.

Below is a gif of this command in use:

![](https://michael-perdue.github.io/assets/Discord-Clear-command.gif)

### Kicking and banning a user 

Both Kicking and banning exist already default unlike all these other commands I have mentioned, so the reason for these commands are to extend what the existing kicking/banning does by adding the reason to the moderation channel so that all logs are stored in one channel.

#### Kicking a user

>Command: */kick user reason*  
*user* - The person you want to ban  
*reason* - The reason why they are being banned
{: .prompt-info }
>Example use: */kick @michael 'idiot'*  
{: .prompt-tip }

To kick a user all you need to do is type */kick* followed by the user and then the reason. Kicking a user means that they are forcibly removed from the server but they can join back given a invite. Once the command is executed then the user will be kicked and the bot will send a message to the moderation channel stating who kicked who and the reason.

Below is a gif of this command in use:

#### Banning a user

>Command: */ban user reason*  
*user* - The person you want to kick  
*reason* - The reason why they are being kicked
{: .prompt-info }
>Example use: */ban @michael 'idiot'*  
{: .prompt-tip }

To ban a user all you need to do is type */ban* followed by the user and then the reason. Banning is a permenant version of kicking and it make it so that the user is forcibly removed from the server and cannot join back even if given a invite. Once the command is executed then the user will be banned and the bot will send a message to the moderation channel stating who banned who and the reason.

Below is a gif of this command in use:

## Clock functionality

### Stopwatch

Below is a gif of the stopwatch functionality working with all 3 command variations:

![](https://michael-perdue.github.io/assets/Discord-Stopwatch-command.gif)

#### Starting the stopwatch

>Command: */stopwatch start*
{: .prompt-info }
>Example use: */stopwatch start*  
{: .prompt-tip }

#### Checking the stopwatch

>Command: */stopwatch time*
{: .prompt-info }
>Example use: */stopwatch time*  
{: .prompt-tip }

#### Stopping the stopwatch

>Command: */stopwatch stop*
{: .prompt-info }
>Example use: */stopwatch stop*  
{: .prompt-tip }
### Timer

>Command: */timer hours minutes seconds*  
*Hours* - Optional by default 0 and its the amount of hours you want the timer to go off in  
*minutes* - Optional by default 0 and its the amount of minutes you want the timer to go off in  
*seconds* - Optional by default 0 and its the amount of seconds you want the timer to go off in
{: .prompt-info }
>Example use: */timer 1 10 10*  
{: .prompt-tip }
Below is a gif of the timer command working:

![](https://michael-perdue.github.io/assets/Discord-Timer-command.gif)

Here is a screenshot of the 1 hour timer going off as for obvious reasons it couldn't be a gif:

![](https://michael-perdue.github.io/assets/Discord-Timer-Complete.PNG)


### Countdown

>Command: */countdown x*  
*x* - The number you want the countdown to start at
{: .prompt-info }
>Example use: */countdown 3'*  
{: .prompt-tip }

Below is a gif of the countdown command working:

![](https://michael-perdue.github.io/assets/Discord-Countdown-command.gif)

## Fun functionality

### Eight-ball

>Command: */8ball question*  
*question* - The question you want the 8-ball to answer
{: .prompt-info }
>Example use: */8ball 'will it rain?'*  
{: .prompt-tip }


Below is a gif of the eight-ball flip command working:

![](https://michael-perdue.github.io/assets/Discord-Eightball-command.gif)

### Coin flip

>Command: */coin times*  
*times* - The number of times you want to flip the coin
{: .prompt-info }
>Example use: */coin 2*  
{: .prompt-tip }

Below is a gif of the coin flip command working:

![](https://michael-perdue.github.io/assets/Discord-Coin-command.gif)

### Dice rolls

>Command: */dice amount sides*  
*amount* - The number of dice you want to roll  
*sides* - The number of sides on the dice
{: .prompt-info }
>Example use: */dice 1 6*  
{: .prompt-tip }

Below is a gif of the dice roll command working:

![](https://michael-perdue.github.io/assets/Discord-Roll-command.gif)

## Messaging functionality

### Greeting 

> Command: */hey* 
{: .prompt-info }
>Example use: */hey*  
{: .prompt-tip }

Below is a gif of the greeting command working:

![](https://michael-perdue.github.io/assets/Discord-Hey-command.gif)

### Mirror 

>Command: */mirror message*  
*message* - What you want the bot to repeat
{: .prompt-info }
>Example use: */mirror 'I am not a bot'*  
{: .prompt-tip }

Below is a gif of the mirror command working:

![](https://michael-perdue.github.io/assets/Discord-Mirror-command.gif)

### Forwarding

>Command: */forward member message*  
*member* - Could be a group or user (example @user or @role) that you want to anonymously recieve the message  
*message* - What you want to forward to the group or user
{: .prompt-info }
>Example use: */forward @michael 'stop that'*  
{: .prompt-tip }

Below is a gif of the forwarding command working:

![](https://michael-perdue.github.io/assets/Discord-Forward-command.gif)

### Spam

>Command: */spam member times*  
*member* - Could be a group or user (example @user or @role) that you want to notify  
*times* - How many notifications you want to send to the user or group
{: .prompt-info }
>Example use: */spam @everyone 10*  
{: .prompt-tip }

Below is a gif of the Spam command working:

![](https://michael-perdue.github.io/assets/Discord-Spam-command.gif)
