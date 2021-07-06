### Perspective from n Points (PnP Problem)
The goal of Perspective from n Points algorithms is to find the pose of the camera in respect to the world reference frame. This means we want to find the six degrees of freedom describing the position and orientation of the camera. As input we have the landmarks in the world reference frame as well as their images on the camera plane. In the case of an uncalibrated camera we additionally want to find out the camera's intrinsic parameters which we have discussed already in chapter 3.
Let's now find out how many points we need to get a unique solution. First, we look into the case of a calibrated camera.

##### One Point

First for only one point for which we know the position in the world and the position on the image plane of the camera we have infinite possible solution. This is because the camera can be placed all around that point in the world with any distance from it. We see that the point on the camera plane is always at the same image coordinates independently from where we place the camera as long the relative location from the camera plane to the ray from C to the world point is the same.

![0_P1P](https://user-images.githubusercontent.com/60929939/124594371-4b02d380-de92-11eb-8c71-1123856ce2a4.png)

Figure 1: 1 Point (P1P)

##### Two Points
For two points we know the actual distance between the points in the world as well as for the ones on the image plane. Because of the fixed focal length of the camera we know the angle between the two points in the image plan and the camera. From the intrinsics we also know the position of the image origin (u0/v0). The formula for the angle follows from the normal tangent formula and the use of the triangle Cp1(u0/v0) and is:

𝜃=𝑡𝑎𝑛−1(𝑑1+𝑠1𝑓)−𝑡𝑎𝑛−1(𝑑1𝑓)

![angle_from_f](https://user-images.githubusercontent.com/60929939/124594634-9fa64e80-de92-11eb-8b97-7077896b82b4.png)

##### Three Points
In the case where all three lines intersect in one single point, we have found a valid place for the camera point to be. To derive how many solutions there are and how to find them we take advantage of the law of cosine. In the previously described situation we have three different triangles with their corners at two of the world-points and one at the camera center. Also, for these triangles two edges intersect the camera plane at the image-points. The law of cosine states that for each triangle the squared distance between the two image points si is equal to the sum of the squared distance both edges connecting the image points with the camera center subtracting the twice the product of the times the cosine of the angle at the camera center.

𝑠𝑖2=𝐴𝑖2+𝐵𝑖2−2𝐴𝑖𝐵𝑖𝑐𝑜𝑠(𝜃𝑖)
 
Given by the points in the world reference frame we know si and θi is given by the points on the image plane and the focal length given by the camera intrinsics. We see that each of these equations has degree two. In total we have three unknows since A2 = B1, A3 = B2, A1 = B3 and we also have three equations. For systems with n independent non-linear equations and n variables it is know that the number of solutions has an upper bound equal to the product of the degrees of the equations. In our case this results to 23=8. Due to the second degree of the equations we know that for every positive solution there is also a negative solution. So, given that we expect the camera plane to be in between the world points and the camera point there are only 4 possible solutions. So, to get an unambiguous solution we need a 4th point.

Worth mentioning is also the system of the three equation can also be reformulated into a system with degree 4.

𝐺0+𝐺1𝑥+𝐺2𝑥2+𝐺3𝑥3+𝐺4𝑥4
 
Since we only need three points to get down to 4 solutions the PnP is often also referred to as P3P however as we have seen this is not completely correct as we need one more point to get an unambiguous solution. So P3+1P would fit best as a name.

![P3P](https://user-images.githubusercontent.com/60929939/124594986-0592d600-de93-11eb-9f62-41b70a86bbf3.png)

Figure 2: Points.

##### Omnidirectional Camera
Omnidirectional Cameras are ones with a very large Field of view (FOV)
![15_omnidir_camera](https://user-images.githubusercontent.com/60929939/124595191-4be83500-de93-11eb-8e89-7a62e4251de9.png)

Figure 3: Fisheye and Catadioptric

Catadioptric cameras use a bowl-shaped mirror which allow to reflect a 360-degree view onto the camera plane. However, in the center a blind spot remains where the camera itself is reflected. There are multiple different shapes possible for that mirror. One Issue that can arise is that the shape of the mirror produces a reflection where the rays no not intersect in a single point. Cameras with this property are called Non-central catadioptric cameras.

![16_noncentral_catadipotric](https://user-images.githubusercontent.com/60929939/124595206-4f7bbc00-de93-11eb-84ab-5153e226890c.png)

Figure 4: non-central Catadioptric 

Central projection can be achieved by different combinations of mirror shapes and camera types.

![17_central_catadipotric](https://user-images.githubusercontent.com/60929939/124595328-74702f00-de93-11eb-9ed9-2a2db9f90a7c.png)

Figure 5: Central Catadioptric

Perspective and Central Catadioptric cameras are really equivalent as the following image shows.

![18_equivalence_omnidir_perspective](https://user-images.githubusercontent.com/60929939/124595335-776b1f80-de93-11eb-9424-22d6e6392fc4.png)

Figure 6: Equivalence between Perspective and Central Catadioptric model 

![Unified_Omnidirectional_Camera_Model](https://user-images.githubusercontent.com/60929939/124595619-d29d1200-de93-11eb-91ae-3fd412e4925a.png)

##### Summary¶
For Calibrated cameras the PnP P3P (Perspective from n Points) algorithm is used since the intrinsics are already know. The minimum number of points that are needed is 3+1.
For uncalibrated camera the DLT (Direct Linear Transform) can be used since the intrinsics first have to be calculated. Here 4 coplanar or 6 non coplanar points are needed
