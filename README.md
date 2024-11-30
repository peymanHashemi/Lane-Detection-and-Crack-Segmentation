# Lane Detection and Crack Segmentation
This project focuses on implementing a basic lane departure warning system using computer vision techniques. The system aims to assist drivers by detecting road lanes and warning about potential deviations. Additionally, the project explores crack detection using active contour models. The project is divided into four main steps:

## Step 1: Understanding and Configuring Hough Line Transform Parameters
Introduce the parameters of cv2.HoughLinesP, such as threshold, minimum line length, and maximum line gap.
Explain the impact of each parameter on the output and how this method differs from the standard Hough Transform.

## Step 2: Lane Detection Implementation

### Canny Edge Detection:
Load an image from the AAIC dataset (img1.jpg).
Apply edge detection using the Canny operator. Fine-tune its parameters to optimize the result and use smoothing filters to reduce noise.
### Region of Interest Masking:
Limit the search area for lanes by creating a triangular mask that focuses on the road region, using cv2.fillPoly.
### Line Detection:
Use cv2.HoughLinesP to detect lines within the masked region and display the results.
### Line Averaging:
Refine detected lines by averaging (weighted) the right and left lane lines separately. Extend the averaged lines across the image based on slope to ensure continuity.

## Step 3: Video Processing

Apply the lane detection process to videos (vid1 and vid2).
Adjust parameters frame-by-frame to maintain line continuity and reduce noise. Implement a smoothing technique across frames to stabilize the detected lines.

## Step 4: Crack Segmentation Using Active Contour Models
Use active contour models to segment cracks in images (img2 and img3).
Tune model parameters to improve segmentation accuracy, especially for thin, elongated cracks.
Discuss limitations of using active contour models for crack detection, particularly regarding their sensitivity to narrow and stretched structures.
