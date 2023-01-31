# CaTSper Processing Steps: A Detailed Description

This document aims to provide a detailed description to the scientific basis of the processing steps involved in [CaTSper](https://github.com/CamTHz/catsper). A detailed in-line annotation of CaTSper's code is available here(**link to be inserted).

## Time Domain Analysis

The time delay $\Delta t$ is the extra time needed for the THz pulse to traverse through the sample thickness $H$, compared to the THz pulse traversing the same thickness in the reference measurement (air, refractive index $n_a = 1$). The effective refractive index $n_{eff}$ of the sample is thus calculated by

$$ n_{eff} = \frac{\Delta t c}{H} + 1 $$

where $c$ is the speed of light with a value of $3 \times 10^8$ ms $^{-1}$. $n_{eff}$ is calculated to four significant figures.

The time delay due to one internal reflection occurring $\Delta t_{1etl}$ is also considered. For one internal reflection, the THz pulse additionally travels through two times the sample thickness, compared to the original $\Delta t$. $\Delta t_{1etl}$ is thus calculated by

$$ \Delta t_{1etl} = \Delta t + \frac{2H n_{eff}}{c}$$

## Fourier Transform

### Setting Frequency Range and Spectral Resolution

<!-- stopband? -->

### Choice of Apodisation Function

### Dynamic Range

### Amplitude and Phase

Amplitude and phase data is obtained by unwrapping the frequency domain data. 
The built-in MATLAB ['unwrap'](https://uk.mathworks.com/help/matlab/ref/unwrap.html) function is adopted as it eliminates discontinuities between consecutive phases by adding multiples of $\pm 2 \pi$ until the difference is less than $\pi$.
Due to the high signal-to-noise ratio at 0.8 THz, it is set as the starting point for unwrapping phase to reduce errors. This is instrument specific and one can change the value accordingly by accessing the 'TDSunwrap' function in the [Catsper.m](https://github.com/CamTHz/catsper/blob/main/Catsper.m) code.
<!-- create this as an editable value on the app? then this sentence needs to be updated -->
Frequency domain data, that corresponds to frequencies greater than 0.8 THz, will be unwrapped in increasing values starting at 0.8 THz, and vice versa for data corresponding to frequencies less than 0.8 THz.
A straight line was fitted to unwrapped phase against frequency data from 0.05 to 0.4 THz. The intercept of the striaght line at 0 THz gives the phase offset. The phase offset is then applied to all phase data for correction.


## Frequency Domain Analysis

### Transmittance

### Absorption Coefficient

### Refractive Index

### Dielectric Constant
