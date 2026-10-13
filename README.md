# Resistance Measurement

<img width="2172" height="495" alt="e5dde2d4-d234-4b6d-b2ef-499cbe08c8d7" src="https://github.com/user-attachments/assets/8c326b0f-75e0-4572-a42d-d7a170ba73e7" />


## Project Overview

**Resistance Measurement** is a LabVIEW educational project developed as an integrative project for **Prácticas Profesionalizantes III** in the **Tecnicatura Superior en Automatización y Robótica** at CENT 35, Río Grande, Tierra del Fuego, Argentina.

The project brings together programming, instrumentation, automation, data processing, software architecture, and laboratory practices in a practical application. Its intended workflow is to identify a resistor from its color code, calculate its nominal resistance and tolerance, measure the physical component, and check whether the measured value falls within the admissible tolerance range.

Development is incremental. The resistor-identification and calculation functionality is implemented; integration of the Agilent 34401A measurement and PASS/FAIL tolerance verification remain pending.

## Current Development Stage

The current implementation covers resistor identification from its color code and calculation of its electrical specifications. The operator can:

- Select a 3-, 4-, 5-, or 6-band resistor configuration.
- Select the significant digit, multiplier, and tolerance colors that apply.
- Calculate and display the nominal resistance.
- Calculate lower and upper tolerance limits, deviation, and tolerance range.
- Display E-Series membership indicators for the configured series data.
- See the resistor rendered with the selected colors on the Front Panel.

The project is now moving toward instrument integration. The VI named `Measuring R with Agilent 34401A.vi` is present in the project tree, but completion and integration of the physical measurement workflow are pending. Comparing a measured resistance with its nominal value and tolerance to produce a PASS/FAIL result is also pending.

## Resistor Identification

The operator selects the number of bands and enters the applicable colors. The number of bands determines which significant digits and additional parameters are used. Supported configurations are:

- 3-band resistors
- 4-band resistors
- 5-band resistors
- 6-band resistors

### Operator Input

The Front Panel includes controls for:

- **Number of Bands**
- **1st Digit** and **2nd Digit**
- **3rd Digit**, when applicable
- **Multiplier**
- **Tolerance**
- **Series Data File Name**, used to select the series data/configuration set

The graphical resistor updates to reflect the selected values. For six-band configurations, the color code also includes a temperature-coefficient band.

## Resistor Color Code Processing

The application keeps the physical band colors separate from the electrical characteristics interpreted from them. Band colors represent significant digits, multiplier, tolerance, and, for six-band configurations, temperature coefficient. Color values are translated into numerical values used by the calculation logic, while the graphical representation uses the same selections to render the resistor.

## Nominal Resistance and Tolerance Calculation

The application combines the significant digits with the multiplier to calculate nominal resistance, then derives the lower and upper limits from the selected tolerance.

For example, a four-band resistor with:

- 1st digit: Orange (3)
- 2nd digit: White (9)
- Multiplier: Yellow (×10 k)
- Tolerance: Gold (±5%)

has a nominal resistance of **39 × 10,000 = 390,000 Ω (390 kΩ)**. At ±5%, the displayed limits are **371 kΩ** and **410 kΩ** (rounded for display), with a deviation of **20 kΩ** and a tolerance range of **39 kΩ**. The Front Panel also shows the selected bands on the resistor graphic.

## E-Series Data and Configuration

The application uses standard resistor E-Series data stored externally in CSV files. The current series data set covers **E3, E6, E12, E24, E48, E96, and E192**. Series data is not hard-coded: an INI configuration associates each series with the CSV file that contains its values, so files can be changed without modifying the LabVIEW source code.

The application displays membership indicators for the configured series. The current design does not use an E-Series Level classification; each series is handled as its own data set.

Example INI configuration:

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

The Front Panel currently exposes a **Series Data File Name** control. The ability to introduce/select the configuration file as a dedicated event-driven operation is planned for a future increment.

## Graphical Resistor Representation

The resistor graphic changes with the operator's color selections and provides immediate visual feedback between the inputs, interpreted code, calculated resistance, and physical appearance represented in the application.

## LabVIEW Project Architecture

Functionality is divided into libraries and VIs rather than placed in a single monolithic VI. The project tree shown in the current project contains:

```text
Resistance Measurement.lvproj
└── My Computer
    ├── Support VIs
    ├── Type Def
    ├── Front Panel Interface.lvlib
    │   ├── Private
    │   └── Public
    ├── E-Series Reader.lvlib
    │   ├── Private
    │   ├── Public
    │   └── Tester.vi
    ├── Resistance Value.lvlib
    │   ├── Private
    │   └── Public
    ├── Band Colors.lvlib
    │   ├── Private
    │   ├── Public
    │   └── Tester.vi
    ├── Resistance Colors.lvlib
    │   ├── Private
    │   └── Public
    ├── Resistance Controls.lvlib
    │   ├── Private
    │   └── Public
    ├── Main.vi
    ├── Measuring R with Agilent 34401A.vi
    ├── Dependencies
    └── Build Specifications
```

