# Areal-time-FPGA-based-Spectrum-Analyzer-that-performs-frequency-domain-analysis-of-signals-using-FFT
combines Digital Signal Processing (DSP), FPGA design, and embedded systems concepts to acquire, process, and visualize signals in the frequency domain. Implement FFT processing on FPGA using Verilog. Calculate frequency spectrum magnitude.

## Overview

This project implements a real-time audio spectrum analyzer using the ZedBoard's FPGA capabilities. The system converts time-domain audio signals to frequency-domain representation without relying on external IP cores. It captures audio through the Pmod MIC, processes it using a custom FFT implementation, and outputs the frequency spectrum through the Pmod DAC for oscilloscope visualization.

<p align="center">
  <img src="c:\Users\rutup\Downloads\block_diagram\Block_Diagram (1).png" alt="Block Diagram">
</p>

## Features

- Custom 8-point Decimation-In-Time (DIT) FFT implementation
- Real-time audio acquisition and frequency domain analysis
- Custom SPI interfaces for Pmod MIC3 and Pmod DA2
- Fixed-point arithmetic for efficient resource utilization
- Dual-channel visualization of real and imaginary components
- Rotating output scheme for complete spectral display

## Hardware Used

- ZedBoard (Zynq 7000 ZC702 Evaluation Board - xc7z020clg484-1)
- Digilent Pmod MIC3 (MEMS Microphone with SPI Interface)
- Digilent Pmod DA2 (12-bit Digital-to-Analog Converter)
- Tektronix Digital Oscilloscope (for visualization)

## FFT Processing

The FFT implementation in this project uses a radix-2 Decimation-In-Time (DIT) 8-point algorithm to transform audio signals from time domain to frequency domain. The architecture employs a butterfly network structure with three processing stages.

Prior to hardware implementation, the algorithm was verified through a C program.

Upon arrival of each new audio sample into the 8-sample shift register (updated at an effective 781.25 kHz), the FFT module immediately computes the frequency transform. The computation processes input samples through butterfly operations with appropriate twiddle factors at each stage.

The implementation maintains separate register paths for real and imaginary components throughout the computation pipeline. Both real and imaginary registers expand in bit width through each stage to accommodate computational growth without overflow.

### Complex Multiplication in Butterfly Operations

Each butterfly operation involves complex multiplication between data samples and twiddle factors. For complex numbers (a+jb) × (c+jd), the multiplication expands to:
- Real part: (a×c) - (b×d)
- Imaginary part: (a×d) + (b×c)

The FPGA implementation performs these multiplications in parallel and combines the results. For example, when multiplying by twiddle factor W¹ = 0.707-j0.707, the circuit computes:
- 0.707 × real_input - (-0.707) × imaginary_input for the real result
- 0.707 × imaginary_input + (-0.707) × real_input for the imaginary result

The butterfly then adds and subtracts these complex products to produce two outputs, one real and one imaginary.

The final 67-bit outputs (both real and imaginary components) are truncated to produce 12-bit values compatible with the DAC interface, enabling real-time spectral visualization on the oscilloscope. The dual-channel DAC allows simultaneous display of both real and imaginary components.
