# Building Archlinux ARM packages
Just like in Archlinux, you'll need `base-devel` and `git` before you can build anything.
Get them now.
```
> sudo pacman -S base-devel git
```

## The PKGBUILDs repo
Most Archlinux PKGBUILDs will actually just compile on Archlinux ARM without modification.
Some of them, though, required modification.
The modified PKGBUILDs are hosted in [a single git repo on github](https://github.com/archlinuxarm/PKGBUILDs).
Clone this repo:
```
> git clone --depth=1 https://github.com/archlinuxarm/PKGBUILDs.git
```
Then search the repo for your package.
Suppose you want to build `geoclue`.
```
> find PKGBUILDs -name geoclue
> 
```
It's not there.
That means the default Archlinux PKGBUILD must build just fine without modification.

## Building an unmodified package

The process is almost the same as in Archlinux, only the `-A` switch must be added to tell `makepkg` to ignore the architecture mismatch.
```
> git clone --depth=1 https://gitlab.archlinux.org/archlinux/packaging/packages/geoclue.git
...
> cd geoclue
> makepkg -As
```

## Building a modified package

What if you actually wanted to build `pacman`?
```
> find PKGBUILDs -name pacman
PKGBUILDs/core/pacman
> 
```
Oh! Looks like this one *has* been modified.
Build this package instead of the Archlinux one, using the same procedure as before:
```
> cd PKGBUILDs/core/pacman
> makepkg -As
```
