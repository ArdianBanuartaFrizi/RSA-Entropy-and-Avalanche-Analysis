# RSA Ciphertext Analysis: Entropy, Avalanche Effect, and Computation Time

This repository contains a Python-based analysis of encrypted electrical
measurement data associated with an ESP32-based monitoring project. The
analysis focuses on three aspects:

-   **Shannon entropy** of Base64-encoded ciphertext after decoding it
    into bytes.
-   **Ciphertext bit-difference percentage** between corresponding
    records in two datasets (ESP1 and ESP2).
-   **Encryption and decryption computation time** using recorded timing
    measurements.

The datasets contain electrical parameters (voltage, current, power, and
energy), together with their encrypted representations.

> **Scope note:** This repository contains the analysis notebook and its
> input datasets. It does not contain the ESP32 firmware, RSA
> key-generation code, or the original encryption/decryption
> implementation. The notebook analyzes ciphertext that has already been
> generated and stored in the CSV files; it does not perform RSA
> encryption itself.

## Repository Contents

  ----------------------------------------------------------------------------
  File                                     Description
  ---------------------------------------- -----------------------------------
  `Uji_Entropy_Avalanche_Enkripsi.ipynb`   Main Jupyter/Google Colab notebook
                                           for data preparation, entropy
                                           analysis, ciphertext bit-difference
                                           analysis, visualization, and
                                           computation-time analysis.

  `DataEsp1Clean.csv`                      Dataset 1 containing raw electrical
                                           measurements and their
                                           Base64-encoded ciphertext values.

  `DataEsp2Clean.csv`                      Dataset 2 containing raw electrical
                                           measurements and their
                                           Base64-encoded ciphertext values.

  `DataWaktuKomputasi.csv`                 Recorded encryption, decryption,
                                           and total computation-time
                                           measurements.
  ----------------------------------------------------------------------------

Each ESP dataset contains 120 measurement records.

## Analysis Overview

### 1. Dataset Preparation

The notebook loads the three CSV files using Pandas.

The ESP datasets contain the following columns:

  Column               Description
  -------------------- -------------------------
  `Tegangan`           Raw voltage measurement
  `Arus`               Raw current measurement
  `Daya`               Raw power measurement
  `Energi`             Raw energy measurement
  `TeganganEnkripsi`   Encrypted voltage value
  `ArusEnkripsi`       Encrypted current value
  `DayaEnkripsi`       Encrypted power value
  `EnergiEnkripsi`     Encrypted energy value

The encrypted values are stored as Base64 strings. Base64 is an encoding
format used to represent binary ciphertext as text; it is not an
encryption algorithm.

### 2. Ciphertext Length Analysis

The notebook measures the length of the raw measurement strings and the
encrypted Base64 strings.

In the current datasets, each encrypted Base64 value is 344 characters
long. This is the length of the encoded representation, not the number
of bytes in the decoded ciphertext.

### 3. Shannon Entropy Test

Shannon entropy is used to describe the distribution of byte values in
each decoded ciphertext sample.

The notebook: 1. Decodes each Base64 ciphertext into binary bytes. 2.
Counts the frequency of each byte value. 3. Converts the byte
frequencies into probabilities. 4. Calculates Shannon entropy using base
2. 5. Summarizes the results and plots the entropy values for each
encrypted parameter.

The formula is:

\[ H(X) = -`\sum`{=tex}\_{i=1}\^{n} p(x_i)`\log`{=tex}\_2 p(x_i) \]

Where: - (H(X)) is the Shannon entropy. - (p(x_i)) is the probability of
byte value (x_i). - The theoretical maximum for a byte-frequency
distribution is 8 bits per byte.

The notebook calculates entropy for the encrypted columns in
`DataEsp1Clean.csv`.

### 4. Ciphertext Bit-Difference Analysis

The notebook compares the decoded ciphertext values in corresponding
rows of `DataEsp1Clean.csv` and `DataEsp2Clean.csv`.

The comparison process: 1. Decodes the Base64 ciphertext from both
datasets. 2. Converts each ciphertext into a bit sequence. 3. Performs a
bitwise XOR comparison. 4. Counts the number of differing bits. 5.
Calculates the percentage of differing bits over the compared bit
length.

The formula used is:

\[ D(%) =
`\frac{N_{\text{different bits}}}{N_{\text{compared bits}}}`{=tex}`\times 100`{=tex}
\]

Where: - (N\_{`\text{different bits}`{=tex}}) is the number of bits that
differ between the two ciphertexts. -
(N\_{`\text{compared bits}`{=tex}}) is the number of bits included in
the comparison.

The implementation compares the common bit length of each ciphertext
pair. The current datasets contain ciphertext values of consistent
encoded length.

> **Important methodological note:** The notebook labels this
> calculation as an *Avalanche Effect* test. However, the implemented
> procedure compares ciphertexts from corresponding ESP1 and ESP2
> records. It does not explicitly flip exactly one bit in a plaintext
> and then encrypt both plaintexts under controlled, identical
> cryptographic conditions. Therefore, the result should be interpreted
> as a **paired ciphertext bit-difference measurement**, not as a formal
> Strict Avalanche Criterion (SAC) test. A formal avalanche test would
> require a controlled plaintext-bit change and a clearly specified RSA
> padding/randomization procedure.

