# Maintainer: Que Quotion <quequotion@bugmenot.com>
# Contributor: Xiao-Long Chen <chenxiaolong@cxl.epac.to>
# Original Maintainer: György Balló <ballogy@freestart.hu>
# Contributor: thn81 <root@scrat>

pkgbase=libappindicator-bzr
pkgname=('libappindicator-bzr' 'libappindicator3-bzr')
pkgver=r273
pkgrel=2
pkgdesc="A library to allow applications to export a menu into the Unity Menu bar"
arch=('i686' 'x86_64')
url="https://launchpad.net/libappindicator"
license=('LGPL')
makedepends=('dbus-glib' 'libindicator' 'libindicator3' 'libdbusmenu-gtk2' 'libdbusmenu-gtk3' 'gobject-introspection' 'vala' 'gtk-sharp-2' 'pygtk' 'perl-xml-libxml' 'gtk-doc')
groups=('unity')
source=("bzr+lp:libappindicator"
        '0001_Timeout.patch')
sha512sums=('SKIP'
            'b14f77a93473d7ef92a76aff0185010858f130dfe8c140b78876c6cc3e835508e66063139fc4adc469d81b6b9bec0c04fab77a990fef67df00ed95c4b905046d')

pkgver() {
  cd libappindicator

  printf "r%s" "$(bzr revno)"
}

prepare() {
  cd "${srcdir}/libappindicator"

  patch -Np1 -i "${srcdir}/0001_Timeout.patch"
  #sed -i 's|/\*\*|*|g' src/app-indicator.c
  #sed -i 's|/\*\*|*|g' src/app-indicator.h

  # nunit renamed to mono-nunit in /usr/lib/pkgconfig/mono-nunit.pc
  sed -i 's/nunit >= 2\.4\.7/mono-nunit >= 2.4.7/' configure.ac

  # The /usr/lib/cli location for Mono bindings is specific to Ubuntu
  sed -i 's|assemblydir = $(libdir)/cli/appindicator-sharp-0.1|assemblydir = $(libdir)/appindicator-sharp-0.1|' bindings/mono/Makefile.am
  sed -i 's|assemblies_dir=${prefix}/lib/cli/appindicator-sharp-0.1|assemblies_dir=${libdir}/appindicator-sharp-0.1|' bindings/mono/appindicator-sharp-0.1.pc.in


  #patch -Np2 -i "${srcdir}/fixwhat.patch"
}

build() {
  cd "${srcdir}/libappindicator"

  gtkdocize
  autoreconf -vfi

  [[ -d build-gtk2 ]] || mkdir build-gtk2
  pushd build-gtk2
  ../configure --prefix=/usr --with-gtk=2 --disable-static PYTHON=python2
  make -j1
  popd

  [[ -d build-gtk3 ]] || mkdir build-gtk3
  pushd build-gtk3
  ../configure --prefix=/usr --with-gtk=3 --disable-static PYTHON=python2
  make
  popd
}

package_libappindicator-bzr() {
  pkgdesc+=" (GTK+ 2 library)"
  provides=(libappindicator{,-gtk2,sharp}{,-bzr}=${pkgver})
  depends=('libindicator' 'libdbusmenu-gtk2')
  conflicts=('indicator-application<0.2.91' libappindicator{,-gtk2,-sharp})

  cd "${srcdir}/libappindicator/build-gtk2"

  export MAKEFLAGS="-j1"
  make DESTDIR="${pkgdir}/" install
}

package_libappindicator3-bzr(){
  pkgdesc+=" (GTK+ 3 library)"
  depends=('libindicator3' 'libdbusmenu-gtk3')
  provides=(libappindicator{,-gtk}3{,-bzr}=${pkgver})
  conflicts=(libappindicator{,-gtk}3)

  cd "${srcdir}/libappindicator/build-gtk3"

  make -C src DESTDIR="${pkgdir}/" install
  make -C bindings/vala DESTDIR="${pkgdir}/" install
}

