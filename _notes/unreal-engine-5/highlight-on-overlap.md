---
title: "UE5: Highlight on overlap"
feed: show
date : 28-10-2023
tags: unreal-engine-5
---

**UE version: 5.3.1**

The goal here is to create this effect:

![](attachments/final%202.gif)

when your character is near a specific object (i.e., their collisions overlap), the object gets a highlight color.

For this, the Custom Stencil Buffer will be used. This post is adapted from [this tutorial from the official UE youtube channel](https://youtu.be/PiQ_JLJKi0M?si=E_IfIMWh3laC1LQt), and checked with UE 5.3.

## The set-up

In the project settings, change `Custom Depth-Stencil Pass` to `Enabled with Stencil`

![](attachments/Pasted%20image%2020231027092420.png)

I created my "interact-able" cube object as a blueprint with a static mesh as the root and a box collision. 

![](attachments/Pasted%20image%2020231027092345.png)

Search for "depth" in the static mesh's details,

![](attachments/Pasted%20image%2020231027092555.png)

set `Render CustomDepth Pass` to true, set `CustomDepth Stencil Value` to 1. This could really be any value besides 0, but this value will come up again later.

## Visualization

Now I added the target object cube BP to the level

![](attachments/Pasted%20image%2020231027092706.png)

To see how the stencil value makes that cube  "special," set the view to Buffer Visualization > Custom Stencil

![](attachments/Pasted%20image%2020231027092720.png)

And the static mesh get highlighted. It has a stencil value of 1, everything else has `Render CustomDepth Pass` set to false. 

![](attachments/Pasted%20image%2020231027092736.png)

I create a material called `PPM_InteractOverlay` and set the `Blend Mode` to `Post Process`

![](attachments/Pasted%20image%2020231027093000.png)

Create a `Scene Texture` node and set the `Scene Texture Id` of them to `Post Process Input 0`, and another one to `Custom Stencil`

![](attachments/Pasted%20image%2020231027093145.png)

To see how the PPM affects the entire level, we can add a post process volume to the level, set the extent to unbound, then add the material as a post process material

![](attachments/Pasted%20image%2020231027093507.png)

![](attachments/Pasted%20image%2020231027093456.png)

In order to get the same effect as in the video, add a desaturate node

![](attachments/Pasted%20image%2020231027093653.png)

And then add a Linear Interpolate node - 

![](attachments/Pasted%20image%2020231027093633.png)

## On Collision

The effect should be shown only when there's something colliding with the object. So, get the event `OnComponentBeginOverlap` from the collision in the BP

![](attachments/Pasted%20image%2020231027094930.png)

It is known that the object is highlighted only when Custom Render Depth Pass is set to True (or when the stencil value is set to a nonzero value)

![](attachments/Pasted%20image%2020231027095048.png)

![](attachments/Pasted%20image%2020231027095110.png)

So change that value when there's another actor overlapping the object.

![](attachments/Pasted%20image%2020231027095540.png)

(Or, instead of `Set Render Custom Depth`, the `Set Custom Depth Stencil Value` node can also be used, which is what I ended up using eventually. See below for why)

![](attachments/final%201.gif)

BUT WAIT.

Since the highlight turns on when checking for overlapping actors, this can happen

![](attachments/but_wait%201.gif)

Two objects can highlight each other!

I added a Blueprint Interface to the player BP and added a check to the overlapping actor if it implements that BP interface

![](attachments/Pasted%20image%2020231027100933.png)

And now, only I may highlight the cubes

![](attachments/only_me.gif)

## But colors.

Flip the input of the linear interpolate node to get this

![](attachments/Pasted%20image%2020231027102018.png)

Then just replace B with some color

![](attachments/Pasted%20image%2020231027102117.png)

Or some color with an alpha

![](attachments/Pasted%20image%2020231027102351.png)

We can also use a parameter so that we can create a material instance

Aside: I added an "if custom stencil value" node so that if there are other meshes in the level with other custom stencil values, the highlight will only be applied to meshes with that particular stencil value

![](attachments/Pasted%20image%2020231027135236.png)

Why? In this example, if the stencil value of the big cube is set to 2, the effect is still applied to it. 

![](attachments/Pasted%20image%2020231027114935.png)

Limiting the effect to specific stencil values will prevent this from happening.

Bonus: You can also use textures

![](attachments/Pasted%20image%2020231027114128.png)

Here's how I set it up

![](attachments/Pasted%20image%2020231027114149.png)

## Overlaps

This can happen too:

![](attachments/Pasted%20image%2020231027114229.png)

This is because the custom render depth pass of the character mesh is set to false. The fix is to set that to true. Basically, anything highlighted will be "see through" to any meshes with custom render depth pass set to false. 

Since that value needs to be true regardless, I changed the highlight command from setting that to true/false and instead changed the stencil value from 0 to 1 instead.

![](attachments/Pasted%20image%2020231028113923.png)

### Code

#### PPM_InteractiveOverlay

<iframe src="https://blueprintue.com/render/sm6xf--x/" scrolling="no"  width="100%" height=300 allowfullscreen></iframe>
#### BP_TargetCube Event Graph

<iframe src="https://blueprintue.com/render/ihky-egn/" scrolling="no" width="100%" height=300 allowfullscreen></iframe>
