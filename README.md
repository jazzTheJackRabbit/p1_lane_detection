# **Finding Lane Lines on the Road**

---

**Finding Lane Lines on the Road**

The objective of this project is to build a simple pipeline to understand the fundamentals of computer vision techniques that can be applied in the context of self-driving cars.



[//]: # (Image References)

[grayscale]: ./test_images_output/grayscale.jpg "Grayscale"
[gaussian_blur]: ./test_images_output/gaussian_blur.jpg "Gaussian blurred image"
[canny]: ./test_images_output/canny.jpg "Output from Canny Edge Detection"
[region_mask]: ./test_images_output/region_mask.jpg "Region Masked"
[hough]: ./test_images_output/hough.jpg "Lines from Hough Transformation"
[lane_lines]: ./test_images_output/lane_lines.jpg "Lane Lines"

---

### Reflection

### 1. Pipeline description.

My pipeline consists of the following steps:

1. The image is first converted into grayscale so that we only have one channel/2-dimensional matrix to work with and process.

  ![alt text][grayscale]

2. The image is then blurred using a kernel size of 5 for the gaussian blur process.

  ![alt text][gaussian_blur]

3. The next process is to detect the edges in the blurred-grayscale image. The Canny Edge detection algorithm is used with the low and high thresholds in a 1:3 ratio (with manual tuning I arrived at 50 for the low threshold and 150 for the high threshold).

  ![alt text][canny]

4. The next step of lane detection is to use prior-knowledge of where the car's lane would be and block out regions of the image (mask) that may not be directly useful (for the purpose of lane detection. However, these regions will be useful for detecting other cars in other lanes).
  * 60% of the top of the image is blocked out.
  * 10% of the left of the image is blocked out.
  * 5 % of the right of the image is blocked out.
  * Now, we have 4 points that form the vertices of the region mask.
  * Since the lane lines look like they may have an angle between 25 degrees and 45 degrees, we further improve the points moving the top-left point further in x direction based on a line drawn from the bottom-left vertex with an angle of 35 degrees. Similarly we set the top-right vertex based on a line drawn from the bottom-right vertex with an angle of 30 degrees.
  * Applying the mask eliminates most of the unwanted regions of the image and leaves just the region important for lane detection.

  ![alt text][region_mask]

5. Next, we transform the points in the blurred-grayscale image space into the hough space and retrieve lines in the image space.
  * For the hough transformation process, an important parameter is the number of sine-curves (of points) that intersect at specific points throughout the hough-space. We can eliminate a lot of detected lines, by setting a high threshold value (15).
  * Further, we set 10 as the minimum number of pixels required to form a line and also as the maximum gap between pixels of line segments that form an extended line.

  ![alt text][hough]

6. Once we detect the line segments from the image, we overlay the "lane lines/line segments" on the original image.

  ![alt text][lane_lines]

### 2. Potential shortcomings

The potential shortcomings of the current pipeline are as follows:
1. Only the raw lane line segments are detected.

2. Line segments are not extrapolated to one single lane line (left or right).

3. If the lane line seems curved in the image - it will not be detected (currently only uses line segments).

4. Dark regions of the lane (under trees) are not correctly detected as lane lines.


### 3. Possible improvements

Potential improvements to the pipeline are definitely required and a few ideas to do so, are as follows:
* We can detect left lanes lines and right lane lines by finding slopes of the line segments. If they have a positive slope they will be a left lane line and a negative slope will be a right lane line.

* We can use the points that form all of the line segments on each side of the lane and fit a line through these line-segment-end-points by minimizing the squared error and extend the fit line to start from bottom (y=image_height) of the image for each of the sides of the lane.

* For lane lines that look curved in the image - we may have to use the detected line segments and fit a curve to represent the lane.
