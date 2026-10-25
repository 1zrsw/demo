# OS3 Technical Interview — Question Bank with Answers
### 3 Slabs × 50 Questions | Increasing Difficulty | L1 → L2 Role Level
### Focus: Administration, Troubleshooting, Monitoring (Linux, Containers/Kubernetes, RKE2)

---

# SLAB 1 — FOUNDATIONAL (L1)

**A. Shell & Command Line**

1. **What is the difference between a shell and a terminal?**
   The terminal is the interface/window you type into; the shell is the program (bash, zsh, etc.) that interprets your commands.

2. **How do you redirect standard output to a file, and how is `>` different from `>>`?**
   `>` writes output to a file, overwriting it; `>>` appends output to the end of the file.

3. **What does the pipe (`|`) operator do?**
   - Sends the stdout of one command as stdin to the next.
   - Chains multiple commands into a single pipeline.
   - No temp file is created; data streams directly.
   - Example: `ps aux | grep nginx`.

4. **How would you search for "error" inside all `.log` files under a directory?**
   `grep -r "error" /path/to/dir --include="*.log"`

5. **How do you find all files larger than 100MB under `/var`?**
   `find /var -type f -size +100M`

6. **Difference between `man`, `info`, and `--help`?**
   `man` gives the full manual page; `info` gives a more detailed, hyperlinked GNU doc; `--help` gives a quick command-line usage summary.

7. **What does `history` do, and how do you re-run the last `ssh` command?**
   `history` lists previously run commands; `!ssh` re-executes the most recent command starting with "ssh".

8. **How do you check your current shell?**
   `echo $SHELL` or `echo $0`.

**B. File System**

9. **What is FHS and why does it matter?**
   The File-system Hierarchy Standard defines standard directory locations (`/etc`, `/var`, etc.) so software and admins know where to expect files across distros.

10. **What's typically stored in `/etc`, `/var`, `/home`?**
    - `/etc` — system-wide configuration files.
    - `/var` — variable data: logs, spool, cache.
    - `/home` — user personal directories.
    - (Bonus) `/usr` — installed software and libraries.

11. **Hard link vs symbolic link?**
    A hard link is another name pointing to the same inode (same data); a symlink is a separate file that points to a path and breaks if the target moves.

12. **How do you identify file type from `ls -l`?**
    The first character of the permission string: `-` regular, `d` directory, `l` symlink, `b`/`c` block/char device.

13. **Command to check directory disk usage?**
    `du -sh /path`

14. **Difference between `df` and `du`?**
    `df` reports filesystem-level free/used space; `du` reports space used by specific files/directories.

**C. Text Editing (Vim)**

15. **Three main Vim modes and how to switch?**
    Normal (default, `Esc`), Insert (`i` to enter, `Esc` to exit), Visual (`v` to select text, `Esc` to exit).

16. **How to save and quit in Vim?**
    `:wq` (or `:x`) saves and quits; `ZZ` in normal mode does the same.

17. **How to search and replace all occurrences of a word?**
    `:%s/old/new/g`

**D. Users, Groups & Permissions**

18. **`/etc/passwd` vs `/etc/shadow`?**
    `/etc/passwd` holds user account info (UID, shell, home) readable by all; `/etc/shadow` holds encrypted passwords and aging info, readable only by root.

19. **Create a user and add to a secondary group?**
    `useradd -G groupname username` (or `usermod -aG groupname username` for an existing user).

20. **Meaning of `rwxr-xr--`?**
    Owner: read/write/execute; Group: read/execute; Others: read-only.

21. **`755` in symbolic form and meaning?**
    `rwxr-xr-x` — owner has full access, group and others can read/execute but not write.

22. **Purpose of SUID?**
    - Runs the executable with the file owner's privileges, not the caller's.
    - Used so normal users can perform privileged actions safely.
    - Example: `passwd` runs as root via SUID so users can change their own password.
    - Shown as `s` in the owner execute position.

23. **What is the sticky bit?**
    Set on shared directories (like `/tmp`) so users can only delete/rename their own files, even with write access to the directory.

