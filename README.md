# Cloth-Simulation

This project was completed in 2022 for COS426: Computer Graphics @ Princeton University. The code implements a 2D image processor with a variety of filters and operations.

## Description

Original Images can be found in the images file. Further results can be found in results. 

The implemented features are as follows:

* Cloth (Contraints & Gravity)

For each of the constraints, I went through each of the particles in the cloth. 

For Structural constraints, I added constraints between the particle and it's neighboring particle to the right and bottom.
For Shearing constraints, I added constraints between the particle and it's neighboring particle to the bottom-left and bottom-right.
The distance is this.restDistance * this.restDistanceS since it is a diagonal constraint. 
For Bending constraints, I added constraints between the particle and it's neighboring 2 particles to the right and 2 particles to the bottom.
The distance is this.restDistance * this.restDistanceB since it is a constraint connecting particles 2 particles away. 

For each constraint, I enforced it by finding the correction vector using the given formula on the assignment page. 


Firstly, I made sure to add the force acted on the particle to it's overall netforce. Then, I implemented created a force using the equation F=ma -> (-p.mass * GRAVITY) for the y direction of a particle.

* Verlet Integration

To implmement verlet integration, I followed the equation given in the assignment details.
First, I cloned the current position of the particle. Then, I applied the formula to find the new position.
I had simplified the formula out to be: new position = 2(currentPosition) - DAMPING * currentPosition - previousPosition(1-DAMPING) + Force/Mass * deltaTime * deltaTime.
I then updated the previous position to the cloned last position and reset the net force. 
I then went through all the particles in the cloth and upadted its position using the integration function I just implemented.  

![VerletIntegration.png](https://github.com/Danica-T/Cloth-Simulation/blob/main/results/VerletIntegration.png)

* Floor Collision

If the y position of the particle is less than the y position + EPS of the floor, this means that the particle has gone through the floor. Therefore, I set that particle's y position to be equal to the floor + EPS. EPS is used to avoid clipping.

![floorCollision.png](https://github.com/Danica-T/Cloth-Simulation/blob/main/results/floorCollision.png)

* Sphere Collision

If the distance between the particle to the position of the sphere (center of the sphere) is less than the radius + EPS, then the particle is inside the sphere and needs to be projected to the surface of the sphere. In order to project the particle to the surface of the sphere, I set posNoFriction to be the original position of the particle subtracted by the sphere's center position, multiplied by the ratio of [the orginal position's distance to the center of the sphere] to the radius of the sphere. This allows me to project the particle to the nearest point on the sphere's surface. 

If the position of the particle in the last time step (previous position) was outside the sphere, I accounted for friction. I followed the details from the assignment page for this. Lastly, I called .handleSphereCollision() for every particle in the cloth. 

![sphereCollision.png](https://github.com/Danica-T/Cloth-Simulation/blob/main/results/sphereCollision.png)

* Box Collision

To handle box collisions, I compared each of the position's coordinates to the corresponding side of the box (ie:found distance between particle's x coordinate and box's max and min x). I then found the smallest length between these comparisions and used that to set the particle's position to the corresponding side. If the previous position of this particle was not inside the box, I accounted for friction.

![boxCollision.png](https://github.com/Danica-T/Cloth-Simulation/blob/main/results/boxCollision.png)

* Extensions

Event Handlers Revisited: I added 3 more events based on the user's mouse inputs. If the user clicks down, the particle at the mouse pointer is recorded and I caulcuated the distance between this particle to the camera's position. When the user moves the mouse while holding down and the shift key, I make the cloth follow the mouse by using the raycaster multiplied by the distance previously calculated and adding camera position to get the new position.

![mousemove.webm](https://github.com/Danica-T/Cloth-Simulation/blob/main/results/mousemove.webm)

Rain: To apply rain, I went through each "rate" raindrop and found a random coordinate. At the particle that was "hit", I added the impulse (which is just a negative y value with magnitide of the given strength).I added a weaker impusle (by 0.15, which was choosen arbitrarily based on what I thought looked best) to the particles surrounding the hit particle.

![rain.mov](https://github.com/Danica-T/Cloth-Simulation/blob/main/results/rain.mov)

Wind: To apply a more interesting wind, I used sinusoidal functions. The used functions were choosen arbitrarily based on what I thought looked best.

![wind.webm](https://github.com/Danica-T/Cloth-Simulation/blob/main/results/wind.webm)

