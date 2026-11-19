# ZT9101 USB WiFi (350b:9101)

The module shipped in the image is

`rootfs/dArkOS/usr/lib/modules/4.4.189/kernel/drivers/net/wireless/zt9101/zt9101_ztopmac_usb.ko`

Firmware and config live next to the other wireless blobs:

- `rootfs/dArkOS/usr/lib/firmware/zt9101/`
- `rootfs/dArkOS/etc/zt9101_wifi.cfg`
- `rootfs/dArkOS/etc/modprobe.d/zt9101_ztopmac_usb.conf`

`clone_support.sh` copies `rootfs/dArkOS` into both dArkOS and ArkOS images.
USB id `350b:9101` is in `modules.alias`, so udev loads the module when the
dongle is enumerated. There is no `modules-load.d` entry: the `.ko` exists
only for 4.4.189, and an unconditional `modprobe` fails on the 5.10 images.

Source is the ZTop GPL driver
https://github.com/yjun123/ZT9101_vendor_linux_driver
(tree `ZT9101_android_V1.2.1800.20241115`). The only local change is
`patches/0001-r36s-platform.patch`, which adds `HOST_PLAT=r36s` (aarch64,
kernel 4.4.189).

## Rebuild

```bash
docker build -f scripts/zt9101/Dockerfile -t zt9101-build .
docker run --rm -v "$PWD/out:/out" zt9101-build
```

The `.ko` must be rebuilt when `arkos.bsp.4.4` (`rg351`) changes.
`vermagic` of the shipped module is `4.4.189 SMP mod_unload aarch64`.

Tested on clone-r36s: after reboot the dongle on OTG comes up as `wlan0`
with no extra install step.
