![WatchDuck](https://github.com/user-attachments/assets/17b54b08-6cc3-4f62-bdea-91d6c97d9757)

# WatchDuck - Your feathered eye in the sky
*If it looks like a hack, the duck goes quack.*

WatchDuck is a programmable server-side anticheat that can be configured to sit between your players (over P2P) and your game server (C2S).

# Basics
WatchDuck leverages the Relay peer on the server. If WatchDuck is installed on a signaling server, and once a lobby relay is initialized, WatchDuck will begin to monitor all broadcast messages, which is recommended since the relay peer is intended
to relieve peers of network and CPU strain.

WatchDuck won't be able to monitor direct peer messages (anything that doesn't require immediate verification, like simple in-game chat or events) unless you specify otherwise. 

By providing a JSON-formatted configuration string when loading a lobby, you can define specific rules for tagged messages.

# Integration with rollback
When a client implements rollback, WatchDuck can perform basic checks on each frame of the rollback net data. 

There are several basic algorithms present to verify the authenticity of the data.

* Lazy - Checks if a value (like a player's points value, HP, damage, etc.) behaves linearly within a degree of error.
* Watchful - Performs semi-aggressive message validation using the Lazy protocol alongside specific tags in messages. Also, WatchDuck signs certain tagged messages and cross-checks them across states.
* Ravenous - Aggressively tags and checks signatures of each message present in the player stream.

WatchDuck can also use a custom Algorithm function defined in (TODO), given a slice of the most recent rollback frame capture.