24. **What is an ACL and when is it needed?**
    Access Control Lists allow permissions for multiple specific users/groups beyond the standard owner/group/other model — needed when more than one group needs distinct access levels.

25. **Change owner and group in one command?**
    `chown user:group file`

26. **Primary group vs supplementary group?**
    Primary group is the default group assigned at file creation (in `/etc/passwd`); supplementary groups grant additional access and are listed in `/etc/group`.

**E. Process Management**

27. **What is a process, and what does `ps aux` show?**
    A process is a running instance of a program; `ps aux` shows all processes with user, PID, CPU/memory usage, and command.

28. **`top` vs `ps`?**
    `top` gives a real-time, continuously refreshing view; `ps` gives a static snapshot at the moment it's run.

29. **Graceful vs forceful termination?**
    `kill -15` (SIGTERM) asks the process to shut down cleanly; `kill -9` (SIGKILL) forcibly terminates it immediately without cleanup.

30. **Foreground vs background job?**
    A foreground job holds the terminal until it finishes; a background job (`&`) runs without blocking the shell.

31. **Send a foreground job to background?**
    `Ctrl+Z` to suspend, then `bg` to resume it in the background.

32. **What does `nice` do, and its range?**
    Sets a process's scheduling priority; range is -20 (highest priority) to 19 (lowest), default 0.

33. **What is a zombie process?**
    A terminated process whose exit status hasn't been read by its parent yet, so it still holds an entry in the process table.

**F. System & Service Management**

34. **What is systemd, and what problem does it solve?**
    A modern init system and service manager that replaced SysV init; it offers parallel startup, dependency management, and unified logging (journald).

35. **Start/stop/status a service with systemctl?**
    `systemctl start/stop/status servicename`

36. **Enable v/s start a service?**
    "Start" runs it now; "enable" configures it to auto-start at boot — they're independent.

37. **systemd targets vs runlevels?**
    Targets are systemd's equivalent of old SysV runlevels (e.g., `multi-user.target` ≈ runlevel 3), but are more flexible and can group multiple units.

38. **High-level boot process?**
    BIOS/UEFI initializes hardware → bootloader (GRUB2) loads the kernel → kernel initializes and mounts root filesystem → init/systemd starts services.

39. **What is GRUB2?**
    The default Linux bootloader; it presents a boot menu and loads the selected kernel and initramfs into memory.

**G. Task Scheduling**

40. **`cron` vs `at`?**
    `cron` runs recurring scheduled jobs; `at` runs a job once at a specified future time.

41. **Meaning of `0 2 * * *`?**
    Run at 2:00 AM every day (minute=0, hour=2, every day/month/weekday).

42. **View and edit current user's crontab?**
    `crontab -l` to view, `crontab -e` to edit.

43. **Advantage of systemd timers over cron?**
    - Integrated logging via journald.
    - Can express dependencies on other units.
    - Support for random delays and persistent (catch-up) execution after downtime.
    - Better monitoring via `systemctl status`.

**H. Privilege Delegation & Remote Access**

44. **`su` vs `sudo`?**
    `su` switches to another user's full session (usually root) after entering that user's password; `sudo` runs a single command as another user (default root) using the invoker's own password, with logging.

45. **Where is sudo configured, and why `visudo`?**
    In `/etc/sudoers` (and `/etc/sudoers.d/`); `visudo` locks the file and validates syntax before saving, preventing a broken config that locks out sudo access.

46. **SSH key-based authentication and why preferred?**
    Uses a public/private key pair instead of a password; more secure against brute-force attacks and enables passwordless automation.

47. **Default SSH port and connecting with an identity file?**
    Port 22; `ssh -i /path/to/key user@host`

**I. Storage & Software Basics**

48. **Disk vs partition vs file-system?**
    A disk is the physical storage device; a partition is a logical division of that disk; a file-system is the structure (EXT4, XFS, etc.) that organizes files within a partition.

49. **Two filesystem types and one difference?**
    ext4 and XFS — ext4 is broadly compatible and easy to shrink, while XFS scales better for very large files/filesystems but can't be shrunk online.

50. **RPM directly vs Zypper/YUM?**
    RPM installs individual packages without resolving dependencies; Zypper/YUM wrap RPM and automatically resolve and fetch dependencies from repositories.

