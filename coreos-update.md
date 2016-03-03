---
layout: default
title: CoreOS notes
permalink: /coreos
---

* TOC
{:toc}

## CoreOS Updates

### [FastPatch](https://coreos.com/using-coreos/updates/)

***Continous stream of updates***

#### 1. Operating System

- Access to public Update Service
- All CoreOS customers get updates
 - Release channels
   - stable <-- beta <-- alpha
   - how to switch channels? higher version to lower version?
 - Update strategies:
   - `best-effort`, `etcd-lock`, `reboot`, `off`
   - download to passive partition, so reboot is needed always
   - why other strategies besides `reboot`:
     - etcd-lock to make sure to get reboot lock, not losing quorum
     - `locksmithctl` to change the reboot lock
   - How
     - `update-engine` service do automatic update
     - Manually triggering an Update: `update_engine_client - check_for_update`
     - Create service to make reboot only happen in Maintenance Window
       - XXX: Could be better only do real update in Maintenance Window  
 - Premium Managed Linux customers have access to panel: `CoreUpdate`
   - Full control, reporting, downloading (Just GUI part?)
   - Custom channels
   - Using `Omaha` protocol developed by Google
 - Recoverable System Upgrades
   - Based on `ChromeOS`
   - rootA/rootB, cgroups for QoS
   - automatic rollback -- also in ChromeOS
     - Record `tries` counter in partition table
     - When to set the flag?

#### 2. Application Code
- Update containers

#### 3. Configuration Values
- Traditionally: `Chef` or `Puppet`, can not audit the state
- CoreOS uses `etcd`, each application listens the changes

### Technical Details

