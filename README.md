#  UKNNSS Storage Benchmark

This repository contains information on the storage benchmark for the UK NNSS
procurement. 

> [!IMPORTANT]
> Please do not contact the benchmark or code maintainers directly with any questions. All questions must be submitted via the procurement response mechanism.

## Benchmark Overview

The storage microbenchmarks are intended to measure the performance of storage systems.

## Software

MPI and MPI-IO are required to build and run the codes. The source code
used for this benchmark is derived from IOR 3.3.0 and it is included here. 
More information about IOR is available at https://github.com/hpc/ior.

> [!CAUTION]
> All results submitted should be based on the versions of IOR and
mdtest included in this repository.

> [!NOTE]
> This benchmark/repository is closely based on the one used for the [NERSC-10 benchmarks](https://www.nersc.gov/systems/nersc-10/benchmarks/)

## Building the benchmark

MPI and MPI-IO are required to build and run the codes.

After extracting the tar file, ensure that the MPI compiler wrappers (e.g.,
`mpicc`) are in `$PATH` and then

    cd ior-3.3.0
    ./configure
    make

This will build both IOR with the POSIX and MPI-IO interfaces and create the
IOR executable at `src/ior`.  It will also build mdtest and place the
executable at `src/mdtest`.

### Pre-approved code modifications

Modifications to the benchmark application code are only permissible to enable
correct compilation and execution on the target platform.
Any modifications must be fully documented (e.g., as a pull request, diff or patch file)
and reported with the benchmark results.


## Running the benchmark

### Required tests

To complete the entire benchmark, two sets of measurements are required:

- IOR will be used to measure parallel I/O performance using both the POSIX
  and MPI-IO APIs. The specifics of the IOR tests to be performed are in the
  file named [`README.ior.UKNNSS.md`](README.ior.UKNNSS.md).
- mdtest will be used to measure the performance of various metadata operations
  using MPI to execute the operations and collect the results. The specifics of
  the mdtest tests to be run are in the file named [`README.mdtest.UKNNSS.md`](README.mdtest.UKNNSS.md).

## Results

### Benchmark platform description

The bidder must provide a comprehensive description of the environment in which
each benchmark was run. This must include:

- Client and server system configurations, including node and processor counts,
  processor models, memory size and speed, and OS (names and versions)
- Storage media and their configurations used for each tier of the storage
  subsystem
- Network fabric used to connect servers, clients, and storage, including
  network configuration settings and topology
- Client and server configuration settings including
    * Client and server sysctl settings
    * Driver options
    * Network interface options
    * File system configuration and mount options
- Compiler name and version, compiler options, and libraries used to build
  benchmarks
- Include all the source and makefiles used to build on the target platform and input files and runscripts
- Include all standard output files.

### Performance data

The required measurements to be reported are described in the files
[README.ior.UKNNSS.md](README.ior.UKNNSS.md) and
[README.mdtest.UKNNSS.md](README.mdtest.UKNNSS.md).