---

# SLAB 2 — WORKING ADMINISTRATOR (L1/L2)

**A. Troubleshooting & Monitoring — Linux**

1. **Server is "slow" — first five checks?**
   - `uptime` / load average.
   - `top` or `htop` for CPU/memory hogs.
   - `iostat`/`vmstat` for I/O and swap activity.
   - `df -h` for disk space.
   - `dmesg`/journal for kernel or hardware errors.

2. **Identify top memory consumer and confirm a leak?**
   Use `top`/`ps aux --sort=-%mem` for current usage; confirm a leak by sampling memory of the process over time (e.g., repeated `ps` or `smem`) and watching for steady growth without release.

3. **`df` shows 100% but `du` doesn't match — why?**
   Likely a deleted-but-open file still held by a running process; find it with `lsof +L1` or `lsof | grep deleted` and restart/kill the holding process to reclaim space.

4. **User can't SSH into a server that's up — troubleshooting steps?**
   - Confirm sshd is running (`systemctl status sshd`).
   - Check firewall/port 22 reachability (`ss -tlnp`, `nc`).
   - Check `/etc/hosts.allow`/`deny` or fail2ban bans.
   - Review `/var/log/auth.log` or journal for auth failures.

5. **Check journald logs for one service, last hour?**
   `journalctl -u servicename --since "-1 hour"`

6. **A systemd service keeps failing to start — how do you find out why?**
   `systemctl status servicename` for the immediate error, then `journalctl -u servicename -xe` for detailed logs and exit codes.

7. **What is load average, and what do the three numbers mean?**
   Average number of processes waiting for CPU/IO over 1, 5, and 15 minutes — rising numbers indicate increasing contention over time.

8. **How to determine what changed in the last 24 hours?**
   - Check package manager history (`zypper history` / `yum history`).
   - `find /etc -mtime -1` for recently modified configs.
   - Review journal/log timestamps.
   - Check config management/version control (e.g., etckeeper, git) if in use.

9. **What is log rotation, and how to troubleshoot `/var/log` filling too fast?**
   Log rotation (via `logrotate`) archives/compresses/deletes old logs on a schedule; if it's filling fast, identify the noisiest log with `du -sh /var/log/*`, check for a misbehaving service spamming logs, and tighten rotation frequency/size limits.

**B. Users, Groups, Sudo & Security**

10. **Audit sudo access and allowed commands?**
    Review `/etc/sudoers` and files under `/etc/sudoers.d/`, and check group membership (e.g., `wheel`/`sudo` group) via `getent group sudo`.

11. **Deleted user's files now show a UID — why, and fix?**
    The UID no longer maps to a username in `/etc/passwd`; fix by recreating the user with the same UID or reassigning ownership with `chown -R newuser:newgroup /path`.

12. **Authentication vs authorization via PAM?**
    Authentication verifies identity (who you are — e.g., password check); authorization decides what you're allowed to do (e.g., account/session restrictions) — PAM modules handle both in separate stacked steps.

13. **What is SSSD and what does it solve?**
    System Security Services Daemon provides a unified client for centralized identity/auth (LDAP, AD, Kerberos), caching credentials for offline login and simplifying integration.

14. **Enforce password aging/complexity for local users?**
    Use `chage` for aging policy (max age, warning days) and `/etc/security/pwquality.conf` (via PAM) for complexity rules.

**C. LVM & Storage Administration**

15. **PV, VG, LV relationship?**
    Physical Volumes (raw disks/partitions) are grouped into a Volume Group (a storage pool), from which Logical Volumes (usable "virtual partitions") are carved out.

16. **Extend an LV and filesystem without downtime?**
    `lvextend -L +10G /dev/vg/lv` followed by `resize2fs /dev/vg/lv` (ext4) or `xfs_growfs` (XFS) — both can typically run on a mounted, live filesystem.

17. **Migrate data off a failing disk in a multi-PV VG live?**
    Use `pvmove` to relocate extents from the failing PV to other PVs in the VG, then `vgreduce` to remove the failing PV once empty.

