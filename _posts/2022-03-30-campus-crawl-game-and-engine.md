---
title: Campus Crawl game and engine
date: 2022-03-30 
categories: [University Projects, 2nd year]
tags: [csharp]     # TAG names should always be lowercase
---

## Tile engine

The game which I made was based off a tile engine that was made by someone I was working with and was specifically made for this project. The engine created for this project, “tileEngine”, is a ground-up game engine, relying only upon the widely used MonoGame graphics library for convenient sprite batch draw calls, and serialization libraries for writing and reading data from file. It is split down into several modular sections, namely the editor, platform specific player implementations, documentation, and SDK.

The UI of tile engine:

### Editor
The engine’s editor is built upon a modified version of the open source DarkUI using WinForms and allows the user to build and create dynamic tiled maps, manage events and mark tiles for collision for these maps without the need for manual programming.

example of making an event: 


Once projects have been built using the editor, the assets, code, and additional metadata is compiled down into a compressed, easy to use format by the tileEngine compiler, which can then be provided to end users. The tileEngins compiler manages the conversion of all the assets into “.XNB” format and it also performs packing of all project metadata such as the project’s virtual file tree and default settings into packed binary form.

External code written by the gameplay programmer for their project is also carried in a portable C# assembly with this compiled executable and this executable is passed to the SDK to handle. 

### SDK and API

The SDK is entirely platform independent and contains as few features as possible for platforms to be forced to implement individually, instead providing a wealth of base functionality that can be shared across all platforms. 

The API of this engine is structured with a traditional game object and component model, however, is limited to a single layer of hierarchy, instead opting to use components to control transform parenting. This takes the benefits of a Unity-like model allowing for the attachment of many different additional pieces of functionality without excessive inheritance, while also keeping simplicity within game object handling by removing the game object tree. 

## Campus Crawl game

The game that I made through using the tile engine is based off lancaster campus and is a wave based top down combat game that has pathing, combat, scoring, sound and UI which was all made specific for this game. 

### Enemy movement and A* pathing

Enemy movement in the game is based upon an implementation of A* pathing algorithm that I made, which efficiently calculates the shortest path of many enemies multiple times each second. Every 0.5 seconds the enemy checks if the player is in view (view is based on a 10x10 tile range) and then if they are, a new path is generated, the enemy then follows this path and ignores the previous path that they were on. If the player leaves the enemy view, then they will continue to the end of the path that they are currently on, it will still check every 0.5 seconds to see if the player is in view and if so, will generate a new path but if not and the enemy gets to the end of the path and the player is still not in view then it goes on patrol. Patrol is a set movement path that the enemy has and is given at construction, it is a simple direction i.e., left, right, up, down and set number of blocks, if the enemy reaches a collision block (a wall etc.) then it goes back the reverse direction i.e., up turns to down.

Here you can see the pathing where the red squares are collision tiles and the green npcs are enemies: 


### Combat system

The enemy combat works of the basis of different enemy types. Depending on the type of enemy, the amount of damage they deal and speed they travel at can vary, as the tank deals a lot of damage but is very slow. The game has a default enemy, whose stats are all average, a tank enemy which deals high damage and moves at a low speed, and a speedy enemy which deals low damage but moves quickly. The enemy can attack with a basic attack which they move forward a small amount towards the enemy and it checks every 1 second if the player is in range(1 tile) and if so it attacks and knockbacks the player. The enemy can also perform a special attack where the enemy lunges 3 tiles toward the player and deals more damage and knockback this attack has a longer range(3 tiles) and can only be performed every 15 seconds. An important thing to note is that the enemy cannot charge the special attack and can only perform a non-charged special attack. Upon the enemy's health reaching zero they are removed from the map, thus disappear from player's vision. When the enemy attacks its player model changes and once its done attacking it turns back to its resting model.


Player combat works in a similar way to the enemy combat, with the only differences being that they trigger attacks with their mouse and have a reduced cooldown in between attacks. The player can click left mouse button to perform the basic attack of the weapon, by default the player has fists as weapons, and they can perform the special attack through right click. Unlike the enemy they can hold right click to perform a charged special attack. The player has 100 health and has a speed of 110 units while the default enemy has a speed of 100 units and 100 health. Upon players health reaching zero they will respawn at a set point and the wave they reached will be reset to wave 1.

Below is a gif of the combat system in action:

### Wave system

The game itself is based on a wave system, what this means is that the player starts off in Alexandra Square and then a set of enemies will spawn i.e., 4 default enemies and one tank enemy all with fists as weapons, they will spawn at a random position on the map for wave. The player will then have to defeat all the enemies by getting their health to zero and then when all enemies are eliminated the next wave will start, and the player will be healed by 30 health (if health is higher than 70/100 then it will only be put to 100 and will not go over base health). As the player progresses, they must fight more enemies each with more specialised enemy types such as tanks. As mentioned above if the player dies then the player will respawn, and the wave system will reset.

### Scoring, UI and Sound

The scoring system of the game is based off the speed at which waves are completed and the amount and type of enemy that you defeat. The equation for the score calculated at the end of the wave is:
`WaveCompleteScore = ( (WaveCount *30) - TimeTaken) *5`
Where “WaveCount” is the current wave number i.e., the first wave and “TimeTaken” is the time since the wave started in seconds.

In terms of GUI, in the top left of the screen there is a health bar. This has the amount of health the player has in whole integer values, and each time they lose health or gain health the amount of green in the bar increases or decreases. To represent the lost health, the other part of the bar is red. In the top right is the wave information such as the wave count and the number of enemies left in the current wave. At the top middle of the screen there is the players overall score count. There is also a home screen which has our logo, a start button, and a description of how to play the game, also there is a defeated screen which shows the user their score, and there is button to click to restart the game (which resets everything back to the first wave). All of this is updated constantly so it always displays the current information.

When it comes to sound in the game for Alexandra Square there is a background noise of birds chirping, this is based on a short audio clip and is looped so that there is always audio. Every time the player or enemy gets hit a clashing noise is played, at the end of each wave you hear “wave complete, new wave begins”, enemies and players also have a unique death sound. There is also audio for clicking the buttons, it is important to note that I did not make these audio clips and instead were audio clips that were found online that allowed free usage for this type of project

The ingame UI:


### Short demo of the game as whole

This is a short demo of the finished game:

<video width="500px" height="500px" src="https://michael-perdue.github.io/assets/gameDemo.mp4"></video>
