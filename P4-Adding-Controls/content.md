---
title: Adding player controls
slug: adding-controls
---

Thus far the bunny drops to the ground quickly, and there is nothing the player can do about it. Keeping the game like this would make for a pretty poor experience. In this section we are going to add player touch controls for the bunny and tweak the physics engine to ensure the physics are balanced and fun.

#Code Connections

Before we're able to control the bunny, we first need to make a connection between the SpriteKit Scene object and our code.

If you recall, we changed the name of the bunny sprite to `hero` in *Hero.sks*, this gives us an easy way to find this node in the *GameScene* scene graph.

> [action]
> Open *GameScene.swift*. In order to control our bunny, you need to set up the code connection.  
> Add the hero property to the top of the *GameScene* class.
>
```
class GameScene: SKScene {
>
  var hero: SKSpriteNode!
```
>

We now have a property to use for connecting to our bunny object. However, this alone will not do anything. First we need to add some code to find the bunny inside the scene graph and assign it to our newly added *hero* property.

> [action]
> Add the following code to the `didMoveToView(...)` method:
>
```
override func didMoveToView(view: SKView) {
  /* Set up your scene here */
>
  /* Recursive node search for 'hero' (child of referenced node) */
  hero = self.childNodeWithName("//hero") as! SKSpriteNode
```
>

<!--  -->

> [info]
> In this case we need to perform a recursive search as the hero node is not directly in the *GameScene*, even though you can see it in the scene editor. In the *GameScene* we added a `Reference node` which holds the bunny (it could easily be pointed to another *SpriteKit Scene*, however, if we had one). By making use of the `//` search operator, we ensure the search will check all  nodes recursively in our scene.
>

#Adding touch

The goal is to have the bunny hop every time we touch the screen, which will keep the bunny flying high.

> [action]
> Open *GameScene.swift*, you will notice there is already a `touchesBegan(...)` method ready and waiting for our code.
> Replace the declaration:
>
```
override func touchesBegan(touches: Set<UITouch>, withEvent event: UIEvent?) {
  /* Called when a touch begins */
>
  /* Apply vertical impulse */
  hero.physicsBody?.applyImpulse(CGVectorMake(0, 250))
```
> We are applying an impulse to the *hero's* `physicsBody`. Think of an impulse as like being hit by a baseball bat.
> In this case a short vertical burst to make the bunny move vertically.

<!--  -->

> [info]
> You'll notice the keyword `override` appears before `func` in the declaration of this method. An important concept in object-oriented programming is the idea of inheritance - that is, a child class inherits methods and properties from its parent class.
> In this case, our `GameScene` inherits from `SKScene`. We must use the `override` keyword to indicate that our child class will override `SKScene`'s existing implementation of `touchesBegan(...)`

Try to run the game and test out the new touch control!

#Adding a speed limit

It works, but it doesn't quite feel right. You may have noticed while testing that when you touch the screen repeatedly, the impulses accumulate and the bunny blasts off of the screen. It can be gone for seconds or even (seemingly) forever.  In order to make the game playable, we will want to limit the upward vertical velocity. The best way to limit the bunny's speed is by modifying it in the *update* method, which is automatically called every frame.

> [action]
> Modify the `update(...)` method as shown:
>
```
override func update(currentTime: CFTimeInterval) {
  /* Called before each frame is rendered */
>
  /* Grab current velocity */
  let velocityY = hero.physicsBody?.velocity.dy ?? 0
>
  /* Check and cap vertical velocity */
  if velocityY > 400 {
    hero.physicsBody?.velocity.dy = 400
  }
```
>

We've added a simple yet effective modification:

- Grab the y velocity value of our bunny.
- Check this value and if necessary limit it to 400.

There is no need to limit the falling speed or modify the x velocity as the bunny should never move horizontally.

#Adding SFX

If a bunny flaps its ears in the forest and no one is around to hear it, does it make a sound?

In any case, it would be nice to add some sound effects to accompany the flapping animation.

Adding sound effects is quite straight-forward in SpriteKit. You can make use of the `playSoundFileNamed` *SKAction* to play sounds in code.

> [action]
> Add the following to the `touchesBegan(...)` method:
>
```
/* Play SFX */
let flapSFX = SKAction.playSoundFileNamed("sfx_flap", waitForCompletion: false)
self.runAction(flapSFX)
```
>

Run the game: let there be sound!

#Make the bunny rotate

