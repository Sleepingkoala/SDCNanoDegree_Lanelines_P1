#**Finding Lane Lines on the Road** 

##Writeup Template

###*You can use this file as a template for your writeup if you want to submit it as a markdown file. But feel free to use some other method and submit a pdf if you prefer.*

---

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)
[image0]: ./illustration_images_output/whiteCarLaneSwitch.jpg
[image1]: ./illustration_images_output/grayscale.jpg "Grayscale"
[image2]: ./illustration_images_output/blur_gray.jpg
[image3]: ./illustration_images_output/edges.jpg
[image4]: ./illustration_images_output/masked_edges.jpg
[image5]: ./illustration_images_output/color_edges.jpg
[image6]: ./illustration_images_output/whiteCarLaneSwitchOutput.jpg
[image7]: ./illustration_images_output/challengeSnapShot.jpg

---

### Reflection

###1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My ordinary pipeline consisted of 5 steps with several helper functions, taking WhiteCarLaneSwitchOutput.jpg as an example: 
![alt text][image0]
1. Converting the images into grayscale. 
![alt text][image1]
2. Implementing Gaussian smoothing.
![alt text][image2]
3. Extracting edge information by Canny edge detection.
![alt text][image3]
4. Implementing the region of interest selection to calculate masked regions.
![alt text][image4]
5. Running Hough line transformation detection, drawing lanelines and overlaping this result with original image.
![alt text][image5]
![alt text][image6]

Then I extended this pipeline to all the test_images and the chosen test_images/solidwhiteRight.mp4. For now the annotated image and video output mostly have had detected laneline segments, even with fine-tuning canny and hough transform parameters. Please refer to test_videos_output/solidwhiteRight_beforeExtrapolation.mp4.


**To draw a continuous and precise line on the left and right lanes, respectively, I modified the functions draw_lines(), hough_lines() and process_image() into draw_lanelines_extrapolation(), hough_extrapolated_lanelines()and process_extrapolated_image(), respectively.** Here are the details about how it works.

draw_lanelines_extrapolation() is an improved version of draw_lines() for laneline extrapolation, which should be realized in 3 steps: 
1. Calculating all the slopes of detected houghlines and dividing them into two categories: left lanelines or right lanelines.
2. Excludiung the outliers: calculating the left/right laneline slope mean, and delect the lines which have large slope differences with slope mean with delete_lines(), respectively. 
3. Getting the final vertices of left/right lanelines: doing polyfit() to the result vertices from step 2, and drawing the extrapolated, precise, left/right lanelines.

Correspondingly, hough_extrapolated_lanelines() called draw_lanelines_extrapolation() for laneline detection. And process_extrapolated_image() called hough_extrapolated_lanelines() for video processing. If you want to check the effect of this improvement, please refer to test_videos_output/solidWhiteRight.mp4 and test_videos_output/solidYellowLeft.mp4.

**Besides, I tried my pipeline on test_videos/challenge.mp4, and the result seemed awful. So I took some modifications from draw_lanelines_extrapolation(), hough_challenge_lanelines() and process_extrapolated_image() into draw_challenge_lanelines(), hough_challenge_lanelines() and process_challenge_image(), respectively.** 

In draw_challenge_lanelines(), I changed the region of interest (section mask) based on a snapshot of the video. And hough_challenge_lanelines() called it for new laneline detection. Then **the most important modification was in process_challenge_images(), where the module of color selection was added in HSV colorspace**, because both yellow and white lanelines should be detected in the complex, illumination changing video.

Please refer to test_videos_output/challenge.mp4 to see the dynamic effect. Here is one snapshot to illustrate the result.
![alt text][image7]

###2. Identify potential shortcomings with your current pipeline

There will be several potential shortcomings in my present pipeline.

One is that all the parameters in canny, hough tranformation detectors, region selection and color selection are set empirically on some tests and adjustments. If the scenes change, these parameters may fail. Then lots of testing start again.

Another shortcoming is that the modules like edge detection, houghline transformation, and color selection are chosen to assemble manully. The pipeline cannot pick its module automatically according to various scenes.


###3. Suggest possible improvements to your pipeline

A possible improvement would be setting up an evaluation function. Based on this evaluation, the pipeline can adjust the parameters by itself.

Another potential improvement could be expoloring an end-to-end framework, the pipeline can choose its module based on simple descriptions about the tested scenes.