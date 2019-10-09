# Arch prime support
### This collection of scripts wants to provide GPU-switching for Hybrid-GPUs
This is a fork of kentsommer's [AntergosPrime](https://github.com/kentsommer/antergos-prime)
with modifications to be able to use it on an Aero 15X with Linux kernel >= 5.3.


### Install
Run the `install.sh` script as root.

With **lighdm** you also need to update its configuration (`nano /etc/lightdm/lightdm.conf`)
and add `display-setup-script=/etc/lightdm/Xsetup` under `[Seat:*]`.
If not using **lighdm** please see konstantingoretzki's [AntergosPrime](https://github.com/kentsommer/antergos-prime)
for futher instructions.


### Support for Aero 15X with kernel > 5.3
Before kernel update (kernel 5.2) getting HDMI sound to work required the **nvhda** module (taken from https://github.com/hhfeuer/nvhda).
To find the NVidia HDMI sound card the following could then be ran: `sudo modprobe nvhda && sudo tee /proc/acpi/nvhda <<<ON`
when the discrete card is powered on (freeze/kernel panic if not).

After some digging I notice that with kernel **linux-5.3.1.arch1-1** and higher HDMI is
detected automatically and **nvhda** module is no longer needed. So before kernel update
`aplay -l` gives me only the intel card and after the update both Intel and NVidia are
listed even when bbswitch disables Nvidia card.

As a workaround I force at module initialization **snd_hda_intel** to discard the NVidia sound card to avoid freeze when starting
a graphical session with the discrete card off .
```
    options snd-hda-intel enable=1,0,0
```
This correctly avoid finding the HDMI sound card (`cat /proc/asound/modules` give only one result).

I currently don't know how to make the module forget about the Nvidia sound card after boot so
switching between both GPU required a complete boot.
Just Login / Logout would likely result in a freeze / kernel panic.
