# Project Summary

Optimal transport is a natural distance metric between distributions with applications to geometric problems, including image and video processing. We explore how optimal transport preconditioners can improve color transfer fidelity and reduce the condition number of the Gibbs kernel. Additionally, we demonstrate a proof-of-concept for extending optimal transport displacement interpolation to a video blending technique.

# Optimal Transport

Fundamentally, optimal transport is a problem about how to move one shape into another by exerting the least energy. The problem is best understood through examples:

### Moving Boulders to Boulders
Suppose we have 12 boulders in the shape of a smiley face.  We want to roll these boulders so that they resemble a stick figure instead. The amount of energy I spend in total depends on which location I decide to move each boulder to. 

<div class="ot-instructions">
  <figure>
    <img src="{{ '/assets/media/ot-instruction/smile.png' | relative_url }}" alt="Boulders arranged as smile">
    <figcaption>Original Boulder Formation</figcaption>
  </figure>
  <figure>
    <img src="{{ '/assets/media/ot-instruction/stick-figure.png' | relative_url }}" alt="Boulders arranged as stick figure">
    <figcaption>Desired Boulder Formation</figcaption>
  </figure>
  <figure>
    <img src="{{ '/assets/media/ot-instruction/smile-stick-matching.png' | relative_url }}" alt="Matching between smile boulders and stick figure boulders">
    <figcaption>A Matching Between Formations</figcaption>
  </figure>
</div>

There are two valuable objects relating to optimal transport
1. The minimal total distance boulders need to move in order to reach the target shape
2. The one-to-one matching associating a boulder of the smiley face with a boulder of the stick figure. Specifically, we care about the matching that corresponds with the minimal total distance.

### Moving Boulders to Rocks

Now, suppose that the boulders are not of equal size. Additionally, suppose we want the stick figure to have more detail. We can split the boulders into smaller rocks and move those rocks into the locations desired to draw the stick figure. Now, solving the optimal transport problem involves
1. Choosing which boulders to split.
2. Choosing how to split each boulder. For example, we could split a boulder in half and then split one of those halves into nine equal sized rocks.
3. Choosing where to move each boulder and rock to create the stick figure.

### Sand Pile to Sand Castle

For a more general setting, imagine that my rocks have weathered into infinitely many sand particles in a pile. We want to find a way to reshape the sand pile into a sand castle, minimizing the total distance travelled by each sand particle.

This is not actually the most general setting for optimal transport. This is the continuous case in one or two dimensions. We can perform optimal transport in higher dimensions, or even between completely different spaces.

# Color Transfer

<div class="ot-instructions"> 
    <figure>
      <img src="{{ '/assets/media/color-transfer/sunset.jpg' | relative_url }}" alt="Color Image">
      <figcaption>Color Image</figcaption>
    </figure>

    <figure>
      <img src="{{ '/assets/media/color-transfer/penacony.png' | relative_url }}" alt="Content Image">
      <figcaption>Content Image</figcaption>
    </figure>

    <figure>
      <img src="{{ '/assets/media/color-transfer/penacony__to__sunset__precond_plain_sinkhorn_knn.png' | relative_url }}" alt="Resulting Image">
      <figcaption>Resulting Image</figcaption>
    </figure>
</div>

## An Overview of Color Transfer

Given two images, color transfer is the task of recoloring of the second image in the color scheme of the first. Optimal transport is an effective solution and can solve the problem with the following simplified steps:
1. Embed each pixel of image one into RGB color space, the space with three axes representing the red component of the component (0-255), the green component of the pixel (0-255), and the blue component of the pixel (0-255).
2. Embed each pixel of image two into RGB space.
3. Find an optimal transport plan between the point cloud of image one and the point cloud of image two in RGB space
4. Change the color of each pixel in image two to the color of the pixel from image one that it was paired with in the optimal transport plan

## Improving Color Transfer

Our project explored ways to improve both the quality and speed of color transfer solvers, primarily through the method of preconditioning.

### Preconditioning

A preconditioner is a preprocessing routine that makes a problem easier or faster to solve. Given two point clouds in color space (one for each image), our preprocessing routine is as follows:
1. Find the sample mean and covariance for each point cloud
2. Rotate and stretch the clouds so that they have the same covariance
3. Align the clouds so that they have the same sample mean

After running this preconditioning routine, we can run the optimal transport solver as usual and then transform each point cloud back to its original position. The optimal map obtained through from this procedure is guaranteed to be as optimal as the map obtained without the preconditioning step. Below we visualize the preconditioning routine (note that in reality, the two distributions would lie directly on top of each other after they have been transformed).

<div class="gallery-grid">
  <figure>
    <img src="{{ '/assets/media/color-transfer/precond-visual2.png' | relative_url }}" alt="Preconditioning Routine">
    <figcaption>Preconditioning Routine</figcaption>
  </figure>
