#### Other  
What worked well?  
Painpoints, what worked but was hard/painful?  
Broken stuff? Bugs?

_Nothing specific comes to my mind related to LiveOps itself. Any complications we had were the usual ongoing issues that can happen in any Evergreen game environment, not something unique to LiveOps

Event-specific code on host, what was it about? How much of it was needed?  
Event specific but host-agnostic code? Smth shared between all EG who use the event?

_Yes, if we are talking about API requests, we had both types. Some were event-specific. For example, for board-game events we had calls like roll_the_dice or mega_roll. In Battleship, there were specific calls like shoot or last_ai_shoot.

_At the same time, we also had host-agnostic calls shared across events and games, like trade collectibles, collect rewards, first open, replay, etc.

_Here is the API specification link so you can see the full list.

How was A/B and FF organised for LO events?  
_For A/B testing, we used the unified library, which is the standard solution across evergreen games.

Was it possible to _reliably_ turn-off/feature-flag a single event so that if there are issues with it the host-game can be fully protected?  
_Yes, it was possible._
  
#### Versioning  
Did you participate in updating a version of LO event?

_It depended on the type of update. If the event required significant changes, like updates to logic or specific features, then yes, we participated in updating it. But if it was more of a simple reskin or something that could be handled through configuration, then it usually didn’t require development work

Has an update to client ever broke an LO event?
_I can recall a few cases, but it wasn’t something regular. For example, once the LiveOps button was simply not included in the client build, so the event wasn’t accessible. In another case, there was a WebView error, and the LiveOps event just wasn’t shown. Overall, these were rare and usually caused by specific mistakes rather than systemic issues._

  
#### Server  
Did you need to write any code for server requests? What was it?  
Did you need to write any code for async messages? What was it?

_If you mean writing code to handle API requests, then yes, we did implement some code for that, mainly to support specific event mechanics and client-server interactions.

_For async messages, we didn’t have anything very special on our side. Most of the communication with the backend was handled through the standard request/response flow.

Analytics/Telemtry? Event-specific and LO engine-related?  
_We use Grafana, the same setup we use across Evergreen games.
  
#### Assets Management  
How did the LO asset pipeline work?  
Was it separate from the game's asset pipeline?  
Was it integrated with the client build pipeline?
Do you know if/how was it different for other EG games?  
How were assets identified? between host and LO  
Were there any conflicts?  

assets-only-reskin support? with no code changes whatsoever

run-time "pulling" of the host-game's assets into the event? e.g. currencies, icons of item, etc.

For Web LiveOps, assets were uploaded to a Google Cloud Storage bucket, so the asset pipeline was completely separate from the game’s asset pipeline and not part of the Unity client build.
This allowed assets-only reskins with no code changes. Visuals or UI assets could be updated independently of the client. So any asset could also be pulled at runtime by the event when needed.

This approach is different from Unity LiveOps, where assets are usually packaged with the client build. However, Unity can also support runtime asset updates through asset bundles.