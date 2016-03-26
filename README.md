## Ansible Playbook for Docker / Kuberentes / OpenShift on RaspberryPIs 3

*This is work-in-progress, it might not work for you and things will change. Expect a Blog with more details soon.*

Here's a playground for setting up a Raspberry 3 cluster connected with WLAN and with Docker preinstalled.

### Basic Setup

To start use a plain Raspbian OS with WLAN support and connect it for the initial setup via Ethernet:

* Install [Raspbian Jessie Lite](https://www.raspberrypi.org/downloads/raspbian/) on a Micro SD card (Hypriot's [flash script](https://github.com/hypriot/flash) comes handy here)
* Connect via ethernet to your LAN and detect the IP (either via nmap or by checking your DHCP logs)
* SSH to the Pi and resize the disk

        ssh pi@1.2.3.4    # Use 'raspberry' as password
        $ sudo raspi-config
        # Select first menu item, then reboot

* Copy `group_vars/pis/credentials-sample.yml` to `group_vars/pis/credentials.yml` and enter your WLAN SID and preshared key in the file. Add a hashed user password which is replacing 'raspberry' (use `mkpasswd` on a Linux system).
* Edit the `hosts` inventory file and remove everything from group `[pis]`, add your ethernet based IP instead (1.2.3.4 here)
* Run

        ansible -i hosts setup-playbook.yml -k

* Remove LAN cable and reboot. Your Pi should connect now to your WLAN router.
* Repeat steps for each Pi.


Finally add the WLAN IPs to the `hosts` file. Ideally your WLAN router has fixed IPs for your PIs configured. Add these IPs to your hosts file later on in group 'pis'. Select a single IP as `master` and the rest for `nodes`.
Don't forget to given everyone a `name`, the master node should carry ``

When all Pis running and the proper IPs are entered you can re-run ansible with the playbook any time (without -k option).

### Install Kubernetes

* Then simply run

        ansible -i hosts kubernetes-playbook.xml

#### Features

* [Hypriot Docker packages](http://blog.hypriot.com/downloads/)
* `iperf` and `hdparm` for benchmarks installed ( + `mtr`)
* APT update
* Copy of `~/.ssh/id_dsa.pub` to `/home/pi/.ssh/authorized_keys`
* Setup of `/etc/hosts` with the IPs of all nodes (named `n0`, `n1`, `n2`, `n3`)
* Look into `tools/` for additional goodies.

#### To come

* Setting up OpenShift
* fabric8

#### Final words

Bear with me, that my first Ansible playbook ;-)

-------