#### Build notes
- Doc: <https://coreos.com/os/docs/latest/sdk-modifying-coreos.html>
  - Using google's `repo` tool to get sources
  - Using chromeOS's chromite/bin/cros_sdk to set up chroot environment
  - Using gentoo's `emerge` build system
  - Using `KVM` for testing
  - See also:
    - [Chromium OS Developer Guide](https://www.chromium.org/chromium-os/developer-guide)
    - [Running a Chromium OS image under KVM](https://www.chromium.org/chromium-os/how-tos-and-troubleshooting/running-chromeos-image-under-virtual-machines)

#### OS partitions layout

##### 1. partitions

    core@coreos1 ~ $ sudo cgpt show /dev/vda
           start        size    part  contents
               0           1          Hybrid MBR
               1           1          Pri GPT header
               2          32          Pri GPT table
            4096      262144       1  Label: "EFI-SYSTEM"
                                      Type: EFI System Partition
                                      UUID: 4E5F1B27-B7A2-4F17-B1CD-A97A6DE38722
                                      Attr: Legacy BIOS Bootable
          266240        4096       2  Label: "BIOS-BOOT"
                                      Type: BIOS Boot Partition
                                      UUID: 689F6981-D078-4D19-AD44-A931FB689996
          270336     2097152       3  Label: "USR-A"
                                      Type: Alias for coreos-rootfs
                                      UUID: 7130C94A-213A-4E5A-8E26-6CCE9662F132
                                      Attr: priority=1 tries=0 successful=1
         2367488     2097152       4  Label: "USR-B"
                                      Type: Alias for coreos-rootfs
                                      UUID: E03DD35C-7C2D-4A47-B3FE-27F15780A57C
                                      Attr: priority=0 tries=0 successful=0
         4464640      262144       6  Label: "OEM"
                                      Type: Alias for linux-data
                                      UUID: 8C689B68-EF14-4E73-AB41-28C33540ADF4
         4726784      131072       7  Label: "OEM-CONFIG"
                                      Type: CoreOS reserved
                                      UUID: FBF66043-FC8C-4609-899B-9AD6095AF4CE
         4857856    12943360       9  Label: "ROOT"
                                      Type: CoreOS auto-resize
                                      UUID: 455AF012-4703-49F0-A9A1-3D68A0807B79
        17805279          32          Sec GPT table
        17805311           1          Sec GPT header
    core@coreos1 ~ $ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        485M     0  485M   0% /dev
    tmpfs           499M     0  499M   0% /dev/shm
    tmpfs           499M  6.6M  493M   2% /run
    tmpfs           499M     0  499M   0% /sys/fs/cgroup
    /dev/vda9       6.0G   13M  5.6G   1% /
    /dev/vda3       985M  492M  442M  53% /usr
    /dev/vda1       128M   35M   94M  27% /boot
    tmpfs           499M     0  499M   0% /media
    tmpfs           499M     0  499M   0% /tmp
    /dev/vda6       108M   52K   99M   1% /usr/share/oem
    config-2        440G  308G  110G  74% /media/configvirtfs

##### 2. OS file systems

###### 2.1 /boot

    core@coreos1 /boot/coreos $ ls
    grub  vmlinuz-a
    core@coreos1 /boot/coreos $ ls grub/grub.cfg*
    grub/grub.cfg.tar

After untaring, there are there options:

    # Assemble the options applicable to all the kernels below
    set linux_cmdline="rootflags=rw mount.usrflags=ro $linux_root $linux_console $first_boot $oem_id $linux_append"

    menuentry "CoreOS default" --id=coreos {
        gptprio.next -d usr -u usr_uuid
        if [ "$usr_uuid" = "7130c94a-213a-4e5a-8e26-6cce9662f132" ]; then
           linux$suf /coreos/vmlinuz-a mount.usr=PARTUUID=$usr_uuid $linux_cmdline
        else
           linux$suf /coreos/vmlinuz-b mount.usr=PARTUUID=$usr_uuid $linux_cmdline
        fi
    }

    menuentry "CoreOS USR-A" --id=coreos-a {
       linux$suf /coreos/vmlinuz-a mount.usr=PARTLABEL=USR-A $linux_cmdline
    }

    menuentry "CoreOS USR-B" --id=coreos-b {
       linux$suf /coreos/vmlinuz-b mount.usr=PARTLABEL=USR-B $linux_cmdline
    }

###### 2.2 /usr
Read-only OS is either on /dev/vda3 or /dev/vda4, mounted under `/usr`

    core@coreos1 /boot/coreos $ mount | grep /usr
    /dev/vda3 on /usr type ext4 (ro,relatime,seclabel)
    /dev/vda6 on /usr/share/oem type ext4 (rw,nodev,relatime,seclabel,commit=600,data=ordered)

###### 2.3 ROOT
ROOT is writable, /dev/vda9 mounted

    core@coreos1 /boot/coreos $ df -h
    Filesystem      Size  Used Avail Use% Mounted on
    /dev/vda9       6.0G   13M  5.6G   1% /

Some are symlinks to read-only /usr:

    core@coreos1 /boot/coreos $ ls -l /
    total 68
    lrwxrwxrwx  1 root root     7 Feb 12 03:40 bin -> usr/bin
    drwxr-xr-x  6 root root 16384 Jan  1  1970 boot
    drwxr-xr-x 15 root root  2800 Mar  2 22:11 dev
    drwxr-xr-x 21 root root  4096 Feb 12 04:07 etc
    drwxr-xr-x  3 root root  4096 Feb 12 03:40 home
    lrwxrwxrwx  1 root root     9 Feb 12 03:40 lib -> usr/lib64
    lrwxrwxrwx  1 root root     9 Feb 12 03:40 lib64 -> usr/lib64
    drwxrwxrwt  3 root root    60 Mar  2 22:11 media
    drwxr-xr-x  2 root root  4096 Feb 12 03:40 mnt
    dr-xr-xr-x 78 root root     0 Mar  2 22:11 proc
    drwx------  2 root root  4096 Feb 12 04:05 root
    drwxr-xr-x 22 root root   620 Mar  2 22:11 run
    lrwxrwxrwx  1 root root     8 Feb 12 03:40 sbin -> usr/sbin
    drwxr-xr-x  2 root root  4096 Feb 12 03:40 srv
    dr-xr-xr-x 13 root root     0 Mar  2 22:11 sys
    drwxrwxrwt  8 root root   160 Mar  2 22:11 tmp
    drwxr-xr-x 10 root root  4096 Feb  2 13:42 usr
    drwxr-xr-x  9 root root  4096 Feb 12 03:41 var
    core@coreos1 ~ $ ls -l /etc | grep usr
    lrwxrwxrwx 1 root root    36 Feb 12 03:40 idmapd.conf -> ../usr/share/libnfsidmap/idmapd.conf
    lrwxrwxrwx 1 root root    31 Feb 12 03:40 inputrc -> ../usr/share/baselayout/inputrc
    lrwxrwxrwx 1 root root    21 Feb 12 03:40 ld.so.conf -> ../usr/lib/ld.so.conf
    lrwxrwxrwx 1 root root    26 Feb 12 03:40 limits -> ../usr/share/shadow/limits
    lrwxrwxrwx 1 root root    25 Feb 12 03:40 localtime -> ../usr/share/zoneinfo/UTC
    lrwxrwxrwx 1 root root    32 Feb 12 03:40 login.access -> ../usr/share/shadow/login.access
    lrwxrwxrwx 1 root root    30 Feb 12 03:40 login.defs -> ../usr/share/shadow/login.defs
    lrwxrwxrwx 1 root root    31 Feb 12 03:40 lsb-release -> ../usr/share/coreos/lsb-release
    lrwxrwxrwx 1 root root    37 Feb 12 03:40 nsswitch.conf -> ../usr/share/baselayout/nsswitch.conf
    lrwxrwxrwx 1 root root    23 Feb 12 03:40 ntp.conf -> /usr/share/ntp/ntp.conf
    lrwxrwxrwx 1 root root    21 Feb 12 03:40 os-release -> ../usr/lib/os-release
    lrwxrwxrwx 1 root root    31 Feb 12 03:40 profile -> ../usr/share/baselayout/profile
    lrwxrwxrwx 1 root root    38 Feb 12 03:40 request-key.conf -> ../usr/share/keyutils/request-key.conf
    lrwxrwxrwx 1 root root    29 Feb 12 03:40 securetty -> ../usr/share/shadow/securetty
    lrwxrwxrwx 1 root root    30 Feb 12 03:40 shells -> ../usr/share/baselayout/shells
    lrwxrwxrwx 1 root root    33 Feb 12 03:40 sudo.conf -> ../usr/share/baselayout/sudo.conf

#### Tempfiles service

Others could be created by tempfiles service `coreos-tmpfiles.service`:

    core@coreos1 /usr/lib/systemd/system $ cat coreos-tmpfiles.service
    [Unit]
    Description=Create missing system files
    DefaultDependencies=no
    After=local-fs.target
    Before=sysinit.target
    ConditionPathIsReadWrite=/etc

    [Service]
    Type=oneshot
    ExecStart=/usr/sbin/coreos-tmpfiles
    core@coreos1 /usr/lib/systemd/system $ cat /usr/sbin/coreos-tmpfiles
    #!/bin/bash
    # systemd-tmpfiles doesn't support skipping writing to files that already exist
    # - copy the docker group to /etc because docker reads /etc/group directly
    # - copy the sudo and wheel groups to /etc so that new uses can be added to them
    # - copy the core and root users to /etc so the passwd utility works correctly

    # Inherit root from environment or command line
    ROOT="${1:-$ROOT}"
    BASE="${ROOT}/usr/share/baselayout"

    # Users/Groups to copy so they can be modified
    COPY_USERS="root|core"
    COPY_GROUPS="docker|rkt|sudo|wheel"

    # readable files
    umask 022
    if [[ ! -e "${ROOT}/etc/passwd" ]]; then
        grep -E -e "^(${COPY_USERS}):" "${BASE}/passwd" > "${ROOT}/etc/passwd"
    fi
    if [[ ! -e "${ROOT}/etc/group" ]]; then
        grep -E -e "^(${COPY_GROUPS}):" "${BASE}/group" > "${ROOT}/etc/group"
    fi

    # secure files
    umask 027
    if [[ ! -e "${ROOT}/etc/shadow" ]]; then
        grep -E -e "^(${COPY_USERS}):" "${BASE}/shadow" > "${ROOT}/etc/shadow"
    fi
    if [[ ! -e "${ROOT}/etc/gshadow" ]]; then
        grep -E -e "^(${COPY_GROUPS}):" "${BASE}/gshadow" > "${ROOT}/etc/gshadow"
    fi
    </pre>
    (Above code can handle `rm -rf /` case, since it will copy files)

    <pre>
    core@coreos1 /usr/lib/systemd/system $ cat systemd-tmpfiles-setup.service
    #  This file is part of systemd.
    #
    #  systemd is free software; you can redistribute it and/or modify it
    #  under the terms of the GNU Lesser General Public License as published by
    #  the Free Software Foundation; either version 2.1 of the License, or
    #  (at your option) any later version.

    [Unit]
    Description=Create Volatile Files and Directories
    Documentation=man:tmpfiles.d(5) man:systemd-tmpfiles(8)
    DefaultDependencies=no
    Conflicts=shutdown.target
    After=local-fs.target systemd-sysusers.service
    Before=sysinit.target shutdown.target
    RefuseManualStop=yes

    [Service]
    Type=oneshot
    RemainAfterExit=yes
    ExecStart=/usr/bin/systemd-tmpfiles --create --remove --boot --exclude-prefix=/dev

All the symlinks, directories will be created by tmpfiles service:

    core@coreos1 /usr/lib64/tmpfiles.d $ ls
    audit-rules.conf     baselayout-home.conf  ca-certificates.conf  home.conf           libnfsidmap.conf  nfs-utils.conf     shadow.conf           systemd-nspawn.conf  tmp.conf
    base_image_etc.conf  baselayout-ldso.conf  etc.conf              issuegen.conf       libsemanage.conf  ntp.conf           ssh.conf              systemd-remote.conf  update-engine.conf
    base_image_var.conf  baselayout-usr.conf   etcd.conf             journal-nocow.conf  logrotate.conf    polkit.conf        systemd-coreos.conf   systemd-resolv.conf  var.conf
    baselayout-etc.conf  baselayout.conf       etcd2.conf            keyutils.conf       lvm2.conf         selinux-base.conf  systemd-nologin.conf  systemd.conf         x11.conf
    core@coreos1 /usr/lib64/tmpfiles.d $ cat var.conf
    #  This file is part of systemd.
    #
    #  systemd is free software; you can redistribute it and/or modify it
    #  under the terms of the GNU Lesser General Public License as published by
    #  the Free Software Foundation; either version 2.1 of the License, or
    #  (at your option) any later version.

    # See tmpfiles.d(5) for details

    v /var 0755 - - -

    L /var/run - - - - ../run

    d /var/log 0755 - - -
    f /var/log/wtmp 0664 root utmp -
    f /var/log/btmp 0600 root utmp -

    d /var/cache 0755 - - -

    d /var/lib 0755 - - -

    d /var/spool 0755 - - -
    core@coreos1 /usr/lib64/tmpfiles.d $ cat shadow.conf
    L   /etc/limits         -   -   -   -   ../usr/share/shadow/limits
    L   /etc/login.access   -   -   -   -   ../usr/share/shadow/login.access
    L   /etc/login.defs     -   -   -   -   ../usr/share/shadow/login.defs
    L   /etc/securetty      -   -   -   -   ../usr/share/shadow/securetty

    d   /etc/default            -   -   -   -   -
    L   /etc/default/useradd    -   -   -   -   ../../usr/share/shadow/useradd

    f   /var/log/lastlog        -   -   -   -   -
    f   /var/log/faillog        -   -   -   -   -

#### Notes
- When and how the coreOS changes the boot sequence ?
  - /var will be overwritten
    - Cache invalidation ?
    - Logs not touched
  - System configs will be changed, (/etc/passwd, /etc/group ...)
    - How about there needs a change? Like adding a new user during software update,
      or change configuration format?
- grub support tar config ???
- Architecture
  - Update service with server/client, using dbus to communicate
    - /usr/sbin/update_engine -foreground -logtostderr
    - update_engine_client
    - src: src/third_party/update_engine/
  - chromeos also has update_engine
    -  src/platform/system_api/dbus/update_engine/dbus-constants.h
    - chromiumos/src/third_party/chromiumos-overlay/chromeos-base/update_engine
    - <https://www.chromium.org/chromium-os/chromiumos-design-docs/boot-design>
