
What's done

1. Add hyperv-iso builder section to `debian.json`
2. Fixed ISO download links to 8.7.1. This was submitted as https://github.com/boxcutter/debian/pull/46


Work Needed

1. ~~Get IP address detection working. Per https://packages.debian.org/sid/hyperv-daemons scripts are needed to get IP info~~
2. ~~Update to use kernel 4.8 and [hyperv-daemons from jessie-backports](https://packages.debian.org/jessie-backports/hyperv-daemons)~~
 - Add `deb http://http.debian.net/debian jessie-backports main` to `/etc/apt/sources.list`
 - `sudo apt-get install hyperv-daemons=4.*`
 - `sudo apt-get install linux-image-amd64=4.* linux-base=4.*`
3. Revert the change to boot_command in debian.json, take the note out of readme.md



Current Bugs

`hv_kvp_daemon` service isn't starting automatically. Not sure why.


```
vagrant@vagrant:~$ systemctl status *hyper*
● hyperv-daemons.hv-fcopy-daemon.service - Hyper-V file copy service (FCOPY) daemon
   Loaded: loaded (/lib/systemd/system/hyperv-daemons.hv-fcopy-daemon.service; enabled)
   Active: failed (Result: exit-code) since Wed 2017-02-01 06:42:11 UTC; 11min ago
  Process: 557 ExecStart=/usr/sbin/hv_fcopy_daemon -n (code=exited, status=1/FAILURE)
 Main PID: 557 (code=exited, status=1/FAILURE)

● hyperv-daemons.hv-vss-daemon.service - Hyper-V volume shadow copy service (VSS) daemon
   Loaded: loaded (/lib/systemd/system/hyperv-daemons.hv-vss-daemon.service; enabled)
   Active: failed (Result: exit-code) since Wed 2017-02-01 06:42:11 UTC; 11min ago
  Process: 560 ExecStart=/usr/sbin/hv_vss_daemon -n (code=exited, status=1/FAILURE)
 Main PID: 560 (code=exited, status=1/FAILURE)

● hyperv-daemons.hv-kvp-daemon.service - Hyper-V key-value pair (KVP) daemon
   Loaded: loaded (/lib/systemd/system/hyperv-daemons.hv-kvp-daemon.service; enabled)
   Active: failed (Result: exit-code) since Wed 2017-02-01 06:42:11 UTC; 11min ago
  Process: 561 ExecStart=/usr/sbin/hv_kvp_daemon -n (code=exited, status=1/FAILURE)
 Main PID: 561 (code=exited, status=1/FAILURE)
```

```
vagrant@vagrant:/var/log$ sudo grep "\(hv\|kvp\)" syslog
Feb  1 06:40:26 vagrant kernel: [    0.761700] hv_vmbus: Hyper-V Host Build:14393-10.0-0-0.576; Vmbus version:4.0
Feb  1 06:40:26 vagrant kernel: [    0.761937] hv_vmbus: Unknown GUID: f8e65716-3cb3-4a06-9a60-1889c5cccab5
Feb  1 06:40:26 vagrant kernel: [    0.762226] hv_vmbus: Unknown GUID: 3375baf4-9e15-4b30-b765-67acb10d607b
Feb  1 06:40:26 vagrant kernel: [    0.762249] hv_vmbus: Unknown GUID: 276aacf4-ac15-426c-98dd-7521ad3f01fe
Feb  1 06:40:26 vagrant kernel: [    0.803999] hv_vmbus: registering driver hv_storvsc
Feb  1 06:40:26 vagrant kernel: [    0.806136] hv_vmbus: registering driver hid_hyperv
Feb  1 06:40:26 vagrant kernel: [    0.808964] hv_vmbus: registering driver hv_netvsc
Feb  1 06:40:26 vagrant kernel: [    0.809724] hv_netvsc: hv_netvsc channel opened successfully
Feb  1 06:40:26 vagrant kernel: [    0.998668] hv_netvsc vmbus_16: Send section size: 6144, Section count:2560
Feb  1 06:40:26 vagrant kernel: [    1.000979] hv_netvsc vmbus_16: Device MAC 00:15:5d:7e:80:11 link state up
Feb  1 06:40:26 vagrant kernel: [    1.915011] hv_utils: Registering HyperV Utility Driver
Feb  1 06:40:26 vagrant kernel: [    1.915012] hv_vmbus: registering driver hv_util
Feb  1 06:40:26 vagrant kernel: [    1.921422] hv_vmbus: registering driver hv_balloon
Feb  1 06:40:26 vagrant kernel: [    1.921990] hv_vmbus: registering driver hyperv_keyboard
Feb  1 06:40:26 vagrant kernel: [    1.923664] hv_vmbus: registering driver hyperv_fb
Feb  1 06:41:14 vagrant kernel: [   50.024363] hv_balloon: Received INFO_TYPE_MAX_PAGE_CNT
Feb  1 06:41:14 vagrant kernel: [   50.024405] hv_balloon: Data Size is 8
Feb  1 06:42:11 vagrant systemd[1]: hyperv-daemons.hv-vss-daemon.service: main process exited, code=exited, status=1/FAILURE
Feb  1 06:42:11 vagrant systemd[1]: Unit hyperv-daemons.hv-vss-daemon.service entered failed state.
Feb  1 06:42:11 vagrant systemd[1]: hyperv-daemons.hv-fcopy-daemon.service: main process exited, code=exited, status=1/FAILURE
Feb  1 06:42:11 vagrant systemd[1]: Unit hyperv-daemons.hv-fcopy-daemon.service entered failed state.
Feb  1 06:42:11 vagrant systemd[1]: hyperv-daemons.hv-kvp-daemon.service: main process exited, code=exited, status=1/FAILURE
Feb  1 06:42:11 vagrant systemd[1]: Unit hyperv-daemons.hv-kvp-daemon.service entered failed state.
```


So I tried starting them, and it worked:
```
vagrant@vagrant:/var/log$ sudo systemctl start *hyper*
vagrant@vagrant:/var/log$ systemctl status *hyper*                                                        
● hyperv-daemons.hv-fcopy-daemon.service - Hyper-V file copy service (FCOPY) daemon
   Loaded: loaded (/lib/systemd/system/hyperv-daemons.hv-fcopy-daemon.service; enabled)
   Active: active (running) since Wed 2017-02-01 07:00:31 UTC; 15s ago
 Main PID: 1034 (hv_fcopy_daemon)
   CGroup: /system.slice/hyperv-daemons.hv-fcopy-daemon.service
           └─1034 /usr/sbin/hv_fcopy_daemon -n

● hyperv-daemons.hv-vss-daemon.service - Hyper-V volume shadow copy service (VSS) daemon
   Loaded: loaded (/lib/systemd/system/hyperv-daemons.hv-vss-daemon.service; enabled)
   Active: active (running) since Wed 2017-02-01 07:00:31 UTC; 15s ago
 Main PID: 1035 (hv_vss_daemon)
   CGroup: /system.slice/hyperv-daemons.hv-vss-daemon.service
           └─1035 /usr/sbin/hv_vss_daemon -n

● hyperv-daemons.hv-kvp-daemon.service - Hyper-V key-value pair (KVP) daemon
   Loaded: loaded (/lib/systemd/system/hyperv-daemons.hv-kvp-daemon.service; enabled)
   Active: active (running) since Wed 2017-02-01 07:00:31 UTC; 15s ago
 Main PID: 1036 (hv_kvp_daemon)
   CGroup: /system.slice/hyperv-daemons.hv-kvp-daemon.service
           └─1036 /usr/sbin/hv_kvp_daemon -n

```