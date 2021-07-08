##### Speeded Up Robust Features (SURF) Detector & Descriptor
Works similar to sift but is faster since it uses box filters instead of Difference of Gaussians, so the convolution is a bit faster and the resulting descriptor are smaller, e.g. the feature space easier to compare.

1.**Features from Accelerated Segment Test (FAST) Detector**
The FAST detector works differently than SIFT. It compares a circle of N (usually 16) pixel around the central pixel to the central pixel and marks them with either 1 (brighter) or 0 (darker). Only if we have all-1 pixels followed by all-0 ones, we expect the circle to contain a corner region. This descriptor is extremely fast.

2.**Binary Robust Independent Element Features (BRIEF) Descriptor**
After using FAST to find the feature locations, BRIEF is a high-speed patch descriptor. We sample randomly 256 pixel pairs in our patch and store in a vector of length 256 whether the second pixel was higher (1) or lower (0) than the first pixel. Once this pattern is generated randomly, it is reused for all other patches and images. To compare two such BREIF descriptors together, one can aply very fast Hamming distance (count the number of bits that are different) to compare two patches. Note that this descriptor is neither scale nor rotation invariant.

3.**Oriented FAST and Rotated BREIF (ORB) Descriptor**
We use FAST to detect the features, then use BRIEF descriptors but steered according to the keypoint orientation to make the whole thing rotation invariant. Furthermore, instead of using random pairs, ORB uses learned pairs by minimizing the correlation on a set of training patches.

4.**Binary Robust Invariant Scalable Keypoints (BRISK) Descriptor**
To be written

5.**Fast Retina Keypoint (FREAK) Descriptor**
To be written

6.**Learned Invariant Feature Transform (LIFT) Descriptor**
To be written

7.**Self-Supervised InterestPoint (SuperPoint) Detector and Descriptor**
To be written

##### Overview

![detector_descriptor_overvie_table](https://user-images.githubusercontent.com/60929939/124854674-46e1cd80-dfda-11eb-9a66-c528a6714af2.png)
