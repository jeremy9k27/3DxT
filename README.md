# 3DxT
Introducing ‘3 Dimensional’ Expected Threat (3DxT) 

**Intro to xT**

This novel stat is based on Sarah Rudd's xT, where the pitch is broken up into an (x,y) grid and a value is assigned to each grid location on the pitch. Essentialy, the value of a grid is the probability of the team in possession scoring if the ball is in that grid. More specifically, the xT of a gridpoint is the probabiility of scoring within the next n actions, where n is any positive integer. A gridpoint's xT with n=1, or xT(1), is the probability of shooting and scoring from that location since that's the only way of scoring in 1 action (note: xT(1) isn't exactly the same as xG since xT(1) is the proability of shooting and scoring while xG is the probability of scoring given a shot). A gridpoint's xT(2) is the probability of scoring from 1 action + the probaility of scoring from 2 actions. Scoring from 2 actions would be any non-shot action first and then a shot. xT(3) and so forth work similarly. 



**Why 3dxT?**

Consider the two screenshots below:

![image](https://github.com/jeremy9k27/3DxT/assets/118779230/a586ab4d-47c9-4219-a7ee-2f8f99263ccd)

![image](https://github.com/jeremy9k27/3DxT/assets/118779230/491470c4-0601-4c1e-8553-0a0b56039ee4)

According to xT, the value of the two situations is roughly equal because the ball is at a roughly symmetrical spots. However, it's obvious that Son is in a much more threatening position than Kompany. One way we can tell, and the reasoning we will feed to our algorithm, is that Son is **ahead of all the defenders** whereas there are many defenders in front of Kompany.


3DxT addresses this shortcoming by incorporating defender positioning to xT, adding a new level of highly important context.


**Methodology (Overview)**

As mentoned in the introduction, xT is based on representing the pitch as an (x,y) grid. 3DxT instead is based on the pitch represented as an (x,y,z) rectangular prism, with the new z dimension representing the height of the defensive line. (We will refer to a single unit on this prism as a cell, being analogous to a gridpoint on an (x,y) grid.) Whereas xT(n) gives the probability of the team scoring within n actions from a certain (x,y) grid location, 3DxT(n) requires defensive line height as an extra input, but gives a more accurate proability of the team scoring within n actions from an (x,y,z) cell. 

The intuition for deriving 3DxT is very similar to xT (though we will see how the computation is much more complex). We begin with computing 3DxT(1) then iterating forward. This computation is simple as the 3DxT(1) of a cell is just the probability of scoring given that a player shoots from that cell. To compute 3DxT(2) of a cell, we need the probability that a player does any action other than shoot, and the probability of scoring given that the next action is a shot. We then sum that new value to the 3DxT(1) of that cell. Because the probabilty of moving the ball to every other cell is not equal, and the probability of scoring from every other cell is not equal, we must approach this granularly by computing what we will call the transition probabilities. The transition probabilities of a cell is the probability of the ball moving to each cell (including the original cell itself) in one action, given that the player doesn't just shoot. Each probability is then multiplied by each recieving cell's corresponding probability of scoring and shooting - the cell's 3DxT(1). The sum of these weighted probabilties is the probability of scoring in exactly 2 actions from a cell, and when added with the 3DxT(1) of the cell, we get the cell's 3DxT(2).


3DxT(2) and so on. 





**Methodology (Code)**

**Discussion**
![image](https://github.com/jeremy9k27/3DxT/assets/118779230/044b0bcb-1c16-495d-b577-c83e34d7dd3c)



*Limitations*

insufficient data

separating into grids

speed of player?

*Applications*

tactical fouls

**Writeup will completed in October**
The derivation of xT is completed - however, illogical tracking data from StatsBomb must now be sorted out for the results to make sense.
