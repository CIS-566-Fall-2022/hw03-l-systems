# Cherry Blossom L-system

## Final product

For my L-system I decided to generate cherry blossom branches. My main focus was trying to recreate the elegant branching shape, so I put a lot of time into creating L-system rules that would generate the patterns I saw in cherry blossom references. 

First, here is a video of my overall system. In the first part, my L System generates the overall branching structure, and then a seperate algorithm places leaves and flowers in a non-intersecting way. 

https://user-images.githubusercontent.com/25019996/196563781-057ea85b-b60a-4722-beee-beaa8b9be2f6.mp4

Here are some other models created by my L System (without the intersection analysis and smart placement)

![CherryBlossomModels_NoIntersectionTest](https://user-images.githubusercontent.com/25019996/196593545-1be08f8b-7efa-4f05-8435-7c61a0e092c9.png)

And here are some of the references I used

![References](https://user-images.githubusercontent.com/25019996/196564040-fc9c5c50-e129-4fa5-840c-96e4e416c235.png)

## L-system

Essentially, my L-System does the following:

The main shape of my cherry blossom branch is defined by a recursive rule (X) that grows the center branch every generation and randomly creates branches and flower structures. Specifically, each generation it does the following: move forward, chance of leaf, move forward, random branch, rotate, move forward, chance of flower cluster, move forward, random branch. The random branch option chooses between creating a single (symbol S, one branch leaving the main branch) or double branch (symbol D, two branches of different length leaving the main branch). If neither are chosen, there is also a chance of a flower cluster being placed there instead. This logic is handled by the rule E. The branching system (with flowers/leaves stripped away for clarity) is show below.

<img width="641" alt="Screen Shot 2022-10-18 at 8 02 12 PM" src="https://user-images.githubusercontent.com/25019996/196567424-50e90bed-1b12-4f13-a6ad-f034dadae3d1.png">

The single and double branch are both composed of the repeated branch unit, represented by symbol B. This is a small movement forward of a fixed length that also rotates a random amount (up to 10 degrees) and tapers. This repeated small step size and random rotation (along with additional non random and random rotation encoded into each branch's rule) helps form smooth looking curves for the branches and nice tapered shape. These single and double branches have the symbol O scattered throughout, which has a random chance of turnng into a small bud-like structure (symbol Y) that I noticed on branches in my reference photos. They also have some ls and Cs, which have a random chance of becoming leaves and flower clusters respectively. The pictures below show the buds in my system and reference. Also notice how you can see the branching units create nice curves.

<img width="1217" alt="Buds" src="https://user-images.githubusercontent.com/25019996/196748165-698cff47-de58-4fa2-835e-fa1359ced831.png">

On the end of every single branch and one of each double branch is the symbol R, which recursively grows single branches and flowers off the ends of branches. This allows for longer branching structures to randomly grow off the main branch. Growth of the branches is capped at ten iterations or a branch with of less than 0.04. I found that this helped avoid branches that grow too crazily (either looking strangely long or thin). Similarly, the symbol P is included in the middle of single and double branches (rather than on the end) which functions similarly to R but has a lower chance of growing and doesn't grow flower clusters. 

Each flower cluster is definied by the symbol C, which also takes a parameter for the random chance of a cluster occuring. This allowed me to make flower clusters more likely to appear in certain places than others. The cluster is defined by flower stems with flowers on the end. Stems can either be short (symbol G) or long (symbol H). I used a similar strategy to how I created a branch unit with a small forward step and random rotation for creating branches for the stems (I is the symbol for a "stem unit"). The difference is stem units also use the function T to apply gravity so stems droop down slightly. I actually later ended up subsituting out many of the "I"s for hardcoded forward steps to give me control over branch width so the branches would line up with the flowers though. I also created symbols Z and Q which randomly can turn into long and short stems respectively. This means that because flower clusters are defined with GHGZQ, they can randomly have anywhere between 3 and 5 stems, which gives the system a more natural look. You might notice that here the raw output of the l system causes some intersections between the flowers. I explain how I resolved this in the flowers section below.

<img width="990" alt="Screen Shot 2022-10-18 at 8 46 19 PM" src="https://user-images.githubusercontent.com/25019996/196571755-e8768ea1-e1db-444c-b6c5-dd628c6ddee2.png">

Lastly, the symbol J is used for leaf clusters. This function similarly to flowers in that it takes a parameter for probability of creating a cluster. The leaf cluster stem is also similar to how the short flower stem is defined. The leaves are then randomly placed with a little bit of branching. Leaf placement is discussed in more detail in the leaves section. 

Below I have pasted the rules for my L System (I used the houdini option to import rules from a text file).

```
# premise: FX

# values 
# b = branch chance
# c = flower chance

##### ---------------------------------------------------------------------- #####
##### --------------------------     BRANCHES     -------------------------- #####
##### ---------------------------------------------------------------------- #####

# ----- MAIN STRUCTURE ----- #
# main branch structure 
# (move forward, chance of leaf, move forward, random branch, rotate, move forward, chance of flower cluster, move forward, random branch, repeat structure)
# continues until arc length from root > 1.5 
# length bound allows generations to be turned up to finish expanding rules without creating a crazy long branches
X : A < 1.5 = !~(25)F(0.05)l(0.1, 5)F(0.05)E/(30)F(0.05)C(0.3)F(0.05)EX

# singular "unit" that makes up branches, used in S and D (single and double branches)
B = !~(10)F(0.03)

# single branch
S = !/(20)[!!^BBO~(10)PBOBR(0)]

# double branch
D = [!!!^(20)BBOBO~(15)BOBOl(0.3, 10)BF(0.005. 0.035)F(0.005. 0.03)C(1.0)]/30[!&(40)BBOBBO~(20)PBC(0.3)BOBC(0.8)BOBOBR(i)]

# ----- RANDOMIZATION ----- #

# randomly becomes either a single or double branch (biased 0.6 towards single)
E : (rand(i) < 0.6*b) = S
E : (rand(i) < b) = D
# if neither, 0.2 chance of becoming a flower cluster
E : (rand(i) < b + 0.2) = C(1.0)
E : (rand(i) > b + 0.2) = N

# P included in double and single branches grow into single branches (these always branch off from the middle)
# 0.2 chance, less likely than branch growing straight
P : g < 2 = S : 0.2

# R on the end of double and single branches (these always extend the length of the end). Recursive so growth can continue.
# when a branch grows forward, also has a 0.4 chance of growing a flower cluster
# stops growing once width is less than 0.03 to avoid comically thin branches
# iterations of growth are cappted at 10 by parameter i to avoid really long branches that spiral off in weird drxns
R(i) : ((g < 2) & (W > 0.04) & (i < 10)) = C(0.4)SR(i+1) : 0.8

##### --------------------------------------------------------------------- #####
##### --------------------------     FLOWERS     -------------------------- #####
##### --------------------------------------------------------------------- #####

# stems for flower cluster
C(p):(rand(i) < p * c & g < 2) = [+(10)&(10)/(10)GHGZQ]

# short flower stem w flower J on end
G = ///^(30)[!F(0.01, 0.035)!!I!!!~(15)F(0.01, 0.02)~(15)F(0.01, 0.025)J]

# long flower stem w flower J on end
H = ///^(30)[TF(0.01, 0.025)II!!I!!!T~(15)F(0.01, 0.02)T~(15)F(0.01, 0.025)J]

# singular unit that makes up flower stems
I = !!!!~(15)TF(0.015, 0.016)

# 0.5 percent chance of each potential long stem in a cluster being renderd
Z : g < 2 = H : 0.5

# 0.2 percent chance of each potential short stem in a cluster being renderd
Q : g < 2 = G : 0.2

##### ------------------------------------------------------------------ #####
##### --------------------------     BUDS     -------------------------- #####
##### ------------------------------------------------------------------ #####

# note: these are the weird stumpy buds note the actual flower budes

# O gets turned into flower bud Y with probability 0.3 (included in single and double branch rules)
O : g < 2 = Y : 0.3

# flower bud shape
Y = [+(30)&(30)/(30)~(30)F(0.006, 0.022)F(0.006, 0.03)F(0.002, 0.035)F(0.004, 0.05)F(0.004, 0.04)F(0.003, 0.015)F(0.001, 0.005)]

##### -------------------------------------------------------------------- #####
##### --------------------------     LEAVES     -------------------------- #####
##### -------------------------------------------------------------------- #####

# the shared stem for the whole leaf cluster, modified from the short flower cluster stem
j(s) = ///^(30)!T(s)F(0.01, 0.035)!!T(s)I!!!~(15)T(s)F(0.01, 0.02)~(15)F(0.01, 0.025)

# the leaf cluster
l(p, s):(rand(i) < p) = [+(20)&(20)/(20)~(20)j(s)[IKK]KKIKK[IKK]IKK]
```

## Flower Modeling

First, I procedurally modeled the shape of one petal by applying a series of scales, tapers, and bends to a disc. Then, I copy and rotate the petal to create the overall flowers shape. Two layers of noise (one high frequency and one low frequency) help give the flower a more natural shape.

<img width="1235" alt="PetalModeling" src="https://user-images.githubusercontent.com/25019996/196748285-344e6b05-d654-441d-a226-8699740135a6.png">

Next, I created the filaments (curvy tubes) and anthers (detail on the ends of the tubes). The filaments were created by applying two bends to a line. I created two different filament shape variants and also created a scale attribute so the filament widths would taper. Then, I copied them onto some scattered points and determined which filament shape variant based on how close the scattered point was to the origin. Using a little bit of math, I ensured that the filament curves always faced radially outward. Then, I converted the filaments lines to tubes and copied the anthers (also modled from spheres with some tapers/bends/scales) onto the ends of the filaments.

<img width="1215" alt="FilmentAndAntherModeling" src="https://user-images.githubusercontent.com/25019996/196748402-6a1fd9d1-abd5-4bfd-aef4-1dc9fa11409f.png">

I generated a stem and leaves with similar strategies, and put them all together to get a flower!

<img width="1052" alt="FinalFlower" src="https://user-images.githubusercontent.com/25019996/196748433-55d35acf-a9a5-49a8-9714-b5b13e3d3963.png">

By tweaking parameters (mostly the bends, tapers, and copying), I created three variants of the flower (full bloom, half bloom, and bud)

<img width="1209" alt="FlowerVariants" src="https://user-images.githubusercontent.com/25019996/196748474-0407eca1-9212-4415-b519-361e49c3b181.png">

## Leaf Modeling

I modeled the leaves after the flowers, so my process was similar but I made a few improvements. Most notably, instead of scaling and tapering a disc I used a ramp to drive the length of a series of line segments which define the shape of the leaf's side, which gave me exact control over its shape. I found this technique from here https://www.youtube.com/watch?v=oincHvffZtw&ab_channel=SergeyGolubev. Then I moved the edge points with some noise to give the leaf some slight imperfections, and used the "skin" node to convert the lines into a mesh. 

<img width="1189" alt="LeafBase" src="https://user-images.githubusercontent.com/25019996/196748495-892dca2e-1027-4cef-8a27-0f7e00f84de6.png">

After that I used similar strategies as the flower (bends, noise, etc.) to finish off shaping the leaf.

<img width="1127" alt="LeafBendAndNoise" src="https://user-images.githubusercontent.com/25019996/196748528-58adfb66-36c0-4bb5-a47f-41f9361ce64c.png">

I added vein details to the leaves by remeshing the leaf, picking start and end points for the veins (start points randomly selected from the middle, end points randomly selected from the edges), and then finding the shortest path from a start to each end

<img width="1217" alt="Veins" src="https://user-images.githubusercontent.com/25019996/196748558-b191b6f1-d43a-412a-a144-afd06ed3ee53.png">

With this approach, I created two leaves to be used for the branch (with the intention of randomly scaling and rotating to get a bit more variation)

<img width="1162" alt="LeafVariants" src="https://user-images.githubusercontent.com/25019996/196748586-9cce7bdf-d900-48dc-b56e-ce1e2016e2e1.png">

## Detail Placement

One of the trickiest parts of this project (which I'm still trying to improve) is the placement of these flowers and leaves. The raw output from the L-system generates many intersecting leaves and flowers. This is somewhat unavoidable, because I want a decent amount of flowers and I specifically want them to be in clusters. 

In order to combat this, I wanted to place my flowers after the l-system was generated so I could have finer control over their placement. Initially I tried to feed in a single point to the l system instead of the flower geoemtry so I could use those points as sources to place flower. However, for some reason, feeding points into the l-system as geometry doesnt work (they all end up at the origin and lose their normals? idk why), so instead I inputted a 2x2 grid geo. Then, I was able to use the orientation of the placed grids to infer how the flowers should be oriented, so I was able to copy flowers onto the branches as a post process. Currently, I have the flowers getting copied to points in a for loop. Each iteration of the for loop alternatingly places half bloom and full bloom flowers. Before placing a flower, it checks whether the flower would intersect with existing geometry. If not, it places the flower. If so, it attempts to place a smaller flower bud instead. If that bud would also intersect existing geometry, nothing gets placed. Visually I like the results this creates, but unfortunately it runs extremely slowly. (more notes on other things I'm trying in the future steps section)

<img width="990" alt="Screen Shot 2022-10-18 at 11 28 58 PM" src="https://user-images.githubusercontent.com/25019996/196591268-70f034e7-3f97-48c8-92a3-a3b201af8592.png">


For the leaves, I used a similar strategy. However, youll notice that in the L-system for each possible leaf placement I place several leaf geo source points. This is because when I do the for loop to place the leaves I randomly rotate and scale each leaf, so this has the effect of trying to place a few different leaf possibilities at each source point. To be honest, this didn't work as well as I hoped. Occasionally leaves end up being rotated and scaled so they just barely don't intersect and then I get stacked leaves on top of eachother. At the same time, leaves very often intersect, so the leaf clusters end up being very sparse. Overall, I think the leaf placement needs a lot more work. I think the leaves have the potential to be a really nice cherry on top for the l-system, but at the moment they don't really add much. (more notes on my plan for combatting this in the future steps section)

## Render with Procedural Background

Tbh shading and the procedural background ended up being lower priority for me compared to modeling, so I didn't go as in depth for them, but here are two renders with a procedural background:

<img width="1248" alt="Renders" src="https://user-images.githubusercontent.com/25019996/196748620-58312639-025c-45e6-befe-7a210a90d497.png">

For the background, I have a grid with a top to bottom gradient and some noise for the sky. I also added some clouds created by scattering spheres (start with one big squashed sphere, scatter some large ones to define overall shape, then scatter some smaller ones to get more interesting silhouette), converting them to a cloud volume, and adding some noise. I didn't dive into shading for the branches, I just assigned some colors. 

## Future Steps
- Flower and leaf placement:
  - Some friends of mine suggested a couple possible approaches that I'm currently messing with:
  (1) To check for flower intersections, using an rbd solver to de intersect geometry could potentially be much faster than the for-loop setup. Right now I have a single frame rdb solver that I've set up, and it correctly de-intersects the flowers but it also pushes them away from the branches in the process. I set up a constraint network to keep the flowers glued to the branches, but that isn't working super well, so I'm still trying to fiddle around. I've never done rdb stuff before so I'm definetly just doing something wrong lol. From my understanding it could also be possible to use it to detect collisions and use that info like my current for-loop works (instead of moving the flowers), but I don't quite know how to do that yet.
  (2) The relax sop is another way that I might be able to move the flowers away from eachother. I started messing with this a little and there is definetly potential, but it would require warping the branches after they're generated and I'm not sure how to do that, so I need to do a little more digging to figure that out.
  (3) For the leaves, I realized that because I want they to be pretty infrequently placed, it makes more sense to art direct a few nice clusters of leaves to be placed rather than trying to get the perfect procedural system to create nice leaf clusters. Then, I can do a much cheaper intersection test for just placing the whole cluster (rather than a ton of individual leaves) and get nicer looking output
- Shading/procedural background
  - I want to actually properly shade the flowers, leaves, and branches. Cherry blossom branches have a lot of very nice and distinct materials, so I think I could get something really nice looking. I also want to revisit the clouds because they had a nice volumetric look but I squished them down because with the minimally shaded branch the clouds just looked weird and out of place, but once the branch is shaded I think they could look really nice. 
