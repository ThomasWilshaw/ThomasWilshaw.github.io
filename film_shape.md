# Exploring the shape of film

I'm currently working on a film project that was shot on Ektachrome S16mm film
and I have been trying ot figure out how to handle digital post production. The
directors inital plan was create a single master release print cut from the 
original negative which could then be projected to get the full effect and 
look of a film shot on reversal film. However this quickly became inpracticle
and it was decided we would do a complete scan of the film and do digital post production.

To the best of my knowledge there hasn't been a full feature film shot of 
Ektachrome and finished digitally (Poor Things shot several scenes on 35mm
Ektachrome and ingested that into their pipeline) so the question became how
do we handle grading am already fully formed image?

Due to budget constraints the film was scanned on a Filmfabriek HDS+ scanner 
and whilst this gave reasonably quality results the colour left a lot to be 
desired and seemd to be missing a lot of the colourfulness and punchness 
inherant in Ektachrome. As it turned out however, a friend happened to be 
experimenting modelling with modeeling film print papers and with a lot of help 
from him I was able to use his techniques to model the Ektachrome film purley 
from the data sheet.

The overall approach is to model the film as seen by the Standard Observer and
as seen by the scanner and then use the information to come up with a way to 
push the scanned files towards the "correct" look either visual and 
aesthetically or analytically. The results have so far been very promising and 
whilst I can't share any test images I will try to explain the process here.

## Denisty curves
We begin with the dye curves found in most film data sheets (the example below 
is Kodak Ektachrome 100D). These show the reposonse of the three seperate dyes
(Yellow, Magenta and Cyan) to light from the visible spectrum as well as a 
combined response to form a neutral density. IT is worth noting that the curves
in these data sheets are usually low quality/precision and only really give a
sense of the general shape and relationship between the three dyes. YOu can see 
that the magenta dye has a small hump at around 450nm which is probably the 
reulst of joining multiple data sets or a computation error somewhere. Neither
do they tell us anything about the reposnse of the dyes to wavelengths of light
outside the visible spectrum which could have an affect in certain conditions
(for example shots with a lot of UV light from atmospheric haze). However for 
the majority of genral cases this is plenty of data to be going with.

![Dye curves for Ektachrome 100D](/docs/assets/images/ektachrome_100D.jpg)

## Density steps
The curves given above are normalised response curves which means they have been
linearly scaled by some amount to give a neutral response and this implies that
an increase or decrese in the amount of light hitting the film casues a similar 
scaling of the curves. Our next step then in to generate a series of density 
steps from D 0.1 to the DMax of the film, which in the case of Ektachrome 100D 
is approximatley D 4.0 (also usually given in the data sheet). To get a better
overview of the entire reposnse of the film we also generate the dye responses
for the additive primaries (red, green and blue) and achromatic. These can be
created by adding the initial three curves together in various combinations
(cyan plus yellow makes green etc.). Below I have plotted the response of the 
seven dyes (real and synthetic) at a selction of density steps.

![Density steps of the cyan, magenta and yellow dyes](/docs/assets/images/CMY_density_steps.jpg)

![Density steps of the red, green and blue response](/docs/assets/images/RGB_density_steps.jpg)

![Density steps of the acrhomatic reposnse](/docs/assets/images/achromatic_density_steps.jpg)

## Transmitatnce steps
Whilst the density steps are themselves interesting we are more interested in
the transmittance of the film, how it affects light  projected through it. 
Calculating transmittance from density is relatively straight forward:

$$transmittance = \frac{1}{10^{density}}$$

If we apply the above formula to our data sets we get the follwoing charts:

![Transmittance steps of the cyan, magenta and yellow dyes.](/docs/assets/images/CMY_transmittance_steps.jpg)
![Transmittance steps of the generated red, green and blue dyes.](/docs/assets/images/RGB_transmittance_steps.jpg)
![Transmittacne stesp of the generated archomtic dye](/docs/assets/images/achromatic_transmittance_steps.jpg)