### 5. Computation-Time Analysis

The notebook reads `DataWaktuKomputasi.csv` and analyzes these columns:

  Column           Description
  ---------------- -------------------------------------------------
  `waktu_enkrip`   Recorded encryption time in milliseconds
  `waktu_dekrip`   Recorded decryption time in milliseconds
  `Total`          Recorded total computation time in milliseconds

It calculates the average encryption time, average decryption time, and
average total time from 120 records.

## Recorded Results

The following values are taken from the outputs currently stored in the
notebook. They describe the uploaded datasets and should not be
interpreted as universal RSA performance benchmarks.

### Average Shannon Entropy

  Encrypted parameter     Mean entropy (bits/byte)
  --------------------- --------------------------
  Voltage                                 7.173480
  Current                                 7.180361
  Power                                   7.170801
  Energy                                  7.169560

### Average Ciphertext Bit-Difference

  Encrypted parameter     Mean differing bits
  --------------------- ---------------------
  Voltage                          49.990234%
  Current                          49.952393%
  Power                            50.100505%
  Energy                           49.930420%

These percentages represent comparisons between corresponding ciphertext
records from the two datasets, as described in the methodological note
above.

### Average Computation Time

  Measurement     Average time
  ------------- --------------
  Encryption        114.322 ms
  Decryption          7.893 ms
  Total             122.216 ms

## Requirements

The notebook was prepared for Google Colab and uses the following Python
libraries:

-   `pandas`
-   `numpy`
-   `scipy`
-   `bitstring`
-   `matplotlib`

The original notebook setup cell also installs `pycryptodome`. The
visible analysis cells use the libraries listed above for data
processing and analysis; they do not call PyCryptodome to perform RSA
encryption.

To install the packages in a local Python environment, run:

``` bash
pip install pandas numpy scipy bitstring matplotlib pycryptodome
```

## How to Run

### Option 1: Google Colab (Recommended)

1.  Open `Uji_Entropy_Avalanche_Enkripsi.ipynb` in GitHub.
2.  Select **Open in Colab** if available, or download the notebook and
    upload it to [Google Colab](https://colab.research.google.com/).
3.  Upload these three CSV files to the Colab session:
    -   `DataEsp1Clean.csv`
    -   `DataEsp2Clean.csv`
    -   `DataWaktuKomputasi.csv`
4.  Make sure the files are available in the `/content/` directory. The
    notebook currently uses these paths:
    -   `/content/DataEsp1Clean.csv`
    -   `/content/DataEsp2Clean.csv`
    -   `/content/DataWaktuKomputasi.csv`
5.  Run the notebook cells from top to bottom.
6.  Review the printed statistical summaries and generated plots.

### Option 2: Local Jupyter Notebook

1.  Clone or download this repository.
2.  Install the required Python packages.
3.  Open the notebook using Jupyter Notebook or JupyterLab.
4.  Update the CSV file paths in the notebook if the files are not
    located in the working directory.
5.  Run all cells from top to bottom.

Some notebook cells use Google Colab-specific features, including
`google.colab.files` for downloading generated plots. Those cells need
to be adapted or skipped when running outside Google Colab.

## Interpretation and Limitations

-   A high Shannon entropy value indicates a more uniform byte-frequency
    distribution in the analyzed ciphertext sample. It does not, by
    itself, prove that an encryption system is secure.
-   A ciphertext bit-difference percentage near 50% is an observation
    from the paired records in these datasets. It is not sufficient to
    establish the formal avalanche property of the RSA implementation.
-   The notebook analyzes existing ciphertext values. It does not verify
    the RSA key size, padding scheme, key management, or encryption
    implementation because those source components are not included in
    this repository.
-   The recorded computation times depend on the environment and
    measurement procedure used when the data was collected.
-   The Base64 representation is only an encoding of ciphertext and must
    not be confused with the encryption process.

## Possible Future Improvements

-   Include the ESP32 firmware and the RSA encryption/decryption source
    code.
-   Document the RSA key size, padding scheme, and cryptographic library
    used to generate the ciphertext.
-   Add a controlled avalanche experiment in which exactly one plaintext
    bit is changed between paired encryption trials.
-   Repeat the avalanche experiment across multiple plaintext samples
    and controlled randomization settings.
-   Add automated result exports and a reproducible experiment
    configuration.

## License

No license has been specified for this repository yet. Add a `LICENSE`
file if you intend to grant others permission to reuse, modify, or
distribute the code and datasets.

------------------------------------------------------------------------

**Project:** RSA Cryptography Analysis\
**Focus:** Shannon Entropy, Ciphertext Bit-Difference, and Computation
Time\
**Tools:** Python, Pandas, NumPy, SciPy, Bitstring, Matplotlib, and
Jupyter Notebook / Google Colab
