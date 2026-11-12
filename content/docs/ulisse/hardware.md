---
title: Compute Nodes
weight: 10
---

Ulisse have several different classes of compute nodes; each of them with its own 
different setup and that can be accessed by different user groups.

| Nodes | Access | CPUS (number of cores) | RAM | Scratch |
|:---|:---|:---|:---|:---|
| **kim[01-08]** | **Offline** | 2 x AMD EPYC 9224 (48) | 755 GB | 3.5 T `/scratch` SSD |
| **kim[01-08]** | everyone | 2 x AMD EPYC 9224 (48) | 755 GB | 3.5 T `/scratch` SSD |
| **diamond[01-12]** | `diamond` | 2 x Intel Xeon Gold 5120 (28) | 128 GB | 1.6 T `/scratch` |
| **diamond[25-32]** | `diamond` | 2 x Intel Xeon Gold 5120 (28) | 128 GB | 1.6 T `/scratch` |
| **euterpe[01-12]** | `euterpe` | 2 x AMD EPYC 7413 (48) | 512 GB | 4.4 T `/scratch` + 1.8 T `/fast_scratch/` |