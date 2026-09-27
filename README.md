# Resistance Measurement

<h4>
<a href="https://cent35.edu.ar/">Proyecto Integrador de la materia Prácticas Profesionalizantes III</a><br>
<a href="https://cent35.edu.ar/">Tecnicatura Superior en Automatización y Robótica</a><br>
<a href="https://cent35.edu.ar/">CENT 35 "Prof. José Julián Godoy" — Río Grande, Tierra del Fuego, Argentina</a>
</h4>

## Project Overview

**Resistance Measurement** is a LabVIEW-based educational project developed as an integrative project for the subject **Prácticas Profesionalizantes III** of the **Tecnicatura Superior en Automatización y Robótica** at CENT 35, Río Grande, Tierra del Fuego, Argentina.

The project is intended to integrate programming, instrumentation, automation, data processing, software architecture, and laboratory practices into a single practical application.

The final objective is to develop an application capable of identifying a resistor from its color code, determining its nominal resistance and tolerance, validating the corresponding E-Series, measuring the physical component, and finally verifying whether the measured resistance is within the admissible tolerance range.

The project is being developed progressively, with each stage providing a functional increment toward the complete application.

## Current Development Stage

The project is currently in the **first development stage: resistor identification from its color code**.

This stage is approximately **90% complete**. The main functionality is already operational, although some adjustments, refinements, and validation tasks remain before this stage can be considered complete.

At this point, the application can:

- Select the number of resistor bands.
- Enter the color of the significant bands.
- Enter the multiplier band.
- Enter the tolerance band.
- Calculate the nominal resistance value.
- Validate the normalized value against the corresponding E-Series data.
- Display the resistor graphically on the Front Panel.
- Represent the entered color bands on the graphical resistor.
- Indicate the E-Series ranges associated with the selected configuration.

## First Stage — Resistor Identification

The first stage focuses on translating the physical appearance of a resistor into its electrical parameters.

The operator selects the number of bands and enters the corresponding colors.

The application then interprets the color code and calculates the nominal resistance.

The supported resistor configurations are based on:

- 4-band resistors
- 5-band resistors
- 6-band resistors

The number of bands determines which significant digits and additional parameters are applicable.

### Operator Input

The Front Panel provides controls for:

- **Number of Bands**
- **1st Digit**
- **2nd Digit**
- **3rd Digit** when applicable
- **Multiplier**
- **Tolerance**
- **INI File** containing the E-Series configuration

The graphical representation of the resistor is updated according to the values selected by the operator.

## Resistor Color Code Processing

The application separates the physical color information from the interpreted electrical characteristics.

The resistor band colors represent:

- Significant digits
- Multiplier
- Tolerance
- Temperature coefficient for six-band configurations

The color information is processed to obtain the corresponding numerical values used by the application.

This separation allows the color representation to remain independent from the electrical interpretation of the resistor.

## Nominal Resistance Calculation

Once the significant digits and multiplier have been identified, the application calculates the nominal resistance.

For example, a four-band resistor with:

- 1st digit: Brown
- 2nd digit: Red
- Multiplier: Red
- Tolerance: Gold

is interpreted as:

**12 × 100 = 1200 Ω**

The Front Panel displays the resulting value as:

**1.2 kΩ**

The graphical resistor is simultaneously updated to represent the selected bands.

## E-Series Validation

The application uses standard resistor E-Series data stored externally in CSV files.

The currently implemented E-Series dataset includes:

- E3
- E6
- E12
- E24
- E48
- E96
- E192

The E-Series data is not hard-coded into the application. The file names are defined through an INI configuration file, allowing the data files to be changed without modifying the LabVIEW source code.

The application internally distinguishes three E-Series ranges:

### Lower

- E3
- E6
- E12
- E24

### Middle

- E48

### Higher

- E96
- E192

The application verifies whether the significant-value combination obtained from the resistor bands is present in the corresponding E-Series data.

## E-Series Configuration

The E-Series configuration is externalized through an INI file.

The configuration associates each E-Series with its corresponding CSV data file.

Example:

