# Maintainer: Taijian <taijian@posteo.de>
# Contributors: Patrick Burroughs (Celti), Abbradar, Zephyr, Christian Autermann, Biginoz, Martin Lee, Ricardo Funke,
#               PirateJonno, lh, Cilyan Olowen, Shaffer, Brcha, Lyle Putnam, Det, Boohbah,
#               Lara Maia, Padfoot, Jorge Barroso, carstene1ns, Sebastian Lau

pkgname=plymouth
pkgver=0.9.4.r228.gd7c737d
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
provides=('plymouth')
conflicts=('plymouth' 'plymouth-legacy' 'plymouth-nosystemd')
backup=('etc/plymouth/plymouthd.conf')

options=('!libtool' '!emptydirs')

source=("git+https://gitlab.freedesktop.org/plymouth/plymouth.git"
        'arch-logo.png'
        'watermark.png'
	'plymouth.encrypt_hook'
	'plymouth.encrypt_install'
	'gdm-plymouth.service'
	'lxdm-plymouth.service'
	'lightdm-plymouth.service'
	'slim-plymouth.service'
	'sddm-plymouth.service'
	'plymouth-deactivate.service'
	'plymouth-start.service'
	'plymouth-start.path'
	'plymouth.initcpio_hook'
	'plymouth.initcpio_install'
	'sd-plymouth.initcpio_install'
	'plymouth-quit.service.in.patch'
	'plymouth-update-initrd.patch')

md5sums=('SKIP'
         '65fa2763d5c9bb9f80973ea5e6e3db3e'
         '2d82f692d36fee2bfc0de0aee1e7d6f9'
         'd67132b297ccfb1a877a2efd78076963'
         'de482fa4d91887c3aafa673341b6c719'
         'ae6e97bb1104bb12cbca6738e3fb872d'
         '1430ae2ec501d600f8f6771454dc9bbe'
         '870ea3e63c6989e2badf79d1fbafa914'
         'a3cfc30df846b2d7057a29e7fbe8733a'
         'b95f6979dc2f373045b2ab88a36d6771'
         '006847d16b852c7a50ee2f241fd9647e'
         '606cd558141551a1ce0d80150a045d83'
         '672ad913e2383483bcb4599a0a6bee48'
         '32f04fdbd1eb94ade30d1e63fdcdd9b5'
         '1979a4d9a1f496b52be9c29a7adb698b'
         'a08c422b67314e21100207f61caef095'
         '69f16be8d44b25c199eb53c436884b07'
         '98c20619291ee7c5a1c5f24b50e92db8')

pkgver() {
  cd plymouth
  git describe --long | sed 's/-/.r/;s/-/./'
}


prepare() {
	cd plymouth
	patch -p1 -i $srcdir/plymouth-update-initrd.patch
	patch -p1 -i $srcdir/plymouth-quit.service.in.patch
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
		--with-logo=/usr/share/plymouth/arch-logo.png \
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

  install -Dm644 "$srcdir/arch-logo.png" "$pkgdir/usr/share/plymouth/arch-logo.png"
  install -Dm644 "$srcdir/watermark.png" "$pkgdirusr/share/plymouth/themes/spinner/watermark.png"

  install -Dm644 "$srcdir/plymouth.encrypt_hook" "$pkgdir/usr/lib/initcpio/hooks/plymouth-encrypt"
  install -Dm644 "$srcdir/plymouth.encrypt_install" "$pkgdir/usr/lib/initcpio/install/plymouth-encrypt"
  install -Dm644 "$srcdir/plymouth.initcpio_hook" "$pkgdir/usr/lib/initcpio/hooks/plymouth"
  install -Dm644 "$srcdir/plymouth.initcpio_install" "$pkgdir/usr/lib/initcpio/install/plymouth"
  install -Dm644 "$srcdir/sd-plymouth.initcpio_install" "$pkgdir/usr/lib/initcpio/install/sd-plymouth"

  for i in {gdm,sddm,lxdm,slim,lightdm}-plymouth.service; do
    install -Dm644 "$srcdir/$i" "$pkgdir/usr/lib/systemd/system/$i"
  done

  install -Dm644 "$srcdir/plymouth-deactivate.service" 	"$pkgdir/usr/lib/systemd/system/plymouth-deactivate.service"
  install -Dm644 "$srcdir/plymouth-start.service" "$pkgdir/usr/lib/systemd/system/plymouth-start.service"
  install -Dm644 "$srcdir/plymouth-start.path" 	"$pkgdir/usr/lib/systemd/system/plymouth-start.path"
  install -Dm644 "$pkgdir/usr/share/plymouth/plymouthd.defaults" "$pkgdir/etc/plymouth/plymouthd.conf"
}