18. **What is an LVM snapshot and a use case?**
    A point-in-time, copy-on-write copy of a logical volume; useful to take a safety snapshot before a risky upgrade so you can roll back quickly if it fails.

19. **What are Btrfs subvolumes?**
    Independently mountable, lightweight logical divisions within a single Btrfs filesystem, unlike fixed-size traditional partitions — they share the underlying storage pool dynamically.

20. **How does Snapper use Btrfs snapshots?**
    Snapper automatically creates and manages Btrfs snapshots (e.g., before/after package installs) enabling quick rollback of the system to a previous state.

21. **Check mounted filesystems and mount options?**
    `mount | column -t` or `findmnt`, and `cat /proc/mounts` for raw details.

22. **Filesystem won't unmount — "device is busy" — how to find the cause?**
    `lsof +D /mountpoint` or `fuser -vm /mountpoint` to list processes holding it open, then stop/kill them before retrying `umount`.

**D. Networking**

23. **Check IP configuration and interface status?**
    `ip addr show` and `ip link show`

24. **Diagnose DNS v/s routing v/s firewall failure?**
    - Ping the destination IP directly (rules out DNS if it works).
    - `dig`/`nslookup` the hostname to isolate DNS.
    - `ip route` / `traceroute` to check routing path.
    - `ss`/firewall rules (`iptables`/`firewalld`) to check blocking.

25. **Static vs dynamic IP — where is each used?**
    Static IPs are manually assigned and used for servers/infrastructure needing a fixed address; dynamic IPs are assigned via DHCP and typical for client workstations.

26. **Find which process is listening on a port?**
    `ss -tlnp | grep :PORT`

27. **Ping works but DNS resolution fails — what to check?**
    Check `/etc/resolv.conf` for correct nameservers, verify the DNS server is reachable, and test resolution directly with `dig @nameserver hostname`.

28. **IPv4 vs IPv6 conceptually, relevant for troubleshooting?**
    IPv4 uses 32-bit dotted-decimal addresses with widespread NAT; IPv6 uses 128-bit hex addresses with built-in larger address space — dual-stack systems can fail differently on each, so check both `ip -4` and `ip -6`.

29. **Capture traffic to debug connectivity?**
    Use `tcpdump -i eth0 host <ip> -w capture.pcap` to capture, then inspect with `tcpdump -r` or Wireshark.

**E. Software & Repository Management**

30. **Find which package provides a specific file/command?**
    `zypper what-provides /path/to/file` or `rpm -qf /path/to/file`

31. **Update fails due to dependency conflict — how to resolve?**
    Inspect the conflict details in the error output, check for held/locked packages, try `zypper install --force-resolution` or resolve by removing the conflicting package, then retry.

32. **Add, prioritize, and remove a repository?**
    `zypper addrepo <url> <name>`; set priority with `zypper mr -p <priority> <name>`; remove with `zypper removerepo <name>`.

33. **Roll back a package to a previous version?**
    Check available versions with `zypper se -s <pkg>` or use the local RPM cache/repo history, then `zypper install <pkg>=<old-version>` (or `--oldpackage`).

**F. Logging & Diagnostics**

34. **syslog vs journald — can they coexist?**
    syslog writes structured text logs to `/var/log`; journald stores structured binary logs with rich metadata — yes, they commonly coexist, with journald forwarding to rsyslog.

35. **What is supportconfig/SOSReport used for?**
    It collects a comprehensive snapshot of system configuration, logs, and hardware info into one archive — typically generated for vendor support/diagnostics.

36. **Persist journald logs across reboots?**
    Create `/var/log/journal` directory (`mkdir -p /var/log/journal`) and set `Storage=persistent` in `/etc/systemd/journald.conf`, then restart journald.

37. **Suspected kernel-level issue — where to look first?**
    `dmesg` and `journalctl -k` for kernel ring buffer messages.

**G. Encryption & Certificates**

38. **SSL/TLS handshake conceptually, for a "certificate not trusted" error?**
    The client checks the server's certificate against a trusted CA chain; "not trusted" means the cert is self-signed, expired, or the CA isn't in the client's trust store.

39. **Generate and inspect a certificate with OpenSSL?**
    Generate: `openssl req -x509 -newkey rsa:2048 -keyout key.pem -out cert.pem`; inspect: `openssl x509 -in cert.pem -noout -text`.

