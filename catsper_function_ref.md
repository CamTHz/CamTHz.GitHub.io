# CaTSper Processing Steps: A Detailed Description

This document aims to provide a detailed description to the scientific basis of the processing steps involved in [CaTSper](https://github.com/CamTHz/catsper). A detailed in-line annotation of CaTSper's code is available here(**link to be inserted).

## Time Domain Analysis

<!-- updateThickness function? -->

The time delay $\Delta t$ is the extra time needed for the THz pulse to traverse through the sample thickness $H$, compared to the THz pulse traversing the same thickness in the reference measurement (air, refractive index $n_a = 1$). The effective refractive index $n_{eff}$ of the sample is thus calculated by

$$ n_{eff} = \frac{c \Delta t}{H} + 1 $$

where $c$ is the speed of light with a value of $3 \times 10^8$ ms $^{-1}$. $n_{eff}$ is calculated to four significant figures.

The time delay due to one internal reflection occurring $\Delta t_{1etl}$ is also considered. For one internal reflection, the THz pulse additionally travels through two times the sample thickness, compared to the original $\Delta t$. $\Delta t_{1etl}$ is thus calculated by

$$ \Delta t_{1etl} = \Delta t + \frac{2H n_{eff}}{c}$$

## Fourier Transform

The following and the user-selected processing options in CaTSper apply to both the reference and sample data.

### Windowing

The time range in which relevant data needs to be Fourier transformed shall be specified. This can be done manually, or via the auto window function. The auto window centres the time range around the measured time delay with a symmetrical width that includes the time for first internal reflection, giving a resulting range of $\Delta t \pm (\Delta t_{1etl} - \Delta t)$.
<!-- double check auto window range -->

The selected data does not have a time range that extends over $(- \infty, \infty)$, and may not not have an integer number of periods (i.e. the start and end value of the data are different over the specified time range). This may lead to discontinuities in the subsequent Fourier transform results. To mitigate this situation, the selected data should be multiplied with apodisation functions, which gradually tends to zero at both ends. The following lists the provided apodisation function options in CaTSper:

- Boxcar: Heaviside step function. The values of the selected data are not changed and hence the function is suitable for transient data.
- [Bartlett](https://uk.mathworks.com/help/signal/ref/bartlett.html): Symmetrical triangular function with zero as the two end values. The value at the triangular peak positively scales with the length of the data. The function length is the same as the data length. It gives little ripple in the results obtained after Fourier transform. 
- [Blackman](https://uk.mathworks.com/help/signal/ref/blackman.html): Summation of three cosine terms. The function is created with a length greater than the data length by one, and the removing the last value from the function. It is suitable for applications where minimal leakage is required.
- [Hamming](https://uk.mathworks.com/help/signal/ref/hamming.html): Raised cosine. The two end values are not at zero. The function length is one greater than the data length. After Fourier transform, the sidelobes has a value lower than that of Hann, making Hamming suitable for optimising signal quality. 
- [Hann](https://uk.mathworks.com/help/signal/ref/hann.html): Raised cosine. The two end values are at zero. The function length is one greater than the data length. It is suitable for random signals and is good against spectral leakage.
- [Taylor](https://uk.mathworks.com/help/signal/ref/taylorwin.html): The MATLAB default settings are used. The coefficients in the function are not normalised. After Fourier transform, it gives a narrow mainlobe with sidelobe values that decrease monotonically. It is suitable for radar applications.
- [Triangular](https://uk.mathworks.com/help/signal/ref/triang.html): Symmetrical triangular function. If the length of the data has an odd value, the two end values are zero and the triangular peak is at one. If the length is instead even, the two end values are equal to the reciprocal of the length and a plateau instead of a triangular peak is resulted. The function length is the same as the data length.

### Fast Fourier Transform

The data is usually upsampled before Fourier transform. Upsampling approximates the situation when the signal is sampled at a higher rate. This is done by extending the data length, where the new length is determined by multipling the original length of data by a power of two. The exponent is specified by the user and should have a value greater than zero. The additional entries created beyond the original data length are filled with zeros.

The augmented data is then respectively discrete Fourier transformed into frequency domain via [fast Fourier transform (MATLAB built-in function)](https://uk.mathworks.com/help/matlab/ref/fft.html). A $N$-by-$N$ transformation matrix is multiplied with the data. $N$ takes the length of the augmented data, or the original data length if upsampling is not performed.

### Frequency Range and Spectral Resolution

The frequency domain data will be trimmed according to the user-specified frequency range, which should be set based on considerations such as the instrument's signal-to-noise ratio, the range that gives relevant features, etc. Values beyond the upper limit can be trimmed right after Fourier transform, but those below the lower limit have to be trimmed after [phase unwrapping](catsper_function_ref.md#amplitude-and-phase), as otherwise erroneous values may result.

The spectral resolution $v_{res}$ of the frequency-domain data is defined by 

$$ v_{res} = \frac{1}{t_{res} N} $$

where $t_{res}$ is the time resolution of the measured signal in time domain.

<!-- stopband? -->

### Amplitude and Phase

Amplitude and phase data is obtained by unwrapping the frequency domain data. 
The built-in MATLAB ['unwrap'](https://uk.mathworks.com/help/matlab/ref/unwrap.html) function is adopted as it eliminates discontinuities between consecutive phases by adding multiples of $\pm 2 \pi$ until the difference is less than $\pi$.
Due to the high signal-to-noise ratio at 0.8 THz, it is set as the starting point for unwrapping phase to reduce errors. This is instrument specific and one can change the value accordingly by accessing the 'TDSunwrap' function in the [Catsper.m](https://github.com/CamTHz/catsper/blob/main/Catsper.m) code.
<!-- create this as an editable value on the app? then this sentence needs to be updated -->
Frequency domain data, that corresponds to frequencies greater than 0.8 THz, will be unwrapped in increasing values starting at 0.8 THz, and vice versa for data corresponding to frequencies less than 0.8 THz.
A straight line was fitted to unwrapped phase against frequency data from 0.05 to 0.4 THz. The intercept of the striaght line at 0 THz gives the phase offset. The phase offset is then applied to all phase data for correction.


## Frequency Domain Analysis

### Dynamic Range

### Transmittance

### Absorption Coefficient

### Refractive Index

### Dielectric Constant

## Data Manipulation

### Finding Peaks

The MATLAB built-in function [findpeaks](https://uk.mathworks.com/help/signal/ref/findpeaks.html) is used to find peaks for a set of selected data (e.g. absorption coefficient $\alpha$) against another (e.g. frequency). A peak is defined such that it has a value greater than its adjacent neighbours or has a value of infinity. A minimum peak [prominence](https://uk.mathworks.com/help/signal/ug/prominence.html) can be specified such that only peaks with prominence greater than that will be recorded.

