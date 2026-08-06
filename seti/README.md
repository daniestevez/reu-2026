# Integrating GNU Radio with SETI tools

The following examples use the filterbank parameters for the Allen Telescope Array:

- 2048 Msps ADC sample rate
- 500 kHz coarse channel bandwidth
- 192 coarse channels processed per node
- FFT size 262144 (2^18), 32 integrations (frequency resoulution ~1.9 Hz, time resolution ~16.77 s)

To run a simulation of the ATA polyphase filterbank and generate a GUPPI file,
use `ata_pfb.grc`. The output file is called `guppi.0000.raw`.

To process this output with `rawspec`, run

```
rawspec -j -f 262144 -t 32 guppi
```

This produces a HDF5 filterbank file called `guppi.rawspec.0000.h5`.

Bliss can be run on this output file by running either

```
~/bliss/build/bliss/bliss_find_hits guppi.rawspec.0000.h5 --number-coarse 192 && \
    ~/bliss/build/bliss/bliss_hits_to_dat -i guppi.rawspec.0000.capnp && \
    cat guppi.rawspec.0000.dat
```

to process all the coarse channels, or

```
~/bliss/build/bliss/bliss_find_hits guppi.rawspec.0000.h5 -c 96 && \
    ~/bliss/build/bliss/bliss_hits_to_dat -i guppi.rawspec.0000.capnp && \
    cat guppi.rawspec.0000.dat
```

to target a specific coarse channel (the center channel, 96, in this example).



To run a simulation of fine channelization (`rawspec` or similar) on a single
coarse channel, run `spectrogram_simulation.grc`. This produces an output file
`single_channel_spectrogram.f32` that needs to be converted to HDF5 by running
the following:

```
./convert_to_h5.py single_channel_spectrogram.f32 single_channel_spectrogram.h5 \
    --fft-bins 262144 --bandwidth 500000 --nint 32 --freq 8000
```

The output file is called `single_channel_spectrogram.h5`. Bliss can be run on this file as follows.

```
~/bliss/build/bliss/bliss_find_hits single_channel_spectrogram.h5 && \
    ~/bliss/build/bliss/bliss_hits_to_dat -i single_channel_spectrogram.capnp && \
    cat single_channel_spectrogram.dat
```
