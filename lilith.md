* [Return to home](/index.md)

# Lilith: A 2.5D First Person Shooter in Unity

This project is a simple first person shooter created in Unity and coded in C#. It utilizes sprites rather than 3D models to represent objects in the game world, much like early 90's shooters such as Doom and Marathon. It has basic features such as player movement (jumping, crouching, etc.), enemy AI (monsters and turrets), and shooting.

* [Link to project code](https://github.com/cadenkesey/lilith)

<iframe width="560" height="315" src="https://www.youtube.com/embed/bYQVP_nNBZo" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

## Introduction

My goal with Lilith was to give myself an opportunity to learn both Unity and C#. I had programmed many small games in the past in programs such as Game Maker and Unreal Engine, so this time I wanted to create a first person shooter to challenge myself. Thankfully there are many resources for learning Unity online, and I have cited the tutorials I used in my code. All of the assets used in the game were created by me.

## Player

The player's movement is very simple. I used Unity's new input system which allows for controls to be remapped easily. It requires more coding to be done than the old system but it avoids hardcoding keymappings. The only real calculations being done for player movement are with the player's vertical speed. This includes checking if the player is on the ground, jumping, hitting the ceiling, or in the air. The player is also able to crouch, so checks are made to see if the player should be forced to crouch if there is no room to stand back up after entering a small area. The player is also able to be crushed by moving platforms.

![Shooting](/images/lilith/shoot.gif)

The player can interact with certain objects throughout the game's world. This includes doors, messages, and platform switches. If the player is targeting one of these objects and presses the 'Use' key, a method will be evoked in the target object. The player is also able to shoot, which will produce a bullet hit effect wherever the player is targeting. Firing on the Fairy enemy will instead trigger a blood effect and will call a method in the enemy to inflict damage.

## Enemy 1: Fairy

### Sprites

Although the world of Lilith is a 3D environment, the objects in the game are made up of 2D sprites. The Fairy is no exception, with 8 different sprites to represent the various angles at which the Fairy can be viewed.

![Fairy](/images/lilith/fairy.gif)

### AI - Finite State Machine

I used a finite state machine for the Fairy enemy since it is a straightforward and common design for AI in video games. I found tutorials from Table Flip Games to be particularly helpful: [Link to YouTube](https://youtu.be/21yDDUKCQOI). This design allows the Fairy to switch between four different states: Idle, Patrol, Chase, and Attack. My choice to use these states is influenced by the behavior of enemy AI in the original Doom games. The Idle state simply has the Fairy wait in place until a certain amount of time has passed, at which point the Fairy will enter the Patrol state. The Patrol state will move the Fairy to the next Patrol point, where the Fairy will enter the Idle state again. During either of these states the Fairy can move to the Chase state if the player comes within the Fairy's line of sight. The Chase state is the most complicated state. The Fairy wants to get close to the player, but also not so close as to run into the player. This means the Fairy will move towards the player until reaching a certain distance. At this point they will move to random points around the player. At any point during the Chase state the Fairy may decide to move to the Attack state. In the Attack state, the Fairy aims towards the player and then fires. Firing or failing to maintain aim on the player will move the Fairy back to the Chase state.

#### Movement in the Chase State

The Fairy's movement while in the Chase state is somewhat complicated as there are several factors to consider. While Table Flip Games utilized a Navigation Mesh Agent to control AI movement, I wanted more control. Rather than using an Agent to interact with Unity's Navigation Meshes, I have the Fairy calculate Navigation Mesh pathways itself. Following an array of every corner along this pathway, the Fairy can then be rotated and moved manually each frame. By manually controlling these steps the Navigation Mesh Agent would normally handle, I can ensure the Fairy looks in the direction of each corner as it moves which proved to be troublesome with the Navigation Mesh agent.

Additionally, the Fairy will update its Navigation Mesh pathway a random amount of time (between a half a second and three seconds) after its last update in order to avoid calculations every frame while also ensuring the pathway is never stale for too long. During these updates, the Fairy will make a probability check to decide whether it should attack the player, partially based on its distance from the player. Additionally, if the Fairy moves close enough to the player it will update its pathway to move to a random point around the player within a certain radius. This way the Fairy never gets too close and instead dances around the player. The Fairy checks if it is getting too close by both distance to the player and by calculating the angle between its movement and the player.

## Enemy 2: Turret

### Sprites

The turret has more animations than the Fairy. This is because the turret has animations for activating and deactivating and can be viewed in 8 directions while in the middle of these animations.

![Turret](/images/lilith/turret.gif)

The turret also has emission maps which allow its camera to glow when active and the turret to light up when firing.

### Turret AI

The turret is much simpler than the Fairy in its AI. It is either active or deactive, and will turn towards the player when the player is nearby and visible. The turret is able to keep track of multiple targets though, and will attack whatever target is closest. This means the turret is not designed to only attack the player, but can also attack the Fairy if they were in the same room together.

## Animating Sprites

Rather than use Unity's built-in animator, I wrote my own scripts to handle the animation of object sprites. This is to ensure that an animation can be viewed in different directions as it is playing, with each frame updating to reflect the player's position relative to the object.

![FairyAngles](/images/lilith/fairyangles.gif)

My custom anination controller works by using recursive coroutines to play an animation. Each time the method is called, it will calculate the correct angle to be displayed, update the object's sprite renderer, update the object's emission map, and then wait the length of time specified for one frame of animation before calling the method again with the next animation index. This means that the animation can update its direction every frame.

### Billboard Sprites

In order for this method of rendering objects to work in a 3D environment, the sprites need to be billboards. This is when the sprites are always transformed to be parallel to the screen, otherwise they could appear to be like thin sheets of paper in the world. I accomplished this with a shader that ensures sprites are always rendered facing the player.

![PistolShadow](/images/lilith/pistol.gif)

## Textures

To create an interesting spaceship environment for the player to explore, I drew many different textures for walls, floors, and ceilings. In order to bring the world to life, I included a height map, a normal map, and an emission map with each texture. This can be seen in the image below:

![Comparison](/images/lilith/comparison.png)

##### Top Left: Texture | Top Right: Height Map | Bottom Left: Normal Map | Bottom Right: Emission Map

A height map shows what levels each part of the texture should be rendered at. Black is the lowest and white is the highest. Unity will show this in-game by bringing the lighter sections on the height map forward on the original texture. A normal map shows how shadows should affect the texture. Normal maps show how the parts of the texture are angled, which means that Unity can then appropriately shadow parts of the texture depending on the light source for a more realistic environment. Finally, an emission map lets Unity know what areas are emitting light, so these areas will become light sources of their own in-game. All of these maps working together can be seen in the textures from the screenshot below:

![Textures](/images/lilith/textures.png)
