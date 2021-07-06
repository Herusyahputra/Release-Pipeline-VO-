
### Introduction Camera Projection

In the last chapter, we've seen how a lens works. In this chapter, we are finding a mathematical model    for converting the (X,Y,Z)-coordinates of an object in our world to pixel coordinates (u,v) on an image.
In this example, (u,v) are pixel coordinates measured from the top-left of an image. The center of our   image is the origin, or principal point O. The focal length f is given as well as the optical center,     e.g. the Center of the Lens C.
  
  To convert from World to Pixel coordinates, we have to perform three steps:

Convert the world point (XW,YW,ZW) with optical center W to a new coordinate system that has the cameras optical center C as the (0,0,0) Origin. We do that via rigid body transformation, so rotation R and     translation t.
Convert the resulting camera point (XC,YC,ZC) to new coordinates in the same coordinate system on the optical sensor, (x,y,z) using the cameras intrinsic parameter matrix K. The optical sensor is also referred to as "image plane" or "image frame"
Convert (x,y,z) to pixel coordinates (u,v) by normalizing with z and shifting the origin of the image.

![1_world_and_pixel_coordinates](https://user-images.githubusercontent.com/60929939/124547887-74eed280-de5f-11eb-9620-0df88edc9403.png)

Figure 1: Overview: World Coordinates -> Camera Coordinates -> Image Coordinates -> Pixel Coordinates

##### From World to Camera
Given world coordinates (XW, YW, ZW), we want to find a new coordinates (XC, YC, ZC) which is just a new coordinate system that has the cameras principal point C as the origin point (0, 0, 0). This can be done using a rigid body transformation, so a combined rotation R and translation t. The matrix [R|t] is called the Matrix of extrinsic parameters.

![2_world_to_camera_coordinates](https://user-images.githubusercontent.com/60929939/124548029-b3848d00-de5f-11eb-9f9d-5d432b031959.png)

Figure 2: World Coordinates -> Camera Coordinates. 

##### From Camera to Image plane
We can map a point from the Camera Coordinate System, PC = (XC, YC, ZC) to the optical sensor p = (x, y) by using the concept of similarity triangles again, with thinking back to the pinhole approximation from the last chapter.

![perspective](https://user-images.githubusercontent.com/60929939/124548106-d57e0f80-de5f-11eb-93ea-e146545cb50a.png)

Let's isolate the cameras x,y dimension and have a look at them seperatly. We see that PC = (XC, 0, ZC) projects into p = (x,0). We now need to find out the projections x coordinate. From the concept of similarity, we see that x/f = XC / ZC must hold. Therefore, we can deduct that x = fXC / ZC. This also holds for the y-dimenion, of course: y = fYC / ZC.

![3_camera_to_frame_coordinates](https://user-images.githubusercontent.com/60929939/124548157-e9c20c80-de5f-11eb-8a75-35a71dd89e61.png)

Figure 3: Camera Coordinates -> Image Coordinates.

##### From Camera to Pixel Coordinates
The first step is already done: with x = fXC / ZC and y = fYC / ZC, we bring the Camera coordinates to Image coordinates. Now we only have to map them to Pixel coordinates by shifting them from the Origin O = (u0, v0) to the top-left (0,0) pixel. We further introduce a new parameter ku and kv that determines the scale of the pixels in both dimensions. We get the following u,v coordinates:

u = u0 + ku * x and v = v0 + kv * y

In combination with the previous results, we get

u = u0 + ku * fXC / ZC and v = v0 + kv * fYC / ZC

We can now bring the point p = (u,v) into the previously seen homogeneous coordinates: p = (u,v) = λ(u,v,1). Expressed in matrix form, we get

![4_camera_to_pixel_coordinates](https://user-images.githubusercontent.com/60929939/124548328-255cd680-de60-11eb-8cec-a7c5b8ca2194.png)

Figure 4: Camera Coordinates -> Image Coordinates -> Pixel Coordinates.

This matrix is called K, the Matrix of intrinsic parameters, or calibration matrix. It defines the internal properties of the Camera: It's focal length and the resolution, as well as the scewing factors k, which can be assumed to be 1 for modern cameras with good build quality.
Given an image resolution of 640x480 Pixels and a focal length of 210 pixels, we get a matrix of intrisics:

[[ f   0  u0 ],
 [0    f  v0 ],
 [0    0  1  ]]
or, with numbers:

[[210  0  320],
 [0   210 240],
 [0    0   1 ]]
 
 ##### From World to Pixel Coordinates
 We can finally bring it all together by combining the matrix of intrinsics and extrinsics.
 
 ![5_world_to_pixel_coordinates](https://user-images.githubusercontent.com/60929939/124548509-6654eb00-de60-11eb-80e5-dd293a5d69ff.png)
 
 Figure 5: World Coordinates -> Pixel Coordinates. The matrix K[R,t] is also known as the Projection Matrix M.
 
##### Normalizing the Image Coordinates
When we work with different cameras with different intrinsics it is handy to have a representation which is independent of those intrinsics. We have used K to get from the camera frame into the image frame. Then we have removed the depth by removing λ. As a result we got the 2D coordinated u and v. Now we go back to the camera frame by inverting the intrinsic dependent transformation K with K-1. As a result we are in the camera frame but with normalized depth ZC=1.

Since K-1 is just

[[1/f  0   -u0 ],
 [ 0  1/f  -v0 ],
 [ 0   0    1  ]]
We can create a normalized system with multiplying (u, v, 1) with K-1.

![6_normalized_image_coordinates](https://user-images.githubusercontent.com/60929939/124548643-93a19900-de60-11eb-9231-bf06d7cde3cd.png)


Figure 6: Normalized Image Coordinates.
with  𝑢¯=𝑢𝑓−𝑢0  and  𝑣¯=𝑣𝑓−𝑣0

##### Python implementation
Now let's start with a simple example in python that demonstrates the conversion from given world coordinates to pixel coordinates (u, v), given the cameras intrinsics and cameras 3D position relative to the world origin (0, 0, 0). The position is given by a rotation matrix R and a translation vector t, while the cameras intrinsic is given as a matrix K.

We are going through three steps:

- Map the world point PW (XW, YW, ZW) into the camera frame PC (XC, YC, ZC)
- Project the point into the image plane to get normalized coordinates p = (x,y) = (X/Z, Y/Z)
- Apply lens distortion to p to get distorted normalized coordinates p_dist = (u,v) = (1 + kx * r^4 + ky * r^2)(x, y) with r^2 = (x^2 + y^2)
- Convert the normalized coordinates p_dist to get discretized pixel coordinates (u,v).

At first, we will not take lens distortion into account. We'll see later in this article how to fix lens distortion.
 

