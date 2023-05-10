# "Correct" sRGB and Rec.709 Viewing

A lot of confusion has been generated surrounding the proper way to display 
Rec.709 and sRGB images. To clarify my own thoughts and hopefully help others
I've outlined my understanding below. Be warned, I am no expert here and if you
see errors please do inform me. 

To view images strictly per specification (assuming you're starting from open 
domain uniform tristimulus image) the short answer is:

- **sRGB:**
    1. Encode with the two part OETF
    2. Display on a monitor with a `2.2` power function EOTF in a dim surround

- **Rec.709**:
    1. Encode with the two part OETF
    2. Display on a monitor with a `2.4` power function (BT.1886) in a dark 
       surround

That is the correct way to view images. However as most images are not viewed
under ideal circumstances things get a little more complicated

## Implicit vs Explicit Management

Most modern imaging software uses what could be called *explicit* colour
management, every step of the workflow is (to quote Merriam-Webster) "fully
revealed or expressed without vagueness". Well in theory at least. Images are
generally decoded into some working space and then pushed through various
technical and creative transforms onto a display. This is often implemented via
OCIO for example.

Before this a much simpler system was used, an image would be encoded with a
Opto-Electronic Transfer Curve (OETF) such as Rec.709 and then displayed on a
monitor with a power law Electro-Optical Transfer Curve (EOTF). This OETF ->
EOTF system is not a no-op however and there is a slight mismatch between the
functions that has very beneficial side effects. Some people have labelled this 
set up as *implicit appearance management*. It is worth noting that with a 
little work this system can be implemented in a explicit system.

## Viewing Conditions

The effects of viewing conditions are many and complex but the essential point
is that the lighting conditions of the environment you are seeing an image in
have a large effect your perception of said image. It can be quite easily seen
for example that the same monitor that appears bright and contrasty in a dim
room looks dim and washed out in a bright one. The fact that we have a much
brighter reference point (say the sky outside) and that there is a lot more
light bouncing of the screen (viewing flare) causes the image to appear to have
much lower contrast.

This is where the OETF/EOTF mismatch comes in, the slight difference between
the two functions causes a small change in contrast and hue which neatly
adapts the image for the viewing conditions. 

The Rec.709 specification only defines an OETF and
does not define a corresponding EOTF or reference display. Historically skilled 
users would adjust the power function of the monitor to make the image appear
correct for the given viewing conditions. The sRGB specification however does 
very clearly define a EOTF and viewing conditions (one could even argue that an
image cannot be a true sRGB image until it is displayed on a correctly
calibrated monitor in a correctly illuminated room). The specification defines a
power law of `2.2` and a "dim" viewing context which approximates the expected 
environment one would view sRGB content in.

> ### Confusion
> The sRGB specification was approved in 1999 and it wasn't until 2011 that a
canonical EOTF was decided on for Rec.709 (BT.1886 with a `2.4` power function).
This is potentially where the confusion came from as in that intervening decade
vast amounts of of video was encoded in Rec.709 but viewed under a sRGB
compliant monitor. The sRGB spec in fact has a whole annex (Annex B) on
compatibility with Rec.709 and states that:
>
> *"This sRGB standard provides a clear and well-defined reference display for
ITU-R BT.709-3 for a dim viewing environment"*

It becomes apparent that if one wishes to "correctly" view Rec.709 images one 
must take into account the current (or expected) viewing conditions and select 
the appropriate EOTF, as summarised below:

| Surround | EOTF | Note |
| - | - | -|
| Dark | 2.4 | Official BT.1886|
| Dim | 2.2 | REC.709 pushed straight to an sRGB display. This is what a lot of people expect to see as it's what a lot of basic video software does |
| Average | 2.0 | Rarely used |
| Bright | 1.0| Rarely used |

The only correct way to view sRGB however is using a 2.2 EOTF as per the
specification

## Implementing An Implicit System Explicitly

As I mentioned earlier it is possible to simulate any of these EOTFs even if you
are stuck with, for example, sRGB monitor. Given we know our monitor is going to
force a `2.2` power on us we can calculate what power we need to encode our 
image with to generate a specific output power.

Lets say we want to view a Rec.709 signal under a BT.1886 EOTF as we are in a 
dark surround (say a grading suite) but we are stuck with an sRGB monitor and 
it's `2.2` EOTF. WE need to figure out what EOTF we need to apply to our Rec.709
image, that combined with our monitors `2.2` EOTF will equal `2.4` We end up
with the following equation:

![Equation for finding the power required to simulate BT.1886 on a sRGB monitor.
\\k^{x^{2.2}}=k^{2.4}\\
k^{2.2x}=k^{2.4} \\
2.2x=2.4\\
x=\frac{2.4}{2.2}\\
x\approx 1.09 ](/docs/assets/images/simulate_bt1886_on_srgb.svg)

Therefore if we encode out Rec.709 image with a power function of `1.09` and 
push it to a sRGB monitor, it will roughly colourimetrically match displaying 
the Rec.709 image on a true BT.1886 monitor.
