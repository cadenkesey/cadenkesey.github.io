* [Return to home](/index.md)

# Lilith: A 2.5D First Person Shooter in Unity

This project is a simple first person shooter created in Unity and coded in C#. It utilizes sprites rather than 3D models to represent objects in the game world, much like early 90's shooters such as Doom and Marathon. It has basic features such as player movement (jumping, crouching, etc.), enemy AI (monsters and turrets), and shooting.

<iframe width="560" height="315" src="https://www.youtube.com/embed/bYQVP_nNBZo" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

* [Link to project code](https://github.com/cadenkesey/lilith)

## Introduction

My goal with Lilith was to give myself an opportunity to learn both Unity and C#. I had programmed many small games in the past in programs such as Game Maker and Unreal Engine, so this time I wanted to create a first person shooter to challenge myself. Thankfully there are many resources for learning Unity online, and I have cited the tutorials I used in my code. All of the assets used in the game were created by me.

## Enemy 1: Fairy

### Finite State Machine

I used a finite state machine for the Fairy enemy since it is a straightforward and common design for AI in video games. I found tutorials from Table Flip Games to be particularly helpful: [Link to YouTube](https://youtu.be/21yDDUKCQOI). This design allows the Fairy to switch between four different states: Idle, Patrol, Chase, and Attack. My choice to use these states is influenced by the behavior of enemy AI in the original Doom games. The Idle state simply has the Fairy wait in place until a certain amount of time has passed, at which point the Fairy will enter the Patrol state. The Patrol state will move the Fairy to the next Patrol point, where the Fairy will enter the Idle state again. During either of these states the Fairy can move to the Chase state if the player comes within the Fairy's line of sight. The Chase state is the most complicated state. The Fairy wants to get close to the player, but also not so close as to run into the player. This means the Fairy will move towards the player until reaching a certain distance. At this point they will move to random points around the player. At any point during the Chase state the Fairy may decide to move to the Attack state. In the Attack state, the Fairy aims towards the player and then fires. Firing or failing to maintain aim on the player will move the Fairy back to the Chase state.

#### Movement in the Chase State

The Fairy's movement while in the Chase state is somewhat complicated as there are several factors to consider. While Table Flip Games utilized a Navigation Mesh Agent to control AI movement, I wanted more control. Rather than using an Agent to interact with Unity's Navigation Meshes, I have the Fairy calculate Navigation Mesh pathways itself. Following an array of every corner along this pathway, the Fairy can then be rotated and moved manually each frame. By manually controlling these steps the Navigation Mesh Agent would normally handle, I can ensure the Fairy looks in the direction of each corner as it moves which proved to be troublesome with the Navigation Mesh agent.

Additionally, the Fairy will update its Navigation Mesh pathway a random amount of time (between a half a second and three seconds) after its last update in order to avoid calculations every frame while also ensuring the pathway is never stale for too long. During these updates, the Fairy will make a probability check to decide whether it should attack the player, partially based on its distance from the player. Additionally, if the Fairy moves close enough to the player it will update its pathway to move to a random point around the player within a certain radius. This way the Fairy never gets too close and instead dances around the player. The Fairy checks if it is getting too close by both distance to the player and by calculating the angle between its movement and the player.
