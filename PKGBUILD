# Contributor: Johannes Dewender	arch at JonnyJD dot net
# Contributor: Ionut Biru <ibiru@archlinux.org>
# Contributor: Jakub Schmidtke <sjakub@gmail.com>

pkgname=firefox-esr-alt
pkgver=10.0.7
_pkgveresr=${pkgver}esr
pkgrel=1
pkgdesc="Mozilla Firefox ESR (Extenden Support Release) as additional install"
url="http://www.mozilla.org/projects/firefox"
arch=('i686' 'x86_64')
license=('MPL' 'GPL' 'LGPL')
depends=('gtk2' 'mozilla-common' 'nss>=3.13.1' 'libxt' 'hunspell'
	'startup-notification' 'mime-types' 'dbus-glib' 'alsa-lib' 'sqlite3'
	'libnotify' 'desktop-file-utils' 'libvpx' 'libevent'
	'hicolor-icon-theme' 'libffi5')
makedepends=('unzip' 'zip' 'pkg-config' 'diffutils' 'python2' 'wireless_tools'
	'yasm' 'mesa' 'autoconf2.13' 'libidl2' 'xorg-server-xvfb')
conflicts=()
replaces=()
install=firefox.install
source=(ftp://ftp.mozilla.org/pub/mozilla.org/firefox/releases/$_pkgveresr/source/firefox-$_pkgveresr.source.tar.bz2
        mozconfig vendor.js
        firefox-install-dir.patch libvpx.patch unistd.patch)
md5sums=('649326579aa409e86742659cb1da3694'
         '2c40f7df6b6c680d33c59311e1354aa1'
         '0d053487907de4376d67d8f499c5502b'
         '594523c536a9be951b52c90dd85aff18'
         '5d418ecdbdb9f40597df6b978b0b5ee5'
         '51f1c3b0c072fb2ec12de28be61a7787')

build() {
  cd "$srcdir/mozilla-esr10"

  cp "$srcdir/mozconfig" .mozconfig
  patch -Np1 -i "$srcdir/firefox-install-dir.patch"
  patch -Np1 -i "$srcdir/libvpx.patch"
  patch -Np1 -i "$srcdir/unistd.patch"

  # Fix PRE_RELEASE_SUFFIX
  sed -i '/^PRE_RELEASE_SUFFIX := ""/s/ ""//' \
      browser/base/Makefile.in
  
  export LDFLAGS="$LDFLAGS -Wl,-rpath,/usr/lib/firefox-esr"
  export PYTHON="/usr/bin/python2"
  
  LD_PRELOAD="" /usr/bin/Xvfb -nolisten tcp -extension GLX -screen 0 1280x1024x24 :99 &
  LD_PRELOAD="" DISPLAY=:99 make -j3 -f client.mk profiledbuild MOZ_MAKE_FLAGS="$MAKEFLAGS"
  kill $! || true
}

package() {
  cd "$srcdir/mozilla-esr10"
  make -j1 -f client.mk DESTDIR="$pkgdir" install

  mv "$pkgdir/usr/lib/firefox" "$pkgdir/usr/lib/firefox-esr"
  rm -f "$pkgdir/usr/bin/firefox"
  ln -sf "/usr/lib/firefox-esr/firefox" "$pkgdir/usr/bin/firefox-esr"

  install -m644 "$srcdir"/vendor.js "$pkgdir/usr/lib/firefox-esr/defaults/pref"

  rm -rf "$pkgdir"/usr/lib/firefox-esr/{dictionaries,hyphenation}
  ln -sf /usr/share/hunspell "$pkgdir/usr/lib/firefox-esr/dictionaries"
  ln -sf /usr/share/hyphen "$pkgdir/usr/lib/firefox-esr/hyphenation"


  # We don't want the development stuff
  rm -r "$pkgdir"/usr/{include,lib/firefox-devel,share}

  #workaround for now
  #https://bugzilla.mozilla.org/show_bug.cgi?id=658850
  ln -sf firefox "$pkgdir/usr/lib/firefox-esr/firefox-bin"
}

# vim:set ts=2 sw=2 et:
