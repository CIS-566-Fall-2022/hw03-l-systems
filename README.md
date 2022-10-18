# Cherry Blossom L-system

## Final product

For my L-system I decided to generate cherry blossom branches. My main focus was trying to recreate the elegant branching shape, so I put a lot of time into creating L-system rules that would generate the patterns I saw in cherry blossom references. 

First, here is a video of my overall system. In the first part, my L System generates the overall branching structure, and then a seperate algorithm places leaves and flowers in a non-intersecting way. 

https://user-images.githubusercontent.com/25019996/196563781-057ea85b-b60a-4722-beee-beaa8b9be2f6.mp4

Here are some other models created by my L System (without the intersection analysis and smart placement)

![CherryBlossomModels_NoIntersectionTest](https://user-images.githubusercontent.com/25019996/196564026-7dfef2a5-cd23-4fbf-92bb-501963387617.png)

And here are some of the references I used

![References](https://user-images.githubusercontent.com/25019996/196564040-fc9c5c50-e129-4fa5-840c-96e4e416c235.png)

## L-system

Essentially, my L-System does the following:

The main shape of my cherry blossom branch is defined by a recursive rule (X) that grows the center branch every generation and randomly creates branches and flower structures. Specifically, each generation it does the following: move forward, chance of leaf, move forward, random branch, rotate, move forward, chance of flower cluster, move forward, random branch. The random branch option chooses between creating a single (symbol S, one branch leaving the main branch) or double branch (symbol D, two branches of different length leaving the main branch). If neither are chosen, there is also a chance of a flower cluster being placed there instead. This logic is handled by the rule E. 

The single and double branch are both composed of the repeated branch unit, represented by symbol B. This is a small movement forward of a fixed length that also rotates a random amount (up to 10 degrees) and tapers. This repeated small step size and random rotation (along with additional non random and random rotation encoded into each branch's rule) helps form smooth looking curves for the branches and nice tapered shape. These single and double branches have the symbol O scattered throughout, which has a random chance of turnng into a small bud-like structure (symbol Y) that I noticed on branches in my reference photos. They also have some ls and Cs, which have a random chance of becoming leaves and flower clusters respectively. 

On the end of every single branch and one of each double branch is the symbol R, which recursively grows single branches and flowers off the ends of branches. This allows for longer branching structures to randomly grow off the main branch. Growth of the branches is capped at ten iterations or a branch with of less than 0.04. I found that this helped avoid branches that grow too crazily (either looking strangely long or thin). Similarly, the symbol P is included in the middle of single and double branches (rather than on the end) which functions similarly to R but has a lower chance of growing and doesn't grow flower clusters. 

Each flower cluster is definied by the symbol C, which also takes a parameter for the random chance of a cluster occuring. This allowed me to make flower clusters more likely to appear in certain places than others. The cluster is defined by flower stems with flowers on the end. Stems can either be short (symbol G) or long (symbol H). I used a similar strategy to how I created a branch unit with a small forward step and random rotation for creating branches for the stems (I is the symbol for a "stem unit"). The difference is stem units also use the function T to apply gravity so stems droop down slightly. I actually later ended up subsituting out many of the "I"s for hardcoded forward steps to give me control over branch width so the branches would line up with the flowers though. I also created symbols Z and Q which randomly can turn into long and short stems respectively. This means that because flower clusters are defined with GHGZQ, they can randomly have anywhere between 3 and 5 stems, which gives the system a more natural look.

Below I have pasted the rules for my L System (I used the houdini option to import rules from a text file).

```
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

## Leaf Modeling

## Flower Modeling

## Detail Placement

## Procedural Background

Here is a render with the procedural background:

## Future Steps


# Homework 4: L-systems

For this assignment, you will design a set of formal grammar rules to create
a plant life using an L-system program. Once again, you will work from a
TypeScript / WebGL 2.0 base code like the one you used in homework 0. You will
implement your own set of classes to handle the L-system grammar expansion and
drawing. You will rasterize your L-system using faceted geometry. Feel free
to use ray marching to generate an interesting background, but trying to
raymarch an entire L-system will take too long to render!

## Base Code
The provided code is very similar to that of homework 1, with the same camera and GUI layout. Additionally, we have provided you with a `Mesh` class that, given a filepath, will construct VBOs describing the vertex positions, normals, colors, uvs, and indices for any `.obj` file. The provided code also uses instanced rendering to draw a single square 10,000 times at different locations and with different colors; refer to the Assignment Requirements section for more details on instanced rendering. Farther down this README, we have also provided some example code snippets for setting up hash map structures in TypeScript.

## Assignment Requirements
- __(15 points)__ Create a collection of classes to represent an L-system. You should have at least the following components to make your L-system functional:
  - A `Turtle` class to represent the current drawing state of your L-System. It should at least keep track of its current position, current orientation, and recursion depth (how many `[` characters have been found while drawing before `]`s)
  - A stack of `Turtle`s to represent your `Turtle` history. Push a copy of your current `Turtle` onto this when you reach a `[` while drawing, and pop the top `Turtle` from the stack and make it your current `Turtle` when you encounter a `]`. Note that in TypeScript, `push()` and `pop()` operations can be done on regular arrays.
  - An expandable string of characters to represent your grammar as you iterate on it.
  - An `ExpansionRule` class to represent the result of mapping a particular character to a new set of characters during the grammar expansion phase of the L-System. By making a class to represent the expansion, you can have a single character expand to multiple possible strings depending on some probability by querying a `Map<string, ExpansionRule>`.
  - A `DrawingRule` class to represent the result of mapping a character to an L-System drawing operation (possibly with multiple outcomes depending on a probability).

- __(10 points)__ Set up the code in `main.ts` and `ShaderProgram.ts` to pass a collection of transformation data to the GPU to draw your L-System geometric components using __instanced rendering__. We will be using instanced rendering to draw our L-Systems because it is much more efficient to pass a single transformation for each object to be drawn rather than an entire collection of vertices. The provided base code has examples of passing a set of `vec3`s to offset the position of each instanced object, and a set of `vec4`s to change the color of each object. You should at least alter the following via instanced rendering (note that these can be accomplished with a single `mat4`):
  - Position
  - Orientation
  - Scaling

- __(55 points)__ Your L-System scene must have the following attributes:
  - Your plant must grow in 3D (branches must not just exist in one plane)
  - Your plant must have flowers, leaves, or some other branch decoration in addition to basic branch geometry
  - Organic variation (i.e. noise or randomness in grammar expansion and/or drawing operations)
  - The background should be a colorful backdrop to complement your plant, incorporating some procedural elements.
  - A flavorful twist. Don't just make a basic variation of the example F[+FX]-FX from the slides! Create a plant that is unique to you. Make an alien tentacle monster plant if you want to! Play around with drawing operations; don't feel compelled to always make your branches straight lines. Curved forms can look quite visually appealing too.

- __(10 points)__ Using dat.GUI, make at least three aspects of your L-System interactive, such as:
  - The probability thresholds in your grammar expansions
  - The angle of rotation in various drawing aspects
  - The size or color or material of the plant components
  - Anything else in your L-System expansion or drawing you'd like to make modifiable; it doesn't have to be these particular elements

- __(10 points)__ Following the specifications listed
[here](https://github.com/pjcozzi/Articles/blob/master/CIS565/GitHubRepo/README.md),
create your own README.md, renaming the file you are presently reading to
INSTRUCTIONS.md. Don't worry about discussing runtime optimization for this
project. Make sure your README contains the following information:
    - Your name and PennKey
    - Citation of any external resources you found helpful when implementing this
    assignment.
    - A link to your live github.io demo (refer to the pinned Piazza post on
      how to make a live demo through github.io)
    - An explanation of the techniques you used to generate your L-System features.
    Please be as detailed as you can; not only will this help you explain your work
    to recruiters, but it helps us understand your project when we grade it!

## Writing classes and functions in TypeScript
Example of a basic Turtle class in TypeScript (Turtle.ts)
```
import {vec3} from 'gl-matrix';

export default class Turtle {
  constructor(pos: vec3, orient: vec3) {
    this.position = pos;
    this.orientation = orient;
  }

  moveForward() {
    add(this.position, this.position, this.orientation * 10.0);
  }
}
```
Example of a hash map in TypeScript:
```
let expansionRules : Map<string, string> = new Map();
expansionRules.set('A', 'AB');
expansionRules.set('B', 'A');

console.log(expansionRules.get('A')); // Will print out 'AB'
console.log(expansionRules.get('C')); // Will print out 'undefined'
```
Using functions as map values in TypeScript:
```
function moveForward() {...}
function rotateLeft() {...}
let drawRules : Map<string, any> = new Map();
drawRules.set('F', moveForward);
drawRules.set('+', rotateLeft);

let func = drawRules.get('F');
if(func) { // Check that the map contains a value for this key
  func();
}
```
Note that in the above case, the code assumes that all functions stored in the `drawRules` map take in no arguments. If you want to store a class's functions as values in a map, you'll have to refer to a specific instance of a class, e.g.
```
let myTurtle: Turtle = new Turtle();
let drawRules: Map<string, any> = new Map();
drawRules.set('F', myTurtle.moveForward.bind(myTurtle));
let func = drawRules.get('F');
if(func) { // Check that the map contains a value for this key
  func();
}
```
TypeScript's `bind` operation sets the `this` variable inside the bound function to refer to the object inside `bind`. This ensures that the `Turtle` in question is the one on which `moveForward` is invoked when `func()` is called with no object.

## Examples from previous years (Click to go to live demo)

Andrea Lin:

[![](andreaLin.png)](http://andrea-lin.com/Project3-LSystems/)

Ishan Ranade:

[![](ishanRanade.png)](https://ishanranade.github.io/homework-4-l-systems-IshanRanade/)

Joe Klinger:

[![](joeKlinger.png)](https://klingerj.github.io/Project3-LSystems/)

Linshen Xiao:

[![](linshenXiao.png)](https://githublsx.github.io/homework-4-l-systems-githublsx/)

## Useful Resources
- [The Algorithmic Beauty of Plants](http://algorithmicbotany.org/papers/abop/abop-ch1.pdf)
- [OpenGL Instanced Rendering (Learn OpenGL)](https://learnopengl.com/Advanced-OpenGL/Instancing)
- [OpenGL Instanced Rendering (OpenGL-Tutorial)](http://www.opengl-tutorial.org/intermediate-tutorials/billboards-particles/particles-instancing/)

## Extra Credit (Up to 20 points)
- For bonus points, add functionality to your L-system drawing that ensures geometry will never overlap. In other words, make your plant behave like a real-life plant so that its branches and other components don't compete for the same space. The more complex you make your L-system self-interaction, the more
points you'll earn.
- Any additional visual polish you add to your L-System will count towards extra credit, at your grader's discretion. For example, you could add animation of the leaves or branches in your vertex shader, or add falling leaves or flower petals.
