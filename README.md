# Negative Latency

## Origins

The term 'Negative Latency' picked up a little bit recently with the release of Google Stadia. With Stadia, the game client is really light-weight, and just needs to send controller inputs and translate video/audio feeds back from the server -- the server is the one doing all the heavy lifting. The problem with this scenario for the vast majority of gamers I'm immersed in is the problem of input latency.

Input latency is the time it takes from a button press to seeing the results of the input show on-screen. For a typical gaming setup you would expect somewhere in the neighborhood of 40ms of input latency from your mouse through the computer's bus, the operating system, the drivers, the game engine, the graphics drivers, the video card and the monitor. What frustrates this pipeline further with Stadia is the fact that there is additional processing required to convert a video to a network-sendable stream as well as there typically being a lot of physical network nodes between you and the game server - the more jumps there is, the more latency will accrue as a result.

To this end - Google developers for Stadia have coined the term 'Negative Latency' to describe a couple of in-house technologies they're experimenting with to try and mitigate the feeling of heavy-listless controller inputs. Basically they are trying to simulate the responses to your inputs locally on the client before the server even knows anything has occurred, it's got some complications to work through but I don't doubt if anyone can do it, Google can.

In this post I don't want to dwell as much on Google's advancements in tightening up their latency problem as much as what the term 'Negative Latency' has sparked in my imagination.

## It's Already In Your Games!

I'm gonna go ahead and broaden the definition of Negative Latency beyond what Google describes and have some fun with it. In my definition, 'Negative Latency' is any game mechanic where-in the game responds faster to your inputs than would have been realistic in a true simulation. Let's go through some genres:

* Fighting

> The moment you press a light-attack button your characters fist/foot is jutted out forward *as if* the character had time to wind up and push out their limb in advance of you pushing the button.

* Real-time Strategy

> The moment you place a building down, the frame is already placed down *as if* the frame was already in-development long before you desired it's placement.

* Real-time Tactics

> The moment you order new units into the battlefield, they appear at the bottom of the screen *as if* your general had anticipated their necessity well in advance of your command to send them in.

* First Person Shooter

> The moment you hit respawn, you are placed within seconds of a combat encounter with the enemy *as if* your character was marching towards the battlefield long before you took control of him.

## Snappy Actions

So my initial observation when noting that we already have some semblance of negative latency is: *Instant responses to input resonate well with digital buttons.*

Consider a subset of games that don't have instant response - VR titles. In a VR fighting game, the player himself performs wind-ups and followthroughs before making contact with the opponent, since that's what the analog nature of motion controllers asks of the player. Having an instant punch come out due to motion controls would be strange and out-of-place.

In contrast, the shiny colored buttons of a fightstick with satisfying tactile clicks and a clear distinction between inactive and activated states almost demands a parallel within the game - where the action of the player slamming their finger on a button perfectly mirrors the in-game character making contact with their opponent.

Nintendo has refined this technique of complementing the user's inputs so wel that it also extends to the user interfaces. Within their game menus every selection made has a instant chime, only then followed by satisfying yet subtle menu transitions. This creates an experience that almost sings a harmony to the player's own audible button presses. You know Nintendo is self-aware of this part of their design when snapping fingers is the catch phrase for the Switch.

## Can We Generalize?

I'm a programmer by trade, so my first response after coming to this conclusion is to try and figure out some sort of universal generalization. To this end I need to go back to my initial definition - *'Negative Latency' is any game mechanic where-in the game responds **faster** to your inputs than would have been realistic in a **true simulation***.

>Let's imagine a simple fighting game and figure out if we can come up with a rule-of-thumb.
* Start with a **true simulation**
    > * When the user hits the punch button, the character has to plant his feet, wind up his fist and push it forward before he is able to damage the opponent
    > * Time taken during this process might be ~1 second, or 60 frames

Ok, well this game's bad, if you know anything about the infamous Genesis game Shaq Fu, you know that adding build-up frames and delay before actions where in other games there was none feels backwards and terrible. Shaq Fu was nuts enough to try adding buildup animations to *jumping animations* no less. We need to keep moving forward.

