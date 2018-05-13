# **Finding Lane Lines on the Road** 

---
## Objective

**Finding Lane Lines on the Road**

The goals / steps of this project are the following:
* Make a pipeline that finds lane lines on the road
* Reflect on your work in a written report


[//]: # (Image References)

[image1]: ./Results/OriginalImage.png "Original Image"

[edges]: ./Results/Edges1.png "Canny Edges"

[colorMask]: ./Results/ColorFilter.png "Color Filter"

[edgesMasked]: ./Results/EdgesMasked.png "Edges Masked"

[finalEdges]: ./Results/FinalEdges.png "Final Edges"

[lines]: ./Results/Lines.png "Lines"  

[finalLines]: ./Results/FinalLines.png "Final Lines"

---

### Reflection

### 1. Describe your pipeline. As part of the description, explain how you modified the draw_lines() function.

My pipeline consisted of 3 main steps. First I get the edges of the image using Canny Edge detection and applying a color mask to help isolate the lane lines. Next I use a Hough transform on the edges to extract just the lines. Finally I process the lines from the Hough transform to create the two lane lines, and draw them on the original image.

#### Getting Edges
I start by getting the edges of the whole image. I wanted to try to minimizing the amount I masked the image soley based on where things were located in the image. So instead of masking the image to only use a triangle around where the lane lines are expected to be I used color selection to mask the edges after they were created through Canny Edge detection. I did apply a mask to remove anything above a specified height, which is based on how high the projected lanes will be drawn on the image.

After the color mask, I applied a dialtion method then an erosion method in an attempt to imphasizse the lane lines and remove the unwanted edges.

Through the combination of masking the edges with color detection then dialting and eroding the remaining edges I feel that I did a pretty go job of extracting the road lines.

Below is an image of the starting picture:

![alt text][image1]

Next I have an image of the detected edges before any masks are applied

![alt text][edges]

Below is the image with the applied color mask, so you can see what the color mask does

![alt text][colorMask]

Below is the image after the masks are applied

![alt text][edgesMasked]

Finally, below is the final edges after dialationa and erosion

![alt text][FinalEdges]


#### Getting the Lines
With the edges I then use a Hough transform to try and only get the two lane lines.Below is the image of just the lines

![alt text][Lines]

#### Processing Lines

I loop through all the lines. I calculate the slope and intercept of the line. I first make sure the slope is in an expected range, then I categorize it to being a left lane line or a right lane line by checking what side of the image the average x position of the line falls in. This allows me to create two sets of lists a list of all the slopes and intercepts for each line, the left lane line and the right lane line. 

After going through all the lines I use the maximum height I want to draw each line to and the minimum height of the picture and draw a left and right lane line from the median slope and intercept of each line. Below is a picture of the final lines in red. The lines used for the left lane line are drawn in green, the liens used for the right lane are drawn in blue and the middle lane is in white

![alt text][FinalLines]


####Applying to Vidoe

Finally to try to prevent large lane changes, or an invalid lane recognition causing a huge issue. I created a list for the medium slope and intercept of each line. The list contains the last 5 median slope and intercept values and then an average of these 5 values are used to draw the lines in the video


### 2. Identify potential shortcomings with your current pipeline

The main issue I had was I was trying to identify the lines without requiring that the lines be in a specific region of the screen. This caused the color mask, Canny edge detection and Hough transforms to be more strict then if I used a specific region of where I expected the lines to be. So in the videos you can see some jumpy sections where I must be factoring in lines from shapes that are not the lines, but with the color mask and averaging the previous images, I am doing a decent job of maintaining the lines.

One potential shortcoming is that the color mask relies on the coloring of the vidoe to remain consistant. This obviously would not always be true, and I dont expect that this would work well at night without good lighting.

Another shortcoming is that this algorithm really only expects straightlines and as you can see from the challenge video does not do anything to try to detect or draw the curves in the road

The final shortcoming is that the pipeline seems rather slow to run. I beleive there is a lot of room for improvement in the processing time of the pipeline.

### 3. Suggest possible improvements to your pipeline

I think there are three main improvments that could be made.

The first improvement is in the color mask. The color detection is just fixed values, so changes in lighting could very negatively affect the ability of the pipeline to detect lanes. I think I could either try to use some additional techiniques to adapt the values of the color mask depending on some measurements of the overal image or sections of the image, or I could try to not rely on the color mask as much.

The second improvement would be in utilizing previous frames to help draw the line of the current frame. I am doing a very simple average of the previous 5 frame, but I think I could do a much better job of using the previous frames to intelligently decide what to use in the current frame. At a minimum I could weight the frames based on how long ago they appeared, but I could also try to add in a factor for how good the prediction of the lines is expected to be.


Finally I think creating the final line could be improved to not just be a linear fit, but to also handle cases where the road and lines curve


