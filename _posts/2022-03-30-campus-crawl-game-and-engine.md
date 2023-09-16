---
title: Campus Crawl game and engine
date: 2022-03-30 
categories: [University Projects, 2nd year]
tags: [csharp, game, pathing, algorithms]     # TAG names should always be lowercase
---

**Project Overview:** 

The Campus Crawl Game and Engine is a collaborative project undertaken during the second year of my academic journey spanning the 20 weeks of development and planning. This project consisted of two major components: the ***"tileEngine"*** and the ***"Campus Crawl"*** game. The tileEngine I was not involved in coding but I was continually testing and working closely with them to get rid of bugs and give guidance on what is needed to be a successful map making and game engine. I was the lead in the campus crawl game which is a wave based beat-em-up style game based on lancaster campus built on the ***"tileEngine"***. The code for this project can be found at **[this GitHub repo](https://github.com/c272/campus-crawl)**.

## Tile Engine

The Campus Crawl game is built upon a versatile tile engine, custom-designed for this project. The engine, known as ***["tileEngine"](https://github.com/c272/tileEngine)***, is a ground-up game engine optimized for efficient sprite batch draw calls using the MonoGame graphics library. It incorporates serialization libraries for data read/write operations. The engine is modular, comprising an editor, platform-specific player implementations, documentation, and SDK. While I didn't code the engine itself, I closely collaborated with the development team to provide requirements, test features, and assist in debugging.

**Editor Interface:**  

![Editor Interface](https://michael-perdue.github.io/assets/editor.png)

### Editor

The engine's editor, based on a modified version of the open-source DarkUI and using WinForms, empowers users to construct dynamic tiled maps, manage events, and define collision zones without manual programming.

**Creating an Event:**

![Creating an Event](https://michael-perdue.github.io/assets/editorevent.png)

The tileEngine compiler compiles project assets, code, and metadata into a compressed format for end-user distribution. It converts assets into ".XNB" format and packs project metadata into a binary form. The SDK handles the compiled executable, including external code authored by gameplay programmers.

### SDK and API

The SDK is platform-independent, emphasizing shared base functionality across platforms. The API follows a traditional game object and component model but simplifies game object handling by removing the game object tree in favor of using components to control transform parenting.

## Campus Crawl Game

The Campus Crawl game, based on Lancaster campus, is a top-down wave-based combat game that includes pathing, combat mechanics, scoring, sound effects, and a user interface tailored to the game.

### Enemy Movement and A* Pathing

Enemy movement in the game is based upon an implementation of A* pathing algorithm that I made, which efficiently calculates the shortest path of many enemies multiple times each second. Every 0.5 seconds the enemy checks if the player is in view (view is based on a 10x10 tile range) and then if they are, a new path is generated, the enemy then follows this path and ignores the previous path that they were on. If the player leaves the enemy view, then they will continue to the end of the path that they are currently on, it will still check every 0.5 seconds to see if the player is in view and if so, will generate a new path but if not and the enemy gets to the end of the path and the player is still not in view then it goes on patrol. Patrol is a set movement path that the enemy has and is given at construction, it is a simple direction i.e., left, right, up, down and set number of blocks, if the enemy reaches a collision block (a wall etc.) then it goes back the reverse direction i.e., up turns to down.

**Pathing Demonstration:**

![Pathing Demonstration](https://michael-perdue.github.io/assets/PathingCampusCrawl.gif)

### Combat System

The game features various enemy types, each with distinct damage and speed attributes. The default enemy is balanced, while the tank enemy deals high damage but moves slowly, and the speedy enemy moves quickly but deals less damage. Enemies have basic and special attacks. Basic attacks trigger when the player is within a one-tile range, and special attacks involve a three-tile lunge with increased damage, with a longer cooldown. Player combat operates similarly but allows a charged special attack upon right-click. Players respawn with a reset wave upon reaching zero health.

### Wave System

The game employs a wave system, with increasing waves of enemies to defeat. Each wave introduces new enemy types and challenges. Players must clear all enemies in a wave to progress to the next. Player deaths result in respawning with the wave reset.

### Scoring, UI, and Sound

The scoring system evaluates the player's performance based on wave completion speed and enemy type eliminations. The UI includes health bars, wave information, and a score counter. Sound effects, including ambient background audio and combat sounds, enhance the gaming experience.

**In-game UI:**

![In-game UI](https://michael-perdue.github.io/assets/uibar.png)

## Short Game Demo

Here's a brief demonstration of the completed game:

{% include embed/youtube.html id='29R-4SPj_A8' %}


**Closing Thoughts:** 

Developing the Campus Crawl Game and Engine was a challenging and rewarding experience teaching me many things about group work and collaboration. It highlighted the importance of version control, code reviews and overall good team structure with clear communication and role assignments.