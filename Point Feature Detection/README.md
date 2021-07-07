### Point Feature Detection

### Feature Matching
To illustrate this concept, let's look at an image with some simple structures on it. When we slide this filter over the image, we'll get what's called a correlation map that shows how well the template fits the original image at every possible position. As we might expect, the best correlation point is found at the brightest pixel exactly where we extracted the original template.

![template_matching](https://user-images.githubusercontent.com/60929939/124685602-30694280-df04-11eb-8ee8-f6beef0e389f.png)

Figure 1: Template, Detected template and correlation map

To find the difference between the template and the image, we can use cross correlation, hence the name cross correlation map. In normalized cross correlation (NCC), we interpret pixels as vectors and take their cross product divided by the vectors lengths

![cross_correlation](https://user-images.githubusercontent.com/60929939/124685743-76260b00-df04-11eb-86c1-77e3ea8d4ae4.png)

Figure 2: Cross correlation formula

An alternative and much faster similarity measurement is the Sum of Squared Differences (SSD).

![ssd_moravec_corner_detector](https://user-images.githubusercontent.com/60929939/124685839-a372b900-df04-11eb-85eb-e30c35f59b69.png)

Figure 3: Sum of Squared Differences

Even cheaper than SSD is the third similarity measurement, called Sum of Absolute Differences (SAD).

![sum_of_absolute_differences](https://user-images.githubusercontent.com/60929939/124685931-c9985900-df04-11eb-8124-fc69a8246233.png)

Figure 4: Sum of Absolute Differences.

All measurements of these differences are susceptible to intensity variations. To account for this, we can subtract the average intensity of the two images (usually caused by an additive illumination change) from each image. We then obtain the so-called Zero-mean Normalized Cross-Corrleation (ZNCC), Zero-mean Sum of Squared Differences (ZSSD) and Zero-mean Sum of Absolute Differences (ZSAD).

![zero_mean_similarities](https://user-images.githubusercontent.com/60929939/124686111-1a0fb680-df05-11eb-993a-d9bd2ef00bd7.png)

Figure 5: Zero-mean similarities

The last error measurement we will have a look in this article is called Census Transform. CT maps each pixel in the template patch to either 1 (white) or 0 (black) depending whether it is brighter or darker than the central pixel. Then, it flattens the pixels clockwise into a vector called bit string. For a template with dimensions w * w, the resulting bit string vector would be of length w2 - 1 (minus the central pixel)

![census_transform](https://user-images.githubusercontent.com/60929939/124686263-5e9b5200-df05-11eb-9d37-2730dc76bdef.png)

##### Point Feature Extraction

For visual odometry, we want to find and extract features from an image that we can use as templates in another image. This way, we can match features between two images and calculate the relative motion between them.

##### Panorama Stitching

as an easy example.

1.) Find features in both images
2.) For each feature, find the corresponding pair in the other picture
3.) Overlap images such that the pairs are aligned as good as possible

![panorama_stitching](https://user-images.githubusercontent.com/60929939/124686543-eb461000-df05-11eb-8b60-15c29c33659a.png)

Figure 7: Panorama Stitching.

##### Harris/ Shi-Tomasi Corner Detector

Harris approximates the shift (dx, dy) using the first taylor expansion: I(x + dx, y + dy) = I(x, y) + Ix(x, y)dx + Iy(x, y)dy. The SSD of (dx, dy) can therefore be approximated by taking the sum of squared differences between the images derivative in x-direction (Ix) and the images derivative in y-direction (Iy). These derivatives Ix, Iy can simply be coputed using a normal Sobel filter.

![ssd_harris_corner_detector](https://user-images.githubusercontent.com/60929939/124686930-8f2fbb80-df06-11eb-885f-0f58d5677541.png)

Figure 9: SSD of Harris Corner Detector

![harris_corner_detector_matrix](https://user-images.githubusercontent.com/60929939/124686974-a40c4f00-df06-11eb-8dd9-a15893976fc8.png)

Figure 10: SSD of Harris in Matrix representation

To compute M, we use a Box Filter to sum up the patches I2x, I2y and IxIy. We could also use a box filter to give central pixels more weight. The outcome is a 2x2 matrix.

Recap that we can find the eigenvalues l1, l2 and eigenvectors x of a square matri by solving the following equation:

Ax =  𝜆 x

We can find the eigenvalues by solving det(A -  𝜆 I) = 0

Since we deal with a 2x2 matrix, we can easily determine the eigenvalues as and the eigenvectors in three steps:

![harris_corner_detector_eigenvector](https://user-images.githubusercontent.com/60929939/124687337-462c3700-df07-11eb-87d7-b1407925d8fd.png)

Figure 11: SSD of Harris in Matrix eigenvalues & eigenvectors

By solving the the last matrix calculations, we get two eigenvectors x, out of which we can construct a Matrix R = [x1|x2].

From the symmetric matrix M, we can do a decomposition as follows, with eigenvalues l1 and l2 and eigenvector matrix R:

![harris_corner_detector_matrix_decomposition](https://user-images.githubusercontent.com/60929939/124687381-5e03bb00-df07-11eb-8cde-3c101ca2b5c9.png)

Figure 12: SSD of Harris in Matrix decomposition

We can then visualize [dx dy] M [dx dy]' as an ellipse, where the ellipse axis length are exactly the eigenvalues and the axes orientation are the eigenvectors of M. The two eigenvalues will then identify the direction of largest and smalles change of SSD. The longer axis indicates the magnitude (eigenvalue) and direction (eigenvector) of the slowest change in SSD (slow change in contrast), while the shorter axis indicates the fastes change of SSD, so the strongest edge.

![elipses](https://user-images.githubusercontent.com/60929939/124687455-8390c480-df07-11eb-9f0d-0c4aa7ba6f69.png)

Figure 14: Elilpses of Eigenvalues.

![harris_corner_detector_eigenvalues](https://user-images.githubusercontent.com/60929939/124687467-87bce200-df07-11eb-874f-2c05d979c4e3.png)

Figure 13: SSD of Harris in Matrix Eigenvalues.

A corner can now be identified by sliding a patch over the image, calculating the eigenvalues of M and checking whether the minimum of the two eigenvalues of M is larger than a certain user-defined threshold. So, if R = min(l1, l2) > threshold, we have identified the patch to contain a corner. If both eigenvalues lay under the threshold, we have a flat region. If only one of the eigenvalues is high enough, the region contains a corner - a horizontal one if only the second eigenvalue l2 exceeds the threshold, a vertical one otherwise.

The function (not Matrix) R is called the cornerness function, the detector that uses this function is called Shi-Tomasi detector.

There's only one problem: Computing eigenvalues is expensive. We can therefore replace the cornerness-function by something simpler: R = l1 * l2 - k(l1 + l2)2 = det(M) - k * trace2(M) k is a magic number and empirically found to fit best if 0.04 <= k <= 0.15. A corner-detector using this function is called Harris detector.
