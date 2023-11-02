# Efficient 3D platformer implementations
## Implemented feature breakdowns
1. [Datatable Driven Bounce Pads](#datatable-driven-bounce-pads)
2. [Checkpoints](#checkpoints)
3. [Collectables](#collectables)
4. [Collectable placement helpers](#collectable-placement-helpers)
5. [Trigger and Interaction Components](#trigger-and-interaction-components)
6. [Platforms](#platforms)
7. [Music-driven events (Sequencer)](#music-driven-events)
8. [Slide Mechanic, player movement and dot product fun](#slide-mechanic-and-player-movement)




## Datatable Driven Bounce Pads
![bouncepads](https://github.com/DanielBallem/platformer/assets/33844493/e28d331f-e81c-4355-84dc-17db9c9dffb1)

  Platformers require consistency across different gameplay interactions and objects. Tables are a great way to keep track of this, as any changes in the table will persist across all levels, ensuring that the player knows what height to expect when landing on a particular bounce pad. 
  - By adding a new entry into the data table along with its corresponding enum, this blueprint will automatically display a new selectable type.
  - ![image](https://github.com/DanielBallem/platformer/assets/33844493/ea451f9b-2c5b-41ac-a419-3bc793c46993)

  - This bounce pad sets the height and material from the datatable.

### Bounce pad direction
![image](https://github.com/DanielBallem/platformer/assets/33844493/f751f3c4-6758-4f7d-9831-c38e24332e4c)

Bounce pads can be rotated, and will launch the player wherever they point.

## Checkpoints
![image](https://github.com/DanielBallem/platformer/assets/33844493/fc3e88cc-57c1-42b2-95eb-acce4f5e7d94)

A simple checkpoint system is useful in 3d platforming games to ensure progress throughout the level. 
- On startup, it converts the new spawn location from local space to world space.
- Once the player overlaps with the trigger, the checkpoint will get the game mode and officially set a new spawn point.
![image](https://github.com/DanielBallem/platformer/assets/33844493/dbe6c6e5-f444-43f6-8ff6-4068a2d1330e)
This is particularly useful for expansive open worlds where checkpoints can be utilized in any sequence. To establish an ordered checkpoint system, implementing an index can ensure that only checkpoints representing further progress are considered

## Collectables
![image](https://github.com/DanielBallem/platformer/assets/33844493/dcafd8aa-2a5a-4f0e-b18f-b020feab4da8)

I've made 2 collectables: common and key collectables.
Both inherit from a collectable parent, which sets up the removal of the collectable once it has been collected, and enforces height so that all collectables appear at uniform height.

Level designers can drag and drop collectables into a level without having to set up their height every time.

![collectablesnap](https://github.com/DanielBallem/platformer/assets/33844493/eacf59c7-8499-479b-8ce1-3b5fee2351df)

Common collectables spin and hover using a timeline, which can be edited to ensure uniformity across all common collectables. 

![collectablespin](https://github.com/DanielBallem/platformer/assets/33844493/e0512e67-9067-4ec9-b9b9-bea3d2ac54d1)

An offset can be given so that when placed in sequence, they can offset each other. 

![collectableoffset](https://github.com/DanielBallem/platformer/assets/33844493/6bd8216e-2563-4ba5-ad96-7b2827b55dcc)

## Collectable Placement Helpers

Level designers need to add large collections of collectables quickly, this could be collectables leading down a path, or collectables in a circle on a moving platform, or along the curve of a jump. I have made two classes to help with this:

### Collectable Ring
This object spawns collectables around its origin, with a radius of R and a total rotation from 0-360 degrees.
![image](https://github.com/DanielBallem/platformer/assets/33844493/eedf1b53-39fa-4ecf-a154-75530b2623d3)
A number of parameters can be set:
- Number of collectables
- Collectable BP to spawn (Must inherit Collectable parent class)
- Collectable end offset
- Circle Radius
- Total degrees of rotation.

![image](https://github.com/DanielBallem/platformer/assets/33844493/880e94e7-bf41-45e2-ad7c-9a9adf5ec7c4)

All of these will ensure that designers don't have to work very hard to get a circle of collectables. Creating circles manually can be challenging!

### Collectable Spline

Splines work in a similar way, allowing the designer to follow a path to place collectables along, or do whatever crazy design they want. 
![image](https://github.com/DanielBallem/platformer/assets/33844493/a9ff930d-6f9e-435b-8594-95496e5fd31b)

![image](https://github.com/DanielBallem/platformer/assets/33844493/555b047c-340c-4caa-87b2-d5de1d2d0683)

## Trigger and Interaction Components
Interactable bp actors inherit from an interaction class, which sets up a UI widget to appear and dissapear when the player has them highlighted (close and in front of them).
When the player interacts, it will activate whatever that component will override as its own "interact" function. 
Parent class graph:
![image](https://github.com/DanielBallem/platformer/assets/33844493/fe9f89a7-c3be-4123-8e7a-d913fe5d7220)

### interactable trigger:
 I've set up a switch, which is a child of the interactable actor and uses a TriggerComponent I made. Upon interaction, it uses it to open and close the door.
![image](https://github.com/DanielBallem/platformer/assets/33844493/b74fa262-0f8f-463d-a90d-3db2f767c30f)

### NPC:
This NPC is also a child of the interactable actor, but stops the player from moving and shows a "dialogue" in the form of a string message.
![image](https://github.com/DanielBallem/platformer/assets/33844493/2c6ec189-057e-41cd-aac0-79e454edc188)

#### Using both in action:
![interaction](https://github.com/DanielBallem/platformer/assets/33844493/82bbc107-f23a-4a01-bd93-8b3e49f0b5cb)

###Trigger and Triggerable Component
Trigger Component: Attachable component that facilitates the logic for registering itself with the object you want to trigger. The actor can use it to trigger whatever the designer has set up. Using a dropped, you can make any switch trigger anything with the `triggerable` component.

Triggerable component: Has two behaviours:
1. Toggle on/off. Object can act off of toggle events to enact some kind of behaviour.
2. Buildup trigger. Object will not trigger unless all of the triggers pointing to itself have been fired.

As an example of the buildup trigger, you can see that this door doesn't open unless both key collectables have been collected.
![builduptrigger](https://github.com/DanielBallem/platformer/assets/33844493/faf646ae-2584-4084-aa14-45bcec6ed2a9)

To set what actor the object will trigger, you can use a dropper. 

![image](https://github.com/DanielBallem/platformer/assets/33844493/414b6ae5-daf8-47d5-a356-cd44f3b0f47b)

A designer will be able to swap triggered objects really quickly, or add new key items to open doors. They can do this without needing to change the existing triggered object.

## Platforms

Every good 3D platformer needs moving platforms. I've made two: rotating and movement platforms. 

Using a timeline I can get a position along a spline and move the platform along with it. It can inherit rotation of the spline, or stay upright. It can use any mesh. Start and stop via player interaction, or once the level starts.
Rotating platforms can be set which axis, and the rate each individual axis rotates by.
![movingplatform](https://github.com/DanielBallem/platformer/assets/33844493/18223100-8f52-42f6-ae62-77b19d0ca891)

moving platform:
![image](https://github.com/DanielBallem/platformer/assets/33844493/ee0bf499-2423-4d22-9b5f-63bb198fc1ba)
![image](https://github.com/DanielBallem/platformer/assets/33844493/92ed1279-ee1b-496e-868e-4db19823b82f)

## Music-driven Events

Inspired by the recent Super Mario Wonder, I explored implementing actors in a level that respond to the beat of a song.

- A level sequence with a song starts playing. The sequencer references a Music manager, which periodically fires "StrongBeat" events.
  ![image](https://github.com/DanielBallem/platformer/assets/33844493/eac6590b-7be1-47fe-816f-0541ae81d4fa)

- A trigger component called "MusicTriggerComponentBP" subscribes to the music manager's StrongBeat events, and fires its own event that the parent can use in order to do something. In my case, I made platforms rotate. This creates a harmonious relationship between the audio and the world, which really helps the game feel unified. As well, you don't need to worry about syncing movements yourself with every object you place.

https://github.com/DanielBallem/platformer/assets/33844493/9119755b-e287-40af-a61d-684cf4f0ca3a

An artist could create a track, and someone could go through the song and add events for on beats, off beats, accents, the melody, etc. Then, you could have all of those events be subscribed to by actors that need to act in time. Once you've created the level sequence, you can build off of it really easily. 

## Slide Mechanic and Player Movement

As part of the 3d platformer package, I wanted to experiment on what it's like fine-tuning a playable character that feels fun to control on both keyboard and mouse. WOW this took the most time, and I could still improve it!!

### This character has a few rules:
- In the air they can take two of the following actions:
  1. Dash
  2. Jump
- The character can slide, which has the following effects:
  1. They slow down while on horizontal or upward slope
  2. They carry their momentum while jumping over a minimum threshold
  3. They can slide down a slope in order to build momentum.
  4. They jump higher when sliding.

Implementing the jump and dashing was fine, the tricky part was the sliding, which took a lot of fine tuning in order to get down (and it's still not perfect). I have a system where when sliding, it changes the character's parameters via a data table I've set up.


To emulate the feel of increasing in speed, as the player slides down or up it changes their maximum speed. The math to determine if the player should speed or slow down is as follows:

![image](https://github.com/DanielBallem/platformer/assets/33844493/423be3fa-3821-4036-8cb6-40b0731cc6c3)

When sliding, a raycast is shot directly below the player, and the angle of the slope is used with the current velocity of the player to dictate if they're moving up or down that slope. If the dot product is positive, the player is going downhill. If it is zero or negative, they're horizontal or going uphill, and should then slowdown.

As part of the stylization of the movement, I can use the slope normal to add a very small force to the player, so they slowly slide down in the direction of the slope regardless of how they're sliding. 

![image](https://github.com/DanielBallem/platformer/assets/33844493/b657e9d3-3e25-421f-9eb3-c2a85ae436f8)

Although the slide mechanic isn't flawless, particularly when sliding on an incline as it swiftly transitions back to walking, it still represents a significant improvement from the previous version. I find it to be satisfactory, and I take pride in the mathematical calculations and effort I invested to implement this feature independently.

If you wish to play through the "level" I made, or see any of the implementations yourself, feel free to download the project. If you're going to use the code for your own work, all I ask is that you credit me somewhere.
![image](https://github.com/DanielBallem/platformer/assets/33844493/ff22ba45-0a0a-4f27-ab22-8a83e335fb90)

Thank you!

(note, this github is more about the features than the level. There is no end to it, it's a playground to try out the movement mechanics and how it works with everything else I've built)
![image](https://github.com/DanielBallem/platformer/assets/33844493/5c0d6e1c-b4c8-4ad0-be98-655c94456e39)

I don't own any of the music, or the skybox that was free on the marketplace. 
