---
layout: post
title:  "Modifying battery charge threshold on my Asus laptop running KDE"
date: 2022-08-21 10:18:15 +0530
categories: programming
---

I have been using _Kubuntu-22.04_ on my _ASUS Vivobook S15 510un_ for a while now. While the UX has been good enough till now, there is one problem that I have lazily accepted for a while, i.e, the _battery charging threshold._

I generally set the _battery charging threshold_ to _80%_ but this value is not preserved across reboot. Every time the machine is rebooted, the value is set back to _100%_ This was something I accepted as a quirk, and every time the system was rebooted, I would manually set the value back to _80%_ from the default _100%_

![Setting the battery charging threshold after reboot](/assets/gif/setting-battery-charge-threshold-kde.gif)

As you should have guessed till now, this is an irritating issue, and sooner or later, I would have tried to resolve this in some way or another. And, that's what I have done.

After searching for a couple of hours, modifying my query term to the issue at hand, I finally found the correct [article on the Arch Wiki.](https://wiki.archlinux.org/title/Laptop/ASUS#Battery_charge_threshold)

### Checking some Power Supply parameters

Kernel 5.4 brought the ability to set the battery charge threshold for some Asus laptops, by modifying the `charge_control_end_threshold` variable exposed under `/sys/class/power_supply/BAT0/`

By default this value is set to `100` and **_reset_** on every power cycle.

```bash
$ cat /sys/class/power_supply/BAT0/status
Charging
$ cat /sys/class/power_supply/BAT0/capacity
74
$ cat /sys/class/power_supply/BAT0/charge_control_end_threshold
80
```

![Checking some Power Supply Parameters](/assets/gif/asus-kde-checking-power-supply-param.gif)

### Automatically set the threshold on boot

In order to automaticall change the threshold on boot, let's create a `systemd` service. 

```bash
sudo touch /etc/systemd/system/battery-charge-threshold.service
```

Contents:

```text
[Unit]
Description=Set the battery charge threshold
After=multi-user.target
StartLimitBurst=0

[Service]
Type=oneshot
Restart=on-failure
ExecStart=/bin/bash -c 'echo 80 > /sys/class/power_supply/BAT0/charge_control_end_threshold'

[Install]
WantedBy=multi-user.target
```

Then, enable the service:

```bash
systemctl enable battery-charge-threshold.service
```

![Creating a systemd service to set threshold on boot](/assets/gif/kde-creating-systemd-service-battery-charge-threshold.gif)

### Creating a _udev_ rule, since the *charge_control_end_threshold* class does not exist initially

So, lets create a udev rule for `asus-nb-wmi` kernel module to set the battery's charge threshold:

```bash
sudo vim /etc/udev/rules.d/asus-battery-charge-threshold.rules
```

Contents:

```text
ACTION=="add", KERNEL=="asus-nb-wmi", RUN+="/bin/bash -c 'echo 80 > /sys/class/power_supply/BAT?/charge_control_end_threshold'"
```

![Creating a udev rule for charge control end threshold](/assets/gif/asus-kde-battery-charge-threshold-rules.gif)

### Persist after hibernation

By default, the threshold settings persist across hibernation, so there is not need of any additional configuration here. 

### Conclusion

This was a nifty and fun way to spend a Sunday morning, and the result is very satisfactory.