* Keep a running history of the game state
    > * Every frame of character movement is recorded in-full: Current animation, animation progress, position, velocity
    > * Any previous frame can be perfectly restored and resimulated without any change to final outcome

Another strange in-between step that needs to be made --- Imagine what it takes for a drawing program to restore old changes by pressing ctrl+z [undo], the program has to essentially keep track of the state of the application before the user clicked the mouse versus afterwards. This allows us to do something akin to sorcery:

* Send button presses *into the past*
    > * When the player presses the punch button, restore the state of the game 60 frames in the past **except** the punch input is activated
    > * This would not necessarily trigger the punch animation instantly if another animation is already being played and could simply enqueue a 'punch' action into the action buffer to be played later
* Resimulate the game forward until it reaches the current frame again
    > * Flash-forward to current frame where the punch animation has already gone through its wind-up phase and the fist is firmly in the opponents face

Alright, so we're back where we started essentially, but it's generic - The frame where the player hits the punch is the frame in which the character contacts the opponent. It kind of begs the question of that the point of this extra computing was if we could have accomplished the same thing by just removing the wind-up animation for the punch to begin with.

## Benefits

Beyond just being an interesting mental exercise, I do see a couple of interesting side effects from using a generic solution for Negative Latency.

Again specific to fighting games, if you've watched enough Street Fighter you'll note how strange light attacks look in these titles: Characters conjure force out of nowhere to create an instant attack and have a longer than expected cool-down to balance the attack - reduce repeatedly spamming the move. Conversely you'll have flashy looking special attacks that take a complex user input and display a series of well animated, satisfying build-ups and impacts in each strike. It's all just a canned inescapable animation created by an artist that happens automatically the moment the first strike successfully [and usually **instantly**] lands.

In this *true simulated Negative Latency* fighting game, a player spamming a light attack would look decidedly different - the first attack would still be jilted and snappy to respond to the button instantly, but the cool-down could be legitimately shorter, since the next punch will simply be buffered for when the cool-down finishes and will probably play through its full build-up phase before being able to land the next hit. Essentially beyond the first strike, the character's limbs will have smooth motions and never snap unnaturally.

The end result is a smoother *combination* of attacks beyond the first strike - the player can confirm a light punch into a couple short kicks, jump-punch and heavy kick to knock the opponent down, and it can all look *as smooth* as an artist produced super move, while still being player controlled. Every kick and punch - even jump animation - could all have physically accurate and weighty animations when actions are buffered, while still being snappy and instant when used from a neutral state. The best of both worlds!

## Complications

It will still take a fair amount of finessing to get this system to look right and not have some strange looking side-effects. One simple example is if you coded the left-right movement to resimulate into the past. Since you can essentially cancel moving in one direction to the other instantly, it would be pretty common to see the character popping from left to right as the left movements of the past are erased and replaced with right movements in the new timeline.

Ideally each animation state should have its own max allowable resimulation time that would probably scale with the duration of the animation, where moving left and right gets almost not resimulation time, and heavy attacks can get tonnes more. We should never simulate *beyond* when the character contacts his opponent, and should probably also quit early if his position changes too dramatically. This still requires some artistic finagling.

I've also only imagined this solution for a fighting game example that seems realistic and attainable. I haven't really explored how this concept could expand out to RTS games or FPS games. Maybe it would work similarily, or we'd need extra rules to make the game work with more than 2 players, maybe it doesn't translate at all!

## Conclusion

It may be viable for certain games to introduce some sort of generified Negative Latency algorithm, but stochastic simulations, difficult to quantify game states, and complex multiplayer could make it totally untenable. Regardless, trying to isolate the phenomenon of Negative Latency and quantify how it works can help us design snappier more satisfying games in the future.

## Inspiration / Sources

"Google Stadia shooting for negative latency by predicting players’ moves" - https://www.digitaltrends.com/gaming/google-stadia-negative-latency/

Runahead Method in RetroArch - https://www.libretro.com/index.php/retroarch-1-7-2%E2%80%8A-%E2%80%8Aachieving-better-latency-than-original-hardware-through-new-runahead-method/