40. **What is GPG used for with packages/files?**
    Signing and verifying package/repo authenticity and integrity, and encrypting/decrypting files for secure exchange.

**H. Shell Scripting**

41. **Script structure to loop through only `.log` files?**
    ```
    for f in /path/*.log; do
      process "$f"
    done
    ```

42. **Check if a script argument was provided, exit if not?**
    ```
    if [ -z "$1" ]; then
      echo "Argument required"; exit 1
    fi
    ```

43. **`$@` vs `$*` in bash?**
    `$@` expands to each argument as a separate quoted word (preserves spacing in args); `$*` expands to all arguments as a single word joined by the first character of `IFS`.

**I. Containers & Kubernetes — Working Level**

44. **Container vs VM — why it matters for troubleshooting?**
    Containers share the host kernel and are isolated via namespaces/cgroups (lightweight, fast-starting); VMs run a full separate OS/kernel — so container issues often trace back to the host kernel, not a hypervisor layer.

45. **Pod statuses: Pending, CrashLoopBackOff, ImagePullBackOff?**
    - Pending: pod can't be scheduled (resources/affinity/PVC not ready).
    - CrashLoopBackOff: container keeps crashing after starting.
    - ImagePullBackOff: Kubernetes can't pull the container image (auth/tag/registry issue).

46. **View logs for a crashing pod, including previous instance?**
    `kubectl logs <pod>` for current; `kubectl logs <pod> --previous` for the crashed instance's logs.

47. **ConfigMap vs Secret, and why not put sensitive data in a ConfigMap?**
    Both externalize configuration, but Secrets are base64-encoded and can be encrypted at rest/RBAC-restricted; ConfigMaps are plain text and visible to anyone with read access.

48. **Deployment vs ReplicaSet, and what happens on rolling update?**
    A ReplicaSet just ensures a set number of pod replicas run; a Deployment manages ReplicaSets and orchestrates rolling updates — old ReplicaSet pods are scaled down gradually as new ones scale up.

49. **ClusterIP vs NodePort vs LoadBalancer?**
    ClusterIP: internal-only access; NodePort: exposes a port on every node for external access; LoadBalancer: provisions an external cloud load balancer routing to the service.

50. **PersistentVolume vs PersistentVolumeClaim?**
    A PersistentVolume is the actual provisioned storage resource; a PersistentVolumeClaim is a user's request/claim for storage that gets bound to a matching PV.

---

# SLAB 3 — ADVANCED / L2 OWNERSHIP

**A. Deep Linux Troubleshooting**

1. **High load average but low CPU utilization — diagnosis?**
   This usually points to I/O wait, not CPU contention; check `vmstat` for the `wa` column and `ps aux` for processes in `D` state (uninterruptible sleep, usually disk I/O), then investigate the underlying storage/network filesystem.

2. **Two processes deadlocked on a resource — how to identify?**
   Check process states (`D`/blocked) with `ps`, inspect held locks with tools like `lslocks`, and correlate via `/proc/<pid>/status` and `strace` to see what each process is waiting on.

3. **OOM killer terminated an unexpected process — investigate and prevent?**
   Check `dmesg`/journal for OOM killer messages to identify the trigger and victim; prevent recurrence by tuning `oom_score_adj`, setting cgroup memory limits, or adding swap/memory capacity.

4. **`sudo` broken for all users due to bad sudoers edit — recovery?**
   Boot into single-user/rescue mode (or use a root console session if still open) to fix `/etc/sudoers` directly, or restore from `/etc/sudoers.bak` created by `visudo`'s backup.

5. **Cron job silently stops running — where to check?**
   - `/var/log/cron` or journal (`journalctl -u cron`).
   - Crontab syntax and PATH differences from an interactive shell.
   - Whether the job's script has execute permission.
   - Mail (`mail` command) for cron's captured stdout/stderr, if configured.

6. **LVM volume group won't activate on boot — recovery steps?**
   Boot into rescue mode, run `vgscan`/`pvscan` to detect volumes, then `vgchange -ay` to activate manually; check `/etc/lvm/lvm.conf` filters and initramfs for missing LVM support.

