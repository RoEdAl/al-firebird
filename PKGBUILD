# Maintainer: Edmunt Pienkowsky <roed@onet.eu>
# Based on firebird-superserver AUR package - http://aur.archlinux.org/packages/firebird-superserver

pkgname=firebird
_pkgver=3.0.4
pkgver=$_pkgver.33054
pkgrel=8
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
	'0007-Use-gcc-visibility-attributes.patch'
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
	'firebird-log.socket'
	'firebird-log.service'
	'firebird-logging-socket.conf'
	${_patches[@]}
	"${_debian_patches[@]/#/${_debian_patches_url}}"
)

md5sums=('43569120299b2db7587dcfbddab1e25a'
         '38afc47e2bab01a1797bda93c11b137f'
         'e73bcb4f6a99d80fd8a1f5b31b020159'
         '34c298d809b9524174ba12d147835d65'
         'fb4c8b7a0366176a59434f17a37dc744'
         '5a4211c4b58d964a91f0c03b11e01f3d'
         'f257aea38461095daa655dea0d7a47b3'
         '607d129cc8ced5cdf39c8ce775feaec1'
         'b732af37c6b4ad364aa64f75a808a01f'
         '49edd75f97e0fc664a88d6570cb86d0a'
         '958d2339736040a72274169baf1fb43f'
         'defa1022e7fadce19a51506efa126c0b'
         '321a34e81a3ab096fe7264ac0d66d6e0'
         '766a1094f674256dd07dc272cf6ca4e3'
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
}

build() {
  cd $srcdir/Firebird-$pkgver-0

  local -r EXTRA_CFLAGS=(
	-ffile-prefix-map=$(pwd)/=/
	-fvisibility=hidden
  )

  export CFLAGS="${CFLAGS} ${EXTRA_CFLAGS[@]}"
  export CXXFLAGS="${CXXFLAGS} ${EXTRA_CFLAGS[@]} -fvisibility-inlines-hidden"

  ./autogen.sh \
    --prefix=/usr \
    --with-fbbin=/usr/bin \
    --with-fbconf=/etc/firebird \
    --with-fbdoc=/usr/share/doc/firebird \
    --with-fbglock=/run/firebird \
    --with-fbhelp=/usr/share/doc/firebird/help \
    --with-fbinclude=/usr/include \
    --with-fblib=/usr/lib \
    --with-fblog=/var/log/firebird \
    --with-fbmsg=/usr/lib/firebird/msg \
    --with-fbplugins=/usr/lib/firebird/plugins \
    --with-fbsbin=/usr/lib/firebird/bin \
    --with-fbudf=/usr/lib/firebird/udf \
    --with-fbintl=/usr/lib/firebird/intl \
    --without-fbmisc \
    --without-fbsample \
    --without-fbsample-db \
    --with-fbsecure-db=/var/lib/firebird/system \
    --with-system-editline

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
  install -Dm644 $srcdir/firebird-log.socket $pkgdir/usr/lib/systemd/system/firebird-log.socket
  install -Dm644 $srcdir/firebird-log.service $pkgdir/usr/lib/systemd/system/firebird-log.service
  install -Dm644 $srcdir/firebird-logging-socket.conf $pkgdir/usr/lib/systemd/system/firebird.service.d/logging-socket.conf
  install -Dm644 $pkgdir/etc/firebird/I{,D}PLicense.txt -t $pkgdir/usr/share/licenses/${pkgname}

  # Remove unused files and dirs
  rm $pkgdir/etc/firebird/I{,D}PLicense.txt
  rm $pkgdir/etc/firebird/README
  rm $pkgdir/etc/firebird/WhatsNew
  rm $pkgdir/usr/include/perf.h
  rm -rf $pkgdir/var/log
  mkdir $pkgdir/usr/share/factory
  mv $pkgdir/var $pkgdir/usr/share/factory
  rm -rf $pkgdir/run
  rm -rf $pkgdir/usr/include/firebird/impl
  rm -rf $pkgdir/usr/share/doc/firebird/help

  mv $pkgdir/usr/bin/isql{,-fb}

  chmod -R ugo-w $pkgdir/usr/share/doc/firebird
}
