# 3DxT
Introducing ‘3 Dimensional’ Expected Threat (3DxT) 

**Abstract**

3DxT is an extension of xT, a popular and powerful stat due to its applicability towards all phases of possession. xT is commonly computed as the probability that the team in possession will score during that same possession given the location that they have the ball. Adding much needed context, 3DxT also incorporates player positioning as an additional input. That is, 3DxT is computed as the probability that the team in possession will score given the location they have the ball ***and*** the location of all players on the pitch. This additional dimension makes the statistic much more granular and is important considering that the same ball location with two different sets of player locations can represent two very different situations. The computation of the 3DxT probabilities uses a Markov-inspired approach applied to 3D arrays. Several 3D arrays are necessary, each following the template where the element accessed at array[i][j][k] represents the value of a certain statistic at pitch location i, j with defense line height of k. Most notable of these 3D arrays is the transition array, analogous to a transition matrix, where each entry in the 3D array is a 3D array itself. Each inner array represents the probability of the ball being moved to each location given that the ball originates at the location indicated by the outer array. The proposed methodology is applied to data from StatsBomb's API accessed through Mpl Soccer, resulting in a set of 3DxT probabilities.



**Intro to 3DxT and xT**

This novel stat is based on Sarah Rudd's xT, where the pitch is broken up into an (x,y) grid and a value is assigned to each grid location on the pitch. Essentialy, the value of a grid is the probability of the team in possession scoring if the ball is in that grid. More specifically, the xT of a gridpoint is the probabiility of scoring within the next n actions, where n is any positive integer. A gridpoint's xT with n=1, or xT(1), is the probability of shooting and scoring from that location since that's the only way of scoring in 1 action (note: xT(1) isn't exactly the same as xG since xT(1) is the proability of shooting and scoring while xG is the probability of scoring given a shot). A gridpoint's xT(2) is the probability of scoring from 1 action + the probaility of scoring from 2 actions. Scoring from 2 actions would be any non-shot action first and then a shot. xT(3) and so forth work similarly. 



**Why 3dxT?**

Consider the two screenshots below:

