# Maintainer: Edmunt Pienkowsky <roed@onet.eu>
# Based on firebird-superserver AUR package - http://aur.archlinux.org/packages/firebird-superserver

pkgname=firebird
_pkgver=3.0.4
pkgver=$_pkgver.33054
pkgrel=2
pkgdesc="A open source SQL relational database management system (RDMS)"
arch=('i686' 'x86_64' 'armv6h' 'armv7h' 'aarch64')
url="http://www.firebirdsql.org/"
license=('custom:IPL' 'custom:IDPL')
depends=('icu' 'libedit' 'libtommath' 'libatomic_ops')
makedepends=('boost')
provides=("libfbclient=$pkgver")
conflicts=('firebird-superserver' 'firebird-classicserver' 'libfbclient')
backup=(
    'etc/firebird/firebird.conf'
    'etc/firebird/fbtrace.conf'
    'etc/firebird/databases.conf'
    'etc/firebird/plugins.conf'
    'usr/lib/firebird/plugins/udr_engine.conf'
    'usr/lib/firebird/intl/fbintl.conf')

_patches=(
	'0001-ARM-build-tweaks.patch'
	'0002-Intel-build-tweaks.patch'
	'0003-Do-not-generate-employee-database.patch'
	'0004-Configuration-tweaks.patch'
	'0005-Allow-to-build-using-distcc.patch'
	'0006-gbak-honor-FE-option-along-with-SE-one.patch'
)

_debian_patches_url='http://salsa.debian.org/firebird-team/firebird3.0/raw/master/debian/patches/'
_debian_patches=(
	'out/no-copy-from-icu.patch'
        'out/cloop-honour-build-flags.patch'
        'out/spelling.patch'
        'no-binary-gbaks.patch'
        'packaged-boost.patch'
)

source=("https://github.com/FirebirdSQL/firebird/releases/download/R${_pkgver//./_}/Firebird-${pkgver}-0.tar.bz2"
        'firebird-tmpfiles.conf'
        'firebird-sysusers.conf'
	'firebird.service'
	${_patches[@]}
	"${_debian_patches[@]/#/${_debian_patches_url}}"
)

md5sums=('43569120299b2db7587dcfbddab1e25a'
         'd3f0c0ca2c69ec0d22e64cadd61730e6'
         'e73bcb4f6a99d80fd8a1f5b31b020159'
         '293b709ea19dfb629f745496e5e51020'
         '5edff9e17d12d28fef7affac7daa8706'
         '9962cc872a890b74d0143886f6cb7ee3'
         'ac21ccd2c9bf56cc4f1a8f9383ab730c'
         '2278352167dc3fc10193ecda2dbe03de'
         '2612dd0f9bbd6865411f44148366ad7e'
         '1a2846e73411865273830a69a54504c7'
         'd672c79af5d2fc86a2c2157a72e555bb'
         '2150ce29d4847a7e7ad53792bf5f2277'
         '416a28bc89ab981b7f4947422438009b'
         '24e2860a6744bea5206c4bc6679a7aa9'
         'e48b34d83b0380d4d95161148aa699bc')

prepare() {
  cd $srcdir/Firebird-$pkgver-0

  rm builds/make.new/config/*

  for p in ${_patches[@]}; do
    msg2 "Applying ${p}"
    git apply ../${p}
  done

  for p in ${_debian_patches[@]#out/}; do
    msg2 "Applying ${p}"
    git apply ../${p}
  done

  ./autogen.sh \
    --prefix=/usr \
    --with-fbbin=/usr/bin \
    --with-fbconf=/etc/firebird \
    --with-fbdoc=/usr/share/doc/firebird \
    --with-fbglock=/run/firebird \
    --with-fbhelp=/usr/share/doc/firebird/help \
    --with-fbinclude=/usr/include \
    --with-fblib=/usr/lib \
    --without-fblog \
    --with-fbmsg=/usr/lib/firebird/msg \
    --with-fbplugins=/usr/lib/firebird/plugins \
    --with-fbsbin=/usr/lib/firebird/bin \
    --with-fbudf=/usr/lib/firebird/udf \
    --with-fbintl=/usr/lib/firebird/intl \
    --without-fbmisc \
    --without-fbsample \
    --without-fbsample-db \
    --without-fbsecure-db \
    --with-system-editline
}

build() {
  cd $srcdir/Firebird-$pkgver-0

  make
}

package() {
  cd $srcdir/Firebird-$pkgver-0/gen
  
  ./install/makeInstallImage.sh 
  
  cd $srcdir/Firebird-$pkgver-0
  
  cp -av gen/buildroot/* $pkgdir/

  install -Dm644 $srcdir/firebird-tmpfiles.conf $pkgdir/usr/lib/tmpfiles.d/firebird.conf
  install -Dm644 $srcdir/firebird-sysusers.conf $pkgdir/usr/lib/sysusers.d/firebird.conf
  install -Dm644 $srcdir/firebird.service $pkgdir/usr/lib/systemd/system/firebird.service
  install -Dm644 $pkgdir/etc/firebird/I{,D}PLicense.txt -t $pkgdir/usr/share/licenses/${pkgname}

  # Remove unused files and dirs
  rm $pkgdir/etc/firebird/I{,D}PLicense.txt
  rm $pkgdir/etc/firebird/README
  rm $pkgdir/etc/firebird/WhatsNew
  rm $pkgdir/usr/include/perf.h
  rm -rf $pkgdir/var/log
  rm -rf $pkgdir/run
  rm -rf $pkgdir/usr/include/firebird/impl
  rm -rf $pkgdir/usr/share/doc/firebird/help

  mv $pkgdir/usr/bin/isql{,-fb}

  chmod -R ugo-w $pkgdir/usr/share/doc/firebird
}
