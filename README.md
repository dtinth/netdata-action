# netdata-action

> [!CAUTION]
> This action is a work in progress.

This action sets up [netdata](https://github.com/netdata/netdata) to send metrics to a [Prometheus server](https://github.com/prometheus/prometheus) via the [Prometheus remote write API](https://learn.netdata.cloud/docs/exporting-metrics/prometheus-remote-write) while GitHub Actions is running. This gives you detailed visibility into the performance of your GitHub Actions workflows, which can be helpful optimizing your workflow performance.

![image](https://github.com/user-attachments/assets/14dd94f1-8c12-41ff-8ce2-f3e8d7d7a32f)

In this example, the application server consumes only 2 CPUs out of 4. Further digging shows that our E2E test setup only runs 2 instance of the application server. We can increase the number of instances to 4 to better utilize the available resources and make tests run faster.

Without any configuration, Netdata automatically tracks hundreds of metrics about your system's performance. Here are some metrics we find useful:

<!-- prettier-ignore -->
| Metric | Description |
| --- | --- |
| `system_cpu_percentage` | CPU percentage to see how much is being used (user, system), and how much is waiting for I/O (iowait), and how much is idle. |
| `cgroup_mem_usage_MiB` | Memory usage of each cgroup. A cgroup usually corresponds to a Docker container. |
| `cgroup_cpu_percentage` | CPU percentage of each cgroup. This lets you see which Docker container uses the most CPU. |
| `app_cpu_utilization_percentage` | CPU usage of each process. This lets you see which processes are using the most CPU. |
| `app_mem_usage_MiB` | Memory usage of each process. This lets you see which processes are using the most memory. |
| `system_load_load` | System load average over 1, 5, and 15 minutes. |

It also tracks metrics about your network, disk, and more.
