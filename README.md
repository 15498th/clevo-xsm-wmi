## clevo-xsm-wmi
Kernel module for controlling keyboard backlighting of some Clevo and Clevo-based laptops.

### Original repo notes
Development of [repository](https://bitbucket.org/tuxedocomputers/clevo-xsm-wmi/) this code was taken from was moved to [tuxedocomputers/tuxedo-keyboard](https://github.com/tuxedocomputers/tuxedo-keyboard) (see [issue 44](https://bitbucket.org/tuxedocomputers/clevo-xsm-wmi/issues/44/the-status-and-the-future-of-tuxedo-clevo) on bitbucket). It's probably better to try using it before resorting to this one.

### Adapting code to load on not officially supported models
This module has hardcoded list of supported models (see master branch [readme](https://github.com/15498th/clevo-xsm-wmi/tree/master#supported-devices)), and won't load on laptops not recognised as supported.
Nevertheless it might also work on other models, so these willing to try it can edit code according to this post https://forums.linuxmint.com/viewtopic.php?t=287190:

- run `sudo dmidecode` and go through output from the top, looking for line with `Product Name`. Alternatively, run `sudo dmidecode -H 1`
- open module/clevo-xsm-wmi.c and navigate to line [1414](https://github.com/15498th/clevo-xsm-wmi/blob/DreamMachines_NH50_70RA_support/module/clevo-xsm-wmi.c#L1414)
- look though entries describing supported models and pick one to edit. They should look like this
```
 {
	.ident = "Clevo P870DM",
	.matches = {
		DMI_MATCH(DMI_PRODUCT_NAME, "P870DM"),
	},
	.callback = clevo_xsm_dmi_matched,
	.driver_data = &kb_full_color_with_extra_ops,
},
  ```
 - replace "P870DM" in `DMI_MATCH(DMI_PRODUCT_NAME, "P870DM")` with value from `dmidecode` output, optionally adjust `.ident = "Clevo P870DM"` too
 - if first entry doesn't work, try one with different `.driver_data`
 
 ### Building and testing module
With working directory set to module/ run `make`. If build was successful, it should be possible to load module with 
```
sudo insmod clevo-xsm-wmi.ko
```
 and unload with
 ```
 sudo rmmod clevo-xsm-wmi.ko
 ```
 To make it load on system boot see last part of original post.
 
 ### Usage
 Module supports following hotkeys:
 `Fn`+`Num*` to turn backlights on and off,
 `Fn`+`Num/` to change color,
 `Fn`+`Num+` to increase and `Fn`+`Num-` to decrease brightness.
 
 It is also possible to control it by editing text files in `/sys/devices/platform/clevo_xsm_wmi/` as described in [module/ABI/testing/sysfs-driver-clevo-xsm-wmi](https://github.com/15498th/clevo-xsm-wmi/blob/DreamMachines_NH50_70RA_support/module/ABI/testing/sysfs-driver-clevo-xsm-wmi).
