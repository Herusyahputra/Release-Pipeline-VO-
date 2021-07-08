### Stereo Vision
The basic principle of stereo vision is to recosntruct the 3D position of a point by intersecting its rays thouth its images in different views. For this we need the position of the camera so that we know the absolute world position of the ray. also we need to knwo which points of the different view images to correspond (point correspondence) In general the different the cameras image planes can be orientated independently from each other. However the problem becomes easier when the imageplanes are aligned and also when the same camera intrinsics can be applied to all images. Meaning the cameras are identical.

![3_stereo_cases](https://user-images.githubusercontent.com/60929939/124860628-89100c80-dfe4-11eb-8ab8-5706c2b86713.png)

Figure 1: Stereo simple and general case

To use stereo camera we need the extrinsic parameters like rotation and translation as well as the intrinsic parameters of each camera which are the focal length, optical center and radial disortion. These intrinsics we can get through the calibration methods.

𝑢𝑙−𝑢𝑟  is what we have previously introduced as disparity, the displacement of the same object on the different images in pixel coordinates.

![4_stereo_vision_simple](https://user-images.githubusercontent.com/60929939/124860900-1d7a6f00-dfe5-11eb-960b-2e270b991792.png)

Figure 2: Stereo Vision simple

For practical applications some questions may arise. For example what is optimal baseline distance? Because if the it is too large, then the minimum measurable depth increases and close object can not be searched for. If the baseline is too small then there is a large depth error.

![5_large_and_small_baseline](https://user-images.githubusercontent.com/60929939/124860988-4569d280-dfe5-11eb-8a92-40e3552593af.png)


### Correspondence Problem

![11_epipolar_line](https://user-images.githubusercontent.com/60929939/124861213-b3ae9500-dfe5-11eb-8073-ef50041f7887.png)

Figure 3: Epipolar Line

The plane that is span by the the two camera point and the 3D point is called epipolar plane. We can also define the epipolar line as the intersection of the epipolar plan with the camera plane. We call the constraint that the position of corresponding points have to lie in their epipolar line the epipolar constraint. Using this contraint we can reduce the region to search for a corresonding point to a 1D search on the epipolar line.

![12_epipolar_plane](https://user-images.githubusercontent.com/60929939/124861258-ca54ec00-dfe5-11eb-9709-8d3bcab53fd3.png)

Figure 4: Epipolar Plane

### Trinagulation

Triangulation describes the process of finding the 3D position of a point given known camera location and rotation and a set of corresponding image location of that point. The goal is to instersect multiple viual rays corresponding to the image and camera center locations. The point where these rays intersect is then the 3D location of the point.

#### Linear Approximation

![Linear Approximation](https://github.com/joelbarmettlerUZH/PyVisualOdometry/raw/master/img/chapter_8/6_linear_approximation.png)
*Figure 5: Linear Approximation [source](http://rpg.ifi.uzh.ch/docs/teaching/2019/07_multiple_view_geometry_1.pdf)*

Recalling some knowledge about cross (vector) product and dot product:
- the dot product of twh vectors which are perpendicular to each others is 0
- the cross product of two vectors that are parallel is 0

The cross product can also be expressed as a **skew-symmetric matrix**.

![Skew Symmetric Matrix](https://github.com/joelbarmettlerUZH/PyVisualOdometry/raw/master/img/chapter_8/7_skew_symmetric_matrix.png)
*Figure 6: Skew-Symmetric Matrix [source](http://rpg.ifi.uzh.ch/docs/teaching/2019/07_multiple_view_geometry_1.pdf)*

We can express the image location in terms of world-coordinates as a dot product. We express the 3D position of the point in terms of the world coordinates with origin in the camera position of the left camera. Thefore cross product of $p_i$ and $M_i$ describes the normal vector onto the epipolar plane. As a result the position vector for the 3D position of the point is perpendicular to that normal vector of the epipolar plane. As a result we can set the ebelow equations to 0.

![triangulation equation part1](https://github.com/joelbarmettlerUZH/PyVisualOdometry/raw/master/img/chapter_8/8_triangulation_equation_1.png)
![triangulation equation part2](https://github.com/joelbarmettlerUZH/PyVisualOdometry/raw/master/img/chapter_8/8_triangulation_equation_2.png)
*Figure 7: Triangulation [source](http://rpg.ifi.uzh.ch/docs/teaching/2019/07_multiple_view_geometry_1.pdf)*

In the general case it might happen that the rays of images do not intersect. In such a case the 3D position of the point is determined as the mid point of the shortest segment connecting the two rays.

![Shortest Segment Midpoint](https://github.com/joelbarmettlerUZH/PyVisualOdometry/raw/master/img/chapter_8/9_shortest_segment.png)
*Figure 8: Shortest Segment Midpoint [source](http://rpg.ifi.uzh.ch/docs/teaching/2019/07_multiple_view_geometry_1.pdf)*

#### Non-Linear Approximation

A more complex but more accurate description of the 3D position of the point is using the non-linear approximation.
Here we want to find the point $P$ such that is minimizes the **Sum of Squared Reprojection Errors (SSRE)**.

\begin{align*}
SSRE = {\lVert p_1- \pi_1(P) \rVert }^2 + {\lVert p_2- \pi_2(P) \rVert}^2
\end{align*}

where $\pi_i(P)$ is the projection of P onto the i-th camera plane and ${\lVert p_i- \pi_i(P) \rVert }^2$ is the **Reprojection Error**.

![Reprojection Error](https://github.com/joelbarmettlerUZH/PyVisualOdometry/raw/master/img/chapter_8/10_reprojection_error.png)
*Figure 10: Reprojection Error [source](http://rpg.ifi.uzh.ch/docs/teaching/2019/07_multiple_view_geometry_1.pdf)*