7. **Btrfs "no space left" despite `df` showing free space — why and fix?**
   Btrfs allocates space in chunks per data/metadata type; metadata chunks can fill up even with free data space. Run `btrfs filesystem usage` to confirm, then `btrfs balance start` to reclaim and rebalance space.

8. **How cgroups enforce limits, and diagnosing throttling?**
   Cgroups group processes and apply kernel-enforced CPU/memory/IO limits; check throttling via `/sys/fs/cgroup/.../cpu.stat` (`nr_throttled`) or `systemd-cgtop` to see if a unit is hitting its configured limit.

9. **Methodology for a full system performance optimization exercise?**
   - Baseline current metrics (CPU, memory, disk, network) before changing anything.
   - Identify the actual bottleneck using USE method (Utilization, Saturation, Errors) rather than guessing.
   - Change one variable at a time and re-measure.
   - Document and monitor post-change to confirm sustained improvement.

10. **Multipath (MPIO) device shows degraded paths — diagnosis and recovery?**
    Run `multipath -ll` to see path states, check underlying HBA/network/cabling and target-side logs for the failed path, then restore it and verify `multipath -ll` shows all paths active again without manual failover risk.

**B. Advanced Networking**

11. **How a Linux bridge works, and a use case?**
    A bridge acts as a virtual switch connecting multiple interfaces at Layer 2; commonly used on a hypervisor/container host so VMs/containers share the host's physical network segment.

12. **What are network namespaces, and how do they enable container isolation?**
    They give each namespace its own isolated network stack (interfaces, routes, iptables), so containers can have private networking that doesn't interfere with the host or other containers.

13. **Configuring and troubleshooting a VLAN-tagged interface?**
    Create with `ip link add link eth0 name eth0.10 type vlan id 10`; troubleshoot by confirming the tag matches the switch port config and checking `ip -d link show` for VLAN ID mismatches.

14. **Diagnosing an iSCSI target/initiator connectivity failure?**
    Check initiator discovery (`iscsiadm -m discovery`), verify target IQN and network reachability, review target-side ACLs/CHAP auth, and check `iscsiadm -m session` for active/failed sessions.

15. **IPv6 misconfiguration causing intermittent connectivity — isolation approach?**
    Check for duplicate/conflicting router advertisements, SLAAC vs static config mismatches, and test connectivity separately over `-6` vs `-4` to confirm which stack is affected before narrowing further.

16. **Designing network redundancy using multipath I/O?**
    Configure multiple physical paths (separate HBAs/switches) to the same storage LUN, and use `multipath.conf` policies (e.g., active/active or active/passive) so a single path failure doesn't interrupt storage access.

**C. Advanced Security & Identity**

17. **PAM stack for local + LDAP/AD with fallback?**
    Stack `pam_unix.so` and `pam_sss.so`/`pam_ldap.so` with `sufficient`/`required` control flags so local auth is tried first (or as fallback) if the remote directory is unreachable, ensuring login still works during an outage.

18. **Auditing and hardening SSH across a fleet?**
    - Enforce key-based auth only, disable password login.
    - Disable root login (`PermitRootLogin no`).
    - Restrict ciphers/MACs to modern, secure algorithms.
    - Add brute-force protection (fail2ban) and centralize `sshd_config` via config management.

19. **Rotating a compromised GPG repo-signing key without breaking clients?**
    Generate a new key, sign repo metadata with both old and new keys during a transition window, distribute the new public key to all clients, then revoke the old key once all clients have updated.

20. **SSSD against external IdP — user logs in but has no group memberships — troubleshoot?**
    Check SSSD's group mapping/LDAP filter config in `sssd.conf`, verify the ID mapping range and nested-group support, and clear the SSSD cache (`sss_cache -E`) to force a fresh lookup.

21. **Building/maintaining an internal RMT server for air-gapped clients?**
    Set up RMT to mirror upstream repos on a network-connected host, sync updates on a schedule, then either serve directly to connected clients or export/transfer the mirror to the air-gapped network for offline clients.

**D. Kubernetes — Advanced Administration**

