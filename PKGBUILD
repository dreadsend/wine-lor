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
pkgver=4.20
pkgrel=1

_pkgbasever=${pkgver/rc/-rc}

source=(https://dl.winehq.org/wine/source/4.x/wine-$_pkgbasever.tar.xz
        "wine-staging-v$_pkgbasever.tar.gz::https://github.com/wine-staging/wine-staging/archive/v$_pkgbasever.tar.gz"
        30-win32-aliases.conf
        lor-patch.diff::https://bugs.winehq.org/attachment.cgi?id=65624
        )
sha512sums=('c01af88106b8a808fbceb6ace45882c300656a47913142ba7052283b358d6d5b4379895804e15a2ca1870a4e955ea3ca857ec042a8070ea4ffaefa0c7a84e701'
            '2ce4f6fa83fc275c83bc7f7bbd4b1a4ca65f7e55e16af40958635b8b2efc2b174baa79ed576ed09c4c13b572756fb6262f19281e5b72797c7e0f1f6c4dd1a4ee'
            '6200d75042a5993294ee58583fa9d145e46a36bcc2a38ddae92482366aaf67423b160556f748cd85d3ee1c521c497488810bdc0b79e192742f83ae49e7f55938'
            '02a3f134b8da2c5d1289c43ae732f31eb8a93fae0aa8b61b8087e9f386cec0adb86fe5b836a5a214b97510ce281165e5fd496be9d431e7889b20abe9c07eea4f'
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
  patch -p1 -i "$srcdir/lor-patch.diff"
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

  msg2 "Building Wine-64..."

  cd "$srcdir/$pkgname-64-build"

  ../$pkgname/configure \
    --prefix=/opt/wine-lor \
    --with-x \
    --with-gstreamer \
    --with-xattr \
    --libdir=/opt/wine-lor/lib \
    --enable-win64

  make

  msg2 "Building Wine-32..."

  export PKG_CONFIG_PATH="/usr/lib32/pkgconfig"
  cd "$srcdir/$pkgname-32-build"
  ../$pkgname/configure \
    --prefix=/opt/wine-lor \
    --with-x \
    --with-gstreamer \
    --with-xattr \
    --libdir=/opt/wine-lor/lib32 \
    --with-wine64="$srcdir/$pkgname-64-build"

  make

}

package() {

  msg2 "Packaging Wine-32..."
  cd "$srcdir/$pkgname-32-build"

  make prefix="$pkgdir/opt/wine-lor" \
    libdir="$pkgdir/opt/wine-lor/lib32" \
    dlldir="$pkgdir/opt/wine-lor/lib32/wine" install


  msg2 "Packaging Wine-64..."
  cd "$srcdir/$pkgname-64-build"

  make prefix="$pkgdir/opt/wine-lor" \
    libdir="$pkgdir/opt/wine-lor/lib" \
    dlldir="$pkgdir/opt/wine-lor/lib/wine" install

  # Font aliasing settings for Win32 applications
  install -d "$pkgdir"/etc/fonts/conf.{avail,d}
  install -m644 "$srcdir/30-win32-aliases.conf" "$pkgdir/etc/fonts/conf.avail/30-wine-lor-win32-aliases.conf"
  ln -s ../conf.avail/30-wine-lor-win32-aliases.conf "$pkgdir/etc/fonts/conf.d/30-wine-lor-win32-aliases.conf"
}

# vim:set ts=8 sts=2 sw=2 et: