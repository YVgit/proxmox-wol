# proxmox-wol
https://forum.proxmox.com/threads/wake-on-lan-script.32449/
I wanted to share a configuration I've been using for a little while to enable Wake on LAN with a KVM guest. I've got a guest with an NVIDIA GPU passed through for gaming, which I use in combination with Gamestream on an NVIDIA Shield Android TV box. My goal was to reduce power consumption, and prevent some weirdness I have previously seen with long running VMs with GPU passthrough.

This script was heavily based on the great work by dmacias over at the unRAID forums: https://lime-technology.com/forum/index.php?topic=38289.0

I modified the script to look for WOL magic packets on UDP ports 7 and 9, and UDP port 47998 which is used by Gamestream. The script collects the destination MAC address, and searches for Proxmox configuration files in /etc/pve/qemu-server containing the specified MAC address. Once found, it checks if the VM is already running by issuing a qemu-status command, and if not running, a qm start <vmid> command is issued.

In my use case, I set my Windows VM to hibernate after 15 minutes. I can't recall where, but I've read that sleep is not recommended with KVM VMs, or perhaps just those using GPU passthrough. In any case, hibernation works well for my needs. Within seconds of selecting a game from the NVIDIA Shield, the WOL packet is sent and the Windows VM starts to boot and return to the hibernated state. All up, it takes about 30 seconds to boot.

Usage:

    Unzip contents of attachment to a directory of your choosing. I use /root/scripts
    Create folder /var/log/proxmoxwol - a file named proxmoxwol.log will be created and all logs sent here. Additionally, the below init script will log into this directory.
    To start the service at boot, copy (and rename) the proxmoxwol.init file from the zip contents to '/etc/init.d/proxmoxwol', set as executable with 'chmod +x proxmoxwol' - and then enable with 'update-rc.d proxmoxwol defaults'.
    Make sure to modify the cmd and int variables as required in /etc/init.d/proxmoxwol for your script location and bridged network adapter.
    To rotate the logs, which get quite large in my network due to frequent WOL requests, copy (and rename) proxmoxwol.logrotate to file '/etc/logrotate.d/proxmoxwol'.
    Install pcap with `apt-get install python-libpcap`
    Either reboot or issue '/etc/init.d/proxmoxwol start' and check the above mentioned log file for status and/or errors.

Hope this is useful to someone.



Feb 20, 2018

    #5

Hi - I went ahead and uploaded the files to https://github.com/rinseaid/proxmox-wol

Sorry I haven't looked at this thread in some time as I'm no longer using Proxmox, but I went ahead and also included ;piod's patch- thanks!

For TheFunk's question, I did look into this a little bit and couldn't find any way to run the GeForce Experience/Streaming service under the SYSTEM context.
