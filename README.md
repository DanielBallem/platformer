# Efficient 3D platformer implementations
## Implemented feature breakdowns
1. [Datatable Driven Bounce Pads](#datatable-driven-bounce-pads)
2. [Checkpoints](#checkpoints)
3. [Collectables](#collectables)
4. [Collectable placement helpers](#collectable-placement-helpers)
5. [Trigger and Interaction Components](#trigger-and-interaction-components)
6. [Platforms](#section-1)
7. [Music-driven events (Sequencer)](#section-1)
8. [Slide Mechanic and dot product fun](#section-1)




## Datatable Driven Bounce Pads
  ![bouncepads](https://github.com/DanielBallem/platformer/assets/33844493/b2bac4dc-80d2-4d59-9036-a916afc9ba6d)

  Platformers require consistency across different gameplay interactions and objects. Tables are a great way to keep track of this, as any changes in the table will persist across all levels, ensuring that the player knows what height to expect when landing on a particular bounce pad. 
  - By adding another entry into the data table and its corresponding enum, this blueprint will automatically show a new type to select.
  - ![image](https://github.com/DanielBallem/platformer/assets/33844493/936f8e3d-4ad8-4a53-8368-54818dfca34a)
  - This bounce pad sets the height and material from the datatable.

### Bounce pad direction
![image](https://github.com/DanielBallem/platformer/assets/33844493/2b185c78-3da2-4be2-9d70-56dba4216763)

Bounce pads can be rotated, and will launch the player wherever they point.

## Checkpoints
![image](https://github.com/DanielBallem/platformer/assets/33844493/8981435f-8cfa-42a0-af86-2eabfa7a7dab)

A simple checkpoint system is useful in 3d platforming games to ensure progress throughout the level. 
- On startup, it converts the new spawn location from local space to world space.
- Once the player overlaps with the trigger, the checkpoint will get the game mode and officially set a new spawn point.
![image](https://github.com/DanielBallem/platformer/assets/33844493/8b8f5de1-a294-497e-895f-1d2cc6ab2ab4)
This can be useful for big open worlds that require plenty of checkpoints to be used in any order. For an ordered checkpoint system, an index can easily be implemented to ensure that only checkpoints of further progress are accounted for.

## Collectables
![image](https://github.com/DanielBallem/platformer/assets/33844493/e5b27e85-03bd-4a95-97a0-4ed8a8effa00)

I've made 2 collectables: common and key collectables.
Both inherit from a collectable parent, which sets up the removal of the collectable once it has been collected, and enforces height so that all collectables appear at uniform height.

Level designers can drag and drop collectables into a level without having to set up their height every time.

![collectablesnap](https://github.com/DanielBallem/platformer/assets/33844493/0cbf04d8-fec2-40e4-9b5f-b2d5bd882194)

Common collectables spin and hover using a timeline, which can be edited to ensure uniformity across all common collectables. 

![collectablespin](https://github.com/DanielBallem/platformer/assets/33844493/3bb03c79-74ca-41e2-80ee-8e45661b1556)

An offset can be given so that when placed in sequence, they can offset each other. 

![collectableoffset](https://github.com/DanielBallem/platformer/assets/33844493/c32aaca7-0c41-4edf-997e-e6acb53ce478)

## Collectable Placement Helpers

Level designers need to add large collections of collectables quickly, this could be collectables leading down a path, or collectables in a circle on a moving platform, or along the curve of a jump. I have made two classes to help with this:

### Collectable Ring
This object spawns collectables around its origin, with a radius of R and a total rotation from 0-360 degrees.
![image](https://github.com/DanielBallem/platformer/assets/33844493/354c27c2-0b85-4088-ab89-a6c73440fe23)
A number of parameters can be set:
- Number of collectables
- Collectable BP to spawn (Must inherit Collectable parent class)
- Collectable end offset
- Circle Radius
- Total degrees of rotation.

![image](https://github.com/DanielBallem/platformer/assets/33844493/2c3716dc-c1a3-45fa-9579-2a6dc515f7f6)

All of these will ensure that designers don't have to work very hard to get a circle of collectables. Circles by hand are hard!

### Collectable Spline

Splines work in a similar way, allowing the designer to follow a path to place collectables along, or do whatever crazy design they want. 
![image](https://github.com/DanielBallem/platformer/assets/33844493/560a5d90-d374-449e-a1e0-0125489a3696)

![image](https://github.com/DanielBallem/platformer/assets/33844493/4a7cf680-da01-4a7d-aefb-c81f3fd3d79d)

## Trigger and Interaction Components
