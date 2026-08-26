#### This is a small script for Arch compatible distributions that runs simple benchmarks and stress tests.

Benchmarks are grouped into two categories in the generated charts.

Category 1 is Throughput and Compilation. Lower is better for every test in this group. It includes perf sched and mem, stress-ng cpu and mem, xz compression, ffmpeg compilation, y-cruncher pi calculation, argon2 hashing, blender rendering, primesieve, kernel defconfig, namd and x265 encoding.

Category 2 is Scheduler Latency. Each metric has its own direction. schbench wakeup latency and schbench p50 wakeup latency are lower is better. schbench throughput is higher is better. cyclictest scheduling latency is lower is better. schbench measures wakeup latency through the scheduler under test, while cyclictest at FIFO 99 measures the realtime envelope. Both run first on a quiet machine under the performance governor.

## Quick Start

```bash
# Run the full benchmark suite in a working directory
sudo ./cachyos-benchmarker /path/to/workdir

# The script will:
#   1. Prompt you to drop the page cache
#   2. Ask for a run label (or press Enter for auto-generated name)
#   3. Prepare required assets in the assets/ directory next to <workdir> (NAMD and the Blender model ship with the repo; the rest are downloaded once and shared by sibling workdirs)
#   4. Run all 14 benchmarks (this takes 15–25 minutes)
#   5. Write a .log to <workdir> and generate charts, CSV, JSON, and an HTML report

# After completion, the working directory contains:
#   benchie_<label>_<date>.log        — raw benchmark results
#   categorized_comparison_All.png    — stacked chart (Category 1 + 2)
#   kernel_version_comparison_All.png — cross-kernel grouped chart
#   test_performance.html             — HTML report
#   test_results_*.csv / .json        — machine-readable exports
# Downloaded assets are cached in the sibling assets/ directory and shared by
# sibling workdirs; --cleanup removes them.

# Example: compare two different kernels by running in separate directories
sudo ./cachyos-benchmarker /tmp/bench-kernel-A
# reboot into kernel B
sudo ./cachyos-benchmarker /tmp/bench-kernel-B

# Then run the scraper against both logfiles to generate a combined comparison:
cd /tmp/bench-comparison && cp /tmp/bench-kernel-A/benchie_*.log . && cp /tmp/bench-kernel-B/benchie_*.log . && python3 /path/to/benchmark_scraper.py
```

## How It Works

*   **cachyos-benchmarker**: The core script. It prepares the environment, prepares necessary assets, and runs a suite of 14 synthetic and real-world benchmarks (such as `stress-ng`, Blender CPU render, FFmpeg/Kernel compilation, x265 encoding, schbench, and cyclictest). Results, along with detailed system and `sched-ext` information, are logged to a `.log` file.
*   **benchmark_scraper.py**: A visualization and data extraction tool. It parses the generated `.log` files to aggregate performance metrics, compare different kernel or scheduler configurations, and generate a categorized composite chart with two sections (Throughput & Compilation / Scheduler Latency), alongside a cross-kernel comparison chart and an HTML report. It also automatically exports the aggregated raw data to time-stamped `.csv` and `.json` files for further analysis.
*   **kernel-autofdo.sh**: A helper script for hardware profiling. It automatically configures kernel branch sampling and runs the benchmarker alongside additional workloads (like `sysbench` and base-kernel compilation) to generate a footprint for AutoFDO.


# Credits

- Torvic: Author of this script
- https://github.com/julmajustus for creating the scrapper