</div>

Additionally, our results show that this preconditioning procedure improves
the condition number of the Gibbs matrix generated from the transport cost matrix when using the Sinkhorn algorithm to find an optimal transport map. This has two benefits
1. We can solve for an optimal transport map faster (the Sinkhorn algorithm requires fewer iterations)
2. We can lower the regularization used by the Sinkhorn solver, leading to a more exact solution

<div class="gallery-grid">
  <figure>
    <img src="{{ '/assets/media/color-transfer/precond-results.jpg' | relative_url }}" alt="Preconditioning Results">
    <figcpation>Sinkhorn results with and without preconditioning</figcaption>
  </figure>
</div>

### K-Nearest Neighbors Regressor

For a high-resolution image, it becomes infeasible to store a matrix of color to color distances for each pair of pixels in memory. Luckily, we can obtain a reasonable representation of the color palette of an image by sampling only a few thousand of its colors. The process for extending a color matching for a sample of colors to the entire image is as follows:

1. Sample 2000 pixels from each image
2. Find an optimal transport plan between the two sample color point clouds
3. For each pixel in the image we recolor, find the 10 colors that are closest in color to it that were present within the sample
4. Those 10 nearest colors are associated by the optimal transport map with 10 colors present in the other image. Recolor each pixel to the average of the 10 colors in the other image that it is associated with.


### Other techniques and results

We found superior empricial results by embedding the colors into LAB space rather than RGB space. LAB space has three channels. The L channel represents the lightness of the color and the A and B channels encode the color itself.

We accelerated the process of recoloring videos rather than images. Rather than applying the same color transfer problem to each frame, we performed color transfer between the colors of a reference image and a sample of the colors throughout all frames of the videos.

<div class="color-transfer-showcase" markdown="0">

  <div class="ot-instructions">
    <figure>
      <img src="{{ '/assets/media/color-transfer/mountains.png' | relative_url }}" alt="Color Reference Palette">
      <figcaption>Color Reference</figcaption>
    </figure>

    <figure>
      <video id="vid-target" src="{{ '/assets/media/color-transfer/waterfall.mp4' | relative_url }}" muted playsinline></video>
      <figcaption>Original Video</figcaption>
    </figure>

    <figure>
      <video id="vid-recolored" src="{{ '/assets/media/color-transfer/brown-wf.mp4' | relative_url }}" muted playsinline></video>
      <figcaption>Recolored Result</figcaption>
    </figure>
  </div>

  <div style="text-align: center; margin-top: 20px;">
    <button id="color-play-btn" style="padding: 10px 20px; cursor: pointer;">Play Comparison</button>
  </div>
</div>
<script>
  const colorPlayBtn = document.getElementById('color-play-btn');
  const vidTarget = document.getElementById('vid-target'); 
  const vidRecolored = document.getElementById('vid-recolored'); 

  colorPlayBtn.addEventListener('click', function() {

    if (vidRecolored.ended) {
      vidTarget.currentTime = 0;
      vidRecolored.currentTime = 0;

      vidTarget.play();
      vidRecolored.play();
      colorPlayBtn.innerText = "Pause Comparison";
    }
    else if (vidRecolored.paused) {
      vidTarget.play();
      vidRecolored.play();
      colorPlayBtn.innerText = "Pause Comparison";
    }
    else {
      vidTarget.pause();
      vidRecolored.pause();
      colorPlayBtn.innerText = "Play Comparison";
    }
  });

  vidRecolored.addEventListener('ended', function() {
    vidTarget.pause(); 
    colorPlayBtn.innerText = "Restart Comparison"; 
  });
</script>

<br>
# Video Blend

There is a commonly known method for using optimal transport to blend between two images. Our goal was to find a correlary of this method for blending between two videos. The image technique is as follows:
1. Convert the images you want to blend between to greyscale. The darker a pixel, the more mass it has (value between 0 and 255).
2. Normalize the images so that both have a total mass of 1
3. Find an optimal transport plan between the two images where the distance between two pixels is the euclidean distance
4. Interpolate along that plan to blend between the two images.

Below is a visualization for image blending:

<div class="image-references">
  <figure>
    <img id="start-frame" src="{{ '/assets/media/video-blend/displacement-interpolation/frame_0001.png' | relative_url }}" alt="Walking Image">
    <figcaption>Walking Image</figcaption>
  </figure>
  <figure>
    <img id="end-frame" src="{{ '/assets/media/video-blend/displacement-interpolation/frame_0029.png' | relative_url }}" alt="Jogging Image">
    <figcaption>Jogging Image</figcaption>
  </figure>
</div>

