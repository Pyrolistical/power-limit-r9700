# r9700-power-cap

Sets `power1_cap` to 210 W on every Radeon AI PRO R9700 (`1002:7551`) at boot.

Matches on PCI device ID under `/sys/bus/pci/drivers/amdgpu`, so it is independent of `card*` numbering and covers any number of R9700s.

## Why the device ID

`1002:7551` is the PCI vendor ID : device ID pair in hex, as `lspci -nn` prints it.

- `1002` — AMD/ATI, assigned by PCI-SIG
- `7551` — the SKU, Radeon AI PRO R9700

sysfs splits them into two files on the PCI device:

```
/sys/bus/pci/devices/0000:c7:00.0/vendor  -> 0x1002
/sys/bus/pci/devices/0000:c7:00.0/device  -> 0x7551
```

The script reads `device`; the vendor half is implied by iterating the `amdgpu` driver directory.

## Install

```sh
sudo install -m755 r9700-power-cap /usr/local/bin/
sudo install -m644 r9700-power-cap.service /etc/systemd/system/
sudo systemctl daemon-reload
sudo systemctl enable --now r9700-power-cap.service
```

## Verify

```sh
systemctl status r9700-power-cap.service
grep . /sys/bus/pci/drivers/amdgpu/*/hwmon/hwmon*/power1_cap
```

Each R9700 should read `210000000`.

