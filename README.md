# DTW-RL for Contiki-NG

This repository contains the available implementation and evaluation
artifacts for the manuscript:

“DTW-RL: Adaptive Routing Evaluation Interval Algorithm for Stable and
Reliable RPL in Mobile IoT Networks.”

## Overview

DTW-RL combines MMDEW-based multi-metric parent selection with a linear
Q-learning controller that adjusts the routing-evaluation interval.

## Repository Contents

- `src/`: archived DTW-RL, MRHOF, and MMDEW source files
- `config/`: available compile-time configuration
- `cooja/`: available Cooja simulation scenarios
- `mobility/`: changing mobility trajectories
- `data/`: raw and processed experimental results
- `analysis/`: data-processing and statistical-analysis scripts
- `results/`: manuscript tables and figures

## Experimental Environment

- Operating system: Contiki-NG
- Simulator: Cooja
- Target platform: Zolertia Z1
- Routing protocol: RPL Classic
- Baselines: MRHOF and MMDEW
- Mobility configurations: two and five mobile nodes
- Packet-transmission intervals: 10, 20, and 30 seconds
- Simulation duration: 3,600 seconds
- Mobility dataset: five changing trajectories

## Installation

The objective-function source must be installed as:

`os/net/routing/rpl-classic/rpl-mrhof.c`

for the corresponding DTW-RL, MRHOF, or MMDEW build.

## Reproducibility Status

The archived experiment materials do not contain the exact Contiki-NG
Git commit, original project-conf.h, complete Cooja scenario files,
random seeds, or original command line.

These missing details are documented transparently and are not
reconstructed speculatively.

## License

See `LICENSE`.

## Citation

Citation information is provided in `CITATION.cff`.
