# BPSK Demodulator

## Overview
 * Done with Opamps and 4000 series CMOS
 * Normal design with
   * Multiply by 2 (done with gilbert cell)
   * PLL for carrier recovery
   * Divide by 2 (JK flip-flop)
   * Mixers (XOR gates)
   * Low-pass filter
 * Also finds the initial phase

## Implementation
### Squaring up signals
 * There were several points in the circuit where I wanted to take a signal like a sine-wave and turn it into a square wave with matching frequency. This was implemented with the following circuit.
<img src="images/cleanUp.png" width="80%" />

 * This circuit has feedback so the duty cycle ends up very close to 50% no matter the input signal
 * However this ciruit doesn't do too well with noise as there is no hysteresis
   * This is a bit of a design trade off bc that would have interfeared with the phase
     * The phase would vary with the input amplitude

### Frequency doubler
  * This was a gilber cell set up as a mixer
  * the output is not sinusoidal. Instead it was a "full wave rectified" sine wave
    * This wasn't a problem bc of the function of the "squaring up circuit"

### PLL
  * Made of an astable multivibrator and XOR gate for the mixer
  * Second order with very low frequency pole in the filter stage bc
    * Most important feature is to not lose lock (one cycle lost would ruin data)
    * Doesn't need to change frequency while device is operating

### Startup Detection
  * When the input signal is applied the phase must remain constant for about 10ms
  * This gives time for PLL to lock and for the start up circuity to set the phase of the divide by 2 so that the output will be high for this phase and low for opposite phase.
  * This circuity is also blanks the output while the PLL is locking up and such

## PCB
  * Due to errors I made with startup and phase I had to modify the original circuit and pcb

<img src="images/pcb.JPG" width="40%" />


## Phase
  * The phase works out quite nicely with this design
Here is a matlab plot I made to show the phase as the signal moves throught the circuit

<img src="images/phase.png" />

* In order to get the phase right the divide by two has to update on the falling edge. Initially I got this wrong and I had to use a spare XOR gate as an inverter to fix the problem

## Testing
  * I needed a BPSK signal to Demodulate. Fortunately this was quite easy to generate. I just had an inverting amplifier and a noninverting ampifier with the same input and outputs added with reistors then by changing the gain of one amplifier I could flip the phase
    * The inverting ampifier had fixed gain of 2 V/V
    * The non-Inverting had gain that could be set to 1 or 3 based on the digital input
    * The "mixing" resitors were the same value

## Function
 * With nice high bandwidth low noise signals it worked very nicely
   * Able to achive 1.6 kBits/sec
 * Struggles with noisy signals, and when bandwidth of input has been restricted
 * PLL has narrow lock range so only works with small range of carrier frequencies
   
