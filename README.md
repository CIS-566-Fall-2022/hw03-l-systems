# Homework 4: L-systems

## Houdini Submission

For this assignment I really wanted to make a realistic render of a dandelion flower. Once I started I realised that the l-system logic ended up being pretty straight forward, but I decided to stick with the idea and play with other houdini nodes to get a relatively nicer output render.

![dandelion-small](https://user-images.githubusercontent.com/90112787/200382428-deb0a5f5-ea34-4034-90e9-abd53db56690.gif)

## Approach:

### Modelling
  - I started with modelling the seed of the plant. It consists of a tube with an l-system for the feathery bristles (known as pappus) at one end and the other end of the tube is transformed to give thickness to where it attaches to the plant's center.
  - The plant stem is a line with geometry and the center is a sphere thats a bit squished at the top and bottom.
  - For arranging the seeds randomly around the center, I used a popnet node on a polygon sphere to get random points on its surface and copied the seed geometry at every point in the direction of the sphere's normals.
  - I applied different principle shader materials to the stem, seeds and center geometry.

### L-System
Rules
  - Premise: FA
  - A = !A/[+F-(b)FF]
  - B = F
  
Values:
 - Generations= 50
 - Angle = 75.5
 - b = 15
 
Steps:
- I started with a tube with a node at its end, i.e. the premise is FA

<img width="482" alt="Screenshot6" src="https://user-images.githubusercontent.com/90112787/200387195-750bf618-45dd-4c2d-b244-a77e5414a4a6.png">

- To place the pappus around the end of the tube I replaced the node (A) with tubes using roll angle(/). Then I used the angle value to rotate(+) the new tubes more so that they are closer to 90 degrees from the seed's stem.

<img width="482" alt="Screenshot5" src="https://user-images.githubusercontent.com/90112787/200388048-d650dc2d-b349-489f-9b5a-a66d025ae3c9.png">
 
- The pappus needed some curvature to them so I modified the rules to bend the tube upwards using rotate(-) and reduced the effect from the default angle value using a variable(b). I also played around with the thickness and thickness scale values to get the right proportions.

<img width="482" alt="Screenshot4" src="https://user-images.githubusercontent.com/90112787/200388655-55af66bf-28b0-4fa3-9cb7-c93bb78c1fa7.png">

- Lastly, I used the transform and merge nodes to connect the pappus to the seed stem.

<img width="482" alt="Screenshot3" src="https://user-images.githubusercontent.com/90112787/200389033-57f4448a-0a63-486d-869f-1c4cde049339.png">

- When this seed geometry is copied to random points on the surface of a sphere it looks as follows:

<img width="482" alt="Screenshot2" src="https://user-images.githubusercontent.com/90112787/200389354-8ac4d5fd-6b46-4b95-a6c1-cbcc573186a3.png"> <img width="482" alt="Screenshot1" src="https://user-images.githubusercontent.com/90112787/200389545-e45e2ae0-71ff-46cd-b63b-eac14a49bb28.png">

### Animation
To animate the seeds, I added a popwind node to the popnet network and played around with the settings for wind direction, speed etc. To animate the stem, I used a bend node and set some key frame values for the bend angle values to mimic the wind. I also added some animation the the camera so that it tracks the seeds as the blow across the scene.

### Rendering
For the final output I added an environment light and a blue backdrop and rendered with 10x10 pixel sample.

### Node Networks
<img width="572" alt="Screen Shot 2022-11-07 at 1 57 09 PM" src="https://user-images.githubusercontent.com/90112787/200392124-13533ddc-110d-4b4f-9e4b-2f85ce914ba3.png">

<img width="840" alt="Screen Shot 2022-11-07 at 1 56 54 PM" src="https://user-images.githubusercontent.com/90112787/200392130-cbfb21f3-d311-48b8-bd61-0c9414e72af9.png">

### References & Resources
- [Youtube tutorial](https://www.youtube.com/watch?v=WC71PmmyHpM)
- [Principles of L-Systems](https://www.houdinikitchen.net/wp-content/uploads/2019/12/L-systems.pdf)
- [The Algorithmic Beauty of Plants](http://algorithmicbotany.org/papers/abop/abop.pdf)
