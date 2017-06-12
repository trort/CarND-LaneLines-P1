# **Finding Lane Lines on the Road**

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


---

### Reflection

### 0. Environment

This project is using the conda environment specified [here](https://github.com/udacity/CarND-Term1-Starter-Kit/blob/master/README.md), the version last update on Mar 23th, 2017. Many libraries have been updated since then, especially Tensorflow which updated from 0.12 to 1.0 with tons of API changed.

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. The overall pipeline looks as:

1. Transform to a grayscale graph: this is because the lane markers, either white or yellow, will in general be lighter than the road color when transformed to grayscale.
2. Gaussian blur: to remove the shape edges caused by camera noise so we can focus on the real edges of different colors.
3. Canny transform: calculates the derivative amplitude of each pixel so that the edges are highlighted.
4. Area of interest mask: there are lots of edges obversely unrelated to the lane lines. They should be removed so it is easier to identify the lane lines.
5. Hough transform: lane lines are expected to be long and straight, corresponding to a light spot on the Hough transformed graph.

The original `draw_lines()` function (renamed to `draw_line_segments()`) would highlight the lane line segments detected by the above pipeline.
In order to draw a single continuous line for the left and right lanes, I modified the function to `draw_lines()`. It first separates possible left lane line segments from the right ones, and filters away segments that are not likely to be parallel to the lane line. Then, using the weighted average of the polar coordinated line parameters of the segments, the line parameters of the continuous lane lines are determined.

### 2. Identify potential shortcomings with your current pipeline

The test graphs and videos here represents situations that are easy to detect the lanes. The real-world problem can be more challenging. Here are some potential shortcomings of the current algorithm.

1. The camera may be mounted differently on each car. This would mess up the definition of the mask region.

2. There might be additional visible lines on the ground that can be confused with the lane lines.

3. Due to weather or wear-off, the lane mark edges can be hard to detect.

4. The shade of other objects such as the trees might be mistreated as lane lines.

5. What if the marker is covered by another car changing lanes?


### 3. Suggest possible improvements to your pipeline

The current version of the pipeline only works well on the test images/videos with lane lines that are straight and clearly marked, not very well on the challenge video. Here are some possible improvements:

* To adapt to curved lane lines, an improved Hough transform to a three-parameter space might work assuming constant curvature.

* To better detect the lane markers, the grayscale graph can be rescaled to increase contrast in the area of interest. Here for the challenge video, I am using the R channel data as the grayscale image, and it turns out to be better than simple grayscale.

* In a continuous video, the lane line is not likely to have a sudden change from one frame to the next frame. So the lane detected from previous frames should be considered and the lines in the current frame should be only change slightly from the last few frames.
