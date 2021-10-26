# I. MGS Integration Guide

## 1. Developer Platform

If you have already registered, you can skip this step.

- First, we need to conduct [Registration Certification](https://dev.233leyuan.com/#/doc/9)
- After integrating `MGS Android SDK`, conduct [Game Upload](https://dev.233leyuan.com/#/doc/6)
- Please notice [Cooperation Guidelines](https://dev.233leyuan.com/#/doc/5)
- **TODO 20210224** MGS-specific developer platform steps, set fields, set voice channel and text chat dimensions. This part of function will be operated by `MGS integration engineer`  at present, and the UI page will be opened later

## 2. Integration self-test, sample game code reference

After completion of integrating, please refer to the [Integration Process](https://dev.233leyuan.com/#/doc/1), follow the steps below to self-test.

- **TODO 20210224**  Multichannel self-testing steps, This part of the testing will now be carried out in collaboration with the `MGS integration team`
- **TODO 20210224** MGS function self-test, This part of the testing will now be carried out in collaboration with the `MGS integration team`

## 3. MGS SDK Operating Process

MGS offers a lightweight and extensible Android SDK for game clients. The server API is also provided, and for some specific interfaces, calls are made by the game server.

- **TODO 20210224** The SDK will be provided by the 'MGS Integration Engineer' in the form of a standard Android AAR file
- MGS login process

![image-20210330101933197](C:\Users\admin\Desktop\CP\March\Wolffun\MGS对接文件20210316\mgs document jpg\1)

- MGS room operation process

  ![mgs3](C:\Users\admin\Desktop\CP\March\Wolffun\MGS对接文件20210316\mgs document jpg\4)

## 4. Introduction to Feature Integration

### 1. Account Login

In the environment of 233 Playground, the game client obtains information of players in 233 Playground, including OpenID, nickname, game duration, etc. The game will bind the player's OpenID.

### 2. Friend Invitation

The game uses the MGS Android SDK interface to send friend requests to other players, and players can invite their friends from 233 Playground to join your game match.

### 3. Room Match

The room section is an important traffic entry and exposure position provided by 233 Playground for the game. There are two main categories of room modes in the game: **normal room mode** and **party room mode**. On the other dimension, there are two ways of integrating the game with MGS: **synchronous room** and **hosted room**

#### Room mode: normal room mode

Typical examples are Poker, Chess, Smash Bros, etc. The core feature is that the game is built into a room containing all the players of the game, which is played immediately after the game starts.

At this point, Room = the Room created by the game = the Room displayed in the Multiplayer Game Homepage of 233 Playground.

#### Room mode: party room mode

**TODO 20210224** This function is the second phase function and is not available yet.

A typical example is MOBA with the core feature that the game is built into a room containing a team of players. When you are ready to start, you need to match another team (or teams). After the match is successful, all the team members enter a large room and start the game.

Depending on the configuration, the team must be set or be full for the match to start, or it can be matched after two or three people (2v2/3v3 scenarios).

At this point, Room = the Room created by the game = the Room displayed in the Multiplayer Game Homepage of 233 Playground.

When matching, one or more groups form a Team, two or more teams form a Room, and all players in the Room participate in a game.

#### Integrating Method: Synchronous room

![mgs2](C:\Users\admin\Desktop\CP\March\Wolffun\MGS对接文件20210316\mgs document jpg\5)

In this way, the player pulls up the game client by clicking Create Room/Join Room/Quick Start in 233 playground (as the above screenshot). The game client will call the MGS Android SDK to learn the user's actions (such as joining room A001, creating room, quick match, etc.) and then do the corresponding actions. Existing matching logic can all be kept without affected. When joining a room/create room after the success of the action (game server will check whether the room is full, if players can join the room, match players' level, elo, region that conforms to the rules, and so on), will synchronize to the MGS. MGS will update the room list of 233 Playground online hall.

#### Integrating Method: Host room

**TODO 20210224** This function is the second phase function and is not available yet.

In this way, the player will stay in 233 Playground when clicking Create Room/Join Room/Quick Start. If you create a room, you will enter a room with waiting screen. If you join a room, you will enter the room with its waiting screen. If Quick Start, depending on the game configuration, you will join a room with an empty seat or enter the matching pool, and wait for 233 Playground to match successfully. After that, whether a room starts the game as a group or a successful match enter the game, 233 Playground will send the room information along with the information of all players to the game server and client.

With host room access, the game only has to configure its own game room properties, match patterns and rules, and wait for 233 Playground to bring the room and the player in.

The MGS host room is still under development and is not yet complete.

### 4. Voice channel and text chat

![mgs4](C:\Users\admin\Desktop\CP\March\Wolffun\MGS对接文件20210316\mgs document jpg\6)

Voice channel and text chat are displayed on the hover(floating) layer of 233 Playground. The game only needs to configure whether voice channel and text chat are needed, and on which level voice channel and text chat are placed.

Take MOBA 5V5 for example, voice channel and text chat can exist at the group level (i.e., two people in Duo queue), team level (i.e., all strangers teamates), or room level (10 people in full room). Voice channels and text chat can also be turned off, if the game features decide not to allow users to communicate freely at any time . It is common to select only one tier for both voice channel and text chat. If, for example, text chat is configured with multiple tiers, the player will need to switch back and forth between different text chat rooms.

### 5. Sample Game Server and Sample Game Client

We provide the integrated sample game server and the sample game client, **勇闯233 (YC 233)**, for your reference