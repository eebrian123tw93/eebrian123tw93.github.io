---
title: macmon_exporter
tags:
  - prometheus
date: '2026-01-28 18:13:00'
updated: '2026-02-05 05:46:52'
categories: []
description: ''
keywords:
  - prometheus
abbrlink: bf9fa31c
Aliases:
---
# 程式碼
```python
import sys
import json
import threading
from prometheus_client import start_http_server, Gauge

# ---------- Prometheus Metrics ----------
all_power = Gauge("macmon_all_power_watts", "Total power consumption")
cpu_power = Gauge("macmon_cpu_power_watts", "CPU power consumption")
gpu_power = Gauge("macmon_gpu_power_watts", "GPU power consumption")
gpu_ram_power = Gauge("macmon_gpu_ram_power_watts", "GPU RAM power consumption")
ram_power = Gauge("macmon_ram_power_watts", "RAM power consumption")
sys_power = Gauge("macmon_sys_power_watts", "System power consumption")

cpu_temp = Gauge("macmon_cpu_temperature_celsius", "Average CPU temperature")
gpu_temp = Gauge("macmon_gpu_temperature_celsius", "Average GPU temperature")

ram_total = Gauge("macmon_ram_total_bytes", "Total RAM")
ram_usage = Gauge("macmon_ram_usage_bytes", "Used RAM")
swap_total = Gauge("macmon_swap_total_bytes", "Total Swap")
swap_usage = Gauge("macmon_swap_usage_bytes", "Used Swap")

pcpu_cores = Gauge("macmon_pcpu_cores", "Performance CPU core count")
pcpu_usage = Gauge("macmon_pcpu_usage_ratio", "Performance CPU usage ratio")

ecpu_cores = Gauge("macmon_ecpu_cores", "Efficiency CPU core count")
ecpu_usage = Gauge("macmon_ecpu_usage_ratio", "Efficiency CPU usage ratio")

gpu_cores = Gauge("macmon_gpu_cores", "GPU core count")
gpu_usage = Gauge("macmon_gpu_usage_ratio", "GPU usage ratio")

# ---------- Data Reader ----------
def read_macmon_pipe():
    for line in sys.stdin:
        line = line.strip()
        if not line:
            continue

        try:
            print(line)
            data = json.loads(line)

            all_power.set(data.get("all_power", 0))
            cpu_power.set(data.get("cpu_power", 0))
            gpu_power.set(data.get("gpu_power", 0))
            gpu_ram_power.set(data.get("gpu_ram_power", 0))
            ram_power.set(data.get("ram_power", 0))
            sys_power.set(data.get("sys_power", 0))

            cpu_temp.set(data["temp"]["cpu_temp_avg"])
            gpu_temp.set(data["temp"]["gpu_temp_avg"])

            mem = data["memory"]
            ram_total.set(mem["ram_total"])
            ram_usage.set(mem["ram_usage"])
            swap_total.set(mem["swap_total"])
            swap_usage.set(mem["swap_usage"])

            pcpu_cores.set(data["pcpu_usage"][0])
            pcpu_usage.set(data["pcpu_usage"][1])

            ecpu_cores.set(data["ecpu_usage"][0])
            ecpu_usage.set(data["ecpu_usage"][1])

            gpu_cores.set(data["gpu_usage"][0])
            gpu_usage.set(data["gpu_usage"][1])

        except Exception as e:
            print(f"Parse error: {e}", file=sys.stderr)

# ---------- Main ----------
if __name__ == "__main__":
    start_http_server(9105)  # Prometheus scrape port
    t = threading.Thread(target=read_macmon_pipe, daemon=True)
    t.start()
    t.join()
```

# 執行
```bash
macmon pipe | python main.py

#curl
curl http://localhost:9105/
```