# **Finding Lane Lines on the Road** 

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:

* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./images/bins.png "Grayscale"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 5 steps. First, I converted the images to grayscale, then I applied Gaussian Blur and Canny edge detection algorithm. Resulting image was fed to Hough Line detection. And finally I drew these lines over the initial image and returned it back.

For grouping lines and leaving only one line for left and right lane at first I tried the following:

Walk through all the lines, use linear line equation (`y = k*x + b`) to calculate slope (`k`). Then group positive and negative slope lines into two groups. And then calculate everage `k` and `b` for each group and draw those two lines. Important note that this method does not support vertical lines (you cannon express vertical line using linear equation).

This worked, however it was too sensitive to occasional extra line detected, which was not a part of the lane. So I've added the following improvements:

- Ignore horizontal and short lines (`abs(y2 - y1) < 10`)
- For each slope group (positive and negative) add splitting into buckets (for different `k` range), calculate how many lines fit into one bucket and keep only the ones in the largest bucket

Below I've added an illustration for the method of grouping lines with different slopes. We make bins out of two angle ranges and cound bin with the largest number of lines (they have red background in the image below).

![Line slope bins illustration][image1]


### 2. Identify potential shortcomings with your current pipeline

Resulting pipeline is very sensitive to lighting conditions on the road. One of the difficulties in the challenge task video was the fact that edge detection found extra lines looking at shadows on the road, while missing lane lines on bright sunlight. I solved this by tuning Canny edge detection and Hough Lines parameters, but this solution will not hold in real-life conditions, where there can be extra markings on the road, night/day conditions, rain and snow etc.

Another shortcoming might be with by my line grouping algorithm. It does not support vertical lines and simply ignores horizontal ones. Also its output could only be two lines (one with positive and one with negative slope), for right and left side of the lane. It will be useless for multiple lane detection.


### 3. Suggest possible improvements to your pipeline

We can make this pipeline more stable on multiple lightning and atmospheric conditions by tuning parameters for different cases (for sunny day, for rain, for night etc.), then finding lanes for each condition and keeping the best result.

Supporting other line slopes (horizontal and vertical) can theoretically be also useful. For this we need to change grouping algorithm to use Hough transformation line equation.

And the grouping algorithm itself might be improved. One improvement I thought of was keeping only parallel lines close together (which will be left and right side of each lane line) and discarding other lines. This might make lane detection more accurate.