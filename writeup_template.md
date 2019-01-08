# **Finding Lane Lines on the Road** 

## Writeup Template

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

As a first important note, I did this project with support provided in following video given in Project Instructions section: https://www.youtube.com/watch?v=hnXkCiM2RSg&feature=youtu.be (Self-Driving Car Project Q&A | Finding Lane Lines) which I found very helpful.

My line detection pipeline image-wise consists of following steps:
1. Loading the image (or in case of video recieving the image as input to the process_image() function.
2. Converting image to grayscale using predefined grayscale() function.
3. Applying gaussian blur to the grayscale image with kernel of size 3.
[output_gray_blursolidWhiteRight]: ./image_outputs/output_gray_blursolidWhiteRight.png
4. Applying the canny edge detection to the blurred grayscale image. Color tresholds for canny are 50(minVal) and 150 (maxVal).
[output_gray_blur_edges_solidWhiteRight]: ./image_outputs/output_gray_blur_edges_solidWhiteRight.png
5. Determining 4 points for defining region of interest, where road lines are expected to appear in the image.
[output_gray_blur_edges_ROIsolidWhiteRight]: ./image_outputs/output_gray_blur_edges_ROIsolidWhiteRight.png
6.a) Applying hough_lines() - probabilistic Hough transform for detecting road lines in given region of interest. Parameters: image, 1 (rho), np.pi/180 (theta), 15 (threshold), 15 (min_line_length), 10 (max_line_gap).
[output_gray_blur_edges_ROI_lines_solidWhiteRight]: ./image_outputs/output_gray_blur_edges_ROI_lines_solidWhiteRight.png
6.b) Hough_lines() calls draw_lines() which plots out single line for each side of the road instead of detected line chunks.
7. weighted_img() plots detected lines over original image.
[solid_lines_detected]: ./image_outputs/solid_lines_detected.png

Steps 1-7 are repeated for each frame in given videos.

I modified the draw_lines() function in following way:
1. For each line segment detected by probabilistic Hough transform slope and center of the segment is calculated. Acceptable range for the slope is given with purpose to exclude very horisontal and vertical lines. All lines exceeding the given range are not taken into consideration. (-1 < slope < -0.3 for left lines, 0.3 < slope < 1 for right lines).
2. Average slope for left and right lanes is calculated, along with average center of the lanes.
3. Slope and one point of individual left and right lines are known. Using this information start and end point of each line is calculated, with giving the input of y values (approximate middle and bottom of the image). This gives 4 points - start and end point for left line, and start and end point for right line.
4. In case no (left or right) line is detected in a frame, values are set to 0 to prevent algorithm crash. (Point for improvement)
5. Two lines are given as output of the draw_lines() function.
 

### 2. Identify potential shortcomings with your current pipeline

Potential shortcomings:

1. Parameters are adapted to specific situations - only streight lines can be detected.
2. Detection of the lines is slightly inconsistent from frame to frame which results in flickering of the detected lines in video.
3. Optimised tresholds for "very steep and vertical" slope cutoffs are unknown - one wrong detection despite implemented cutoffs. 
[left_line_detected_wrong]: ./image_outputs/left_line_detected_wrong.png


### 3. Suggest possible improvements to your pipeline

Possible improvements:

1. To prevent line detection flickering, history of detection in previous frames could be included. 
2. Better parameter tuning of canny edge detection, hough transform and regions of interest could be done in bigger dataset to aviod flickering. Although this is also time consuming.
3. Implementation of curved line detection could be done.
4. Improvement of draw_lines function could also be done. If there is no line detected but only couple of pixels, they are not taken into consideration since their slope could have extreme values. But these points can also show tendency where road line will be moving next.
