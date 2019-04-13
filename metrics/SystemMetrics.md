# System Metrics
This document serves to document the "look and feel" of the OpenCensus API for auto-collection of system metrics such as processes, memory & CPU usage, disks, etc.

## Supported platforms
TODO: Add list of supported platforms.

## Common metrics collection
These are some default metrics supported by OpenCensus system metrics package/contrib. This is to ensure consistency across language implementations, as far as practical.

| Metric Name                               | Unit  | Type                       | Description                                                        | Labels                  |
|-------------------------------------------|-------|----------------------------|--------------------------------------------------------------------|-------------------------|
| system/cpu_seconds/total                  | s     | Int64 Cumulative           | Total kernel/system user CPU in seconds.                           | Hostname, Mode          |
| system/processes/created                  | 1     | Int64 Cumulative           | Total number of times a process was created.                       | Hostname                |
| system/processes/running                  | 1     | Int64 Gauge                | Total number of running processes.                                 | Hostname                |
| system/processes/blocked                  | 1     | Int64 Gauge                | Total number of blocked processes.                                 | Hostname                |
| system/memory/total                       | By    | Int64 Gauge                | Total system memory capacity in bytes.                             | Hostname                |
| system/memory/free                        | By    | Int64 Gauge                | Total free system memory in bytes.                                 | Hostname                |
| system/memory/used                        | By    | Int64 Gauge                | Total used system memory in bytes.                                 | Hostname                |
| system/disk/total                         | By    | Int64 Gauge                | The total available system disk space in bytes.                    | Hostname, Volume        |
| system/disk/used                          | By    | Int64 Gauge                | The total used system disk space in bytes.                         | Hostname, Volume        |
| process/memory/rss                        | By    | Int64 Gauge                | Resident memory size in bytes.                                     | Hostname                |
| process/file_descriptor/max               | 1     | Int64 Gauge                | The maximum limit of file descriptor count.                        | Hostname                |
| process/file_descriptor/open              | 1     | Int64 Gauge                | The number of open file descriptor count.                          | Hostname                |
| process/heap/total                        | By    | Int64 Gauge                | The process's total allocated heap size in bytes.                  | Hostname                |
| process/heap/used                         | By    | Int64 Gauge                | The process's total used heap size in bytes.                       | Hostname                |
| process/cpu/usage                         | s     | Int64 Cumulative           | The total user and system CPU time spent in seconds.               | Hostname                |

### Labels
The label keys associated with the above metrics.

| Label Key          | Description                                                                                              |
|--------------------|----------------------------------------------------------------------------------------------------------|
| Mode               | The amount of time spent by the CPU in state (i.e. `user, nice, system, idle, iowait, stolen`).          |
| Volume             | The path the fs is mounted on (i.e. `/, /data`)                                                          |
| Hostname           | The hostname of the host (i.e. `opencensus-test`).                                                       |

## Language specific metrics collection
TODO: Add language-specific documentation for the list of supported metrics collection (e.g. garbage collection, threads, classloaders, etc.).
