---
title: "UE5: First person camera toggle"
feed: show
date : 28-10-2023
tags: unreal-engine-5
---

**UE version: 5.3.1**

Basically, the goal is to do this:

![](attachments/final%203.gif)

be able to toggle from first and third person view using only one camera (tutorials on this topic use two - one for first person, another for third person)

## First Person view

Target Arm Length of the camera boom accepts negative values.

So... move the boom. Adjust target offset to taste.

Done.

![](attachments/Pasted%20image%2020231027143041.png)


In my case, I want to be in third person only when I'm holding one specific button. This was my implementation:
![](attachments/Pasted%20image%2020231027143059.png)

Side note, this took me a while to find - I found `Set Target Arm Length` by dragging the camera boom node.
![](attachments/Pasted%20image%2020231027143128.png)

BUT!

On First Person POV, I can look straight down and it clips through the character mesh, which is not right.

![](attachments/Pasted%20image%2020231027143202.png)

Set the view pitch min/max by first getting the player camera manager

![](attachments/Pasted%20image%2020231027145203.png)

then set the view pitch here
![](attachments/Pasted%20image%2020231027145226.png)

It'll look something like this

![](attachments/Pasted%20image%2020231027145309.png)

but I found that a bit clunky what I did is to add it at the end

![](attachments/Pasted%20image%2020231027145341.png)

where
![](attachments/Pasted%20image%2020231027145353.png)

Aside:

I added a `IsFirstPerson` variable because of reasons that will be useful in a later post

![](attachments/Pasted%20image%2020231027145653.png)

But now I want to make the transition to be smooth, like in the intro

Many thanks to the Unreal Source discord channel for the help on this one: I used the `FInterpTo` node to set the length of the camera boom

![](attachments/Pasted%20image%2020231028100712.png)

### Code

This will produce a warning if the `EventTick` node is already used elsewhere

<iframe src="https://blueprintue.com/render/zmmm65qr/" scrolling="no" width="100%" height=300 allowfullscreen></iframe>

## Troubleshooting

I control the left and right, but not the up and down

Turn on `Use Pawn Control Rotation` in the camera boom

## Extra

With [crouching](ue-reference#basic-crouching):

![](attachments/Pasted%20image%2020231028131522.png)

<iframe src="https://blueprintue.com/render/_q2e-zyz/" scrolling="no" width="100%" height=300 allowfullscreen></iframe>
