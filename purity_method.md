<script>
MathJax = {
  tex: {
    inlineMath: [['$', '$'], ['\\(', '\\)']]
  }
};
</script>
<script id="MathJax-script" async
  src="https://cdn.jsdelivr.net/npm/mathjax@3/es5/tex-chtml.js">
</script>

# Purity Technique

## Overview
The basic principle of this method is we increase the purity of an RGB triplet
by reducing `min(RGB)` whilst retaining the ratio between `min(RGB)`, `max(RGB)` 
and the middle RGB value (this middle or `other` value is usually in between the
min and max values but is sometimes equal to one or both of them). The process
is split into the following steps:

1. First we take our purity slider value and adjust it based on the chrominance
   of the pixel. The less chrominance a pixel has, the less we increase its 
   purity.
2. Next we separate out our RGB triplet into it's minimum, maximum and `other`
   value.
3. Now we actually increase the purity. We scale the `min` value down and then
   also bring the `other` value down so that it stays in the same relative place
   between the `min` and `max` values. We also introduce a `floor`value below which
   the min value cannot go. `max` is never touched.
4. Finally, we put our `min`, `max` and `other` values back into the correct R,
   G and B channels.

## Purity Slider Value
It would be tempting to simply scale all pixel values by the same amount, but
this leads to problems with near neutral, low chrominance, pixels like gradients
into coloured lights. Very small differences in the RGB triplet can be easily
exaggerated and the gradients can break down. 

The basic equation for increasing purity is `min(RGB)*scale` where a scale value
of `1` is no change and a scale value of `0` is maximum purity. From a users
point of view we want to scale to go from zero for no change to `n` for max
change. We also want to build in some kind of reduction in scaling based around
the chrominance. To do that I used the following equation:

\\[adjustedScale = \frac{1.0}{((1.0 - chroma) - 1.0) * (-1.0 * scale) + 1.0}\\]

Which can be viewed [here](https://www.desmos.com/calculator/f3hl6hm4pq) where
`x` is equal to the pixels chrominance and `y` is the adjusted scale factor.

![Purity slider curve](/docs/assets/images/purity_method/purity_slider_curve.jpg "Purity slider curve")

The first thing to note is that no matter what value of `s` is used, zero
chrominance is always pegged to a scale factor of `1`, or no change. Secondly as
chrominance increases the scale factor increases as a gently curving slope which
should hopefully maintain existing gradients in the image to some extent. The
curve also tails off and never reaches zero no matter what `s` value is used 
which provides a gentle approach to maximum purity. It is worth noting that the
curve starts to break down for `s` values below zero and whilst the method *does*
reduce purity in this case it can go a bit haywire as the curve asymptotes to 
plus/minus infinity and break the image.

## Managing min, max and other
To reiterate, `min` is equal to `min(RGB)`, `max` is equal to `max(RGB)` and 
`other` is whichever of RGB is left over. In most cases this is the middle value,
but sometimes it is equal to one or both of the others (for example in a 
neutral). These need careful handling to make sure they behave correctly in edge
cases. 

The most complicated part of this method from a code point of view is keeping
track of which RGB values are `min`, `max` and `other`, so they can be correctly 
recombined later on. Most of the example code is taken up with this and the
method used is quite brittle and liable to introduce bugs, there is probably a 
better way to do it.

## The Floor and Increasing the Purity
The first step here is to figure out how far the `other` value is between the
`min` and `max` values.

\\[ratio = \frac{other - min}{max - min} \\]

We now need to introduce another concept, that of the purity floor value. We
pass the function a value below which `min` should never go. This gives
us some way of ensuring the `min` value can't punch through the floor of the
image. To do this we scale the `min` value as follows:

\\[scaledMin = ((min -floor) * scale) + floor \\]

A simple multiply always scales around zero and by subtracting and then re
adding the floor value we can scale around that instead. This scaling on its
own can cause issues around bright areas of the image, they lose a lot more
brightness than one might want. In an attempt to get around this I have
experimented with further limiting the amount `min` can be scaled down by some
factor of the pixel's luminance, say the square of the luminance. This appears
to give satisfactory results but could be further experimented with.

In the example code I also clamp the upper limit of `min` to be `max` for when
we use this method to reduce the purity (a scale slider value of less than zero).

Finally, we move the `other` value up or down so that it retains its relative
value between `min` and `max`, this appears to retain the hue of the pixel.

\\[other = scaledMin + (max - scaledMin) * ratio \\]

## The Floor
Part of the reason for developing this approach was to build a floor value into
the purity mechanism. This allows for a post formation purity increase that
doesn't break a pre formation lift operation. To do this we take our pre
formation lift triplet (which gets added to the image in the open domain space)
and run just that triplet through our image formation pipeline. We then take
either the maximum or average of that output and use that as our floor value in
the purity function. This also works with a tinted lift.

The floor value can also be used without a lift component. If the purity
function is breaking the image by pushing some value too low one can pick a
floor value (either by eye or some statistical or image segmentation method)
and use that to limit the purity increase.


