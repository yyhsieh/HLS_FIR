# HLS_FIR
HLS program for FIR
Date: 2020/12/22

Reference: https://github.com/KastnerRG/pp4fpgas

## Project Description
This project utilizes finite impulse response (FIR) filter to perform signal processing. It can filter the original signal. 

## HLS Optimization
original file: dft.c, dft_precompute.c, dft_unroll_inner2.c
the modified file by my own is ```dft_pipeline.c```

in the file ```dft_pipeline.c```
```c
	// Calculate each frequency domain sample iteratively
	for (i = 0; i < N; i += 1) {
		#pragma HLS pipeline II=32
		temp_real[i] = 0;
		temp_imag[i] = 0;

		// (2 * pi * i)/N
		w = (2.0 * 3.141592653589  / N) * (TEMP_TYPE)i;

		// Calculate the jth frequency sample sequentially
		for (j = 0; j < N; j += 1) {
			// Utilize HLS tool to calculate sine and cosine values
			c = cos(j * w);
			s = -sin(j * w);

			// Multiply the current phasor with the appropriate input sample and keep
			// running sum
			temp_real[i] += (sample_real[j] * c - sample_imag[j] * s);
			temp_imag[i] += (sample_real[j] * s + sample_imag[j] * c);
		}
	}

	// Perform an inplace DFT, i.e., copy result into the input arrays
	for (i = 0; i < N; i += 1) {
		#pragma HLS pipeline II=32
		sample_real[i] = temp_real[i];
		sample_imag[i] = temp_imag[i];
	}
}
```