<div class="frame-slider-container" markdown="0">
  <figure>
    <img id="sequence-display" src="{{ '/assets/media/video-blend/displacement-interpolation/frame_0001.png' | relative_url }}" alt="Animation Frame">
    <figcaption>Frame <span id="frame-counter">1</span></figcaption>
  </figure>

  <input
    type="range"
    id="frame-slider"
    min="0"
    max="24"
    value="0"
    step="1"
  >
</div>
<script>
  const frames = [
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0001.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0002.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0003.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0004.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0005.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0006.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0007.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0008.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0009.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0010.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0011.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0012.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0013.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0014.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0015.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0016.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0017.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0018.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0019.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0020.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0021.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0022.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0023.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0024.png' | relative_url }}",
    "{{ '/assets/media/video-blend/displacement-interpolation/frame_0025.png' | relative_url }}",
  ];

  const slider = document.getElementById('frame-slider');
  const displayImage = document.getElementById('sequence-display');
  const frameCounter = document.getElementById('frame-counter');

  slider.addEventListener('input', function() {
    const frameIndex = parseInt(slider.value, 10);

    displayImage.src = frames[frameIndex];

    frameCounter.innerText = frameIndex + 1;
  });
</script>

We developed a technique to blend two videos, starting the transition at frame 30 and ending the transition at frame 50. Our technique is as follows
1. For each corresponding frame (e.g. frame 30 of the walking video and frame 30 of the jogging video), find an optimal transport map between the two images
2. Replace frame 31 with a new image created by interpolating 1/20 of the way through the frame 31 optimal transport map. Replace frame 32 with a new image created by interpolating 2/20 of the way through the frame 32 optimal transport map. Continue interpolating by i/20 of the way through the optimal transport map corresponding to frame 30 + i until i reaches 20.
3. Create a video by stitching together the first 30 frames of video 1, the 20 frames created by the process of step two, and the remaining frames from video 2

<div class="gallery-grid">
  <figure>
    <img src="{{ '/assets/media/video-blend/videoblend-diagram.jpg' | relative_url }}" alt="Video Blend Routine">
    <figcaption>Video Blend Routine</figcaption>
  </figure>
</div>

Our hope was that there would be smooth transitions between consecutive maps. We want a small change to the pairs of images (where the change is obtained by moving to the next frame) to correspond with a small change in the map between those pairs of images. If this were true, we would expect interpolating across different maps sequentially to achieve a smooth result. This appears to be the case:

<div class="video-sync-container" markdown="0">
  
  <div class="ot-instructions"> 
    <figure>
      <video id="vid-walk" src="{{ '/assets/media/video-blend/walking.mp4' | relative_url }}" muted playsinline></video>
      <figcaption>Walking</figcaption>
    </figure>
    
    <figure>
      <video id="vid-blend" src="{{ '/assets/media/video-blend/walking-to-jogging.mp4' | relative_url }}" muted playsinline></video>
      <figcaption>Optimal Transport Blend</figcaption>
    </figure>
    
    <figure>
      <video id="vid-jog" src="{{ '/assets/media/video-blend/jogging.mp4' | relative_url }}" muted playsinline></video>
      <figcaption>Jogging</figcaption>
    </figure>
  </div>

  <div style="text-align: center; margin-top: 20px;">
    <button id="master-play-btn" style="padding: 10px 20px; cursor: pointer;">Play Sequence</button>
  </div>

</div>

<script>
  const playBtn = document.getElementById('master-play-btn');
  const vidWalk = document.getElementById('vid-walk');
  const vidBlend = document.getElementById('vid-blend');
  const vidJog = document.getElementById('vid-jog');

  const offsetTime = 0.2;

  vidWalk.currentTime = offsetTime;
  vidBlend.currentTime = offsetTime;
  vidJog.currentTime = offsetTime;

  playBtn.addEventListener('click', function() {

    if (vidBlend.ended) {
      vidWalk.currentTime = offsetTime;
      vidBlend.currentTime = offsetTime;
      vidJog.currentTime = offsetTime;

      vidWalk.play();
      vidBlend.play();
      vidJog.play();
      playBtn.innerText = "Pause Sequence";
    }
    else if (vidBlend.paused) {
      vidWalk.play();
      vidBlend.play();
      vidJog.play();
      playBtn.innerText = "Pause Sequence";
    }
    else {
      vidWalk.pause();
      vidBlend.pause();
      vidJog.pause();
      playBtn.innerText = "Play Sequence";
    }
  });

  vidBlend.addEventListener('ended', function() {
    vidWalk.pause();
    vidJog.pause();
    playBtn.innerText = "Restart Sequence";
  });
</script>

<br>
### Additional Techniques

We used an algorithm based on K-Nearest Neighbors to find and remove the background of the video. For a given pixel, if that pixels values have been relatively constant across the last k frames, then the pixel is classified as a background pixel. Once the background is removed, no mass from the background is considered by the optimal transport solver.
