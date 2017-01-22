
What's done

1. Add hyperv-iso builder section to `debian.json`
2. Fixed ISO download links to 8.7.1. This was submitted as https://github.com/boxcutter/debian/pull/46


Work Needed

1. Get IP address detection working. Per https://packages.debian.org/sid/hyperv-daemons scripts are needed to get IP info
2. Revert the change to boot_command in debian.json, take the note out of readme.md