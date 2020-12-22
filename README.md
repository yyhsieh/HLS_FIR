# HLS_FIR
HLS program for FIR
Date: 2020/12/22

Reference: https://github.com/KastnerRG/pp4fpgas

## Project Description
This project utilizes finite impulse response (FIR) filter to perform signal processing. It can filter the original signal. The each input signal will be multiplied with the corresonding taps and then summed together as an output value.

I add another pragma ```#pragma HLS pipeline``` into the original code at line 7.

## HLS Optimization
original file: ```block_fir.h```, ```block_fir.c```
the modified file by my own is ```block_fir_pipeline.c```

in the file ```block_fir_pipeline.c```
```c
void block_fir(int input[256], int output[256], int taps[NUM_TAPS],
							 int delay_line[NUM_TAPS]) {
	int i, j;
	for (j = 0; j < 256; j++) {
#pragma HLS pipeline
		int result = 0;
		for (i = NUM_TAPS - 1; i > 0; i--) {
#pragma HLS unroll
			delay_line[i] = delay_line[i - 1];
		}
		delay_line[0] = input[j];

		for (i = 0; i < NUM_TAPS; i++) {
#pragma HLS pipeline
			result += delay_line[i] * taps[i];
		}
		output[j] = result;
	}
}

```