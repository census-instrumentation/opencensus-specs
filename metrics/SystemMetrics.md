# System Metrics
This document serves to document the "look and feel" of the OpenCensus API for auto-collection of system metrics such as processes, memory & CPU usage, disks, etc.

## Supported platforms
TODO: Add list of supported platforms.

## Common metrics collection
These are some default metrics supported by OpenCensus system metrics package/contrib. This is to ensure consistency across language implementations, as far as practical.

| Metric Name                               | Unit  | Type                       | Description                                                        | Labels                  |
|-------------------------------------------|-------|----------------------------|--------------------------------------------------------------------|-------------------------|
| system/cpu_seconds                        | s     | Int64 Cumulative           | System CPU in seconds.                                   		  | Hostname, Mode                 |
| system/processes                  		| 1     | Int64 Cumulative           | System processes count.                       					  | Hostname, State                |
| system/memory                       		| By    | Int64 Gauge                | System memory capacity in bytes.                                   | Hostname, Category             |
| system/disk                         		| By    | Int64 Gauge                | System disk space in bytes.                    					  | Hostname, Volume, Category     |
| process/memory/rss                        | By    | Int64 Gauge                | Resident memory size in bytes.                                     | Hostname             |
| process/file_descriptor              		| 1     | Int64 Gauge                | The number of file descriptor count.                          	  | Hostname, fds_type             |
| process/heap                        		| By    | Int64 Gauge                | The process's heap size in bytes.                  				  | Hostname, Category             |
| process/cpu/usage                         | s     | Int64 Cumulative           | The total user and system CPU time spent in seconds.               | Hostname             |

### Labels
The label keys associated with the above metrics.

| Label Key          | Description                                                                                              |
|--------------------|----------------------------------------------------------------------------------------------------------|
| Mode               | The amount of time spent by the CPU in state (i.e. `total, user, nice, system, idle, iowait, stolen`).   |
| State              | The process state (i.e. `created, running, blocked, stopped etc`).   									|
| Category           | The memory/disk category (i.e. `total, free, used etc`).   												|
| fds_type           | The file descriptors type (i.e. `open, max`).   															|
| Volume             | The path the fs is mounted on (i.e. `/, /data`)                                                          |
| Hostname           | The hostname of the host (i.e. `opencensus-test`).                                                       |

## Language specific metrics collection
TODO: Add language-specific documentation for the list of supported metrics collection (e.g. garbage collection, threads, classloaders, etc.).
