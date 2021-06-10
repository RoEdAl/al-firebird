# Maintainer: Edmunt Pienkowsky <roed@onet.eu>
# Based on firebird-superserver AUR package - http://aur.archlinux.org/packages/firebird-superserver

pkgname=firebird
_pkgver=4.0.0
pkgver=${_pkgver}.2496
pkgrel=1
pkgdesc="A open source SQL relational database management system (RDMS)"
arch=('i686' 'x86_64' 'armv6h' 'armv7h' 'aarch64')
url="http://www.firebirdsql.org/"
license=('custom:IPL' 'custom:IDPL')
depends=('icu' 'libedit' 'libtommath' 'libatomic_ops')
optdepends=('boost-libs')
makedepends=('boost' 'unzip')
provides=("libfbclient=$pkgver")
conflicts=('firebird-superserver' 'firebird-classicserver' 'libfbclient')
backup=(
    'etc/firebird/firebird.conf'
    'etc/firebird/fbtrace.conf'
    'etc/firebird/databases.conf'
    'etc/firebird/plugins.conf'
    'etc/firebird/replication.conf'
    'usr/lib/firebird/plugins/udr_engine.conf'
    'usr/lib/firebird/intl/fbintl.conf')

_patches=(
	0001-Remove-QLI.patch
	0002-Foce-to-load-proper-fdclient-library.patch
	0003-ARM-build-tweaks.patch
	0004-Intel-build-tweaks.patch
	0005-Do-not-generate-employee-database.patch
	0006-Configuration-tweaks.patch
	0007-Allow-to-build-using-distcc.patch
	0008-Use-gcc-visibility-attributes.patch
	0009-Link-isql-with-ICU-instead-of-embedding-part-of-it-i.patch
	0010-make-cloop-build-honor-compiler-linker-flags-from-th.patch
	0011-spelling-error.patch
	0012-use-system-wide-boost-headers.patch
	0013-extern-Honor-compiler-and-linker-flags-from-the-buil.patch
)

source=("https://github.com/FirebirdSQL/firebird/releases/download/v${_pkgver}/Firebird-${pkgver}-0.tar.xz"
	'firebird-tmpfiles.conf'
	'firebird-sysusers.conf'
	'firebird.service'
	'firebird-log.socket'
	'firebird-log.service'
	'firebird-logging-socket.conf'
	${_patches[@]}
)

sha256sums=('c155b8893e39d27c90f164d4865a7940749f47e971c7da6cf4828c980c708a57'
            'bff910959c2a0869105300b7964daee055bcadf3f0696ef1ce16d2c114d675e2'
            'e2dc41a67b744b569ba68961549e0555a7dcf2cc792d4813b768007cbe029ddb'
            '46f531bb4cae65bb607c15862b1f7826fc104fc7719d4c99380e1c2637ecb208'
            '701bed4fce773978599c287ddcb2f98c20d3a5d26d7ec25cf931346d762e3098'
            'aeca32ee8e52d73878764f97152ad9e842e7ebda9de7ae4b42ddac550ec60a4c'
            '3cc3b1f898fb8e3b0f2bdd1d2dec65a5f1d5586330de29b85fb1dee809d718e8'
            '8bece67c1184e1576514610c247b96eeb191b5c47fe686eb6e88b588b936defd'
            '57dbc1fbb8f93cf22488e6ece864d066a1e4b94701eed692edb88d51c42b3248'
            '727104c2a8b10baeff7ccbcbbda8e891bcc8e7099f8f4c50bb2e7c20e6b1193b'
            '0f6be98c347bc87a244476051a3d316ae253886818e3bce4edc602f250d6bc6e'
            '18ac1aa440a6fc061bc96b6ef807612d9ace7250846845ad547512cb8848dae9'
            '392c3bf474ae8aefbe71a0e8f39fe57d8e1ad2a3617143e7ae53cf69f4ae3570'
            '840e37e1dd0fb1aafb03777d61a2ebbdbf51b942a1872c654fee1416103e468e'
            '63c655f3ea606c81ee8f386e514c53650f9679a0e486130ea8d69e0af09ad3cd'
            '7bbfb95c2cce25b802de77549b06c89778bfa078b3a08108d77606ca20d6530c'
            '8654ce80cb784441302e2f173ca61e459cf7048284cd372f38c181dab77af67f'
            '94dc0bedb859d1aeac6ab042a839253ccfe9f37e2bd7ed981bf1b1b5a694caa0'
            'd191ae22d2936c0378dc2ff213beac1b34f9e933c9780d80ebe843c852d77803'
            '2aa55ffff2a0b3bdc82e4bf779b5f73497118cf702c6e8e04ae0a55d34b3ff92')

prepare() {
  cd $srcdir/Firebird-$pkgver-0

  rm builds/make.new/config/*

  for p in ${_patches[@]}; do
    msg2 "Applying ${p}"
    git apply ../${p}
  done
}

build() {
  cd $srcdir/Firebird-$pkgver-0

  local -r EXTRA_CFLAGS=(
	-ffile-prefix-map=$(pwd)=.
	-fvisibility=hidden
  )

  local -r ENV_ARGS=(
	"CFLAGS=${CFLAGS} ${EXTRA_CFLAGS[@]}"
	"CXXFLAGS=${CXXFLAGS} ${EXTRA_CFLAGS[@]} -fvisibility-inlines-hidden"
  )

  env "${ENV_ARGS[@]}" ./autogen.sh \
    --prefix=/usr \
    --with-fbbin=/usr/bin \
    --with-fbconf=/etc/firebird \
    --with-fbdoc=/usr/share/doc/firebird \
    --with-fbglock=/run/firebird \
    --with-fbinclude=/usr/include \
    --with-fblib=/usr/lib \
    --with-fblog=/var/log/firebird \
    --with-fbmsg=/usr/lib/firebird/msg \
    --with-fbplugins=/usr/lib/firebird/plugins \
    --with-fbsbin=/usr/lib/firebird/bin \
    --with-fbintl=/usr/lib/firebird/intl \
    --without-fbmisc \
    --without-fbsample \
    --without-fbsample-db \
    --with-fbsecure-db=/var/lib/firebird/system \
    --with-system-editline \
    --with-builtin-tomcrypt

   env "${ENV_ARGS[@]}" make
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
  rm $pkgdir/usr/include/perf.h
  rm -rf $pkgdir/var/log
  mkdir $pkgdir/usr/share/factory
  mv $pkgdir/var $pkgdir/usr/share/factory
  rm -rf $pkgdir/run
  rm -rf $pkgdir/usr/include/firebird/impl/boost

  mv $pkgdir/usr/bin/isql{,-fb}
  mv $pkgdir/etc/firebird/README.md $pkgdir/usr/share/doc/firebird
  mv $pkgdir/etc/firebird/CHANGELOG.md $pkgdir/usr/share/doc/firebird

  chmod -R ugo-w $pkgdir/usr/share/doc/firebird
}
