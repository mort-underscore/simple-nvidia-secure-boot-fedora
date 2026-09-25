***Simple nvidia secure boot fix. On fedora (should work on fedora adjacent non immutable distros)***

 - Basically while secure boot is still working, until you do this, nouvou is being used. Meaning no hdmi 2.1 or other nvidia closed source perks

```The bane of my existance on a new install
modprobe: ERROR: could not insert 'nvidia': Key was rejected by service
NVIDIA-SMI has failed because it couldn't communicate with the NVIDIA driver.
```

---

- Packages needed

Makes sure you have these, otherwise something wont work.

```
sudo dnf install -y \
  kernel-devel \
  kernel-headers \
  akmod-nvidia \
  xorg-x11-drv-nvidia-cuda \
  kmodtool \
  akmods \
  mokutil \
  openssl
```

---

Steps - **TECHNICALLY** you can do these all in one really long command. however I wanted to make a guide look better so you get fancy steps

1 - Remove all keys currently on the install. If you have old keys they kinda f*ck with it.

```
sudo rm -rf /etc/pki/akmods/certs/*
sudo rm -rf /etc/pki/akmods/private/*
```

2 - This is going to make new keys

```
sudo kmodgenca -a --force
```

3 - use mokutil to import keys, its going to ask for a password. this is going to be used only on the next boot and never again, so you can make it something short and dumb

```
sudo mokutil --import /etc/pki/akmods/certs/public_key.der
```

4 - force a rebuild of the kernel - should take a couple seconds up to a couple minutes depending on system speed

```
sudo akmods --force --rebuild
sudo dracut --force
```
5 - make sure nouveau is blacklisted - this just keeps nouveau from loading before your nvidia driver. 

```
sudo grubby --update-kernel=ALL --args="rd.driver.blacklist=nouveau modprobe.blacklist=nouveau"
```

6 - Reboot

```
systemctl reboot
```

When you boot there is gonna be a blue screen, if your uefi bugs and blocks it just hit the arrow keys to get rid of it.
Hit enroll
put your super dumb password
hit reboot

To Varifey just type the following into terminal

```
nvidia-smi
```
Because fedora is smart you will never have to touch this.
Because nvidia is dumb you have to do this for every new install
