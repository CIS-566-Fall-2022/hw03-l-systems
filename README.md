# Homework 4: L-systems

# Renders

![main](https://user-images.githubusercontent.com/33616958/196248196-96164762-54bd-4f9f-8fe7-91ea39560410.png)

# Description

For this assignment, I used the L-system in Houdini to create a cherry tree based on the following reference. I also create a island-like terrian, a solid-color background and clouds. 

<img width="363" alt="reference" src="https://user-images.githubusercontent.com/33616958/196232673-3fee00cc-d0f7-4eae-97bb-4ac643f69a96.png">

# Drawing Rules
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

|Generation = 1 |Generation = 2 |
|--|--|
|<img width="507" alt="1" src="https://user-images.githubusercontent.com/33616958/196241906-98946594-c4ab-4879-ae39-31da5587ced0.png">|<img width="507" alt="2" src="https://user-images.githubusercontent.com/33616958/196241914-27ecf025-27b4-441b-96b8-2361bf1d49f6.png">|

|Generation = 6 |Generation = 10 |
|--|--|
|<img width="504" alt="6" src="https://user-images.githubusercontent.com/33616958/196243640-2ab82626-905f-4823-840a-50b83e0c5f32.png">|<img width="505" alt="10" src="https://user-images.githubusercontent.com/33616958/196243696-350c1f13-2192-4af4-8410-dc1a2df859d1.png">|
