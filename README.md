# DTW-RL for Contiki-NG

This repository provides the available implementation, simulation materials,
mobility traces, experimental logs, analysis code, and result summaries for the
manuscript:

> **DTW-RL: Adaptive Routing Evaluation Interval Algorithm for Stable and
> Reliable RPL in Mobile IoT Networks**

## Overview

DTW-RL combines MMDEW-based multi-metric preferred-parent selection with a
compact linear Q-learning controller. The controller adjusts when the routing
evaluation is performed according to observed transmission reliability, energy
change, and handover evidence. The current evaluation compares the complete
DTW-RL implementation with MRHOF and MMDEW under matched network, mobility,
radio, traffic, and platform configurations.

## Quick Access to Reproducibility Materials

The materials are separated by function so that the implementation,
experimental inputs, raw observations, and processed results can be inspected
independently.

| Material | Description | Access |
|---|---|---|
| Contiki-NG | Upstream Contiki-NG repository | [Contiki-NG](https://github.com/contiki-ng/contiki-ng) |
| Contiki-NG project archive | Original project files used for the reported experiments | [Contiki-NG DTW-RL](https://drive.google.com/drive/folders/1CUULWMXHz0nja5dL8iOZuV9uqTjWGDfP?usp=sharing)  |
| DTW-RL source | DTW-RL controller and modified RPL Classic objective-function source | [DTW-RL.c ](https://drive.google.com/file/d/1HhwdBR-LgiEj56QoShjIl01Qc4UaVcfd/view?usp=sharing) |
| MRHOF baseline | Archived MRHOF objective-function source used in the baseline build  | [MRHOF.c ](https://drive.google.com/file/d/1HhwdBR-LgiEj56QoShjIl01Qc4UaVcfd/view?usp=sharing) |
| MMDEW baseline | Archived MMDEW objective-function source used in the baseline build | [MMDEW.c ](https://drive.google.com/file/d/1W9cSmcpcvp1yYDWPhdzAzex2iOZ_dJn1/view?usp=sharing) |
| Project configuration | Original `project-conf.h` and related compile-time configuration | [project-conf.h ](https://drive.google.com/file/d/1-Rx8EZkIiGFRKBbaebF3JDS5XP47BZkj/view?usp=sharing) |
| Cooja scenarios | Original `.csc` simulation scenario files | **[ADD COOJA CSC LINK]** |
| Mobility traces | Five changing-trajectory realizations for the two- and five-mobile-node configurations | **[ADD MOBILITY LINK]** |
| Raw experiment logs | Raw CSV logs for MRHOF, MMDEW, and DTW-RL | **[ADD RAW LOG LINK]** |
| Analysis code | Python and Colab code used to extract and summarize the logged metrics | **[ADD ANALYSIS CODE LINK]** |
| Processed data | Per-run, per-node, and condition-level processed results | **[ADD PROCESSED DATA LINK]** |
| Tables and figures | Final result tables and individual manuscript figures | **[ADD RESULTS LINK]** |
| Run instructions | Commands, seeds, scenario mapping, and execution notes | **[ADD RUN INSTRUCTIONS LINK]** |

Replace every bold placeholder above with a public, read-only URL before citing
this repository as the complete artifact package.

## Experimental Design

| Item | Configuration |
|---|---|
| Operating system | Contiki-NG |
| Simulator | Cooja |
| Target platform | Zolertia Z1 |
| Network stack | RPL Classic over 6LoWPAN |
| MAC layer | CSMA |
| IEEE 802.15.4 channel | 26 |
| Compared algorithms | MRHOF, MMDEW, and DTW-RL |
| Static sensor nodes | 24 |
| Mobile sensor nodes | 2 or 5 |
| Sink/RPL root | 1 |
| Packet-transmission interval | 10, 20, or 30 seconds |
| Simulation duration | 3,600 seconds per run |
| Mobility realizations | 5 changing trajectories per condition |

The same changing-trajectory realization and experimental configuration should
be applied to all three algorithms within each matched comparison.

## Repository and Archive Organization

The recommended artifact organization is:

```text
dtw-rl-contiki-ng/
├── README.md
├── LICENSE
├── CITATION.cff
├── src/
│   ├── dtw-rl/
│   ├── mrhof/
│   └── mmdew/
├── config/
├── cooja/
├── mobility/
│   ├── mobility-2/
│   └── mobility-5/
├── data/
│   ├── raw/
│   └── processed/
├── analysis/
├── results/
│   ├── tables/
│   └── figures/
└── docs/
```

If the large experiment files remain in Google Drive rather than GitHub, keep
the same logical grouping in Drive and link each folder separately in the
Quick Access table.

## Implementation Files

RPL Classic loads the objective-function implementation from:

```text
os/net/routing/rpl-classic/rpl-mrhof.c
```

For each algorithm build, install the corresponding archived source at that
location and rebuild the project. Keep separate build directories or preserve
the resulting firmware filename so that binaries from different algorithms are
not confused.

The DTW-RL application additionally requires the matching UDP client, UDP
server, `project-conf.h`, and Cooja scenario used for the selected experimental
condition.

## Reproduction Workflow

1. Obtain the Contiki-NG project archive and confirm the recorded version or
   commit information.
2. Select MRHOF, MMDEW, or DTW-RL and install the corresponding objective-
   function source.
3. Apply the supplied `project-conf.h` and related compile-time settings.
4. Open the appropriate Cooja `.csc` scenario.
5. Select the required mobility configuration and changing-trajectory file.
6. Apply the recorded random seed and packet-transmission interval.
7. Run the simulation for 3,600 seconds and export the complete Cooja log as
   CSV with the columns `time`, `node`, and `message`.
8. Run the supplied Python or Colab analysis using the five matched trajectory
   logs for the condition.
9. Retain raw logs, per-run outputs, validation warnings, and processed result
   tables without overwriting the original observations.

Exact commands should be copied from the public run-instruction record linked
in the Quick Access table.

## Logged and Derived Metrics

The available DTW-RL logs support extraction of:

- unique application packets submitted by each non-sink node;
- unique packets received by the sink;
- reconstructed end-to-end packet-delivery ratio;
- packet delay and received-signal strength;
- DTW-RL controller-window updates and interval values;
- DEC and INC controller actions;
- recorded handover events and diagnostic handover delay;
- DIO, DAO, and DIS transmissions;
- local RPL repairs;
- cumulative CPU, radio-transmission, radio-reception, and total energy per
  logged node;
- convergence start/end events; and
- preferred-parent assignment and change events.

### Packet-delivery ratio

For a multi-sender experiment, PDR is reconstructed from unique packet keys:

```text
PDR = unique packets received at the sink
      ----------------------------------- × 100%
      unique packets submitted by all non-sink nodes
```

Each packet is identified by `(source node, sequence number)`. The global PDR
printed by the archived UDP server is retained only as diagnostic output because
that server uses a single sequence tracker for multiple senders.

### Handover normalization

The supplied analysis reports recorded handovers and a diagnostic rate per 100
DTW-RL controller-window updates. This diagnostic rate must not be described as
handover per `rank_via_parent()` evaluation unless the timer-to-objective-
function event coupling has been independently verified.

### Energy

Per-node energy is obtained from the latest cumulative `EN,TOTAL` record for
each logged node. A sum that excludes the sink must be labelled as energy across
logged non-sink nodes, not total energy across all simulated nodes. Window-level
CPU-energy change does not isolate the execution cost of DTW-RL itself.

## Analysis Outputs

The current five-run analysis produces:

```text
run_summary_valid_metrics.csv
multirun_summary_mean_sd.csv
node_delivery_all_runs.csv
node_controller_all_runs.csv
node_handover_all_runs.csv
handover_events_all_runs.csv
convergence_events_all_runs.csv
convergence_pairs_all_runs.csv
parent_change_events_all_runs.csv
local_repair_events_all_runs.csv
node_control_overhead_all_runs.csv
node_energy_all_runs.csv
validation_issues.csv
metric_definitions.csv
```

## Reproducibility Status

The uploaded project, configuration, scenario, mobility, source-code, and
experimental files should be identified as original only when they are the
actual archived files used in the reported experiments. Reconstructed or newly
created documentation must be labelled separately.

The available log identifies the software build as:

```text
Contiki-NG-develop/v5.0-36-gd8f74d170-dirty
```

The `dirty` suffix indicates that the checked-out source contained local
changes. Therefore, the archived modified files remain necessary even when the
base Git revision is identified. Any unavailable commit metadata, seed record,
or command history should be reported explicitly rather than reconstructed
speculatively.

## Known Limitations

The current CSV logs do not independently establish:

- the number of `rank_via_parent()` candidate evaluations;
- parent-change opportunities;
- packet loss specifically attributable to stale-parent periods;
- radio duty cycle from raw CPU/LPM/TX/RX ticks;
- CPU time or energy attributable only to DTW-RL;
- firmware code size, static RAM, stack use, or instruction count; or
- performance on physical Zolertia Z1 devices.

These measurements require additional instrumentation, compiled firmware
artifacts, or controlled hardware validation.

## License

See [LICENSE](LICENSE).

## Citation

Citation metadata should be provided in [`CITATION.cff`](CITATION.cff). Until
that file is added, cite the associated manuscript and this repository URL.
