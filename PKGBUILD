# -*- mode: shell-script -*-
# Maintainer: Julian Sperling <juliansperling@gmail.com>
# Contributor: Manuel Reimer <mail+wine@m-reimer.de>
# Contributor: Felix Yan <felixonmars@archlinux.org>
# Contributor: Sven-Hendrik Haase <sh@lutzhaase.com>
# Contributor: Jan "heftig" Steffens <jan.steffens@gmail.com>
# Contributor: Eduardo Romero <eduardo@archlinux.org>
# Contributor: Giovanni Scafora <giovanni@archlinux.org>

#PKGBUILD loosely based on Manuel Reimers Wine-lol
#Contains Fixes for Legends of Runeterra
#See Bug Report: https://bugs.winehq.org/show_bug.cgi?id=47970 

pkgname=wine-lor
pkgver=4.19
pkgrel=1

_pkgbasever=${pkgver/rc/-rc}

source=(https://dl.winehq.org/wine/source/4.x/wine-$_pkgbasever.tar.xz
        "wine-staging-v$_pkgbasever.tar.gz::https://github.com/wine-staging/wine-staging/archive/v$_pkgbasever.tar.gz"
        30-win32-aliases.conf
        lor-patches::https://bugs.winehq.org/attachment.cgi?id=65592
        )
sha512sums=('a86bd7ccfb1878445ea946e4ca72769c10b4d966bcc893536a8c3eaa29484ba687dc3a44755ec92a2ad6073e736985b814c0c682bf4f90b5ffe2bca0ebbf4b25'
            'a47ae16dd39797b54011f942fe7e722897af7ddc0bed2bf2e39c46aecf60a02769f7287609c63b4c0c3f1c8e40172e0cdf3bdb53562a07d7a2b7ac53e570dbdc'
            '6200d75042a5993294ee58583fa9d145e46a36bcc2a38ddae92482366aaf67423b160556f748cd85d3ee1c521c497488810bdc0b79e192742f83ae49e7f55938'
            '37294cb699af058c24a2ea422360b0325e36119130481170328796bdcbe7c0d0512441650fe8b1924fa149c9b7d0e68a4cc43a675082e3e280d5beb4bdb0e8d3'
            )


pkgdesc="A compatibility layer for running Windows programs - Staging branch with legends of Runeterra fixes"
url="http://www.wine-staging.com"
arch=(x86_64)
options=(staticlibs)
license=(LGPL)

depends=(
  attr             lib32-attr
  fontconfig       lib32-fontconfig
  lcms2            lib32-lcms2
  libxml2          lib32-libxml2
  libxcursor       lib32-libxcursor
  libxrandr        lib32-libxrandr
  libxdamage       lib32-libxdamage
  libxi            lib32-libxi
  gettext          lib32-gettext
  freetype2        lib32-freetype2
  glu              lib32-glu
  libsm            lib32-libsm
  gcc-libs         lib32-gcc-libs
  libpcap          lib32-libpcap
  desktop-file-utils
)

makedepends=(
  autoconf 
  ncurses 
  bison 
  perl 
  fontforge 
  flex
  'gcc>=4.5.0-2'
  giflib                lib32-giflib
  libpng                lib32-libpng
  gnutls                lib32-gnutls
  libxinerama           lib32-libxinerama
  libxcomposite         lib32-libxcomposite
  libxmu                lib32-libxmu
  libxxf86vm            lib32-libxxf86vm
  libldap               lib32-libldap
  mpg123                lib32-mpg123
  openal                lib32-openal
  v4l-utils             lib32-v4l-utils
  alsa-lib              lib32-alsa-lib
  libxcomposite         lib32-libxcomposite
  mesa                  lib32-mesa
  mesa-libgl            lib32-mesa-libgl
  opencl-icd-loader     lib32-opencl-icd-loader
  libxslt               lib32-libxslt
  libpulse              lib32-libpulse
  libva                 lib32-libva
  gtk3                  lib32-gtk3
  gst-plugins-base-libs lib32-gst-plugins-base-libs
  vulkan-icd-loader     lib32-vulkan-icd-loader
  sdl2                  lib32-sdl2
  vkd3d                 lib32-vkd3d
  sane
  libgphoto2
  gsm
  ffmpeg
  samba
  opencl-headers
)

