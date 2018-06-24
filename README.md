# **Finding Lane Lines on the Road** 

---

**Readme: Finding Lane Lines on the Road**

In this project, I used computer vision technique and Python/OpenCV tools to find road lane lines in the images and video.

The following picture showing the detection results on the road lines:


[image1]: ./test_images_output/LaneDetection_whiteCarLaneSwitch.jpg

![Image of Detected Road Lines][image1]
---

### Pipeline of the road detections

### 1. Road lines detection from a picture

First to read image using imread() function from matplotlib package.

Secondly, it is noted that the color of road lines are in yellow or white color. In order to better distinguish the road line from other objects and road surface, I created a color filter to make the yellow and white color standout.

* select_white_yellow() funcStion:

    1. Convert color image from RGB to HLS color space. In the HLS color space, the yellow and white lines are really distinguished. As shown in the following picture:

         [image2]: ./DocImages/ImageinHLS.png
         ![Image in HLS color space][image2]

    2. The image in HLS color space is then filtered by yellow and white color masks by defining the color code's lower and upper limits. The image after being filtered by the color masks are like this:
    
        [image3]: ./DocImages/Yellow_Whilte_Filtered_Images.png
        ![Image filtered by yellow and while color mask][image3]
        
    3. The yellow and white color filtered image is the output of the select_white_yellow() function

Thirdly, convert the color image into greyscale and apply the GaussianBlur to the image, so to prepare the image for detecting the road lines. 

In order to detect the lines, Canny filter is applied to the blurred image, so that the lines with gradient within the set range is depicted in the Canny filtered image. Like the following:

[image4]: ./DocImages/CannyFiltered.png
![Image filtered by Gaussian Blur and Canny Filter][image4]

Then, I just picked up the region in the image showing the road lines, in order to have better estimation on the location and slope of the road lines. The following is the image after removing the dots and lines outside the road lines' possible region.

[image5]: ./DocImages/RegionalPlots.png
![Localized Road Line Image][image5]

One point to note is that, in order to make the program to fit image or video in different size and aspect ration, I used relative position setup for setting the region of interest. To set the ratio, I firstly set a region working for the image in size of 550x1000. For the images/video in other size/aspect ratio, the region setting would by resize based on the relative size of the image to 500x1000. 

In next step, I used 'HoughLinesP()' function to find the lines in the following regional road line image. The lines are sent to draw_lines for extrapolate the left and right road lines.

* draw_lines() function:
    
    1. The input of the draw_lines() function are images and lines segments detected by HoughLineP function. 
    2. Since the lines detected by HoughLineP function are small segment. I need to sort the lines in to two bins: left line bin and right line bin, by checking on the slope of the line segments. Also, in order to further avoid the misdetection of other objects instead of the road lines, I set the slope filters for left line bin and right line bin.
    3. The slope for left line should be positive, and the slope should be within [0.45, 0.75]. And the slope for right line should be negative, and the slope is within [-0.85, -0.6]. The lines with slopes outside those conditions will be filtered out.
    4. After classifying those line segments, the best fit for left line and right line are calculated based on the weighted average on filtered lines slope and position. The weight of each line are their length. Therefore, the best fit right/left road line are calculated as following:
            
           k(slope) = sum(slope of the line in left or right bin * length of the line) / sum(length of the line)
           x(position) = sum (interception of the line in left or right bin at y = 0 * length of the line) / sum(length of the line)
    5. Once the best fit left/right lines are calculated, draw the detected line on the original image.
    
The result of the road line detections are shown in the following:

[image6]: ./DocImages/ResultImage.png
![Road Line Detection Result][image6]

In the image, the green lines are those line segements detected by HoughLineP() functoin. The red lines are the final best fit road line detections.

Depends on the image source, the final image with line detection drawing is save in image or video format.

The following videos are real-time lane detections on white lane detection, yellow lane detections, and challenge lane detection with shadow and curvature.

##### Lane Detection White Lane

[![Lane Detection White Lane](http://img.youtube.com/vi/Ju52FIHjf4A/0.jpg)](http://www.youtube.com/watch?v=Ju52FIHjf4A "Lane Detection White Lane")

##### Lane Detection Yellow Lane

[![Lane Detection Yellow Lane](http://img.youtube.com/vi/cVyy7CjhWiE/0.jpg)](http://www.youtube.com/watch?v=cVyy7CjhWiE "Lane Detection Yellow Lane")

##### Lane Detection on Lane with Shadow

[![Lane Detection with Shadow](http://img.youtube.com/vi/Ju52FIHjf4A/0.jpg)](http://www.youtube.com/watch?v=Ju52FIHjf4A "Lane Detection with Shadow")

### 2. Potential shortcomings with your current pipeline

1. The stability of the road lines drawing will be a little fussy since I did not applying any smoothening function on slope/position change of drawn line from frame to frame. It is more obvious when the line is not solid, and the road is curve.
2. As the road surface color is changing, the road line detection may be affected.
3. Since the yellow and white color mask is applied, the road lines in other color may not work (which is a very rare case).
4. If there a car changes the lane, it may affect the line detections since the car's outline would be considered as the road line.



### 3. Suggest possible improvements to your pipeline


1. Add the smoothening function from frame to frame in the video to make the road line detection line look more smooth. 
2. Add the adaptive color mask for detecting road lines in other colors. 