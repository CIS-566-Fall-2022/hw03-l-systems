# Homework 4: L-systems

# Renders

![main3](https://user-images.githubusercontent.com/33616958/196307000-15f101d3-f9bb-4164-b425-c2a5dc7e3a36.png)

![main](https://user-images.githubusercontent.com/33616958/196248196-96164762-54bd-4f9f-8fe7-91ea39560410.png)


# Description

For this assignment, I used the L-system in Houdini to create a cherry tree based on the following reference. I also create a island-like terrian, a solid-color background and clouds. 

<img width="500" alt="reference" src="https://user-images.githubusercontent.com/33616958/196232673-3fee00cc-d0f7-4eae-97bb-4ac643f69a96.png">

## Scene components

<img width="413" alt="scene_component" src="https://user-images.githubusercontent.com/33616958/196310235-b044b007-b2b2-4016-9a7e-45292d2070e7.png">

## Drawing Rules
```
Premise: X
Rule 1: X = F(0.05)+(40)F(0.4)-(60)F-(10)FF[-(20)F(0.2)A]F-(5)[+(45)FB]F+(15)FC
Rule 2: A:t<8 = T~(1)"(0.9);F(0.05)[+FA]/(80)[+F(0.08)A]/(150)[+A]
Rule 3: B:t<9 = T~(8)"F;[+B]/(90)[+FB]/(160)[+FB]
Rule 4: C:t<9 = T~(10)"F;[+C]/(100)[+FC]/(180)[+FC]
Rule 5: A:t>8 = J
Rule 6: B:t>9 = J
Rule 7: C:t>9 = J
```

## Generation Process
|Generation = 1 |Generation = 2 |
|--|--|
|<img width="507" alt="1" src="https://user-images.githubusercontent.com/33616958/196241906-98946594-c4ab-4879-ae39-31da5587ced0.png">|<img width="507" alt="2" src="https://user-images.githubusercontent.com/33616958/196241914-27ecf025-27b4-441b-96b8-2361bf1d49f6.png">|

|Generation = 6 |Generation = 10 |
|--|--|
|<img width="504" alt="6" src="https://user-images.githubusercontent.com/33616958/196243640-2ab82626-905f-4823-840a-50b83e0c5f32.png">|<img width="505" alt="10" src="https://user-images.githubusercontent.com/33616958/196243696-350c1f13-2192-4af4-8410-dc1a2df859d1.png">|

### Leaves generation process
1. Seperate the leaves from l-system
<img width="407" alt="leaf_1" src="https://user-images.githubusercontent.com/33616958/196309130-ed3999d0-3a38-47c9-8916-c5692c21a605.png">

2. Delete the geometry but keep the points
<img width="407" alt="leaf_2" src="https://user-images.githubusercontent.com/33616958/196309251-12071152-7f97-4872-9052-a9deb3cabb32.png">

3. Add a `copytopoints` node with a `sphere` as the geometry to copy
<img width="407" alt="leaf_3" src="https://user-images.githubusercontent.com/33616958/196309268-9c3795d5-7bb9-4d47-9d9f-d2ba31540d3c.png">

4. Jitter the points by adding a `pointjitter` node
<img width="407" alt="leaf_4" src="https://user-images.githubusercontent.com/33616958/196309304-a202632a-b187-4c95-bf9d-21ce269e6665.png">

5. Add another `copytopoints` node with a `grid` as the geometry to copy
<img width="406" alt="leaf_5" src="https://user-images.githubusercontent.com/33616958/196309329-1356c615-40cc-4c1d-8b27-ab5e8ef6f379.png">

6. Set color
<img width="408" alt="leaf_6" src="https://user-images.githubusercontent.com/33616958/196309355-cc422abf-0a2d-4ca1-bfb8-d2341fff4412.png">
