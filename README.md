![WatchDuck](https://github.com/user-attachments/assets/17b54b08-6cc3-4f62-bdea-91d6c97d9757)

# WatchDuck - Your feathered eye in the sky for game integrity.
*If it looks like a hack, the duck goes quack.*

## How does WatchDuck work?
WatchDuck is a programmable, server-side anti-cheat service designed to help you maintain fair play in your game. It operates by intercepting and monitoring game traffic.

WatchDuck can run in one of two distinct modes, offering different monitoring capabilities:

1.  **Running as Part of the Relay Peer (Omega's Backend):**
    In this mode, WatchDuck is present and runs directly within Omega's backend, specifically on the **Relay Peer**. The Relay Peer is the primary server component that players connect to when joining a lobby with the "Enable Relay" feature activated.
    * **Monitoring Capability:** When running here, WatchDuck automatically intercepts and monitors **all broadcast traffic** that passes through this Relay Peer.
    * **Limitation:** Data sent directly between two players via a pure peer-to-peer connection that bypasses the Relay Peer **cannot be monitored** by WatchDuck in this mode.

2.  **Running as a Game Relay (Independent Server):**
    Alternatively, you can run WatchDuck as a **Game Relay**. This is a type of independent server that you deploy yourself, allowing you to incorporate your own custom, game-specific logic. This Game Relay connects to the main signaling server and appears to players as another peer (like a server-controlled player) within the game.
    * **Monitoring Capability:** When operating as a Game Relay, WatchDuck monitors **broadcasts** (messages sent through the main Relay Peer that this Game Relay receives) and **transferred messages**. Transferred messages are a specific pattern where one player sends a message directly *to the Game Relay player*, and the Game Relay processes it and potentially forwards it to another recipient. This allows for specific message types to be explicitly routed through a WatchDuck-enabled peer for inspection.
    * **Limitation:** Similar to the Relay Peer mode, pure direct peer-to-peer messages that are not sent as broadcasts via the main Relay Peer or explicitly transferred via the Game Relay **cannot be monitored** by WatchDuck in this mode.

**Choosing a Mode:** The mode you choose depends on your game's architecture and monitoring needs. Running on the central Relay Peer offers broad coverage of all broadcast communication. Running as a Game Relay allows you to monitor broadcast traffic *from that relay's perspective* and also inspect messages explicitly routed to the Game Relay peer for processing.

You configure WatchDuck's monitoring rules, algorithms, and potentially its deployment mode by providing a **JSON-formatted string** during the setup of your lobbies or Game Relay instances.

## Integration with Rollback Netcode

WatchDuck is well-suited for games using rollback netcode. Since rollback relies on clients processing received inputs/data frame-by-frame, WatchDuck automatically performs checks and analysis on each frame chunk. By configuring specific rules, you can handle more complicated cheating scenarios.

## Built-in Anti-Cheat Algorithms

WatchDuck offers different monitoring intensity levels via pre-configured algorithms. Their names follow our feathered theme:

* **Lazy:** Performs basic validation checks. This algorithm primarily monitors key game values (like player score, health points, damage dealt, resource counts, etc.) as they appear in messages. It checks if their **rate of change** or their **values themselves** fall outside statistically expected or physically possible bounds based on standard gameplay. It looks for glaring inconsistencies or impossible gains/losses.
* **Watchful:** A more vigilant approach. Watchful combines the rate and bounds checks from the Lazy algorithm with validation based on **specific message tags**. Developers can tag critical messages (e.g., messages reporting a hit, a movement action, an ability use). Watchful performs more detailed plausibility checks on tagged messages and employs **message signing and cross-checking** across recent state updates to detect tampering or injection of fake events.
* **Ravenous:** The most aggressive built-in algorithm. Ravenous inspects and checks signatures on **each tagged message** present in the monitored player stream through the Relay Peer for a given lobby. It applies stringent validation rules to catch subtle manipulations or impossible sequences of actions conveyed through tagged messages. This mode involves higher processing overhead but offers greater scrutiny.

## Custom Algorithm

For game-specific or highly-tuned anti-cheat logic, WatchDuck allows you to define a custom validation algorithm.
_(TODO.)_

## What Happens on Detection?

When WatchDuck detects suspicious activity based on the chosen algorithm, your custom logic, or repeated offenses, the duck quacks!

Instead of automatically kicking or banning players (the ultimate decision is yours), WatchDuck will **emit different kinds of events** that can be handled by your game server or other backend systems in any way you see fit. These events categorize the detected anomaly by severity:

* **Duckling:** Represents **low-severity** warnings or minor suspicious deviations. For example, detecting a player acting *slightly* faster than expected on occasion, or marginal inconsistencies that don't immediately break game rules but are worth logging.
* **Fledgling:** Indicates **medium-severity** suspicious activity. This might be triggered by attempts to perform actions that are plausibly but *highly unlikely* (e.g., collecting an item that was likely already gone), or repeated instances of Duckling-level behavior from a player within a short timeframe.
* **Duck:** Signifies **high-severity** warnings pointing to probable cheating or significant rule violations. Examples include impossible movement speeds, successfully registering an action that was impossible according to server validation (indicating potential client-side state manipulation), or accumulating multiple Fledgling-level offenses. These often warrant immediate attention and may require action like kicking the player.
* **Sage:** The highest severity level, typically indicating a pattern of high-severity offenses or a clear, undeniable cheat signature detected over time. A Sage event strongly recommends **mandatory action**, such as kicking the player from the game or triggering a ban process on your game server.

Each emitted event will contain details about the detection, including the player, the algorithm/rule triggered, the specific data that caused the flag, and the timestamp, allowing your backend to log the incident and apply appropriate consequences.

## Getting Started

*(TODO: Add information on how to configure WatchDuck and integrate it with the default relay peer, as well as installing and configuring a game peer.)*
