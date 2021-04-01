# MGS API Mind maps
![img.png](https://cdn.233xyx.com/1617110054459_929.png)

# MGS Terminology

| Noun         | meaning                                             |
| ------------ | --------------------------------------------------- |
| Room         | room                                                |
| Team         | team                                                |
| voice        | voice                                               |
| roomChat     | chat room                                           |
| roomIdFromCp | Room or team ID(from CP)                            |
| roomShowNum  | MGS ID of the room or team (for display and search) |
| roomState    | The state of a room or team                         |

# Room
Room is a form of organization with a certain number of members, capacity and status.

# Room's life cycle
The life cycle of the room should look like the following figure

![img.png](https://cdn.233xyx.com/1617110054581_242.png)

# Room's functions
There are two functions in the current Room,**voice** and **chat room**。  
**voice** means that members in the room can communicate via instant voice.  
**chat room** means that members of a room can communicate with each other through text.

# Open condition of Room function
### Developer Platform
You need to turn on **voice** and **chat room** switches.  

![img.png](https://cdn.233xyx.com/1617260038777_500.jpg)


# Team

Team is a special form of Room, the essence is still Room, and it is worth noting that the **ID of** **Room and the ID of Team use the same storage system, so it cannot be repeated**.  
However, Team has more properties than Room, such as **parentRoomIdFromCp**, which means that Team and the parentRoom are related.  
A Team must have a parent Room, otherwise it is meaningless to use Room directly.  
The **parentRoomIdFromCp** of Team2 and Team3 in the figure below is Room1.

![img_1.png](https://cdn.233xyx.com/1617110054915_502.png)

Team can only be used in special situations, such as when teams like 5V5 are fighting,

In this case, there are 10 people in the room, but they need to be divided into 2 teams,

There are 5 players in each team against each other.

# Distribution of functions in Room and Team
Since Room(or Team) has both voice and chat Room capabilities, in the case of Room and Team coexist, how to divide the two capabilities?  
We provide two more fields **VoiceScope** and **RoomChatScope** in the interface parameter that creates the Team.  
We use **VoiceScope** to illustrate the relationship between abilities

| voiceScope | function scope |
| ---------- | -------------- |
| 0          | none           |
| 1          | room           |
| 2          | team           |

For example, as shown in the figure below, if **VoiceScope** is 2 when the Team is created, the ability to voice is migrated to the Team.

![img.png](https://cdn.233xyx.com/1617260038981_327.png)



