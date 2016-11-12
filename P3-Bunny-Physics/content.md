---
title: You've got to let the bunny fly!
slug: bunny-physics
---

Time to set up physics for the game world and the objects within it!

#SpriteKit Physics

We will be using the powerful physics engine built into SpriteKit -- thankfully it's as easy as ticking a box to enable physics for each of our game objects.
I recommend you take some time to read [Apple's Documentation on Physics in SpriteKit](https://developer.apple.com/library/ios/documentation/GraphicsAnimation/Conceptual/SpriteKit_PG/Physics/Physics.html#//apple_ref/doc/uid/TP40013043-CH6-SW1). Physics plays a key role in many games, even those that might not seem to use physics may use the physics engine for collision detection!

##Make the ground static

> [action]
> Select the ground node, ensure the *Attributes inspector* is open and scroll down until you see the *Physics Definition* option.
> Set the *Body Type* to `Bounding rectangle`, which will present you with the additional physics properties.
> Make the body `static` by unchecking `dynamic`. There is no need for the ground to rotate, so uncheck `Allows Rotation`. There is also no need for the ground to be affected by gravity, so deselect `Affected By Gravity`.
>
> ![Create the static ground physics body](../Tutorial-Images/xcode_ground_physics.png)
>

##Enable bunny physics

> [action]
> Open *Hero.sks* and select the Bunny sprite. Find the physics definition section and Set *Body Type* to `Bounding circle`.
> You should notice a faint circle around the bunny to show the physics body.
> Make sure the following boxes are checked: *Dynamic*, *Allow Rotation* and *Affected By Gravity*. (By default they should be checked)
> Set *Friction* to `0`
>
> ![Enabling bunny physics](../Tutorial-Images/xcode_hero_physics.png)
>

<!--  -->

> [info]
> You might wonder why we used a circle for our bunny's physics body. When it comes to physics, less is always more. Simulating physics can be intensive on mobile devices and you want to simplify life as much as possible for the physics engine.
> 
> Circles provide the best performance. If you can get away with using a circle, then use it! The trick is to use the most efficient shape for the job at hand.
>

##Adding the bunny to the world

> [action]
> To add our bunny to the game, drag the *Hero.sks* file into the Game Scene. This will automatically create a *Reference node* pointing to the *Hero.sks*
>
> ![Enabling bunny physics](../Tutorial-Images/xcode_add_reference_node_hero.png)
>
> Set the position to `(-80, 0)`.

<!--  -->

> [info]
> Often *Reference node* are not displayed correctly when initially placed into a scene, a quick *Save* of the scene should rectify this.

##Gravity

If you click outside of the *GameScene* yellow box and check the *Attributes inspector* you will see our physics world will default to approximately Earth's gravity, `-9.8`.
![GameScene Gravity](../Tutorial-Images/xcode_gamescene_gravity.png)

> [info]
> Also notice the *Debug Drawing* options in the inspector, the *Show Physics Boundaries* option is handy to check that your physics bodies are where you think they should be. This will draw faint blue outlines around your game objects, showing their size and shape in the physics engine.

##Adding the crystals

> [action]
> Let's add some pretty crystals above the ground to complete the visual appeal of *Hoppy Bunny* by dragging `bg_crystals.png` into the scene:
>
> ![Adding crystals](../Tutorial-Images/xcode_add_crystals.png)
>
> Snap it to the top of *ground*.


#Check your progress

Let's check that the gravity is working correctly. SpriteKit Scene editor allows you to check this without having to run the game. Select `Animate` in *GameScene.sks* as you did before in *Hero.sks*. You should see our animated hero succumb to gravity and fall to the ground. If it doesn't, please go back and double check your work so far!

Click `Layout` to return to editing. 

> [action]
> Next let's run the project to see it in action! But before you hit that `Run`, you will need to clear out the default project template code.
> Open *GameScene.swift* and replace with the following:
>
```
import SpriteKit
>
class GameScene: SKScene {
    override func didMove(to view: SKView) {
        /* Set up your scene here */
    }
>   
    override func touchesBegan(_ touches: Set<UITouch>, with event: UIEvent?) {
        /* Called when a touch begins */
    }
>    
    override func update(_ currentTime: CFTimeInterval) {
        /* Called before each frame is rendered */
    }
}
```
>

##Action
Now you can press `Run` to see your game in action.

![Run Project](../Tutorial-Images/xcode_select_simulator.png)

> [info]
> I tend to run with the iPhone 6 simulator as it's a little faster than the default 6s. You can also try using `cmd+2` or `cmd+3` to reduce the scale to improve simulator performance.
> If you are set up to test on device then please do so, as this will offer the best performance and will accurately reflect what your players will see. Physics based games are best ran on device.

#Z Position

Argghh, it looks weird, the bunny is behind the crystals!

![Z Position issues](../Tutorial-Images/simulator_zorder_before.png)

In SpriteKit there is no implied object rendering order, so all objects are rendered by default at a *Z-Position* of `0`. So if your bunny is at the same position as the crystals, you will have unpredictable rendering results.

*Wait, so what is Z Position?*

Z-Position or Z-Order defines the render ordering for overlapping 2d objects. Imagine the objects on the screen as post-it notes. Some have to be in front of others. 

![Z Position Example](../Tutorial-Images/zorder.png)

Rectangle B is drawn after rectangle A. The result is rectB is drawn “above” rectA. RectB is said to have higher *Z-Position* than rectA.

You can easily set the *Z-Position* of your sprites in the *Attributes inspector*:

![Modify Z Position](../Tutorial-Images/xcode_zorder_modify.png)

> [action]
> It's best to keep it logical, so imagine you are painting a scene, and work from back to front. Set the **background** to `-1`. Let's ensure the crystals and ground are in front of the background by setting them to `0`. Let's put our bunny on top of everything a Z-Position value of `2` (make sure to set the Z-Position of the bunny in Hero.sks, not in GameScene.sks!).

<!-- html comment to break boxes -->

> [action]
> Run the project again and it should look perfect now.

![Z Position Fixed](../Tutorial-Images/xcode_zorder_fixed.png)

#Summary

The game is coming along nicely now, so what did we learn?

- Adding basic physics to the game
- How to use a reference node
- Using **Z-Position** to layer your sprites

In the next chapter, we will be adding player controls to make the bunny flap!
