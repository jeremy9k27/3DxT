# 3DxT
Introducing ‘3 Dimensional’ Expected Threat (3DxT) 

**Intro**
This novel stat is based on Sarah Rudd's xT, where a value is assigned to each grid location on the pitch. Essentialy, the value of a grid is the probability of the team in possession scoring if the ball is in that grid. More specifically, the xT of a gridpoint is the probabiility of scoring within the next n actions, where n is any positive integer. A gridpoint's xT with n=1, or xT(1), is the probability of scoring from a shot from that location since that's the only way of scoring in 1 action (yes, xT(1) is the same as xG). A gridpoint's xT(2) is the probability of scoring from 1 action + probaility of scoring from 2 actions. Scoring from 2 actions would be any non-shot action first and then a shot. xT(3) and so forth work similarly. 

![image](https://github.com/jeremy9k27/3DxT/assets/118779230/a586ab4d-47c9-4219-a7ee-2f8f99263ccd)

![image](https://github.com/jeremy9k27/3DxT/assets/118779230/491470c4-0601-4c1e-8553-0a0b56039ee4)





3DxT incorporates defender positioning to xT, adding a new level of highly important context.

Of course, on the pitch, the grids are still 2D. However, the intution of this stat becomes 3 Dimesnional because the height of the defensive line becomes a new input/dimenson. The visualization (as seen in issues), is a rectangular prism. 

**Writeup will completed in October**
The derivation of xT is completed - however, illogical tracking data from StatsBomb must now be sorted out for the results to make sense.
