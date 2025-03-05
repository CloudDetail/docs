## Issue Description
The `ebpf-agent` in OneAgent remains in an unready state, with logs showing "Auto-adaptation failed" and the inability to fetch the adaptation file from the remote server.

## Issue Explanation
The `ebpf-agent` in OneAgent utilizes eBPF technology to collect polaris metrics. This agent relies on eBPF programs to function, which are compiled from development files specific to certain Linux kernel versions. While `ebpf-agent` supports common kernel versions, the vast array of kernel versions means not all are guaranteed to be supported. If `ebpf-agent` encounters an unsupported kernel version, it will attempt to automatically compile the eBPF program for that kernel version. To ensure the auto-compilation process succeeds, follow the steps below.

## Resolution Steps
### 1. Install Kernel Development Files
APO-one-agent requires kernel development files for kernel adaptation. These are standard development files released with each distribution kernel and uploaded to the installation source. You can directly fetch and install your development files from the source. Note that the installation method and file names may vary across different Linux distributions.

> **Note:** The version management of kernel development files differs across distributions and versions. It's possible that the kernel development files in the installation source do not match the current system kernel version. APO-one-agent's auto-adaptation requires you to install kernel headers that match the kernel version; otherwise, auto-adaptation will fail.

#### RedHat/Centos
```bash
yum install kernel-devel-$(uname -r)
```

#### Debian/Ubuntu
```bash
apt-get install linux-headers-$(uname -r)
```

### 2. Restart `apo-one-agent`
After successfully installing the kernel development files, restart `apo-one-agent` to trigger the auto-adaptation process.

After restarting, observe the startup logs of the `ebpf-agent` in OneAgent. The adaptation results will be clearly displayed in the logs. If adaptation still fails, please contact us for further assistance.

## Contact Us

If APO-one-agent fails to adapt due to various reasons, you can reach out to us through official channels for kernel assistance. We will provide remote adaptation support as soon as possible.
- [GitHub Issues](https://github.com/CloudDetail/apo/issues)