optdepends=(
  giflib                lib32-giflib
  libpng                lib32-libpng
  libldap               lib32-libldap
  gnutls                lib32-gnutls
  mpg123                lib32-mpg123
  openal                lib32-openal
  v4l-utils             lib32-v4l-utils
  libpulse              lib32-libpulse
  alsa-plugins          lib32-alsa-plugins
  alsa-lib              lib32-alsa-lib
  libjpeg-turbo         lib32-libjpeg-turbo
  libxcomposite         lib32-libxcomposite
  libxinerama           lib32-libxinerama
  ncurses               lib32-ncurses
  opencl-icd-loader     lib32-opencl-icd-loader
  libxslt               lib32-libxslt
  libva                 lib32-libva
  gtk3                  lib32-gtk3
  gst-plugins-base-libs lib32-gst-plugins-base-libs
  vulkan-icd-loader     lib32-vulkan-icd-loader
  sdl2                  lib32-sdl2
  vkd3d                 lib32-vkd3d
  sane
  libgphoto2
  gsm
  ffmpeg
  cups
  samba           dosbox
)


install=wine.install

prepare() {
  # Allow ccache to work
  mv wine-$_pkgbasever $pkgname

  # apply wine-staging patchset
  pushd wine-staging-$_pkgbasever/patches
  ./patchinstall.sh DESTDIR="$srcdir/$pkgname" --all
  popd


  #Apply Legends of Runeterra Fixes
  pushd "$srcdir/$pkgname"
  patch -p1 -i "$srcdir/0001-NtContinue-accept-both-i386-and-AMD64-full-contexts.patch"
  patch -p1 -i "$srcdir/0002-Winebuild-rewrote-syscall-dispatcher-now-it-returns-.patch"
  patch -p1 -i "$srcdir/0003-Signal-x86_64-Use-NtContinue-to-restore-context.patch"
  popd

  # Fix opencl header path
  sed 's|OpenCL/opencl.h|CL/opencl.h|g' -i $pkgname/configure* 
}

build() {
  cd "$srcdir"

   # Get rid of old build dirs
  rm -rf $pkgname-{32,64}-build
  mkdir $pkgname-{32,64}-build
 
  # https://bugs.winehq.org/show_bug.cgi?id=43530
  export CFLAGS="${CFLAGS/-fno-plt/}"
  export LDFLAGS="${LDFLAGS/,-z,now/}"
 
  # Make sure everything builds for 32bit
  export CFLAGS="-m32 $CFLAGS"

  msg2 "Building Wine-32..."

  export PKG_CONFIG_PATH="/usr/lib32/pkgconfig"
  cd "$srcdir/$pkgname-32-build"

  ../$pkgname/configure \
    --prefix=/opt/wine-lor \
    --with-x \
    --with-gstreamer \
    --with-xattr \
    --libdir=/usr/lib32

  make
}

package() {
  msg2 "Packaging Wine-32..."
  cd "$srcdir/$pkgname-32-build"

  make prefix="$pkgdir/opt/wine-lor" \
    libdir="$pkgdir/opt/wine-lor/lib32" \
    dlldir="$pkgdir/opt/wine-lor/lib32/wine" install

  # Font aliasing settings for Win32 applications
  install -d "$pkgdir"/etc/fonts/conf.{avail,d}
  install -m644 "$srcdir/30-win32-aliases.conf" "$pkgdir/etc/fonts/conf.avail/30-wine-lor-win32-aliases.conf"
  ln -s ../conf.avail/30-wine-lor-win32-aliases.conf "$pkgdir/etc/fonts/conf.d/30-wine-lor-win32-aliases.conf"
}

# vim:set ts=8 sts=2 sw=2 et: