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

<div class="ot-instructions">
  <figure>
    <img id="sequence-display" src="{{ '/assets/media/video-blend/displacement-interpolation/frame_0001.png' | relative_url }}" alt="Animation Frame">
    <figcaption>Walking Frame<span id="frame-counter">1</span></figcaption>
  </figure>
  <figure>
    <img id="sequence-display" src="{{ '/assets/media/video-blend/displacement-interpolation/frame_0025.png' | relative_url }}" alt="Animation Frame">
    <figcaption>Jogging Frame<span id="frame-counter">1</span></figcaption>
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

