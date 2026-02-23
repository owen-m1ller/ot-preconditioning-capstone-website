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

Now, suppose that my boulders are not of equal size. Additionally, suppose I want the stick figure to have more detail. I can split my boulders into smaller rocks and move those rocks into the desired locations for the stick figure. Now, solving the optimal transport problem involves
1. Choosing which boulders to split.
2. Choosing how to split each boulder. For example, I could split a boulder in half and then split one of those halves into 9 equal sized pieces.
3. Choosing where to move each boulder and rock to create the stick figure.

### Sand Pile to Sand Castle

For a more general setting, imagine that my rocks have weathered into infinitely many pieces of sand in a pile. I want to find a way to reshape my sand pile into a sand castle, minimizing the total distance travelled across each sand particle.

(This is not actually the most general setting for optimal transport. This is the continuous case in one dimension. We can perform optimal transport in higher dimensions, or even between completely different spaces)

# Our Algorithms

# Our Method: Color Transfer

# Our Method: Video Blend
