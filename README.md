# Lane Detection and Crack Segmentation
This project focuses on implementing a basic lane departure warning system using computer vision techniques. The system aims to assist drivers by detecting road lanes and warning about potential deviations. Additionally, the project explores crack detection using active contour models. The project is divided into four main steps:

## Part 1: Understanding and Configuring Hough Line Transform Parameters
Introduce the parameters of cv2.HoughLinesP, such as threshold, minimum line length, and maximum line gap.
Explain the impact of each parameter on the output and how this method differs from the standard Hough Transform.

## Part 2: Lane Detection Implementation

<img style="width:700px" src="https://github.com/user-attachments/assets/cc86117d-b90a-4806-855f-5afa2366e96a">

### Canny Edge Detection:
Load an image from the AAIC dataset (img1.jpg).
Apply edge detection using the Canny operator. Fine-tune its parameters to optimize the result and use smoothing filters to reduce noise.
### Region of Interest Masking:
Limit the search area for lanes by creating a triangular mask that focuses on the road region, using cv2.fillPoly.
### Line Detection:
Use cv2.HoughLinesP to detect lines within the masked region and display the results.
### Line Averaging:
Refine detected lines by averaging (weighted) the right and left lane lines separately. Extend the averaged lines across the image based on slope to ensure continuity.

## Part 3: Video Processing

Apply the lane detection process to videos (vid1 and vid2).
Adjust parameters frame-by-frame to maintain line continuity and reduce noise. Implement a smoothing technique across frames to stabilize the detected lines.

## Part 4: Crack Segmentation Using Active Contour Models
Use active contour models to segment cracks in images (img2 and img3).
Tune model parameters to improve segmentation accuracy, especially for thin, elongated cracks.
Discuss limitations of using active contour models for crack detection, particularly regarding their sensitivity to narrow and stretched structures.


# Methology

