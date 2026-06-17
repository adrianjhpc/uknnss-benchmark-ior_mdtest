# UKNNSS IOR Benchmark 


The intent of these benchmarks is to measure the bandwidth to and from
the parallel file system(s) (PFS) provided by the bidder.  We are
interested in and expect the bidder to measure the performance of
three workloads at multiple levels of concurrency, see
[below](#iii-required-runs).


## I. Run Rules

Observed benchmark performance shall be obtained from storage systems
configured as closely as possible to the proposed storage systems. If
a proposed solution includes multiple file access protocols (e.g.,
pNFS and NFS) or multiple tiers accessible by applications, benchmark
results for IOR shall be provided for each protocol and/or tier. If 
the proposed solution includes multiple, separate file systems then 
benchmark results for IOR shall be provided for all proposed file
systems.

Performance projections are permissible if they are derived from a
similar system that is considered an earlier generation of the
proposed system.

The bidder is expected to avoid any optimisations that cache or
buffer transferred data in system memory. However, page caches on the
storage subsystem's servers may still be used, but they must be
configured as they would be for the delivered UK NNSS system.

Each run of IOR will execute both read and write tests.
Read and write results must be reported from the same run;
reporting the maximum read bandwidth from one run
and the maximum write bandwidth from a different run is NOT valid.

## II. Running IOR

IOR is executed as any other standard MPI application would be on the
proposed system.  For example,
```	 
mpirun -np 64 ior -f load1-posix-filepertask.ior
#- or -
srun -n 64 ./ior -f load1-posix-filepertask.ior
```
will execute IOR with 64 processes and use the input configuration
file called `load1-posix-filepertask.ior`.

Annotated configuration files for required tests are supplied in the
[`inputs.UKNNSS`](inputs.UKNNSS) directory.


## III. Required Runs

We are interested in and expect the bidder to measure the performance of
three workloads at multiple levels of concurrency.

1. sequential access, large-transaction reads and writes, N to N (1 file per MPI process)
    - *a.* single node
    - *b.* 15% proposed number of compute nodes
    - *c.* sufficient compute nodes to achieve maximum result

2. sequential access, large-transaction reads and writes, N to 1 (all MPI processes writing to a single file using MPIIO)
    - *a.* 15% proposed number of compute nodes
    - *b.* sufficient compute nodes to achieve maximum result

3. random access, small-transaction reads and writes, N to N (1 file per MPI process)
    - *a.* single node
    - *b.* 15% proposed number of compute nodes
    - *c.* sufficient compute nodes to achieve maximum result

For each of the above three loads, we have provided an annotated IOR
script in the `inputs.UKNNSS` directory. All three configuration files should
be used and modified as described below.

### a. Mandatory configuration file modifications

In all three scripts, the bidder **MUST** modify the following
    parameters for each benchmark test:

* `numTasks` - the number of MPI processes to use.  The bidder may
  choose to run multiple MPI processes per compute node to achieve the
  highest bandwidth results.

* `segmentCount` - number of segments (blocks * numTasks) in a file.
  This governs the size of the file(s) written/read, and the amount of
  data written/read by each node must exceed 1.5 times the memory
  available for the file system's page cache (typically the entire
  node's RAM). See [below](#d-segmentcount-specification) for details.

* `memoryPerNode` - size (in %) of each node's RAM to be filled before
  running the benchmark test.  This value must be no less than 80% of
  the total RAM available on each compute node and is intended to
  represent the memory footprint of a real application.

* `reorderTasksConstant ` - stride to use to between write and read
  test phases. This should be set to the number of MPI processes per
  node to avoid client sise cache effects. For example, if the test runs
  with 4 MPI processes per node, this would be set to "4".

### b. Optional configuration file modifications for sequential workloads

For the sequential access tests (1 and 2), the bidder **MAY** modify the
following parameters for each test:

* `transferSize` - the size (in bytes) of a single data buffer to be
   transferred in a single I/O call.  The bidder should find the
   transferSize that produces the highest bandwidth results and report
   this optimal transferSize.  `blockSize` must always be equal to
   `transferSize`.

* `testFile` - path to data files to be read or written for this benchmark

* `hintsFileName` - path to MPI-IO hints file. Providing an MPI-IO
 "hints" file for the MPI-IO runs, which IOR will
look for in the file specified by the `hintsFileName` keyword in the
input file, is allowed.  Documentation on IOR's support for MPI-IO
hints can be found in the "HOW DO I USE HINTS?" section of the IOR
User Guide (found in `doc/USER_GUIDE`).

* `collective` - MPI-IO collective vs. independent operation mode

### c. Optional configuration file modifications for random workloads

For the random load (3), the bidder **MAY** modify only the following parameter for each test:

* `testFile` - path to data files to be read or written for this benchmark

### d. segmentCount specification

As mentioned above, `segmentCount` must be set so that the total
amount of data written is greater than 1.5 times the amount of RAM on
the compute nodes.  The total fileSize is given by
```
fileSize = segmentCount * blockSize * numTasks
```
So for a test on nodes with 480 GiB of RAM, `fileSize` must
be at least 720 GiB (737,280 MiB) multiplied by the number of nodes.  Assuming `blockSize=1MB` and `numTasks=64`
is optimal, an appropriate `segmentCount` would be
```
segmentCount = fileSize / ( blockSize * numTasks ) = 11520
```

Repeating the above example for the random load (3) and its fixed
block size of 4K:
```
fileSize = segmentCount * 4K * numTasks
```
So for a test on nodes with 480 GiB of RAM (503,316,480 KiB), `fileSize` must
be at least 720 GiB (754,974,720 KiB) multiplied by the number of nodes.  Assuming `numTasks=64`,
an appropriate `segmentCount` would be
```
segmentCount = fileSize / ( 4K * numTasks ) = 2949120
```

## IV. Results


The bandwidth measurements to be reported are the
`Max Write` and `Max Read` values (in units of `MB/s`) reported to stdout.
In addition, the concurrency 
(number of compute nodes and number of MPI processes used) 
for each run must be stated.


