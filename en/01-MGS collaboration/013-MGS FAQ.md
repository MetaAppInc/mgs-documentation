# MGS docking normal problem sorting

## Dear developer:

This document simply sorts out the common problems in multi channel games. We will update this document from time to time based on the developer's feedback.

### Ⅰ. Basic questions

###### 1. For games integrated with MGS, the game needs to have server capabilities and personnel, because some interfaces only provide game server calls;

###### 2. When the game is fine-tuned, it is necessary to confirm whether the game package is modified according to the game package obtained by both parties;

###### 3. It is necessary to determine whether all the gameplays are connected to the voice room function;

- For games with multiple modes and ways of playing, it is necessary to clearly indicate which modes and ways of playing need to be connected to the MGS voice room. Normal condition: All multiplayer games need to be connected to the voice room;


###### 4. Content provider needs to conduct an overall self-test on the basic game functions, whether the relevant gameplay and functions can work normally;

### Ⅱ. Problems may occur in room docking

###### 1. There is a time sequence for creating a room and joining a room. You need to create a room before you can join a room;

-Partial matches enter the voice room, and the clients of players A and B will create and join the voice room at the same time. At this time, pay attention to the timing;

###### 2. After the client initiates the room creation, the client does not need to call the join room interface;

###### 3. Overtime processing;

-For rooms that are not needed, the server needs to notify MGS to destroy the room;
-After a player is kicked out of the game room during a timeout game, the server needs to notify MGS to remove the user;

###### 4. Disconnect and reconnect;

-After the user kills the process and restarts the client, reconnects back to the game room, and needs to call the MGS join room interface again;

###### 5. Do not use the user name to create the room name, otherwise the user name with prohibited words will cause the creation to fail

-It is recommended to use [Game Name+MGS Room Number] to generate the room name. "Space Werewolf Killing" game processing example is as follows;
-First use the game name to create a room. After creating the room and get the room number, modify the room name to: game name + MGS room number (for example: "Space Werewolf: 102200")

### Ⅲ. Trigger point problems

###### 1. The trigger point has been sent, but the parameters have not been sent;

-The parameters of the trigger points need to be wrapped in json and sent, and the parameters cannot be passed directly;