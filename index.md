# Results

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
1. The optimal matching between boulders in the smiley face and boulders in the stick figure (the plan or map).
2. The total distance boulders move when I rearrange them according to the optimal matching

### Moving Boulders to Rocks

Now, suppose that my boulders are not of equal size. Additionally, suppose I want the stick figure to have more detail. I can split my boulders into smaller rocks and move those rocks into the locations desired to draw the stick figure. Now, solving the optimal transport problem involves
1. Choosing which boulders to split.
2. Choosing how to split each boulder. For example, I could split a boulder in half and then split one of those halves into 9 equal sized pieces.
3. Choosing where to move each boulder and rock to create the stick figure.

### Sand Pile to Sand Castle

For a more general setting, imagine that my rocks have weathered into infinitely many pieces of sand in a pile. I want to find a way to reshape my sand pile into a sand castle, minimizing the total distance travelled across each sand particle.

This is not actually the most general setting for optimal transport. This is the continuous case in one or two dimensions. We can perform optimal transport in higher dimensions, or even between completely different spaces

# Our Algorithms

# Our Method: Color Transfer

# Our Method: Video Blend

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

Now let's say you want to blend two videos, starting the transition at fram 30 and ending the transition at frame 50. Our technique is as follows
1. For each corresponding frame (e.g. frame 30 of the walking video and frame 30 of the jogging video), find an optimal transport map between the two images
2. Replace frame 30 with a new image created by interpolating 1/20 of the way through the frame 30 map. Replace frame 31 with a new image created by interpolating 2/20 of the way through the frame 31 map. Continue interpolating by i/20 of the way through the optimal transport map corresponding to frame i.
3. Create a video by stitching together the first 29 frames of video 1, the 20 frames created by the process of step two, and the remaining frames from video 2

Our hope was that the space of maps would in a sense be continuous. We want a small change to the pairs of images (where the change is obtained by moving to the next frame) corresponds to a small change in a map between those pairs of images. If this were true, we would expect interpolating across different maps sequentially to achieve a smooth result. This appears to be the case:

<div class="gallery-grid">
  <video width="100%" controls>
    <source src="{{ '/assets/media/video-blend/walking-to-jogging.mp4' | relative_url }}" type="video/mp4">
    Your browser does not support the video tag.
  </video>
</div>

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

  playBtn.addEventListener('click', function() {

    if (vidWalk.paused) {
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
</script>
