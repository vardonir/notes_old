---
title: "UE5: Door sliding animation"
feed: show
date : 17-02-2024
tags: unreal-engine-5
---

Door goes up, door goes down, door goes up, door goes down, etc.

![](attachments/door.gif)

I'm using a loaner GPU at the moment - textures are a bit off as a result.

I'm going to start with this Level Instance

![](attachments/Pasted%20image%2020240217095921.png)

There's no door at the far side of it yet.

Since I already made it as a level instance, I'll need to break it

![](attachments/Pasted%20image%2020240217100100.png)

and use a packed level actor instead

![](attachments/Pasted%20image%2020240217100121.png)

When the assets have been turned to a packed level actor, the lights seem to stop working. I don't know why.

![](attachments/Pasted%20image%2020240217100158.png)

Open up the blueprint

![](attachments/Pasted%20image%2020240217100251.png)

And I'm just gonna add a rect light here...
![](attachments/Pasted%20image%2020240217100414.png)

I guess a nice bonus is, now the light can be controlled through the blueprint.

I'm going to do that in the construction script:

![](attachments/Pasted%20image%2020240217100645.png)

The result is that now there's a thing that can be edited to change the light intensity

![](attachments/Pasted%20image%2020240217100718.png)

![](attachments/Pasted%20image%2020240217100735.png)

Now, the door.

Drag in the static mesh for the door

![](attachments/Pasted%20image%2020240217100946.png)

and transform it accordingly in the closed position

![](attachments/Pasted%20image%2020240217101040.png)

and in the open position

![](attachments/Pasted%20image%2020240217101056.png)

Note the transform location values. In my case, the z for the open position was 280, and it's 66 for closed.

Add a box collision around the door. This will be the interaction range of the door.

![](attachments/Pasted%20image%2020240217101346.png)

Go to the event graph and add nodes for the begin and end overlap for the box collision

![](attachments/Pasted%20image%2020240217101448.png)

![](attachments/Pasted%20image%2020240217101510.png)

I want the open/close transition to be a smooth slide, so I'll add a timeline

![](attachments/Pasted%20image%2020240217101642.png)

I gave it the name "Door Transition Outline". Double-click on that

![](attachments/Pasted%20image%2020240217101721.png)

and add a float track

![](attachments/Pasted%20image%2020240217101802.png)

I made the track name to be "Door Location" and set the length to 1 - this is the amount of time that it'll take for the timeline to run.

Right click anywhere on the track to add keys

![](attachments/Pasted%20image%2020240217101856.png)

For simplicity, I just added two keys

![](attachments/Pasted%20image%2020240217101913.png)

The first key at 0 should be the z-location of the door in the closed position

![](attachments/Pasted%20image%2020240217102014.png)

and the second key should be the location in the open position

![](attachments/Pasted%20image%2020240217102036.png)

Go back to the event graph and now it'll look like this

![](attachments/Pasted%20image%2020240217102054.png)

The relative location of the door mesh needs to be updated according to the timeline. Get the door mesh into the event graph and create a Set Relative Location node

![](attachments/Pasted%20image%2020240217102211.png)

Split the struct pin of "New Location", copy-paste the x and y values, and connect the door location to the z value

![](attachments/Pasted%20image%2020240217102240.png)

For the closing animation, that'll just be setting the relative location by running the timeline in reverse:

![](attachments/Pasted%20image%2020240217102415.png)

Done.

![](attachments/door2.gif)