![image](https://github.com/jeremy9k27/3DxT/assets/118779230/a586ab4d-47c9-4219-a7ee-2f8f99263ccd)

![image](https://github.com/jeremy9k27/3DxT/assets/118779230/491470c4-0601-4c1e-8553-0a0b56039ee4)

According to xT, the value of the two situations is roughly equal because the ball is at a roughly symmetrical spots. However, it's obvious that Son is in a much more threatening position than Kompany. One way we can tell, and the reasoning we will use in our algorithm, is that Son is ***ahead of all the defenders*** whereas there are many defenders in front of Kompany.


3DxT addresses this shortcoming by incorporating defender positioning to xT, adding a new level of highly important context.


**Methodology Overview**

As mentoned in the introduction, xT is based on representing the pitch as an (x,y) grid. 3DxT instead is based on the pitch represented as an (x,y,z) rectangular prism, with the new z dimension representing the height of the defensive line. (We will refer to a single unit on this prism as a cell, being analogous to a gridpoint on an (x,y) grid.) Whereas xT(n) gives the probability of the team scoring within n actions from a certain (x,y) gridpoint, 3DxT(n) requires defensive line height as an extra input, but gives a more accurate proability of the team scoring within n actions from an (x,y,z) cell. 

The intuition for deriving 3DxT is very similar to xT (though we will see how the computation is more complex). We begin with computing 3DxT(1) iterating forward. This computation is simple as the 3DxT(1) of a cell is just the probability of a player shooting and scoring from that cell. To compute 3DxT(2) of a cell, we need the probability that a player does any action other than shoot, and the probability of scoring given that the next action is a shot. We then sum that new value to the 3DxT(1) of that cell. Because the probabilty of moving the ball to every other cell is not equal, and the probability of scoring from every other cell is not equal, we must approach this granularly by computing what we will call the transition probabilities. The transition probabilities of a cell is the probability of the ball being moved to each cell in the entire prism (including the original cell itself) in one action, given that the player doesn't just shoot. Each probability is then multiplied by each recieving cell's corresponding probability of shooting and scoring - its 3DxT(1). We multiply the sum of these weighted probabilties by the probability that the player doesn't shoot - this is the probability of scoring in exactly 2 actions from a cell. When added with the probability that the player does shoot multiplied by the probability that the shot is scored (3DxT(1)), we get the cell's 3DxT(2). Iterating beyond works similarly. In general, 
3DxT(n) = (probability of shooting * value of shooting) + (probability of not shooting * value of not shooting), where value of shooting is 3DxT(1) and value of not shooting is the summation of the transition probabiltiies multiplied with corresponding 3DxT(n-1) values. 


**Coding Implementation**

A brief discussion about how to do the code is included here. The full code can be found in 3DxT_main_code. 

We begin by scraping StatBomb's public action data using mpl soccer. This data contains information about each action itself (the type of action, the timestamp, the player, the starting and stopping spot, etc), but we need to link the key piece of data we want to add is the defender locations. Thus, for each row in the dataset, we use the provided position method to find the pitch height of the second lowest defender (the first lowest defender is the keeper). We then reduce the dataset for the sake of simplicity: the only actions we care about are passing, carrying, shooting, and dribbling, and columns like opposition team name are not needed. To make the dataset ready for analysis, we need to partition the pitch into blocks and "bin" each x and y coordinate of each action into one of those blocks. Though the exact numbers don't matter, we break the pitch into a 12 by 8 - np.linspace and pd.cut are useful tools for this. Note that this means we are adding 5 new columns to each entry: the x,y coordinates of the ball's starting location, the x,y coordinates of the ball's ending location, and the y coordinate of the lowest defender. We are using the position of the lowest defender as a proxy for the entire defensive line height, so the lowest defender's x coordinate is not important.

With the dataset complete, we now want to create several 12 by 8 by 12 arrays whose locations correspond to a location on the pitch along with a defender position, and whose entries are a certain value related to that location. For example, we could have a 3d array of shoot probabilities. We want to compute several of these 3d arrays until we have enough information to create a 3d array that stores each cell's 3DxT. One easy array to compute is the shoot probability from each cell. We do this with nested for loops, iterating through each cell in a 12 by 8 by 12 array. For each i,j,k, we consider all actions from our dataset that originates at i,j,k. The proportion of those actions that are shots get stored in i,j,k of the shoot probability array. The move probability array has value at i,j,k that is simply 1 - the value of shoot probability at i,j,k. Similarly easy is the goal probability array where each entry i,j,k has the proportion of shots from i,j,k that are scored.

More complicated is the transition array since each array of the 3d array is a 3d array itself. Each inner 3d array at i,j,k has values at a,b,c which is the proporation of actions beginning at i,j,k that end at a,b,c. 

There is a necessary step before computing the transiition array, however. While we have the starting x, y, and line height in our dataset, we only have the final x and y (and not the final line height). The final line height is therefore be proxied for as follows: if the final y height is above the original line height, the final line height is set to the final y height. If the final y height is not above the original line height, the final line height is set tothe orgininal line height. In soccer terms, if an action places the ball behind the original defensive line, we assume that the defensive line adjusts to be even with the ball. If the action keeps the ball in front of the original defensive line, then we assume the line does not move.

We can now compute the 3DxT values. We start by initializing xT as xT(1). Recalling that this is essentialy xG, we do element by element multiplication with our shot probability array and our goal probability array. We know iterate further. For xT(2), we will again iterate through each i,j,k. We do element by element multiplication of the transition array found at i,j,k with xT(1) and then sum the resulting array. Further adding the xT(1) value at i,j,k to this sum gives us the xT(2) value at i,j,k. xT(3) at i,j,k is computed by summing the product of the transition array with xT(2), and summing again with xT(1) at i,j,k. We can continue iterating up to n, adding xT(1) to the sum of the product of the transition array and xT(n-1). As mentioned before, xT(n) = prob of shooting * value of shooting + prob of moving * value of moving, where value of moving is sum(transition probabilities * xT(n-1)).



**Discussion and Applications**
![image](https://github.com/jeremy9k27/3DxT/assets/118779230/044b0bcb-1c16-495d-b577-c83e34d7dd3c)

The most obvious use of this statistic is player evaluation and recruitment. The impact of players beyond direct goal contributions is notoriously hard to quantify, but 3DxT provides a comprehensive tool to measure all on-ball actions. The value of any action can be measured as the final 3DxT minus the original 3DxT, and the sum of the "3DxT added" of all actions of a player quantifies how much they improve their team's chance of scoring. 

3DxT can also be used for in-game strategy. While some of what 3DxT provides is obvious (for example, getting in behind the defense is good), there may be more hidden obseravtions to be made. From a high level view, 3DxT shows which locations on the pitch are valuable, and teams should strive to move the ball into those areas.


**Limitations**

The biggest limitation of this project is that its dataset is too small. Even though there are 70,000 actions in the dataset, there are less than 200 goals (and the probabilities are derived from these goals). We would expect that our visualization of 3DxT values would be mostly symmetrical and smooth but this is not the case with our limited data. The small size of the dataset is exacerbated by the fact that the proposed methodology partitions the data so frequently. For example, many of the entries in the goal probability 3D array are based on less than 5 shots, which is obviously too small for serious inference to be made. The transition array with its inner arrays partitions the data even further, and many of the entries in the inner arrays are based on sample of less than 5. Using bigger partitions (for example, by making the arrays 10*6*10) would increase the sample of each entry at the loss of making the statistic less granular.

**Future Developments**

Just like xT was improved upon by including player positioning, 3DxT can be improved upon by considering player speed. Similar methods have been used in Liverpool's "pitch control" model. Players are often praised for being able to put the correct weight on the ball, and including current speed as an input can quantify whether a player is unitentionally making their recepient slow down.



