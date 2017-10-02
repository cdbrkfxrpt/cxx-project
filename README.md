
![scruBB Logo](./misc/scruBB.png)

`scruBB` stands for _Scrutineering Algorithms Building Blocks_. It is a library
designed to be used in software for motorsport scrutineering ECUs and is
therefore optimized to run on mid-range computing power embedded devices.
However, it does compile and run anywhere else we have tried and can therefore
be used in other applications, such as server based automated data analysis, as
well.



## Current To Dos

- write all maker functions
- document the code
- get this readme up to speed with reality
- `ScruLab`? yeah...


## Library Structure

The library builds on the Siemens Embedded Multicore Building Blocks (EMBB)
library which provides containers and algorithms designed to run on embedded
multicore systems and the standard template library (STL). An embedded library
is required since scrutineering algorithms are meant to run on embedded devices
inside race cars (see _Background_ section for more information). It of course
also works on most other platforms for which a C++ compiler exists.

![scruBB Library Structure](./misc/03-scrubb_stack.png "Figure 1: scruBB
    Library Structure")

*Figure 1: `scruBB` Library Structure*

As the feature extractors (see _Feature Extractors_ section for more
information) rely on specific data structures for efficient execution, specific
containers are implemented and exposed to the feature extractors. The same is
the case for algorithms necessary for data analysis. The feature extractors
themselves build on `scruBB` algorithms and containers and EMBB directly and
provide fully build feature extractors which are common in technical
regulations. The feature extractors contain basic feature extraction strategies
such as rolling averages or integrators as well as higher level strategies such
as turbo boost pressure checks, which are themselves composites of lower level
feature extractors. The feature extractor collection generator allows
collecting feature extractors in sets or vectors of feature extractors, thus
enabling the application to use more than one set of feature extractors for
different purposes through the application programming interface (API) easily.
The API also includes the preferred implementation of the data interface used
to interact with `scruBB`, which we call _signal queues_ for input data and
_results time series queues_ for output data. The API further exposes raw
feature extractors as well as the collection generator to the application.



## Build and Test

Required to build and test the library are:

- `CMake` (3.8.0+)
- `make` (4.2.1+)
- `gcc` (7.0+)

Simply run

```bash
# create new directory "build" and switch into it
mkdir build && cd build

# execute "cmake" in the parent directory, the call make in this directory
cmake ..
make

# run test suite
make test
```


## Background

Motorsport like any other sports needs rules, which in the case of motorsport
are usually separated in _technical regulations_ holding information about what
competitors can do technologically and _sporting regulations_ holding
information about procedures and codes of conduct. Of course, rules need to be
enforced, which has always been a big factor in motorsport since competitors
can gain advantages by violating technical regulations to be faster on the
track.

In order to speed up the process of checking, hereafter referred to as
_scrutineering_, competitors or manufacturers of cars present technical
documentation as requested by the regulating organisation to show that their
car is in compliance with the technical regulations of the series they intend
to compete in. This process is called _homologation_ and provides the
person responsible for scrutineering with documentation to check the car
against.

In terms of combustion engines, the process is fairly straightforward - both
homologation and scrutineering - when it comes to the mechanical parts of the
engine. Many parts are sealed in such a way that competitors are physically
kept from changing them without notice. However, both homologation and
scrutineering become much more complex when it comes to the software running on
the engine control unit (ECU) of the car. A common approach is to have a
checksum taken from the ECUs' memory to ensure nothing has changed after
homologation. This of course relies on trusting that the competitor has not
manipulated the ECU in such a way as to always provide the same checksum. The
problem becomes arbitrarily more difficult if cars are allowed to run different
software versions, different ECUs or have no inherent possibility of getting
the checksum.

A more modern approach to the ECU software problem in motorsport is to
circumvent the ECU entirely and only look at engine parameters influencing the
engine's performance. This is most easily by installing sensors in the right
positions, logging the parameters in a laboratory environment (e.g. testing the
car on track and on a dynamometer using a neutral driver) and homologating
them. Scrutineering then comes down to checking the sensory values, checking
the software on the ECU software is rendered unnecessary.

For this, the algorithms used to process sensory values have to be part of the
technical regulations, which is increasingly happening in recent years. Once
this is the case in a racing series, scrutineers can extract logged data from
the car and check it for compliance with the technical regulations.

There is, of course, still a more elegant way of doing the scrutineering. The
analysis of the data, which is a feature extraction problem, can be done
online, providing the scrutineer with a feedback as to the compliance of the
car without the need of manually extracting and analysing the data after every
outing (i.e. on-track run of the car).



## System Overview

In this section, a brief overview of the context in which the library is being
used is provided. This is an exemplary usage, the library should also work in a
different context such as a desktop application analysing data.

![System Overview](./misc/02-overview_focus.png "Figure 2: System Overview")

*Figure 2: System Overview*

Figure 2 shows the entire data path within the system. The data source
provides sensory values to the system, where values are picked up in queues
and preprocessed to be available as memory objects. The queues are then
processed by the feature extraction subsystem, which is the part provided by
`scruBB`. After the feature extraction is done, the results are processed and
logged, as well as forwarded to the data presentation subsystem to be presented
to the scrutineer.


### Preprocessing Subsystem

In the preprocessing step, signals are retrieved from raw data and stored in
signal queues, which are the preferred interface to use together with `scruBB`.
The implementation of the signal queue is part of the API.

![Preprocessing Subsystem](./misc/02-preprocessing_subsystem.png "Figure 3:
    Preprocessing Subsystem")

*Figure 3: Preprocessing Subsystem*

Figure 3 is provided as an example to the user. The testing suite also provides
example data in the form of a CSV file which is read in and parsed in the way
shown here. Be advised that and implementation filling the signal queues used
by `scruBB` has to be provided and is not part of `scruBB` itself.  The data
structure to be used as a signal queue is provided as part of `scruBB`'s API.


### Feature Extraction Subsystem

In the feature extraction step, the scrutineering algorithms are applied to the
incoming data. _This is the part of the problem for which `scruBB` is providing
a solution for._

![Feature Extraction Subsystem](./misc/02-feature_extraction_subsystem.png "
    Figure 4: Feature Extraction Subsystem")

*Figure 4: Feature Extraction Subsystem*

As shown in figure 4, data is read from the signal queues into the data
distribution engine, which is part of the feature extractor collection
generators part of the library. This engine feeds the data to the feature
extractors in the feature extractors vector. The scrutineering algorithms are
then applied and the results fed to the feature collection engine, which in
turn feeds the results time series queue (exposed by the API).

The configuration language interface shown in the figure provides the
application with the possibility to adapt the feature extraction process to its
needs using an intermediate language. On top of this, a domain specific
language can be built or a graphical programming environment for simpler user
access. This is not currently part of `scruBB`, although a domain specific
language is on the list of planned features for the near future.


### Results Processing Subsystem

In the results processing step, results of the feature extraction are processed
and forwarded.

![Results Processing Subsystem](./misc/02-results_processing_subsystem.png "
    Figure 5: Results Processing Subsystem")

*Figure 5: Results Processing Subsystem*

In figure 5, the results processing subsystem is shown as it is implemented in
the test suite. It is again, like the preprocessing subsystem, an exemplary
implementation showing the slicing of the results time series queue, provided
in the API, and the forwarding of the data to the light emitting diodes (LED)
configuration on one side and the attachment of the metadata and subsequent
forwarding to persistent storage and the report dataset respectively.


### Data Presentation Subsystem

The data presentation subsystem represents the final data sink, providing the
results of the feature extraction process to the use.

![Data Presentation Subsystem](./misc/data_presentation_sub.png "Figure 6: Data
    Presentation Subsystem")

*Figure 6: Data Presentation Subsystem*

In figure 6 an exemplary setup for the data presentation subsystem is provided
for the sake of completeness. This is not part of `scruBB` and not implemented
therein.



## Algorithms

In this section, an explanation of the algorithms is given. This is
accomplished by explanation of one high level feature extractor holding all
relevant low level feature extractors. Later, the low level feature extractors
are put forward and explained in more detail.


### Turbo Boost Pressure Algorithm

The turbo boost pressure is a highly relevant performance parameter in series
with turbo engines, i.e. engines which pressure the air in the combustion
chamber by means of exhaust gas driven turbine.

![Turbo Boost Pressure Algorithm](./misc/02-boost_algo.png "Figure 7: Turbo
    Boost Pressure Algorithm")

*Figure 7: Turbo Boost Pressure Algorithm Block Diagram*



## Usage

_the following few paragraphs are shit, rewrite immediately_

`scruBB` is header only. For a library of this size, this is arguably a bold
choice, but the reason is simple: software for embedded systems is statically
linked anyway and the first build is going to potentially take a few seconds
anyway as well, so there really is very little reason _not_ to be header only.

As such, it is recommended to put the `scrubb` directory either somehwere in
your `include` path and include `scrubb.h` from there via `#include
<scrubb/scrubb.h>` from there or put it in your project's root and add
`target_link_libraries (your_binary scrubb)` to you `CMakeLists.txt` file. Both
are fine, neither is particularly recommendable.

In order to use `scruBB`, three steps are required:

1. Implement the adapters for your incoming data.
2. Implement the adapters for the results produced by `scruBB`.
3. Configure `scruBB`s behaviour via a `ScruLab` script.

This chapter describes how to do this, and how to connect the dots at the end.

First of all, you need to instantiate a `kernel` object. As a parameter,
you can either pass a path to a `ScruLab` file or a directory containing
`ScruLab` files, in which case `scruBB` will look for `ScruLab` files
recursively in that directory. If your environment does not enjoy the luxury of
something as sophisticated as a filesystem from where you can load `ScruLab`
files, you can alternatively pass in a `std::map<std::string, std::string>`
object containing identifiers for algorithms as keys and `ScruLab` scripts as
values. This, of course, eliminiates the possibility of runtime configuration,
thus requiring building the application whenever the `kernel`
configuration changes.

Instantiate like this:

```cpp
#include "scrubb.h"

int main(int argc, char * argv[]) {

  scrubb::kernel se {"/media/sdcard/"};

  return 0;
}
```

This creates a new `kernel` object on the stack, which is the preferred
way of using it. In this case, the path `/media/sdcard` is passed to the object
to look for `ScruLab` scripts. Instead, you can pass a path to a file or a
`std::map` object, like so:

```cpp
#include <map>
#include <string>
#include "scrubb.h"

int main(int argc, char * argv[]) {

  std::map<std::string, std::string> config {};

  config["boost"]     = "algorithm        = fia_gt3; \
                         gearshift_window = 300;     \
                         rpm_threshhold   = 3600;";
  config["sw_ctrl"]   = "checksum = 0xdeadbeefdeadbeef";
  config["gearratio"] = "first = 12.66; second = 18.14; tolerance = 1.6;"

  scrubb::kernel se {config};

  return 0;
}
```

For more information on the syntax and usage of `ScruLab`, see below.


### Implementing the adapter for incoming data

Before starting to implement the adapter for the incoming data, you have to
configure `scruBB` to work at the frequency you want it to. As this is a
compile time value, the parameter you have to set is found in `conf.h` (which
you should have at least a copy of in your project directory somehwere).  There
you can set

```cpp
// kernel execution frequency in Hz; default: 100Hz, maximum: 1000Hz
const unsigned f_kernel_exec  = 100;
```

Be aware that `kernel` will execute once every step without regard
whether or not your data is coming in faster or slower. For each parameter, the
value at the beginning of the step is used.

To feed your data into `scrubb`, you need to implement your own adapter and
configure `kernel` on startup. `kernel` comes with the following
input channels preconfigured:

| Channel Name      | Internal Type |    Unit   | Comment                 |
|:------------------|:-------------:|:---------:|:------------------------|
| `t_time`          | `double`      | arbitrary | not used internally     |
| `p_manifold`      | `double`      | mbar      | boost pressure value    |
| `p_intake`        | `double`      | mbar      |                         |
| `p_exhaust`       | `double`      | mbar      |                         |
| `p_brake_f`       | `double`      | mbar      |                         |
| `p_brake_r`       | `double`      | mbar      |                         |
| `p_ambient`       | `double`      | mbar      |                         |
| `a_x`             | `double`      | m/s^2     |                         |
| `a_y`             | `double`      | m/s^2     |                         |
| `a_z`             | `double`      | m/s^2     |                         |
| `n_gear_calc`     | `unsigned`    |           | calculated gear         |
| `n_rpm`           | `unsigned`    |           |                         |
| `n_checksum`      | `unsigned`    |           |                         |
| `alpha_throttle`  | `double`      | rad       |                         |
| `alpha_pedal`     | `double`      | rad       |                         |
| `alpha_ignition`  | `double`      | rad       |                         |
| `alpha_inj_s`     | `double`      | rad       |                         |
| `alpha_inj_f`     | `double`      | rad       |                         |
| `v_wheelspeed_fl` | `double`      | m/s       |                         |
| `v_wheelspeed_fr` | `double`      | m/s       |                         |
| `v_wheelspeed_rl` | `double`      | m/s       |                         |
| `v_wheelspeed_rr` | `double`      | m/s       |                         |
| `r_lambda`        | `double`      |           |                         |
| `U_battery`       | `double`      | V         |                         |
| `T_intake`        | `double`      | deg C     |                         |
| `T_water`         | `double`      | deg C     |                         |
| `T_oil`           | `double`      | deg C     |                         |
| `T_logger`        | `double`      | deg C     |                         |
| `P_load_target`   | `double`      | %         |                         |
| `P_load`          | `double`      | %         |                         |
| `v_gps_speed`     | `double`      | m/s       |                         |
| `alpha_lat`       | `double`      | deg       |                         |
| `alpha_lon`       | `double`      | deg       |                         |
| `h_altiture`      | `double`      | m         |                         |
| `n_gps_nsat`      | `unsigned`    |           | number of satellites    |
| `a_gps_lat`       | `double`      | m/s^2     |                         |
| `a_gps_lon`       | `double`      | m/s^2     |                         |
| `h_gps_altitude`  | `double`      | m         |                         |
| `r_gps_accuracy`  | `double`      | arbitrary | given by GPS devices    |

Each of these channels can be configured to your needs and needs to be updated
by your adapter in the following way.

```cpp
#include "scrubb.h"

int main(int argc, char * argv[]) {

  scrubb::kernel se {"/media/sdcard/"};

  se.p_manifold.set_factor(1000);              // your input is in bar, not mbar
  se.p_manifold.set_offset(se.p_bariometric);  // bariometric pressure correction

  while (true) {
    se.p_manifold.publish(my_adapter_output);
  }

  return 0;
}
```

This enables you to correct you incoming data automatically and dynamically.
Especially the ability to cross-correct your channels might be interesting. It
is also possible to set a numeric factor and offset _and additionally define
another channel_ to provide a dynamic factor and offset.

When you are done configuring the channel, you simply `publish` your data to it
whenever it is ready. The preferred method here is to write your adapters so
that they return sensory output and call them as a parameter to the `publish`
function in your loop(s). Yes, `publish` is thread safe.

Furthermore, it is possible to create your own channels:

```cpp
#include "scrubb.h"

int main(int argc, char * argv[]) {

  scrubb::kernel se {"/media/sdcard/"};

  // signature: add_channel(std::string name, std::string unit)
  // defaults:  double factor = 1.0, double offset = 0.0
  unsigned channel_id = se.add_channel("my_awesome_channel", "mm");
  se.channel[channel_id].set_factor(512.0);

  while (true) {
    se.channel[channel_id].publish(my_adapter_output);
  }

  return 0;
}
```

Take note, however, of the fact that at most one channel can be defined as
providing the corrective factor. If you need more levels of abstraction, work
with numeric factors and channel combination to get the channels you want.

The channels can also be configured via `ScruLab` (see below).

