## Summary

The DSP clock is a mechanism that tracks the number of audio samples processed by the Digital Signal Processor (DSP) within the FMOD audio engine.

It is essentially a counter that increments based on the sample rate of the audio system. For example, if the same rate is 48kHz (standard), the clock increments by. 48000 for each second of. audio.

The DSP clock is used for timing and synchronisation of audio events and effects within FMOD.

## Usage

