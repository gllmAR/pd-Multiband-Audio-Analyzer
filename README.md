# pd-Multiband-Audio-Analyzer

## Multiband Audio Analyzer Patch (Logarithmic Scale)

This patch is designed to create a multiband audio analyzer with a logarithmic frequency distribution. It divides the input audio signal into a number of user-defined frequency bands and measures the intensity (amplitude) of sound in each band. The patch is adaptive to the number of bands and provides a list of intensity values for each frequency band.

## Features

- **Logarithmic Frequency Distribution**: The patch splits the audio spectrum into frequency bands spaced logarithmically, mimicking human auditory perception.
- **Adjustable Number of Bands**: You can specify the number of frequency bands, and the patch will automatically adjust.
- **Dynamic Band Creation**: The patch uses the `clone` object in Pure Data or PlugData to create multiple bandpass filters dynamically.

## Requirements

- **Pure Data (Pd)**: This patch is compatible with vanilla Pure Data.
- **PlugData**: You can also use this patch in **PlugData**, which provides an enhanced interface for Pure Data patches.

## How It Works

### Core Components

1. **Bandpass Filters**: The patch uses `bp~` objects (band-pass filters) to isolate specific frequency ranges in each band.
2. **Logarithmic Frequency Calculation**: Using the `expr` object, the center frequency for each band is calculated based on a logarithmic scale.
3. **Amplitude Measurement**: The `env~` object is used to measure the intensity of the signal within each band.
4. **Clone Object**: The `clone` object is used to dynamically create multiple instances of the bandpass filters and amplitude analyzers, each assigned to a specific frequency band.

### Logarithmic Frequency Formula

The center frequency for each band is calculated using the following formula:

\[
f_n = f_{\text{low}} \times \left( \frac{f_{\text{high}}}{f_{\text{low}}} \right)^{\frac{n}{N}}
\]

In the patch, this is implemented using the `expr` object as:

```pd
[expr 20 * pow(20000 / 20, $f1 / ($f2 - 1))]
```

Where:
- `20` is the lowest frequency (in Hz).
- `20000` is the highest frequency (in Hz).
- `$f1` is the band index (0 for the first band, 1 for the second, etc.).
- `$f2` is the total number of bands (e.g., 10).

### Patch Setup

1. **Main Patch:**
   - The patch begins by loading an audio input (`adc~`) and then routes it into multiple `clone` instances of the bandpass filters.

2. **Cloning Bands:**
   - Each `clone` instance represents one frequency band and computes its center frequency logarithmically.
   - The output of each clone instance is the intensity of the sound in that frequency band.

3. **Visualization:**
   - The output is packed into a list that can be printed or visualized in a GUI to show the energy levels across the different bands. (array)

## How to Use

1. Open **Pure Data** or **PlugData**.
2. Load the patch (`multiband_analyzer.pd` or `multiband_analyzer.pd` in PlugData).
3. The patch will automatically analyze the incoming audio signal and split it into the number of bands defined in the `clone` object.
4. Adjust the number of bands by modifying the `clone` object. Example:
   ```pd
   [clone spectral_enveloppe_cln 10 10]
   ```
   - The first `10` is the number of clone instances (number of frequency bands).
   - The second `10` is the number of bands passed as an argument to each clone instance.
5. Each band will measure the intensity of its corresponding frequency range and output the results as a list.

### Customizing the Patch

- **Change the Number of Bands**: Modify the number of instances in the `clone` object to increase or decrease the number of frequency bands.
- **Adjust Frequency Range**: You can change the `f_low` and `f_high` values in the `expr` object to adjust the minimum and maximum frequencies analyzed.
  
   For example, to analyze frequencies from 50 Hz to 10 kHz, modify the `expr` like this:
   ```pd
   [expr 50 * pow(10000 / 50, $f1 / ($f2 - 1))]
   ```

## Output

- Midi CC output by default, ch1 0 to band number, ch2 start at 63
- The patch outputs a list of values representing the intensity of each frequency band, spaced logarithmically.
- You can visualize this data using external GUI elements or route it to other objects for further processing (e.g., visualization, data logging, or triggering events based on band intensity).

