# netdata-action

> [!CAUTION]
> This action is a work in progress.

**CI workflows can be slow because of many reasons.** Maybe we ran out of CPU power (should use bigger runner), or maybe the CPU is underutilized (should add more load). Maybe the CPU is idle waiting for IO, or maybe our configuration didn’t utilize all the CPU cores available. Maybe there is a lot of page faults, or maybe the network or disk I/O is the bottleneck. This action sets up [netdata](https://github.com/netdata/netdata) to send detailed system performance metrics to a [Prometheus server](https://github.com/prometheus/prometheus) via the [Prometheus remote write API](https://learn.netdata.cloud/docs/exporting-metrics/prometheus-remote-write) while GitHub Actions is running. This gives you detailed visibility into the performance of your GitHub Actions workflows, which can be helpful for optimizing your workflow performance.

![image](https://github.com/user-attachments/assets/14dd94f1-8c12-41ff-8ce2-f3e8d7d7a32f)

In this example, the application server consumes only 2 CPUs out of 4. Total CPU utilization is around 75%. Further digging shows that our E2E test setup only runs 2 instance of the application server. We can increase the number of instances to 4 to better utilize the available resources and make tests run faster. We can also slightly increase the number of workers in Playwright to get CPU utilization closer to 90%.

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

Here's a usage documentation based on the provided test.yml file:

## Usage

To use the `eventpop/netdata-action` in your GitHub Actions workflow, add the action to your workflow file:

```yaml
- name: Run Netdata
  uses: eventpop/netdata-action@main
  with:
    exporting-config: |
      [prometheus_remote_write:prom]
      enabled = yes
      destination = <prometheus-ip>:9090
      username = <username>
      password = ${{ secrets.PROMETHEUS_REMOTE_WRITE_PASSWORD }}
      remote write URL path = /api/v1/write
      update every = 5
```

Replace the placeholders and set up the secrets as needed.

If your Prometheus server uses HTTPS and you’re unable to connect directly, you can use Caddy as a reverse proxy by adding the following step before the Netdata action:

```yaml
- name: Run Caddy to proxy Prometheus
  run: |
    docker run -d \
      --name promproxy \
      -p 9090:9090 \
      caddy \
      caddy reverse-proxy \
        --from :9090 \
        --to https://your-prometheus-server \
        --change-host-header
```

In this case, set `<prometheus-ip>` to `127.0.0.1` in the Netdata configuration.