22. **Pod stuck in Pending — full diagnostic path?**
    `kubectl describe pod` for scheduling events → check node resource availability → check taints/tolerations and affinity rules → check if a PVC it depends on is still unbound.

23. **Debugging CrashLoopBackOff with no useful logs (app dies before logging)?**
    Check `kubectl describe pod` for exit code/reason, override the container's entrypoint temporarily to get a shell, check for missing env vars/config, and inspect resource limits (OOMKilled shows in exit code 137).

24. **RBAC for namespace-scoped admin, not cluster-wide?**
    Create a `Role` (not `ClusterRole`) with the needed permissions in that namespace, then a `RoleBinding` linking the team's users/group to that Role — scoped strictly to the one namespace.

25. **Service not routing to healthy pods — systematic check?**
    Verify the Service's selector matches pod labels (`kubectl get endpoints`), confirm pods pass readiness probes, and check for port/targetPort mismatches in the Service spec.

26. **Ingress vs LoadBalancer Service — when both, and debugging a 502?**
    LoadBalancer exposes one service externally; Ingress routes multiple services/hostnames through a single external entry point (often backed by a LoadBalancer for the Ingress controller itself) — a 502 usually means the Ingress controller can't reach a healthy backend pod, so check backend pod health and Service endpoints first.

27. **ResourceQuota vs LimitRange interaction, and violation behavior?**
    LimitRange sets default/min/max resource values per pod/container in a namespace; ResourceQuota caps total aggregate usage for the namespace — a pod violating either is rejected at admission time (won't be created).

28. **Debugging a StorageClass failing to dynamically provision a PVC?**
    Check `kubectl describe pvc` for provisioning events/errors, verify the StorageClass's provisioner is correctly configured and its backend (CSI driver) is healthy, and confirm sufficient underlying storage capacity.

29. **Kustomize overlays for dev/staging/prod, and a common pitfall?**
    A `base` defines common manifests, and per-environment `overlays` patch values (replicas, images, config); a common pitfall is patches silently not applying due to name/kind mismatches between base and overlay resources.

30. **Helm vs Kustomize — when to choose which?**
    Helm suits templated, parameterized, reusable packages (especially third-party charts) with versioned releases; Kustomize suits simpler, patch-based customization of your own plain YAML without a templating layer — many teams use both together.

31. **Rolling update causes brief outage despite readiness probes — what's misconfigured?**
    Likely missing or misconfigured `preStop` hook/`terminationGracePeriodSeconds` (in-flight requests dropped during pod termination), or readiness probe thresholds too lenient/slow to catch a not-yet-ready pod.

32. **Node affinity vs taints/tolerations — dedicating a node pool?**
    Affinity attracts pods to matching nodes (soft or hard preference); taints repel all pods from a node unless they have a matching toleration — combine both (taint the node pool + require both toleration and affinity) to fully dedicate it.

33. **Debugging DNS resolution failures inside the cluster (CoreDNS)?**
    Check CoreDNS pod health/logs, verify the `kube-dns` Service and its endpoints exist, and test resolution from a debug pod (`nslookup` against `kube-dns.kube-system`) to isolate whether it's cluster-wide or app-specific.

**E. RKE2 Deployment & Architecture**

34. **RKE2 vs vanilla Kubernetes/K3s — why for security-sensitive environments?**
    RKE2 is CIS-hardened by default, bundles a secure supply chain, and is built for regulated/government environments; unlike K3s it's not primarily edge-optimized, and unlike vanilla K8s it ships security defaults out of the box.

35. **HA RKE2 architecture and minimum node count?**
    Separates control plane, etcd, and worker roles (can be combined or split); a true HA control plane requires an odd number of server nodes, minimum 3, for etcd quorum.

36. **Hardware/OS/networking prerequisites before deployment?**
    - Supported OS and kernel version.
    - Sufficient CPU/RAM per role, and disk I/O for etcd.
    - Required open ports between nodes (API server, etcd, kubelet).
    - Reliable DNS/NTP and a stable network between all nodes.

37. **Manual vs installer-script deployment — trade-offs for regulated environments?**
    Manual installation gives full control and auditability of every step (preferred for strict compliance); the installer script is faster and reduces human error but requires more trust in the script's actions and internet access unless adapted for air-gapped use.

38. **Embedded Helm controller automating add-ons — troubleshooting a failed one?**
    RKE2 deploys certain add-ons as HelmChart custom resources reconciled automatically at bootstrap; troubleshoot by checking the `HelmChart`/`HelmChartConfig` resource status and the helm-controller job pod logs in `kube-system`.

39. **Multus for multiple CNI interfaces — real use case?**
    Multus attaches multiple network interfaces to a single pod; a real use case is a network function (e.g., a virtual firewall or telco workload) needing separate data-plane and management-plane interfaces.

40. **CIS Kubernetes Benchmark and deploying with hardening profiles?**
    CIS Benchmark is a published set of security configuration best practices for Kubernetes; RKE2 can be deployed with `--profile=cis` (plus required kernel/OS pre-configuration like a dedicated `etcd` user) to conform to it out of the box.

41. **Extra precautions hardening to CIS without breaking workloads?**
    Test PodSecurity/PSP-equivalent policy changes in staging first, ensure workloads don't rely on privileged/root containers, and review any custom admission controllers or network policies for compatibility before rolling out cluster-wide.

42. **Designing an air-gapped RKE2 deployment?**
    - Pre-pull and export all required images/artifacts on a connected system.
    - Transfer via approved offline media into a local/private registry.
    - Configure RKE2 to pull from that private registry instead of the internet.
    - Maintain a controlled process for periodic image/update transfers going forward.

**F. RKE2 Operations, Backup & Upgrades**

43. **Taking and validating an etcd snapshot backup, testing restore?**
    RKE2 automates periodic snapshots (`rke2 etcd-snapshot`); validate by listing snapshots and testing a restore into a non-production cluster (`rke2 server --cluster-reset --cluster-reset-restore-path=<snapshot>`) to confirm it's usable.

44. **HA control plane node corrupted/unrecoverable — recovery procedure?**
    Remove the failed node from the cluster/etcd membership, provision a new node, and join it as a new control plane member — as long as etcd quorum is maintained by the remaining nodes, no cluster-wide restore is needed.

45. **Rolling upgrade strategy for zero application downtime?**
    Upgrade control plane nodes one at a time (etcd quorum preserved), then drain and upgrade worker nodes one at a time so workloads reschedule onto remaining healthy nodes, relying on PodDisruptionBudgets and multiple replicas to avoid downtime.

46. **RKE2 upgrade differences: air-gapped vs internet-connected?**
    Air-gapped upgrades require manually pre-staging the new RKE2 binaries/images and updated artifacts into the private registry/local mirror before upgrading, instead of RKE2 pulling them live from the internet.

47. **Private registry out of storage mid-deployment — triage and recovery?**
    Stop further pulls, free space (prune old/unused images), verify no in-flight pulls were left in a corrupted partial state, then resume the deployment — going forward, add registry storage monitoring/quotas.

48. **Workloads failing post-CIS-hardening due to PodSecurity restrictions — remediation?**
    Identify which policy (e.g., disallowed privileged/root containers) is blocking each workload via admission controller error messages, then fix the workload's security context (non-root user, dropped capabilities) rather than broadly loosening the policy.

49. **DR runbook for RKE2 covering backup cadence, storage, RTO/RPO?**
    - Automated etcd snapshots on a defined schedule (e.g., hourly/daily).
    - Snapshots copied off-cluster to separate durable storage.
    - Documented, tested restore procedure with a target RTO.
    - Defined RPO based on acceptable data loss window between snapshots.

50. **Inheriting an undocumented RKE2 cluster — first-week audit approach?**
    - Inventory nodes, versions, and cluster topology (`kubectl get nodes`, `rke2 -v`).
    - Review RBAC, network policies, and CIS hardening status for security posture.
    - Check etcd backup configuration/history and test a restore.
    - Review resource usage, workload health, and any recurring alerts to establish an operational baseline.

---

*Answer key generated from the "List of Prerequisites for OS3 Technical Interview" syllabus. Answers are intentionally concise (interview-cue level) — use as a scoring rubric, not a verbatim script for candidates.*
