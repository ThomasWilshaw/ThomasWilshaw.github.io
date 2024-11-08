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

# Notes from "Principles of Colour Sensitometry"

## 1. Introduction

The measurement of film must result in the quantitive knowledge of:
- the characteristics of materials in use
- the effects of laboratory handling
- the nature of the photographic images formed
- knowledge sufficiently accurate and comprehensive ensure efficient production 
  of best results

Photographic sensitometry is the science that specifies most of these
measurements and the ways of making them.

The principal concern of photographic sensitometry is the determination of a
quantitive relationship between photographic exposures and the images they
produce. However, it also deals with the entire process from subject to observer. 

- It examines the **kind** and **amount** of radiant energy the film **should**
and **does** receive.
- It tests and controls the sequence of chemical treatments that form in exposed
film.
- It measures the processed images determining the character and contents in
terms of what is most useful for the application.
- Provides means for observers to relate their quality judgements to the
physical characteristics of photographic material.

Colour Sensitometry is used in the motion picture film industry in two broad
fields:
1. Evaluation of techniques and materials
2. The control of the above.

Field one provides straightforward objective descriptions of colour film images
and indirectly describes the characteristics of film and processing operations
or printing systems. It also provides a common language to describe the quality
and performance of those systems. It can be used to compare photographic film
with:

- An ideal
- A practical aim point
- A tolerance limit
- Alternative products (other film stocks etc.)

There are many procedures but the common one is as follows:

1. Exposure imposed on a sample film
2. Sample is processed 
3. Resulting image is processed by some densitometry technique
4. The data is plotted and interpreted


## 2. Fundamentals of Sensitometry

Photographic Sensitometry:
> The science of measuring the sensitivity of photographic materials.

Original paper written by Ferdinand Hurter and Vero Driffield titled
*Photochemical Investigations and a New Method of Determination of the
Sensitivities of Photographic Plates* asked the following questions:

1. What is the relationship between exposure and opacity?
2. What is "correct" exposure?
3. How is emulsion speed expressed?
4. What is the effect of development?
5. How is the degree of development measured?
6. What is "correct" development?

### Concept of Density

Several methods can be used to measure silver deposits of processed
sensitometric strips it is logical to use one based on light transmission. The
value should go **up** as deposits become darker/ light transmission reduces. It
should also show similar differences as one perceives visually. Measuring
**density** (the log of reciprocal light transmittance) does all this.

Imagine sheet of material that allowed one tenth of light incident upon it to be
transmitted. If we stacked two layers it would only allow one hundredth of the
incident light to be transmitted and so on. Likewise, it would take ten and one
hundred units of light respectively to transmit exactly one unit of light. This
can be shown in the table below: 

|Number of layers | Output from Unity Input | Input for Unity Output | Log of column three|
|-----------------|-------------------------|------------------------|--------------------|
| 1               | 1/10                    | 10                     | 1                  |
| 2               | 1/100                   | 100                    | 2                  |
| 3               | 1/1000                  | 1000                   | 3                  |

Hurter and Driffield stated column one consisted of numbers proportional to the
quantity of developed silver (per unit are) in the silver deposit. Column two is
the **Transmission** (T) of the silver deposit (can be expressed as a decimal or
percentage too). The third column is the reciprocal of transmission and is
called **Opacity** (O). The fourth column is defined by Hurter and Driffield as
**Density** (D) and is the log of opacity. We use the reciprocal of transmission
so that density becomes larger as deposits become darker.


\\[\text{Factional Output} = \text{Transmission} = T\\]

\\[\text{Reciprocal of Fractional Output} = \frac{1}{T} = \text{Opacity} = O\\]

\\[\text{Log of Opacity} = \text{Density} = D\\]

\\[D = log(\frac{1}{T})\\]

