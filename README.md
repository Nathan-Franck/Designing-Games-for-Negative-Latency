# Negative Latency

## Origins

The term 'Negative Latency' picked up a little bit recently with the release of Google Stadia. With Stadia, the game client is really light-weight, and just needs to send controller inputs and translate video/audio feeds back from the server -- the server is the one doing all the heavy lifting. The problem with this scenario for the vast majority of gamers I'm immersed in is the problem of input latency.

Input latency is the time it takes from a button press to seeing the results of the input show on-screen. For a typical gaming setup you would expect somewhere in the neighborhood of 40ms of input latency from your mouse through the computer's bus, the operating system, the drivers, the game engine, the graphics drivers, the video card and the monitor. What frustrates this pipeline further with Stadia is the fact that there is additional processing required to convert a video to a network-sendable stream as well as there typically being a lot of physical network nodes between you and the game server - the more jumps there is, the more latency will accrue as a result.

To this end - Google developers for Stadia have coined the term 'Negative Latency' to describe a couple of in-house technologies they're experimenting with to try and mitigate the feeling of heavy-listless controller inputs. Basically they are trying to simulate the responses to your inputs locally on the client before the server even knows anything has occurred, it's got some complications to work through but I don't doubt if anyone can do it, Google can.

In this post I don't want to dwell as much on Google's advancements in tightening up their latency problem as much as what the term 'Negative Latency' has sparked in my imagination.

## It's Already In Your Games!

I'm gonna go ahead and broaden the definition of Negative Latency beyond what Google describes and have some fun with it. In my definition, negative latency is any game mechanic where-in the game responds faster to your inputs than would have been realistic in a true simulation. Let's go through some genres:

### Fighting

The moment you press a light-attack button your characters fist/foot is jutted out forward *as if* the character had time to wind up and push out their limb in advance of you pushing the button.

### Real-time Strategy

The moment you place a building down, the frame is already placed down *as if* the frame was already in-development long before you desired it's placement.

### Real-time Tactics

The moment you order new units into the battlefield, they appear at the bottom of the screen *as if* your general had anticipated their necessity well in advance of your command to send them in.