```ini
[E3_Series]
Filename = E3 Series.csv

[E6_Series]
Filename = E6 Series.csv

[E12_Series]
Filename = E12 Series.csv

[E24_Series]
Filename = E24 Series.csv

[E48_Series]
Filename = E48 Series.csv

[E96_Series]
Filename = E96 Series.csv

[E192_Series]
Filename = E192 Series.csv
```

This approach keeps configuration separate from application logic and allows the E-Series data files to be managed independently.

## Graphical Resistor Representation

One of the main features already implemented in the first stage is the graphical representation of the resistor.

The resistor displayed on the Front Panel changes according to the colors selected by the operator.

The graphical representation therefore provides immediate visual feedback between:

**Operator input → interpreted color code → calculated resistance → graphical resistor**

This also allows the application to reproduce the physical appearance of the resistor being analyzed.

## LabVIEW Project Architecture

The LabVIEW project is organized into libraries and functional groups rather than keeping all functionality inside the Main VI.

Current project organization includes:

```text
Resistance Measurement.lvproj
│
├── Constants Vis
├── Support Vis
├── TypeDef
│
├── Resistor Band Colors.lvlib
│   ├── Private
│   ├── Public
│   └── Tester.vi
│
├── E-Series Reader.lvlib
│   ├── Private
│   ├── Public
│   └── Tester.vi
│
├── Main.vi
│
├── Dependencies
└── Build Specifications
```

### Resistor Band Colors Library

`Resistor Band Colors.lvlib` contains the color definitions used to represent resistor bands.

The library provides the color constants required by the application while keeping their implementation separated from the Main VI.

### E-Series Reader Library

`E-Series Reader.lvlib` is responsible for loading and providing the E-Series data used by the application.

Its purpose is to isolate file handling and E-Series data loading from the application logic.

The library internally handles:

- Reading the INI configuration.
- Obtaining the E-Series file names.
- Reading the CSV files.
- Providing the resulting E-Series data to the application.

### TypeDefs

Type definitions are used to establish consistent data structures throughout the application.

This approach is intended to facilitate maintainability and future development as the project grows.

## Software Engineering Approach

The project is being developed with an emphasis on software engineering practices rather than treating the application as a single monolithic VI.

The architecture progressively separates:

- User interface
- Data representation
- Configuration
- E-Series data
- Color definitions
- Processing logic
- Support functionality

The project also uses LabVIEW libraries to establish functional boundaries and distinguish public functionality from private implementation details.

The naming, organization, documentation, and architectural practices are being developed following the **DQMH Consortium Style Guide** as a reference for LabVIEW software development.

## Current Front Panel

The current Front Panel provides the operator interface for the first stage.

The main elements include:

- Number of Bands selection.
- Individual band color selection.
- INI configuration file selection.
- Graphical resistor representation.
- E-Series validation indicators.
- Calculated nominal resistance.

The application updates the displayed resistor and calculated value according to the selected input parameters.

## Development Roadmap

The complete project is intended to progress beyond resistor identification.

The planned development includes:

### Stage 1 — Resistor Identification

Identify the resistor from its color code and determine:

- Number of bands
- Nominal resistance
- Tolerance
- E-Series applicability
- Graphical representation

**Current status: approximately 90% complete.**

### Stage 2 — Resistance Measurement

The physical resistor will be measured using laboratory instrumentation and/or a data acquisition device.

Potential instrumentation includes National Instruments hardware and laboratory measurement equipment.

### Stage 3 — Tolerance Verification

The measured resistance will be compared with the nominal resistance and its admissible tolerance range.

The application will determine whether the measured component satisfies its specified tolerance.

The final verification will provide a **PASS / FAIL** result.

## Technologies

- **LabVIEW**
- **NI-DAQ / Data Acquisition**
- **Laboratory instrumentation**
- **CSV data files**
- **INI configuration files**
- **Git**
- **GitHub**

## Project Status

**Development status: In progress**

The first stage is already operational and approximately 90% implemented.

The current work is focused on completing the remaining adjustments and validation of the resistor identification stage before moving forward with physical resistance measurement and tolerance verification.

This repository represents the progressive development of the project, including its architecture, implementation, and future integration of measurement functionality.
