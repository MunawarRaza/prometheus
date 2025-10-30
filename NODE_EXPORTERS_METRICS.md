## Node Metrics ##
```
# HELP node_arp_entries ARP entries by device
# TYPE node_arp_entries gauge

# HELP node_boot_time_seconds Node boot time, in unixtime.
# TYPE node_boot_time_seconds gauge

###### CPU ######
# HELP node_cpu_guest_seconds_total Seconds the CPUs spent in guests (VMs) for each mode.
# TYPE node_cpu_guest_seconds_total counter
node_cpu_guest_seconds_total

# HELP node_cpu_seconds_total Seconds the CPUs spent in each mode.
# TYPE node_cpu_seconds_total counter
node_cpu_seconds_total

###### Filesystem ######

# HELP node_filesystem_size_bytes Filesystem size in bytes.
# TYPE node_filesystem_size_bytes gauge
node_filesystem_size_bytes{device="/dev/sda2",device_error="",fstype="ext4",mountpoint="/"} 1.05078775808e+11

# HELP node_filesystem_avail_bytes Filesystem space available to non-root users in bytes.
# TYPE node_filesystem_avail_bytes gauge
node_filesystem_avail_bytes{device="/dev/sda2",device_error="",fstype="ext4",mountpoint="/"} 7.7884633088e+10

# HELP node_filesystem_free_bytes Filesystem free space in bytes.
# TYPE node_filesystem_free_bytes gauge
node_filesystem_free_bytes{device="/dev/sda2",device_error="",fstype="ext4",mountpoint="/"} 8.3269595136e+10

Note: Every information getting from the above commands are same if we do df -k in OS. With df -k, output shows in killobytes and with above commands output shows in bytes


# HELP node_filesystem_device_error Whether an error occurred while getting statistics for the given device.
# TYPE node_filesystem_device_error gauge
node_filesystem_device_error{device="/dev/sda2",device_error="",fstype="ext4",mountpoint="/"} 0

# HELP node_filesystem_files Filesystem total file nodes.
# TYPE node_filesystem_files gauge
node_filesystem_files{device="/dev/sda2",device_error="",fstype="ext4",mountpoint="/"} 6.5536e+06

# HELP node_filesystem_files_free Filesystem total free file nodes.
# TYPE node_filesystem_files_free gauge
node_filesystem_files_free{device="/dev/sda2",device_error="",fstype="ext4",mountpoint="/"} 6.329148e+06

# HELP node_filesystem_readonly Filesystem read-only status.
# TYPE node_filesystem_readonly gauge
node_filesystem_readonly{device="/dev/sda2",device_error="",fstype="ext4",mountpoint="/"} 0

###### Memory ######


