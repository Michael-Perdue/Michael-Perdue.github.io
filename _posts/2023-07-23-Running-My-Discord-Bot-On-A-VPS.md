---
title: How my discord helper bot runs 24/7 on a VPS
date: 2023-07-27
categories: [Additional Projects, Discord Bot Project]
tags: [python, discord, docker, vps]  # TAG names should always be lowercase
---

This page focuses on how I set up my bot to run 24/7 on a VPS and the use of the docker file and image I created. It also covers some justification for the remote infrastructure. You can find the [docker image here](https://hub.docker.com/repository/docker/michaelperdue/discord-helper-bot/general). If you want to see gifs of the helper bot working or if you care more about just what the helper bot offers for the end user then go to [this page](https://michael-perdue.github.io/posts/My-Discord-Bots-Functions/). If you want to see how to set up your own python bot and how some of the complex parts of my helper bot work then go to [this page](https://michael-perdue.github.io/posts/How-My-Discord-Bot-Works/). [Click here for the GitHub repo for this project](https://github.com/Michael-Perdue/Discord-Helper-Bot). If you want to know how I set up my bot to run 24/7 on a VPS and the use of the docker file and image I created there is a [separate page](https://michael-perdue.github.io/posts/Running-My-Discord-Bot-On-A-VPS/) for that.

## The VPS Setup 

I needed a VPS because the purpose of a discord bot is to be available 24/7 as otherwise, someone could type a message with a banned word while the bot is down and it would go through to the server as the bot wouldn't be able to perform message filtering. My computer can't be on 24/7 for various reasons so this led me to set up a VPS. The VPS I set up is through [DigitalOcean](https://www.digitalocean.com/), beyond this project a VPS in the future could also be useful to host databases and such for my future projects. The VPS which is provided by DigitalOcean is a blank server with Ubuntu server installed and contains 25GB of SSD space so to run multiple applications on it you want them to be as lightweight as possible. 

This led me to then install docker and create a docker image so that it could have just what the bot needs to run, I then pulled the [docker image I made](https://hub.docker.com/repository/docker/michaelperdue/discord-helper-bot/general) and ran it with ```docker run -d --env TOKEN='my_discord_token' discord-helper-bot```. The -d is to run the container in the background and the reason why I pass the token as an environment variable on run and not previously is that the docker image is public and I don't want just anyone to run my bot, it also means that people can run their own bot with the same functionality if they want.

## My DockerFile Explained

This was the first time that I had created a DockerFile and I came up with the below DockerFile that works for my discord bot:

```dockerfile
FROM python:3.10-alpine
WORKDIR /code
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
COPY discordbot discordbot
CMD ["python", "discordbot/main.py"]
```

This docker file sets the base image to be python 3.10 then creates and moves to a directory called code and copies the requirements text file into it. It then runs pip install on the requirements text file which contains a line for each python module needed i.e. ```discord.py==2.3.1```. Finally, it copies the folder discordbot which contains all the python files and it then runs the main.py file.

## Running the docker image

To run the docker image all you need to do is pull the [docker image](https://hub.docker.com/repository/docker/michaelperdue/discord-helper-bot/general) with ```docker pull michaelperdue/discord-helper-bot``` and then run it with ```docker run -d --env TOKEN='my_discord_token' discord-helper-bot```. You could also export the TOKEN variable and then just do ```-env TOKEN``` instead of ```-env TOKEN```. The docker image does not auto-build on push to the GitHub repo due to me not having docker pro but in the future, if I continue to further update the project and see a bigger need for it then I will setup automatic building of the image.