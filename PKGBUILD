# Maintainer: Edmunt Pienkowsky <roed@onet.eu>
# Based on firebird-superserver AUR package - http://aur.archlinux.org/packages/firebird-superserver

pkgname=firebird
_pkgver=3.0.6
pkgver=$_pkgver.33328
pkgrel=1
# _commit_epoch=$(git show -s --format=%ct)
_commit_epoch=${SOURCE_DATE_EPOCH:-1593160229}
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
	'0006-Use-gcc-visibility-attributes.patch'
)

_debian_patches_url='http://salsa.debian.org/firebird-team/firebird3.0/raw/debian/3.0.6.33328.ds4-2/debian/patches/'
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

sha256sums=('34c1d2a29bbaf288e682cd1b5f8083f2baf73f351062245ace0bee35a3f7d35f'
            'bff910959c2a0869105300b7964daee055bcadf3f0696ef1ce16d2c114d675e2'
            'e2dc41a67b744b569ba68961549e0555a7dcf2cc792d4813b768007cbe029ddb'
            '46f531bb4cae65bb607c15862b1f7826fc104fc7719d4c99380e1c2637ecb208'
            '701bed4fce773978599c287ddcb2f98c20d3a5d26d7ec25cf931346d762e3098'
            'aeca32ee8e52d73878764f97152ad9e842e7ebda9de7ae4b42ddac550ec60a4c'
            '3cc3b1f898fb8e3b0f2bdd1d2dec65a5f1d5586330de29b85fb1dee809d718e8'
            'a3ca62a00faf77b3870e2eebdac350faaa8c4137b9b5313dce499cf773ccc214'
            'aa4e3889975326e821b8c52b1ac4645b2abb5f1574c05e2584d03d81b00d609a'
            '18ce581582ed865823950b049e7fa9fc3ebed5610382cf63b5fd2943bb6741f3'
            '08a9de5add96c9deee2909d4e32d2c4027dce4f4f56db334275f89833c893074'
            'eb764c4c30a7896a9e9980d01f1b63d613030ce81e1b764b1e80ecdd9a777951'
            'ed6061f49b5e7cb3cf120683975eef4aeb704bde032c0906250fa761466797c5'
            'd4d38cb808a36e444528b82dd459833884c1ad9a30fdfedb70b02cd9659b1fc2'
            '3d4c07c9a1342782e59b84c9a47a1ae90a2929a5d664c3830e8d0260ba7f0139'
            'c2d0e714be2722315fe9350e8091e8e8feae5ef3735e914e7dbfa51303f4391a'
            '32e49f1a3f52db9778def639fd58ef3d87db8a5591abaabc2798d85fbeef2bcb'
            '2356f766b55412d0f3350dea4d6897e2e4ee1476e3165bd2620a22872ee23cf0')

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
