# UKNNSS mdtest Benchmark


## I. Run Rules

This benchmark is intended to measure the metadata capability of the parallel file system(s) (PFS) provided by the bidder.  It measures the rate at which a file system can create, state, and delete files, and contains features that minimise caching/buffering effects. As such, the bidder should not utilise optimisations that cache/buffer file metadata or metadata operations in compute node memory. 

Observed benchmark performance shall be obtained from storage systems configured as closely as possible to the proposed storage systems. If the proposed solution includes multiple file access protocols (e.g., pNFS and NFS) or multiple tiers accessible by applications, benchmark results for mdtest shall be provided for each protocol and/or tier. If  the proposed solution includes multiple, separate file systems then benchmark results for IOR shall be provided for all proposed file systems.

Performance projections are permissible if they are derived from a similar system that is considered an earlier generation of a proposed system.

Each run of mdtest will report create-, stat- and delete-rates. For a given concurrency, all three rates must be reported from the same mdtest run. 

Reporting the maximum creation rates from one run and the maximum deletion rates from a different run is NOT valid.

## II. Required Runs

The bidder is free to determine the optimal number of compute nodes and MPI processes per node to demonstrate the peak metadata performance of their proposed solution.

1. **Single Shared Directory:** Create, stat, and remove files in a single shared directory. <br/>
`mdtest -F -C -T -r -i 3 -n <n> -d <dir> -N 1`
2. **Independent Directories:** Create, stat, and remove files in separate directories, assigning one directory per MPI process. <br/>
`mdtest -F -C -T -r -i 3 -n <n> -d <dir> -N 1 -u`

These tests are configured via command-line arguments, and the following section provides guidance on passing the correct options to `mdtest` for each test.
Note that the two tests differ only by the use of single  separate directories (`-u`). 

## III. Running mdtest

mdtest is executed as any other standard MPI application would be on the proposed system (e.g., with `mpirun` or `srun`).
For the sake of the following examples, `mpirun` is used to execute mdtest with 64 processes.
```
mpirun -np 64 mdtest <mdtest_options>
```

The `<mdtest_options>` are described below.

### a. Mandatory command line settings

The following command-line flags **MUST NOT** be changed or omitted:

* `-F` - only operate on files, not directories
* `-C` - perform file creation test
* `-T` - perform file stat test
* `-r` - perform file remove test
* `-N` - stride number between tasks for file/dir operation: set to 1 to avoid client caching
* `-i 3` - number of iterations the test will run

### b. Mandatory command line modifications

The following command-line flags **MUST** be changed:

* `-n` - the number of files **each MPI process** should manipulate.  For a test run with 64 MPI processes, specifying `-n 16384` will produce the required 1048576 files (64 MPI processes x 16384).  The total aggregate number of files created during  benchmarks (total MPI processes × `<n>`) must be **at least 1,000,000**.
* `-d /scratch` - the directory in which this test should be run.  **This must be an absolute path.**

* `-u` - toggle directory sharing.
If `-u` is absent, then all MPI processes act on a shared directory.
If `-u` is present, then each MPI process acts on its own directory.


## IV. Results

mdtest will execute file creation, file statting, and file deletion tests for each run.  The rate of file creating/stating/deleting are reported to stdout at the conclusion of each test, and the following rates should be reported:

* `File creation`
* `File stat`
* `File removal`

The maximum values for these rates must be reported for all tests. 
In addition, the concurrency (number of compute nodes and number of MPI processes used) for each run must be stated.
Specify whether the test was run with 0-byte files (default) or if a data payload was added (e.g., via the `-w` flag).
Because the `-i 3` flag is mandated, the bidder must report the **mean (average) rate** across the three iterations for file creation, statting, and removal.

