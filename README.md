# SuperJupiter MachineState

Persistent equipment states and observed runtime from scalar signals.

**Version 0.1.0: evolving preview. Hardware validation pending.**

Original MIT-licensed portable C++11 by [SuperJupiter](https://www.superjupiter.net).
No mandatory service, hidden clock reads, network calls or heap allocation in the core.

## What it does

A scalar activity signal <=stoppedThreshold suggests Stopped, >=runningThreshold suggests Running, otherwise Idle.
A candidate must persist for settleMs across qualifying observations. Changing candidates restarts persistence.
Missing or invalid input changes state to Unknown; no runtime is inferred across a gap exceeding maxGapMs.
Intervals within the gap limit are attributed to the previous confirmed state, including candidate transition time.
Use measured signal-specific thresholds; inferred states are not guaranteed physical truth. Not a machinery safety controller.

## Install

PlatformIO exact release (after registry publication):

```ini
lib_deps = superjupiter/SuperJupiterMachineState@0.1.0
```

Arduino: use the versioned [GitHub release ZIP](https://github.com/Super-Jupiter/superjupiter-machine-state/releases/tag/v0.1.0).
Library Manager registration and index propagation are separate steps; check the official index before relying on IDE availability.

## Example

```cpp
#include <SuperJupiterMachineState.h>
using namespace superjupiter::machine_state;
int main() {
MachineState machine(Config(2,50,1000,5000));
machine.push(100,0);
Result state=machine.push(100,1000);
(void)state;
return 0;
}

```

For Arduino, open `examples/Minimal`. It uses synthetic data only.
Results are deliberately local so applications can inspect outcomes before adding I/O.

## CMake

```cmake
add_subdirectory(path/to/superjupiter-machine-state)
target_link_libraries(your_app PRIVATE SuperJupiter::MachineState)
```

For an installed package use `find_package(SuperJupiterMachineState 0.1 CONFIG REQUIRED)`.
Top-level CMake builds tests/examples; a subproject does not enable developer tools by default.

## Documentation and verification

- [API and boundaries](https://github.com/Super-Jupiter/superjupiter-machine-state/blob/v0.1.0/docs/API.md)
- [Tests](https://github.com/Super-Jupiter/superjupiter-machine-state/blob/v0.1.0/tests/test.cpp)
- [Validation status](https://github.com/Super-Jupiter/superjupiter-machine-state/blob/v0.1.0/docs/VALIDATION.md)
- [Shared conventions](https://github.com/Super-Jupiter/superjupiter-machine-state/blob/v0.1.0/docs/CONVENTIONS.md)
- [Migration policy](https://github.com/Super-Jupiter/superjupiter-machine-state/blob/v0.1.0/docs/MIGRATION.md)

```sh
cmake -S . -B build/cmake
cmake --build build/cmake
ctest --test-dir build/cmake --output-on-failure
python3 tools/check_release.py --tag v0.1.0
```

Keep raw observations and their provenance in your application. Derived results are not physical truth.
Do not use fast-math or finite-math-only compiler assumptions. AVR double may have float precision.
Software tests, board compilation and physical validation are reported separately.

## Fits the SuperJupiter measurement portfolio

Complements [SensorQuality](https://github.com/Super-Jupiter/superjupiter-sensor-quality) and
[SampleWindow](https://github.com/Super-Jupiter/superjupiter-sample-window) without a required dependency.
Each component can be used independently. Domain models, customer data and proprietary applications are excluded.

## Support and licence

MIT. Report reproducible bugs through repository issues; private security reports: info@superjupiter.net.
Include version, configuration, inputs, expected and observed behaviour. No credentials or private customer data.