# Content
- Table of Contents
  * [prt1](#Part-1) [Understanding and Configuring the Hough Line Transform]
  * [prt2](#Part-2) [Lane Detection Implementation]
  * [prt3](#Part-3) [Lane Detection in Video]
  * [prt4](#Part-4) [Crack Segmentation with Active Contour Models]
 
  
# Part 1
I started by diving into the Hough Line Transform, specifically its probabilistic version (cv2.HoughLinesP()), which is much more efficient than the standard one. It looks for line segments instead of whole lines, making it more adaptable and precise. The function parameters took some time to fully understand:

* dst is simply the output from an edge detector, which needs to be a grayscale image.
* lines stores the detected line segments as (x1, y1, x2, y2) coordinates.
* rho and theta control the resolution for detecting lines. I kept them at 1 pixel and 1 degree, respectively, for this task.
*  is the minimum number of intersections needed to consider something a line.
* minLineLength and maxLineGap were crucial for fine-tuning—these control the minimum length of a line and the maximum gap allowed between segments.
The standard Hough Transform finds longer, more continuous lines, but I found that the probabilistic version is more flexible and better at picking up on the smaller segments I needed.

# Part 2
### (a) Smoothing and Edge Detection
I started by loading the input image and applied two different filters:

* Gaussian Blur helped in reducing noise by smoothing out the image.
* Bilateral Filter was more interesting because it smoothed the image while preserving edges, which is exactly what I needed.
Once the image was smoothed, I applied the Canny edge detector. Getting the parameters right for Canny was tricky since I had to balance between detecting enough edges and avoiding too much noise.

### Results:

<img style="width:500px" src="https://github.com/user-attachments/assets/0ac97de1-ffa7-4456-95e5-f00f8587ccf9"> <br>
<img style="width:500px" src="https://github.com/user-attachments/assets/a56fa540-b120-4486-affc-cb974440d4c1"> <br>
<img style="width:500px" src="https://github.com/user-attachments/assets/1115ce50-d66d-4e57-8d57-fb90f308e463">


### (b) Creating a Region of Interest (ROI)
Next, I focused on detecting lanes in a specific region. I knew that lanes typically appear in a triangular area from the driver’s perspective. So, I used the following approach:

* I selected a point roughly in the middle of the image to represent a vanishing point.
* I connected this point to the bottom corners of the image, forming a triangle.
* Using cv2.fillPoly(), I created a mask to isolate this triangular area and applied it to the image.

### Results:

<img style="width:500px" src="https://github.com/user-attachments/assets/f8f4c4ea-0dc5-413b-a805-f635270d7b24">

### (c) Line Detection with Hough Transform
With the ROI in place, I used the Hough Line Transform again but now on the masked image. The challenge here was fine-tuning the parameters to get clear, continuous lane lines. The initial results weren’t perfect, but after some tweaking, the output was solid.

### Results:

<img style="width:500px" src="https://github.com/user-attachments/assets/ad3d3291-ed5f-4553-9945-f3cfbad71a53">

### (d) Averaging and Extending the Lines
One issue I faced was that the detected lines were often broken up. To fix this, I:

* Grouped lines based on their slopes into left and right lane lines.
* Calculated a weighted average for each group to create a single, more stable line for each side.
* Extended these averaged lines to cover the entire visible lane area by using their slopes to guide the extension.

### Results:

<img style="width:500px" src="https://github.com/user-attachments/assets/8775cafd-47fc-43a8-95cb-7abf42107855">
<img style="width:500px" src="https://github.com/user-attachments/assets/49946f25-2406-4739-9e5b-9b2f50afb23d">

# Part 3

For video processing, I used OpenCV’s cv2.VideoCapture() to read frames. Each frame was processed the same way as the image in Step 2. However, I added a layer of consistency between frames to keep the detected lines stable:

* I gave more weight (70%) to the previous frame’s lines and 30% to the current frame’s lines. This simple weighted average helped smooth out any sudden changes and made the lines look more natural in the final video.

### Results:

Video 1:

<img style="width:500px" src="https://github.com/user-attachments/assets/5c376439-742a-476c-b4be-53d317982bd9">

Video 2:

<img style="width:500px" src="https://github.com/user-attachments/assets/2393335c-f78e-4042-95e9-b636999e590c">

# Part 4

The second part of the project involved using active contour models (or snakes) for crack detection. This was a bit different but really fascinating. The key parameters for the active_contour function were:

* Alpha and Beta, which control how elastic and smooth the contour is.
* w_line and w_edge, which determine whether the contour is attracted to brightness or edges.
* Gamma, max_px_move, and other parameters that control the optimization process and how much the contour moves in each iteration.
### Improving Results
The basic contour model didn’t work well right away, especially for the thin, irregular shapes of cracks. So, I had to refine the process:

* Edge Detection: I used Canny again to get a good starting point.
* Noise Reduction: Gaussian blur helped here to remove small noise artifacts.
* Crack Highlighting: I used cv2.inRange() to isolate potential crack areas, combining these with the Canny edges.
* Morphological Closing: This helped connect broken crack segments and fill gaps.
* Active Contour: Finally, I applied the contour model with carefully tuned parameters.

### Results:

Image 1:

<img style="width:500px" src="https://github.com/user-attachments/assets/73040f26-a221-480b-8c48-9171d3184513">
<img style="width:500px" src="https://github.com/user-attachments/assets/6cd4f3d7-8a3b-42aa-9fe0-a4785faa7533">

Image 2:

<img style="width:500px" src="https://github.com/user-attachments/assets/a160201f-806f-41c0-8079-e917a11b8667">
<img style="width:500px" src="https://github.com/user-attachments/assets/cfbcf70e-d2d6-4cd8-b11a-01cd017005ff">

### Challenges
Even after all this, there were still limitations. The active contour model struggled with cracks because they’re so irregular in shape and often occur in noisy or uneven lighting conditions. It was clear that more advanced techniques or additional preprocessing would be needed for perfect results.


