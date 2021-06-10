# NPC_Navigaiton_Unity
A simple script responsible for the health, navigation (based on its state and vision), attacking and damage calculation.  

![Screenshot 2021-06-10 at 5 55 58 PM](https://user-images.githubusercontent.com/32577771/121547893-39eeb000-ca15-11eb-92fe-50786dbdaba8.png)

The boss enemy has a special component attached to him, the Nav Mesh Agent component, which differentiates him from all the other monsters. With pathfinding and spatial reasoning being handled by the agent, the boss is given the ability to move around the map avoiding obstacles while "searching" for the player [1]. Moreover, the script attached to the boss monster allows him to attack from distance and change his speed depending on the state he is on.
If the boss is alive, he is on one of the following states: idle, walking, searching, chasing or hunting. Below we discuss each state:
1. "Idle": the boss is either alerted that the player was somewhere around the last position he was seen or not.
• "Alerted": While being alerted the boss sets a new position based on the last seen position of the player and switches to the "Search" state.
• "Not Alerted": While being not alerted the boss searches for the player on the map based on a random position he selects.
2. "Walk": while walking, if the remaining distance is less or equal the stopping distance and the path is in the process of being computed and ready, the boss switches to "Search" state.
3. "Search": being able to switch to this state from either "Walk" or "Hunt" state, the boss during this state rotates for a second around himself giving the illusion that searches for the next position.
4. "Chase": during this state the boss increases his speed and sets his next position at the player's position. The boss can enter this state after seeing the player or getting hit by them.
5. "Hunt": this state is accessible only from within the "Chase" one, after the boss has lost sight of the player either due to the long distance or hiding behind obstacles. The boss here can either switch back to "Chase" state after scouting the player out, or switch to "Search" state while being alerted that the player was last seen somewhere around that area.
11.3.4 Boss Vision Scripting
By implementing the CreateCone.cs [2] I managed to give vision to the boss in order to see the player anytime they enter his field of vision. First, I marked the cone as "is Trigger". Then within the boss script , with a method called ckeckSight(), using the method of raycasting, I gave to the boss the ability to recognize each object he sees, offering the boss the advantage of ignoring other colliders but also assisting the player in beating the boss by letting them hide behind objects to avoid getting hit.

1. Online Manual of Unity 3D about NavMesh Agents. Available at: https://docs.unity3d.com/Manual/class-NavMeshAgent.html
2. http://wiki.unity3d.com/index.php?title=CreateCone
