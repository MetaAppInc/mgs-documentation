- # Main features of MGS Integration

  - - ### 1、Docking basic function【login】

      - login；
      - Friend&Room floating ball；

  ### 2、233APP special entry【queryPlayerAction】

  - Start the game normally；
    - Click the game in 233 to start the game directly, there is no need to dock here, it is directly in 233; ；
  - Click on the game room created by others at 233 to enter；
    - Pull up the game and enter the created room；

  > - Click on 233 to enter quickly; (optional)
  >   - Jump directly to the game matching interface；
  > - Click Create Room at 233 to enter; (optional)
  >   - Jump directly to the game creation room interface；

  ### 3、Friends operation【showFloatingLayer、showUserProfile】

  - Invite 233 friends to join the game room; 【showFloatingLayer】
    - Click on the invite friends button, pull up the floating layer, pass parameters to open the friend interface of the floating layer;
  - Add friend; 【showUserProfile】
    - Click the add friend button to pull up the user profile card pop-up window;

  ### 4、Room operation【create、join、leave、destroy】

  - To create a room in the game, MGS needs to be notified;
    - MGS creates the 233 voice room;
      - MGS will create a corresponding display room in 233APP;
  - If someone joins or leaves the room in the game, MGS needs to be notified;
    - MGS voice room needs to add and delete corresponding users;
      - MGS will update the number of exhibitors in the 233APP room;
  - If the room is destroyed in the game, MGS needs to be notified;
    - MGS will destroy the 233 voice room;
      - 233APP will also destroy the corresponding game room;
  - The room cannot be entered during the game, and MGS needs to be notified;
    - MGS will hide the display of 233APP;
  - In the game, the room is changed to be able to enter, and MGS needs to be notified;
    - For example, when someone leaves when room is full, others can enter the room,. At this time, 233APP needs to display the room again;