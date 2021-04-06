# Maintainer: Taijian <taijian@posteo.de>
# Contributors: Patrick Burroughs (Celti), Abbradar, Zephyr, Christian Autermann, Biginoz, Martin Lee, Ricardo Funke,
#               PirateJonno, lh, Cilyan Olowen, Shaffer, Brcha, Lyle Putnam, Det, Boohbah,
#               Lara Maia, Padfoot, Jorge Barroso, carstene1ns, Sebastian Lau

pkgname=plymouth
pkgver=0.9.5.r84.ge554475
pkgrel=1
pkgdesc="A graphical boot splash screen with kernel mode-setting support"
url="https://www.freedesktop.org/wiki/Software/Plymouth/"
arch=('i686' 'x86_64')
license=('GPL')

depends=('libdrm' 'pango' 'systemd')
makedepends=('git' 'docbook-xsl' 'intltool')
optdepends=('ttf-dejavu: For true type font support'
        'xf86-video-fbdev: Support special graphic cards on early startup'
        'cantarell-fonts: True Type support for BGRT theme')
conflicts=('plymouth-git' 'plymouth-legacy' 'plymouth-nosystemd')
backup=('etc/plymouth/plymouthd.conf')

options=('!libtool' '!emptydirs')

source=("git+https://gitlab.freedesktop.org/plymouth/plymouth.git"
        'manjaro-logo.png'
	'plymouth.encrypt_hook'
	'plymouth.encrypt_install'
	'gdm-plymouth.service'
	'lxdm-plymouth.service'
	'lightdm-plymouth.service'
	'slim-plymouth.service'
	'sddm-plymouth.service'
	'plymouth-deactivate.service'
	'plymouth-start.service.in.patch'
	'plymouth-start.path'
	'plymouth.initcpio_hook'
	'plymouth.initcpio_install'
	'sd-plymouth.initcpio_install'
	'plymouth-quit.service.in.patch'
	'plymouth-update-initrd.patch'
)

sha256sums=('SKIP'
            '014e8a09f88a73b1e5985dcb16a44004e341f5bba90043fa3d7fd7e3a56120cf'
            '7afa97d21444cbac7a6213edda09d9fa73ecbef1a6cea1e745f56669760c6120'
            'b6193aefa4af4449a700bca25565ba13932ceaaf6ecd72ce1ff107f6c3466ad9'
            '0fabb974c1a301da000d5c4b9eea224ba508e577d3a6a798b67b94d4d54af255'
            '06b31999cf60f49e536c7a12bc1c4f75f2671feb848bf5ccb91a963147e2680d'
            '86d0230d9393c9d83eb7bb430e6b0fb5e3f32e78fcd30f3ecd4e6f3c30b18f71'
            '9b5534921c5bf92a9285ba53b323209e812145c204ac5fed6899b7aad78300ef'
            '46b1c4d6c41a888e55d05d21996c4381019e91b490ed13e216b229e264a56648'
            '3b17ed58b59a4b60d904c60bba52bae7ad685aa8273f6ceaae08a15870c0a9eb'
            '3a46f7faced877a913506d59757f0af60ad3d5f0bc365c56ed7ecc7aef75c5eb'
            'ce3d62f4c5a1b5c0ccadd15406c7430251d1a42b232721bfbfc747da1b13e3ff'
            '2a80e2cad8de428358647677afa166219589d3338c5f94838146c804a29e2769'
            'f41d3d1d911bb75c80b6c08d3e85d7a69effc6e162e69a395f7bf03637f2256c'
            '7eeec6934da7f525d6a5e1bdae28ebfbeaaa2a76e3fb4248a8b94ff661fc6466'
            'a1766e3fae000e5158a23b5c01d2c615894e6c3f9923877ccc5ca1bcb36351fa'
            '74908ba59cea53c6a9ab67bb6dec1de1616f3851a0fd89bb3c157a1c54e6633a')

pkgver() {
  cd plymouth
  git describe --long | sed 's/-/.r/;s/-/./'
}


prepare() {
	cd plymouth
	patch -p1 -i $srcdir/plymouth-update-initrd.patch
	patch -p1 -i $srcdir/plymouth-quit.service.in.patch
	patch -p1 -i $srcdir/plymouth-start.service.in.patch
}

build() {
	cd plymouth

	LDFLAGS="$LDFLAGS -ludev" ./autogen.sh \
		--prefix=/usr \
		--exec-prefix=/usr \
		--sysconfdir=/etc \
		--localstatedir=/var \
		--libdir=/usr/lib \
		--libexecdir=/usr/lib \
		--sbindir=/usr/bin \
		--enable-systemd-integration \
		--enable-drm \
		--enable-tracing \
		--enable-pango \
		--enable-gtk=no \
        --enable-gdm-transition \
		--with-release-file=/etc/os-release \
		--with-logo=/usr/share/plymouth/manjaro-logo.png \
		--with-background-color=0x000000 \
		--with-background-start-color-stop=0x000000 \
		--with-background-end-color-stop=0x4D4D4D \
		--without-rhgb-compat-link \
		--without-system-root-install \
        --with-runtimedir=/run

	make
}

package() {
  cd plymouth

  make DESTDIR="$pkgdir" install

  install -Dm644 "$srcdir/manjaro-logo.png" "$pkgdir/usr/share/plymouth/manjaro-logo.png"

  install -Dm644 "$srcdir/plymouth.encrypt_hook" "$pkgdir/usr/lib/initcpio/hooks/plymouth-encrypt"
  install -Dm644 "$srcdir/plymouth.encrypt_install" "$pkgdir/usr/lib/initcpio/install/plymouth-encrypt"
  install -Dm644 "$srcdir/plymouth.initcpio_hook" "$pkgdir/usr/lib/initcpio/hooks/plymouth"
  install -Dm644 "$srcdir/plymouth.initcpio_install" "$pkgdir/usr/lib/initcpio/install/plymouth"
  install -Dm644 "$srcdir/sd-plymouth.initcpio_install" "$pkgdir/usr/lib/initcpio/install/sd-plymouth"

  for i in {gdm,sddm,lxdm,slim,lightdm}-plymouth.service; do
    install -Dm644 "$srcdir/$i" "$pkgdir/usr/lib/systemd/system/$i"
  done

  install -Dm644 "$srcdir/plymouth-deactivate.service" 	"$pkgdir/usr/lib/systemd/system/plymouth-deactivate.service"
  install -Dm644 "$srcdir/plymouth-start.path" 	"$pkgdir/usr/lib/systemd/system/plymouth-start.path"
  install -Dm644 "$pkgdir/usr/share/plymouth/plymouthd.defaults" "$pkgdir/etc/plymouth/plymouthd.conf"
}