These are much more interestign and tell us alot about the colour fidelity of 
the film at different wavelenghts and densities. For example we can see that
the magenta dye varies it's transmittance a lot at different densities
implying we can expect a lot of colour fidelity in those hues, whereas the
yellows transmitte virtually everything from 560nm onwards and are mostly defined by
how much blue they contain.  We can also see that whilst the acrhomatic curve is
fairly flat at the top and bottom of the density range it will have much more of
a colour cast in the low to mid density range. 

## CIE xy plots
Now that we have a whole range of spectral data for the film at a variety of
density steps we can plot the hull of the films gamut on the CYE xy chart and
begin to get a sense of it's overall shape and specifc hue flights. The 
following charts were plotted in Nuke with Jed Smith's `nuke-colortools`[^jed_smith]
and have have all been calculated with the CIE Standard Observer CMFs and 
Illuminate E (uniform spectra) for simplicity. I have also included the Rec.709
gamut for refernce.

![Hue flights of Ektachrome 100D plotted on the CIE xy chart. Also includes Rec.709 gamut fro reference](/docs/assets/images/ektachrome_hue_flights_xyz.jpg)

I also moved all the calculations from Excel to Python which allowed me to
generate a mcuh denser data set which provided the following output.

![Hue flights of Ektachrome 100D plotted on the CIE xy chart. Also includes Rec.709 gamut fro reference](/docs/assets/images/ektachrome_hue_flights_xyz_dense_data_set.jpg)

![Animated 3D plot of the Ektachrome 100D CIE xyz data](/docs/assets/images/ektachrome_100D_ciexyz.gif)

And here are the generated colour andachomatic sweeps matrixed into Rec.709 for 
viewing.

![Ecktachrome sweep matrixewd to Rec.709](/docs/assets/images/ektachrome_1931_rec709_artificial.jpg)
![Ecktachrome achromatic sweep](/docs/assets/images/ektachrome_1931_achromatic_artificial.jpg)

## Verifying the model
As a quick test to make sure our modelling wasn't completely off we took some
spectral data form an IT8 chart that had been formed on the same film stock and
created the corresponding set of patches. These wre then plotted on the CIE xy 
chart and matched our synthetic data extreamly well.

![A simulated IT8 chart printed on Ektachrome](/docs/assets/images/IT8_rec709_artificial.jpg)
![Simulated IT8 chart plotted on the CIE xy chart](/docs/assets/images/IT8_CIE_xy.jpg) 


## Modelling the Scanner response
The next step is to repeat all of the above but use what I am calling the 
scanner observer rather than the CIE Standard Observer. To do that I took the 
spectral power distribution of the LEDs used in the scanner backlight and the
spectral response curve of the scanners camera and convolved them together to
form the scanner observer. I could then generate the same set sweeps as before
and compare them to the Ektachrome.

![The noramlised spectral response curves of the scanners camera](/docs/assets/images/scanner_normalised_response.jpg)
![The red, grenn and blue LED spectral power distributions](/docs/assets/images/scanner_led_spd.jpg)
![The scanner camera responses convolved with a synthetic achromatic backlight generated from the RGB LEDs](/docs/assets/images/scanner_led_convolved.jpg)

Below is the resultant sweep. You can seethe muted colours, particullalry
in the red and blue areas, that are apparent in the raw film scans.

![The generated sweep of Ektachrome as observed by the scanner.](/docs/assets/images/ektachrome_scanner_rec709_artificial.jpg)


Given the scanner has no gamut we decided to interpret the results as Rec.2020 
and work from there. Using a hue matrix, an inset matrix and some tetrahedral 
interpolation we were able to get a reasonable match between the two. I ran 
some test images through this and with a few tweaks got some very impressive 
results, a lot of the missing punchiness and colour contrast that was the 
original reason for choosing the film had returned. Hopefully this will make a 
good basis for the final grading of the film.



[^jed_smith]: https://github.com/jedypod/nuke-colortools/tree/master