One of the nice visual touches in Flappy Bird is the way the bird rotates. If the player does not touch the screen for a little while, the bird turns towards the ground. Touching the screen makes the bird turn upwards again. You are going to imitate this behavior in Hoppy Bunny!

There are a couple of things you will need to do to achieve this:

- On touch, turn the bunny upwards
- If no touch occurred for a while, turn the bunny downwards
- Limit the rotation between slightly up and 90 degrees down (just like in Flappy Bird)

> [action]
> The first step is to add a property to keep track of the time since the last touch. Add this declaration just after our hero property declaration.
>
```
var sinceTouch : CFTimeInterval = 0
```
>
> Next add this code to the `touchBegan(...)` method, after the application of the vertical impulse `applyImpulse(...)`
>
```
/* Apply subtle rotation */
hero.physicsBody?.applyAngularImpulse(1)
>
/* Reset touch timer */
sinceTouch = 0
```

Applying angular impulses with no limitation will put the bunny into a wild head spin. To see this, go ahead and try the game now. Those of a sensitive disposition: please don't. Think of the bunny!

To fix this, we'll need to limit the rotation of the bunny and also perform a downward rotation if no touch has occurred in a while. We can perform both in the update method.

> [info]
> A great way to restrict values to a range is to use *clamp* functionality.  However, as it currently stands there is no handy clamp function available. Thankfully we've provided a handy file of helper functions you can add to the project.

<!--  -->

> [action]
> Right-click and [Download CGFloat+Extensions.swift](https://github.com/MakeSchool-Tutorials/Hoppy-Bunny-SpriteKit-Swift/raw/master/CGFloat%2BExtensions.swift) using "save link as" or the equivalent in your browser. Drag this file into your project.
> Ensure *Copy items if needed* is checked.
>
> ![Add file](../Tutorial-Images/xcode_add_file.png)
>
> Feel free to explore this new code to see how it works.
>

Next we can apply the `clamp(...)` function to limit the rotation of the bunny, limit the angular velocity and increment the new `sinceTouch` timer.

> [action]
> Add this code at end of the `update(...)` method:
>
```
/* Apply falling rotation */
if sinceTouch > 0.1 {
    let impulse = -20000 * fixedDelta
    hero.physicsBody?.applyAngularImpulse(CGFloat(impulse))
}
>
/* Clamp rotation */
hero.zRotation.clamp(CGFloat(-20).degreesToRadians(),CGFloat(30).degreesToRadians())
hero.physicsBody?.angularVelocity.clamp(-2, 2)
>
/* Update last touch timer */
sinceTouch+=fixedDelta
```
>

First thing you will notice are the red errors: we need to define the value for `fixedDelta`. What is *delta*? Delta is typically the name given to the time taken between rendering frames. The target Frames Per Second (FPS) is 60, which makes everything feel silky smooth, so an optimal fixed delta time would be `1 second / 60 frames = 0.01666666666`. For simplicity we will be using this value. However, in practice for more complex scenes we would calculate a more accurate delta.

> [action]
> Add the following code after the declaration of the `sinceTouch` property.
>
```
let fixedDelta: CFTimeInterval = 1.0/60.0 /* 60 FPS */
```
>

There are a couple things going on here:

- We check if more than a tenth of a second has passed since the last touch. If so apply an angular rotation impulse to tip the bunny over.
- We apply a clamp to the *Z-Rotation* to ensure the bunny stays within the range we want.
- We apply a clamp to the *angularVelocity* to ensure the value does not get out of control.
- We add the `fixedDelta` (change in time) to the *sinceTouch* value to capture how much time has passed since the last touch event.

> [info]
> If you want to experiment with clamp values, a handy way is to use the Scene Editor to quickly visualize different values and then apply them in code. Click on *hero* and have a play with the *Z-Rotation* value, just remember to set it back to `0` when you're finished playing.

Now try running your game! The behavior should hopefully be similar to this:

![Bunny rotating](../Tutorial-Images/simulator_bunnyRotation.gif)

#Summary

We've made some real progress in this chapter and learned:

- How to use `childNodeWithName()` to get references to **GameScene** objects in the Swift game code
- How to add touch controls using the `touchesBegan()` method, and how to apply physics forces
- How to clamp values to a range
- About *delta* and making time counters using the `update()` method
- Play sound using *SKAction*

Hopping up and down is fun, but it would be even better if there was a sense of movement. In the next chapter we are going to get things moving.
