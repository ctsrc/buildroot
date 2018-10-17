# Current feature-branches

* [box-20181017](https://github.com/ctsrc/buildroot/tree/box-20181017) -- for RPi3 with webcam and OpenCV

# Development Setup on openSUSE Tumbleweed

## Install dependencies

```bash
sudo zypper install git
```

## Create `/build` owned by your user

The following commands apply assuming openSUSE Tumbleweed was installed with a btrfs root (the default) under a logical volume named `lvroot` (differs from the default name; `root`) under a volume group named `vgsystem` (differs from the default name; `system`), and that mounting is done by volume label (differs from the default; mount by UUID).

```bash
sudo mount -o subvol=/@ /dev/mapper/vgsystem-lvroot /mnt
sudo btrfs subvolume create /mnt/build
sudo umount /mnt
sudo mkdir /build
sudo mount -o subvol=/@/build /dev/vgsystem/lvroot /build
sudo chown $( whoami ):users /build
sudo tee -a /etc/fstab >/dev/null <<EOF
/dev/vgsystem/lvroot                       /build                  btrfs  subvol=/@/build               0  0
EOF
```

## Clone feature-branch and rebase

For this repo, I rebase instead of creating merge commits (see [*Merge Pull Requests without Merge Commits*](https://shinglyu.github.io/web/2018/03/25/merge-pull-requests-without-merge-commits.html)). I do this regularly and then force-push the rebased feature-branches to my repo. The cost of this is that it is no longer possible to pull from my repo if you cloned it in the past but that is a trade-off I have chosen because any commits in my fork that have not been upstreamed were most likely not upstreamed because they aren't all that useful to anyone else.

```bash
mkdir -p /build/buildroot/box
cd /build/buildroot/box/
mkdir -p {dl,ccache}
git clone -b box-20181017 git@github.com:ctsrc/buildroot.git src
cd src
git remote add jumpnow git@github.com:jumpnow/buildroot.git
```

```bash
git fetch jumpnow jumpnow
tgtb=box-$( date +%Y%m%d )
git checkout -b $tgtb 2>/dev/null || git checkout $tgtb
git rebase jumpnow/jumpnow
```

# Building

See also [Building Raspberry Pi Systems with Buildroot](https://jumpnowtek.com/rpi/Raspberry-Pi-Systems-with-Buildroot.html) for additional details.

```bash
make jumpnow_rpi3_defconfig
```
