# Polarity Glossary

List of terms and definitions

| Term         | Definition |
|-----------------------|------------|
| Max(R,G,B)   |The maximum value of an RGB triplet. `Max(0.1. 0.2, 0.3) = 0.3`|
| Min(R, G, B)  |THe minimum value of an RGB triplet. ALso equivelant ot the purity of that triplet|
|Purity|How close an RGB triplet is to the hull of it's colour space. `[0.0, 0.4, 0.6]` is maximally pure as the red component is on the hull. A minimally pure colour is an achromatic triplet (`R=G=B`) as all the components are equally far from the hull|
| Normalize    |Spread the image data to fill the range `0.0 - 1.0`. To do this subtract the lowest none zero pixel value from all pixels and then multiply all pixels by 1 divided by the max pixel value|
|Offset | The base value compnent of an RGB triplet. E.G. `[0.1, 0.2, 0.3]` has an offset of `[0.1, 0.1, 0.1]`. An RGB triplet can be "offset" up or down by a set amount. E.G. we can offset `[0.1, 0.2, 0.3]` by `+0.1` to get `[0.2, 0.3, 0.4]`|
|Gain|The reamining colour component of and RGB triplet after the offset has been removed. E.G. The gain component of `[0.1, 0.2, 0.3]` is `[0.0, 0.1, 0.2]`. A triplet can be gained by multiplying either component wise or all three components together (uniform)|
|Pictorial exposure|  Changing the exposure of the image post formation. Increasing the pictorial exposure leads to a fogging of the image, a totally black stimuli would have an offset|
|Stimuli Exposure|Adjusting the energy in front of the camera either by add/removing light or changing the exposure time/aperture. A fully absorbative surface would remain at zero energy. This has to happen pre image formation otherwise the contrast of the image will fundamentally change and break the image|
