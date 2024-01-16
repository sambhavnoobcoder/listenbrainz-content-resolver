#Beats Per Minute (BPM) Detector

This Python script processes a given .wav file to determine the Beats Per Minute (BPM) using signal processing techniques and wavelet analysis. The BPM detection is based on the analysis of the frequency content of the audio signal.

##Getting Started

Prerequisites

Python 3.x

Required Python packages (install using pip install -r requirements.txt):

-numpy

-matplotlib

-pywt

-scipy

##Run the script:

In order to run the script you can run the following command :

``python bpm.py --filename <path-to-your-wav-file>``

Optional: Adjust the window size using the --window argument (default is 3 seconds).

##Description

The script performs the following steps:

1. Reads a .wav file and extracts the audio samples.

2. Applies a wavelet transform (DWT) to decompose the signal into approximation and detail coefficients.

3. Filters the detail coefficients and performs decimation.

4. Computes the autocorrelation function (ACF) of the processed signal.

5. Identifies the peak in the ACF to estimate the BPM.

##Command Line Arguments

--filename: Path to the .wav file for processing (required).

--window: Size of the window (seconds) used to scan and determine the BPM (default is 3 seconds).

##Example

``python bpm.py --filename example.wav ``

##Output

The script prints the estimated BPM to the console and generates a plot of the autocorrelation function.