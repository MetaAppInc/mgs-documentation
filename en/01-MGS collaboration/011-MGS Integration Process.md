# MGS Integration Process
![011](https://cdn.233xyx.com/1617072660829_407.png)

#### 1、Register your developer information on the "233 Developer Platform" and create your game
  - Developer Platform Links: https://dev.233leyuan.com

#### 2、Adjust the game
  - Check if the game needs to be adjusted according to the MGS plan
  - Documentation is required: game fine-tuning documentation, burying documentation

#### 3、Integrate MGS
  - Documents need to be prepared: MGS API documents, MGS docking list, MGS docking self-testing tools, 233 handle package
  - CP to obtain the docking parameters
    - CP needs to register two games on 233 developer platform and get two sets of parameters: appKey、appSecret；
      - Official Game: Package name ending in.mgs.meta, game name plus "- online"
      - Test Game: Package name ending in Test.mgs. Meta, game name with "-mgs Test"
    - When registering a game, select "MGS Games"
  - During MGS docking, use "test game" docking
  - CP Party uploads the game package to the developer platform, and after the approval of 233 members, it can search for its own game in [233 Playground].
    - During the "test game" through the process, update the package, repeated testing

#### 4、Integrate Advertisement SDK
  - Ad SDK Docking: This can be viewed on the "Ad Collaboration" page of the developer platform

#### 5、MGS Game testing
  - Confirm: the game is normal, the MGS flow is normal, and there is no problem with the buried point

#### 6、Official server release
  - CP needs to make the following preparations to change the official game
    - Take a official package
    - Replace formal parameters: appKey、appSecret
    - Determine the official server capacity
    - Release the official MGS game pack on the developer platform
  - 233 Responsible members will push the game according to the plan after approval
