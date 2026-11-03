# Elgato HD60 For Linux

Experimental Linux driver for the **Elgato HD60 Pro PCIe** capture card.
This is a community project, not an official Elgato release. It does **not**
claim support for the USB HD60, HD60 S, HD60 X, or 4K cards.

**Only tested on CachyOS (Arch Linux based), kernel `7.2.4-1-cachyos`, x86-64.**
Other distributions, kernels, and machines are untested. Version: **0.1.20**.

## Current status

- Tested card: PCI `12ab:0380`, subsystem `1cfa:0003`, resident firmware `1.11`.
- Live 1080p video and stereo HDMI audio work in OBS with a Panasonic GH4.
- Normal capture stop/reopen and bounded audio recovery tests have passed.
- Actual video delivery is around **50 fps in stable periods**, sometimes lower.
  **True 60 fps is not working yet.** The V4L2 mode currently advertises 30 fps.
- Short-clip lip sync was checked by playback. Long-term drift, active signal
  loss, other modes, and broader hardware compatibility remain unverified.
- No flash programming or runtime firmware upload is needed.

This is experimental kernel code. Earlier development builds caused system
freezes. Save your work before trying it. The driver retains DMA memory until
reboot: **do not force-unload, PCI-reset, or unbind it**.

## Install

Install DKMS, headers matching your running kernel, Python 3, make, Clang,
LLD and LLVM utilities through your distribution package manager. OBS needs
V4L2 and ALSA support. No IOMMU boot change was needed on the tested machine.

Download [the complete 0.1.20 source package](Elgato-HD60-For-Linux-0.1.20.zip),
extract it, and open a terminal in the extracted directory. The archive contains
all driver sources, local headers, installer tools and its integrity manifest.
[SHA-256 checksum](SHA256SUMS) is provided for the archive:

```sh
python3 install.py --check
sudo python3 install.py --install
```

Reboot to activate. If an older managed version is installed, use
`sudo python3 install.py --upgrade` instead. The installer does not load or
unload the live module. Secure Boot key enrollment is not automated.

In OBS, add a **Video Capture Device (V4L2)** source, select the HD60 Pro and
YUV420 1920x1080 at 30 fps. Add **Audio Input Capture (ALSA)** using custom
device `hw:HD60Audio,0` at 48000 Hz. Video must be active for audio capture.
Changing OBS output FPS does not increase the card's actual delivery rate.

## Diagnostics and removal

```sh
python3 check-readiness.py --json
python3 check-readiness.py --sample-seconds 10 --json
python3 inspect-installed.py
```

Rate sampling observes an existing capture; it does not start one.
Stop recordings and close OBS normally before upgrading or removing:

```sh
sudo python3 install.py --uninstall
python3 check-removal.py
```

Reboot after removal. See the installation and recovery details in `README.txt` inside the package.

## Testing and scope

The 0.1.20 bundle was built and installed on the tested CachyOS system; its
fresh-boot activation remains pending at publication. Capture source matches
0.1.18/0.1.19, which supplied the reported capture results. Installer failure
handling has fixture coverage; actual multi-kernel upgrade/removal is untested.

Only driver source and installation tools are included. No firmware dumps,
vendor binaries, personal recordings, or machine logs are distributed here.
Elgato is a trademark of its respective owner; this project is unaffiliated.
