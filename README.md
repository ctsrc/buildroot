# Feature-branches

* [box](https://github.com/ctsrc/buildroot/tree/box) -- for RPi3 with webcam and OpenCV

# Development Setup on openSUSE Tumbleweed

## Create `/build` owned by your user

The following commands apply assuming openSUSE Tumbleweed was installed with a btrfs root (the default) under a logical volume named `lvroot` (differs from the default name; `root`) under a volume group named `vgsystem` (differs from the default name; `system`), and that mounting is done by volume label (differs from the default; mount by UUID).

```bash
sudo mount -o subvol=/@ /dev/mapper/vgsystem-lvroot /mnt
sudo btrfs subvolume create /mnt/build
sudo umount /mnt
sudo mkdir /build
sudo mount -o subvol=/@/build /dev/vgsystem/lvroot /build
sudo chown $( whoami ):users /build
```

## Clone feature-branch and rebase

For this repo, I rebase instead of creating merge commits. See [*Merge Pull Requests without Merge Commits*](https://shinglyu.github.io/web/2018/03/25/merge-pull-requests-without-merge-commits.html). I then force-push to my repo. The cost of this is that it is no longer possible to pull from my repo if you cloned it in the past but that is a trade-off I have chosen because any commits in my fork that have not been upstreamed were most likely not upstreamed because they aren't all that useful to anyone else.

```bash
mkdir -p /build/buildroot/box
cd /build/buildroot/box/
git clone -b box git@github.com:ctsrc/buildroot.git src
cd src
git remote add jumpnow git@github.com:jumpnow/buildroot.git
```

```bash
git fetch jumpnow jumpnow
git rebase jumpnow/jumpnow
```
