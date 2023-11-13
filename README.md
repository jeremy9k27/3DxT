# 3DxT
Introducing ‘3 Dimensional’ Expected Threat (3DxT) 

**Intro to xT**

This novel stat is based on Sarah Rudd's xT, where a value is assigned to each grid location on the pitch. Essentialy, the value of a grid is the probability of the team in possession scoring if the ball is in that grid. More specifically, the xT of a gridpoint is the probabiility of scoring within the next n actions, where n is any positive integer. A gridpoint's xT with n=1, or xT(1), is the probability of scoring from a shot from that location since that's the only way of scoring in 1 action (yes, xT(1) is the same as xG). A gridpoint's xT(2) is the probability of scoring from 1 action + probaility of scoring from 2 actions. Scoring from 2 actions would be any non-shot action first and then a shot. xT(3) and so forth work similarly. 



**Why 3dxT?**

Consider the two screenshots below:

![image](https://github.com/jeremy9k27/3DxT/assets/118779230/a586ab4d-47c9-4219-a7ee-2f8f99263ccd)

![image](https://github.com/jeremy9k27/3DxT/assets/118779230/491470c4-0601-4c1e-8553-0a0b56039ee4)

According to xT, the value of the two situations is roughly equal because the ball is at a roughly symmetrical spots. However, it's obvious that Son is in a much more threatening position than Kompany. One way we can tell, and the reasoning we will feed to our algorithm, is that Son is **ahead of all the defenders** whereas there are many defenders in front of Kompany.


3DxT addresses this shortcoming by incorporating defender positioning to xT, adding a new level of highly important context.


**Methodology (Intuition)**

As mentoned in the introduction, xT's visualization is an (x,y) grid. 3DxT instead represents the pitch as an (x,y,z) rectangular prism - each plane z = c that is parallel to the xy plane and c is an integer can be thought of as a a pitch where the defensive line height is c. 3DxT can be thought of as several separate xT grids layered onto each other, each representing a defensive line height. The process for deriving the 3DxT values begins deriving 3DxT(1). This computation is rather trivial as the 3DxT(1) of a cell is just the probability of scoring given that a player shoots from that cell.

3DxT(2) and so on. 





**Methodology (Code)**

**Discussion**
![image](https://github.com/jeremy9k27/3DxT/assets/118779230/044b0bcb-1c16-495d-b577-c83e34d7dd3c)



*Limitations*


*Applications*

tactical fouls

**Writeup will completed in October**
The derivation of xT is completed - however, illogical tracking data from StatsBomb must now be sorted out for the results to make sense.
