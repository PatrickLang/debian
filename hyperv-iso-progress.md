
What's done

1. Add hyperv-iso builder section to `debian.json`
2. Fixed ISO download links to 8.7.1. This was submitted as https://github.com/boxcutter/debian/pull/46


Work Needed

1. ~~Get IP address detection working. Per https://packages.debian.org/sid/hyperv-daemons scripts are needed to get IP info~~
2. Update to use kernel 4.8 and [hyperv-daemons from jessie-backports](https://packages.debian.org/jessie-backports/hyperv-daemons)
 - Add `deb http://http.debian.net/debian jessie-backports main` to `/etc/apt/sources.list`
 - `sudo apt-get install hyperv-daemons=4.*`
 - `sudo apt-get install linux-image-amd64=4.8* linux-base=4.3*`
3. Revert the change to boot_command in debian.json, take the note out of readme.md
