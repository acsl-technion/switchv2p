# Introduction

Welcome to the official source code repository for the paper [*In-Network Address Caching for Virtual Networks* (ACM SIGCOMM, 2024)](https://conferences.sigcomm.org/sigcomm/2024/).

<details>
  <summary>BibTeX</summary>

    @inproceedings {10.1145/3651890.3672213,
    author = {Lior Zeno, Ang Chen, and Mark Silberstein},
    title = {In-Network Address Caching for Virtual Networks},
    booktitle = {Proceedings of the ACM SIGCOMM 2024 Conference},
    year = {2024},
    address = {Sydney, NSW, Australia},
    url = {https://doi.org/10.1145/3651890.3672213},
    publisher = {Association for Computing Machinery},
    }

</details>

This repository includes the following components:

* SwitchV2P simulation code for NS3
* Required patches for NS3
* Scripts for reproducing the paper's results

## Dependencies

The code has been tested with the following versions:

* Ubuntu 22.04
* NS 3.36.1
* Boost 1.74
* Z3 4.8.12

## Reproducing the results

To reproduce the results from the paper, first execute the build script: ```./build.sh```. This script will:

* Download the specified version of NS3
* Install required dependencies
* Patch NS3
* Build the code

### Running the experiments

1. Set the `NS3_HOME` environment variable:
```source ./set-ns3-home.sh```
2. Navigate to the results directory:
```cd ./ns3/scratch/switchv2p/results```
3. Run the desired experiment using the auxilary script:
```./run.py -w <WORKLOAD> [--gw | --topo]```

* Replace `<WORKLOAD>` with one of the supported options: `hadoop`, `websearch`, `alibaba`, `microburst`, `video`.
* To run the gateway or topology scaling experiments, include the `--gw` or `--topo` options, respectively.
* Edit `run.py` and set the value of `MAX_PROC` to the number of CPU cores you want to allocate for the experiments. If you encounter out-of-memory (OOM) exceptions, reduce the `MAX_PROC` value accordingly.

4. Run the VM migration test (section 5.2) using the auxilary script:
```./run_migration_test.sh```

### Understanding the results

Results for each workload are stored in separate folders. For example, the results for `hadoop` are stored in a folder named `hadoop`. Each subfolder within these folders represents a different run, and contains a `config.json` file with the run's configuration. The results of each run are stored in a `results.json` file, which includes the following keys:

* `total_tx_packets`: The total number of packets sent during the simulation.
* `total_rx_packets`: The total number of packets received during the simulation.
* `total_dropped_packets`: The total number of packets dropped during the simulation.
* `total_gw_packets`: The total number of packets processed by the gateways during the simulation.
* `total_learning_packets`: The total number of learning packets generated by SwitchV2P during the simulation.
* `total_invalidation_packets`: The total number of invalidation packets generated by SwitchV2P during the simulation.
* `total_misdelivered_packets`: The total number of misdelivered packets during the simulation.
* `last_misdelivered_packet`: The timestamp when the last misdelivered packet was received by the previous destination.
* `switch_to_processed_bytes`: A mapping between switch IDs and the number of bytes they processed during the simulation. In the `FT8-10K` topology, core switches are 0-15, spines are 16-47, and ToRs are 48-79.
* `switch_to_hits`, `switch_to_first_hits`: Mappings between switch IDs and the number of cache hits for any packet and the number of cache hits for first packets in a flow during the simulation.
* `avg_fpl`: The average first packet latency in the simulation.
* `avg_fct`: The average flow completion time in the simulation.
* `avg_packet_latency`: The average latency of packets during the simulation.
* `avg_packet_hops`: The average number of hops each packet took during the simulation.

To extend the reported metrics, you can modify the `trace-sim.cc` file located under `scratch/switchv2p`. For example, to report the number of packets each switch processed during the simulation, see line 137.

## License

This code is licensed under the MIT License. See the LICENSE file for more details.