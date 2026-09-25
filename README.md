## Project Status

The project architecture is currently under development.

### Current Data Model

The application uses two core data structures:

- `Band Colors.ctl`: represents the physical color bands of a resistor.
- `Resistor.ctl`: represents the decoded electrical characteristics of a resistor.

The resistor decoding process transforms the color bands into:

- Number of Bands
- E-Series
- Nominal Resistance
- Tolerance
- Temperature Coefficient (TCR)

Future development will include resistance measurement and verification against the specified tolerance.
