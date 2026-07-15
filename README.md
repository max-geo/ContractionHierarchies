# Contraction Hierarchies: Top-Down vs. Bottom-Up Node Ordering

An implementation and experimental comparison of two node-ordering heuristics for **Contraction Hierarchies (CH)** — a preprocessing technique that speeds up shortest-path queries on large road networks by several orders of magnitude.

Built for the Algorithms Engineering course at TU Eindhoven.

## Overview

Route planning on large graphs (millions of nodes) is impractical with classical algorithms like Dijkstra alone. Contraction Hierarchies solve this by preprocessing the graph: nodes are contracted one by one in a chosen order, and shortcut edges are added to preserve shortest-path distances. Once preprocessing is done, queries run in microseconds.

The catch: query performance depends heavily on *which order* nodes are contracted in, and finding the optimal order is NP-hard. This project implements and compares two standard heuristics for approximating a good order:

- **Top-down**: importance is estimated once, in advance, using a static local-degree heuristic (`importance(v) = deg⁻(v)·deg⁺(v) + deg⁺(v)`), then all nodes are sorted and contracted in that fixed order.
- **Bottom-up**: importance is estimated dynamically. Nodes are held in a priority queue keyed on local edge difference, witness search cost, and contracted-neighborhood size, and re-scored after each contraction so the ordering adapts as the graph changes.

Both heuristics are implemented within the same CH framework (same shortcut/witness-search logic, same graph representation) so the comparison isolates the effect of the ordering strategy itself.

## Results

Evaluated on a randomly generated graph of 1,000 nodes / 2,000 edges, with 100 random source–target query pairs.

| Metric | Bottom-up | Top-down |
|---|---|---|
| Avg. preprocessing time | ~280 ms | ~85 ms (~3× faster) |
| Avg. query time | ~8,300 ns | ~8,900 ns |

**Takeaway:** top-down wins decisively on preprocessing speed, since it skips dynamic re-scoring and priority-queue maintenance entirely. Bottom-up trades that speed for a slightly more compact hierarchy, yielding marginally faster queries. In other words — top-down is preferable when the graph changes often or preprocessing time is constrained; bottom-up is preferable for static networks with high query volume, where preprocessing happens once but queries run many times.

Full methodology, hypotheses, and discussion are in [`Contraction_Hierarchies_Heuristics_Report_Final.pdf`](./Contraction_Hierarchies_Heuristics_Report_Final.pdf).

## Getting Started

### Prerequisites

Install the `GoogleTest` framework:

```bash
sudo apt-get update
sudo apt-get install libgtest-dev
cd /usr/src/gtest
sudo cmake .
sudo make
sudo cp lib/*.a /usr/lib
sudo ldconfig
```

This project is developed and tested under WSL:

```bash
wsl
```

### Build

```bash
cd src
make build
```

To remove all build artifacts:

```bash
cd src
make clean
```

### Run

```bash
cd src
# Run the experiment
./bld/experiment.exe graph_file destinations_file output_file run_number

# Run the test suite
./bld_tst/test_experiment.exe
```

## Team

- Maximilian Luca Georgescu
- Roberto Tormo Navarro
- Aleksandr Vardanian
- Bogdan Briciu

TU Eindhoven, Algorithms Engineering

## References

- Contraction Hierarchies: An Illustrative Guide
- [Wikipedia: Contraction Hierarchies](https://en.wikipedia.org/wiki/Contraction_hierarchies)