### Front Panel Interface Library

`Front Panel Interface.lvlib` groups Front Panel configuration, control, and initialization functions. It separates user-interface operations from the calculation and data-reading responsibilities.

### E-Series Reader Library

`E-Series Reader.lvlib` handles loading the configured E-Series data. Its responsibilities include reading the INI configuration, resolving CSV file names, and reading the CSV data. The tree also contains a tester VI.

### Resistance Value Library

`Resistance Value.lvlib` contains functions for calculating resistance specifications, including nominal resistance and related values such as multiplier and tolerance limits.

### Band Colors Library

`Band Colors.lvlib` contains the color definitions used for resistor bands. It provides color values used by the application while keeping those definitions separate from `Main.vi`.

### Resistance Colors and Resistance Controls Libraries

`Resistance Colors.lvlib` groups color-related handling for the resistor representation. `Resistance Controls.lvlib` groups control-related functionality used by the application. These libraries establish separate functional boundaries in the current project architecture.

### Type Definitions

Type definitions establish consistent data structures throughout the application and support maintainability as the project grows.

## Event-Driven Queued State Machine

The application uses an **Event-Driven Queued State Machine (EDQSM)** in `Main.vi`. Its Front Panel event handling is decoupled from the processing states through a queue: an operator event enqueues the work required, and the state machine processes the queued states. This lets distinct event paths handle changes to the number of bands, changes to a band color, and application exit.

This is an architectural change from the original sequential state machine. Once Front Panel events were introduced, the application could no longer rely on one fixed sequence for all interactions; each event can trigger its own appropriate sequence of states. A future event will support selecting or loading the configuration file containing the series data file names.

## Software Engineering Approach

The project progressively separates the user interface, data representation, configuration, E-Series data, color definitions, processing logic, and support functionality. LabVIEW libraries define functional boundaries and distinguish public functionality from private implementation details. Naming, organization, and documentation use the **DQMH Consortium Style Guide** as a reference for LabVIEW development practices.

## Current Front Panel

The current Front Panel provides the operator interface for resistor identification and specification calculation. It includes:

- Number of bands and individual band color selections.
- Series data file name input.
- Graphical resistor representation.
- E-Series membership indicators.
- Calculated nominal resistance and tolerance specifications, including lower/upper limits, deviation, and tolerance range.

The displayed resistor and calculated specifications update in response to operator input. Physical measurement and PASS/FAIL tolerance verification are not yet integrated.

## Development Roadmap

### Stage 1 — Resistor Identification and Calculation

Identify a resistor from its color code and calculate its nominal resistance and tolerance specifications. The Front Panel also provides the graphical representation and configured E-Series indicators.

**Status: implemented.**

### Stage 2 — Resistance Measurement

Complete and integrate measurement of the physical resistor using the **Agilent 34401A**. The project currently contains `Measuring R with Agilent 34401A.vi`; the measurement workflow remains pending.

### Stage 3 — Tolerance Verification

Compare the measured resistance with the nominal value and its admissible tolerance limits, then report **PASS / FAIL**. This verification remains pending until the measurement path is completed and integrated.

## Technologies

- **LabVIEW**
- **Agilent 34401A** (planned measurement integration)
- **CSV** data files
- **INI** configuration files
- **Git**
- **GitHub**

## Project Status

**Development status: in progress**

Resistor color-code interpretation, nominal resistance calculation, tolerance-limit calculations, graphical color-band representation, and E-Series indicators are implemented. Completing and integrating measurement with the Agilent 34401A and verifying PASS/FAIL against the nominal value and tolerance remain the outstanding project stages. The project documents this progressive development from its software architecture through the planned laboratory measurement workflow.


<img width="798" height="514" alt="image" src="https://github.com/user-attachments/assets/4db5624e-939a-4c50-bab9-43345fe0f0f2" />

Revision #459 

<img width="797" height="553" alt="imagen" src="https://github.com/user-attachments/assets/a1da6ff0-1e12-4765-a26a-aef8ffec7df1" />

<img width="1802" height="883" alt="imagen" src="https://github.com/user-attachments/assets/5e0bd19a-25ce-4efc-afa9-f52298d57909" />

<img width="1847" height="878" alt="imagen" src="https://github.com/user-attachments/assets/3e5b04e5-9c2f-4cc5-9360-283e84e9732f" />


