# Real Time - part 3

Most games are real-time, they succeed at represent continuous movements and interactions by running at high framerates.  

In multiplayer environments framerates like 60fps are feasible locally but they are impossible or expensive (in terms of processing power) to use remotely due to the inevitable network latency and bandwidth limitations.  

All clients have to agree on crucial game events. If you are playing a shooter, your client cannot simply resolve a successful hit and send it to the server because due to the lag another client may have slightly different information and may have determined a different outcome.  
That's one of the reasons why we want the server to be *authoritative*, that is to be able to resolve the core game logic centrally.

Another reason is cheating. Html and js games can be easily modified in the browser, and you don't want clients to be able to arbitrarily change game variables.


## Client-Side Prediction

This is an example of "dumb" client. The client side just keeps track of controls state (key presses) and renders the game state.
The physics are calculated on the server side at the same time.

In this example we intentionally use a low update rate to highlight the problems with latency. 

To avoid choppy movements on the client side, we predict the objects' expected positions and interpolate them based on the time since the last server update (this would produce more accurate results than the easing of the previous examples in which the mouse "chases" the actual position)

1. Player presses the forward button
2. Client sends the "forward" input to the server
3. Client updates the game state locally, predicting where the other players would be if they kept moving at the same speed.
4. Client receives a new "authoritative" game state from the server and corrects all the positions and game states.

In a deterministic game, where "forward" always means "move n pixels per second" most of the preditions would end up being accurate.  
This concept can be applied to other game mechanics beyond movement.  
If the prediction turns out to be wrong (ie. the ship explodes or hits a wall) there may be a slight visual glitch but since the logic is on the server it won't affect the gameplay. The correct values from the server will replace the predicted ones.

Client-side prediction requires quite a bit of redundant code since the same game logic needs to exist on both client and server. You may want to implement it only when it's strictly necessary for the functioning of the game.

Also note that in order to save bandwidth, different parts of the game states can be updated at different rates. Movements may need to be fast but the position of the sun in a day-and-night cycle can be sent more infrequently.

The tutorial below also covers server reconciliation which address an issue that emerges with client prediction.  

[Client-Server Game Architecture - advanced tutorial](https://www.gabrielgambetta.com/client-server-game-architecture.html)


## Exercise

Extend this logic and create a multiplayer shooting game like SpaceWar!
* add a state object similar to "players" that keeps track of the bullets
* add a way to visually differentiate players or tell your ship apart
* add a simple collision detection based on distance (circle collider)
* add a game over / respawn condition