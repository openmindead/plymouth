# Maintainer: Mark Wagie <mark at manjaro dot org>

# Arch credits:
# Maintainer: Taijian <taijian@posteo.de>
# Contributor: Sebastian Lau <lauseb644@gmail.com>
# Contributor Damian01w <damian01w@gmail.com>
# Contributor: Padfoot <padfoot@exemail.com.au>

pkgname=plymouth
pkgver=22.02.122
pkgrel=9
pkgdesc="A graphical boot splash screen with kernel mode-setting support"
arch=('x86_64')
url="https://www.freedesktop.org/wiki/Software/Plymouth/"
license=('GPL')
depends=('libdrm' 'pango' 'systemd')
makedepends=('docbook-xsl' 'git')
optdepends=('cantarell-fonts: For true type font support'
            'texlive-fontsextra: For true type font support'
            'xf86-video-fbdev: Support special graphic cards on early startup')
backup=("etc/$pkgname/${pkgname}d.conf")
_commit=27764b2a2c2e21ad988cae01dc59d4bb78e5c1dc
source=("git+https://gitlab.freedesktop.org/plymouth/plymouth.git#commit=${_commit}"
        'manjaro-logo.png'
        "$pkgname.encrypt_hook"
        "$pkgname.encrypt_install"
        "lxdm-$pkgname.service"
        "lightdm-$pkgname.service"
        "sddm-$pkgname.service"
        "$pkgname-deactivate.service" # needed for sddm
        "$pkgname.initcpio_hook"
        "$pkgname.initcpio_install"
        "sd-$pkgname.initcpio_install"
        "$pkgname-quit.service.in.patch"
        "$pkgname-update-initrd.patch"
        "${pkgname}d.conf.patch"
        'ply-utils.c.patch'
        'runstatedir.patch'
)
sha256sums=('SKIP'
            '014e8a09f88a73b1e5985dcb16a44004e341f5bba90043fa3d7fd7e3a56120cf'
            '748e0cfa0e10ab781bc202fceeed46e765ed788784f1b85945187b0f29eafad7'
            '373ec20fe4c47e693a0c45cc06dd906e35dd1d70a85546bd1d571391de11763a'
            '06b31999cf60f49e536c7a12bc1c4f75f2671feb848bf5ccb91a963147e2680d'
            '86d0230d9393c9d83eb7bb430e6b0fb5e3f32e78fcd30f3ecd4e6f3c30b18f71'
            'c39f526f7e99173bc8f012900f53257537a25e2d8c19e23df630f1fe9a7627ba'
            '3b17ed58b59a4b60d904c60bba52bae7ad685aa8273f6ceaae08a15870c0a9eb'
            '2a80e2cad8de428358647677afa166219589d3338c5f94838146c804a29e2769'
            '70c638788d216f7f8cec037c00760551c243940fb3abc6bec67600f978f0125b'
            '05acfbf7f7ba2b8094d3e6dd8f0acc1f8d49f32a7af186f7db9e90d98125840f'
            'dec28b86ddea93704f8479d33e08f81cd7ff4ccaad57e9053c23bd046db2278a'
            '74908ba59cea53c6a9ab67bb6dec1de1616f3851a0fd89bb3c157a1c54e6633a'
            '71d34351b4313da01e1ceeb082d9776599974ce143c87e93f0a465f342a74fd2'
            '1bd7693d1e135fe9e22a03f7635309e2ae616e952665d9774eb5ca4d82718e1b'
            '7c0224737119f949b8d5ca24c438f253b5734e3391a47e8f5f1dda28b8c4ab92')

pkgver() {
  cd "$srcdir/$pkgname"
  git describe --tags | sed 's/-/+/g'
}

prepare() {
  cd "$srcdir/$pkgname"
  patch -p1 -i "$srcdir/$pkgname-update-initrd.patch"
  patch -p1 -i "$srcdir/$pkgname-quit.service.in.patch"
  patch -p1 -i "$srcdir/${pkgname}d.conf.patch"

  # apply upstream patches
  patch -p1 -i $srcdir/ply-utils.c.patch
  patch -p1 -i $srcdir/runstatedir.patch
}

build() {
  cd "$srcdir/$pkgname"

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
    --with-release-file=/etc/os-release \
    --with-logo=/usr/share/plymouth/manjaro-logo.png \
    --with-background-color=0x000000 \
    --with-background-start-color-stop=0x000000 \
    --with-background-end-color-stop=0x4D4D4D \
    --without-rhgb-compat-link \
    --without-system-root-install \
    --runstatedir=/run

  make
}

package() {
  cd "$srcdir/$pkgname"
  make DESTDIR="$pkgdir" install

  install -Dm644 "$srcdir/manjaro-logo.png" "$pkgdir/usr/share/$pkgname/manjaro-logo.png"
  install -Dm644 "$srcdir/$pkgname.encrypt_hook" "$pkgdir/usr/lib/initcpio/hooks/$pkgname-encrypt"
  install -Dm644 "$srcdir/$pkgname.encrypt_install" "$pkgdir/usr/lib/initcpio/install/$pkgname-encrypt"
  install -Dm644 "$srcdir/$pkgname.initcpio_hook" "$pkgdir/usr/lib/initcpio/hooks/$pkgname"
  install -Dm644 "$srcdir/$pkgname.initcpio_install" "$pkgdir/usr/lib/initcpio/install/$pkgname"
  install -Dm644 "$srcdir/sd-$pkgname.initcpio_install" "$pkgdir/usr/lib/initcpio/install/sd-$pkgname"

  for i in {sddm,lxdm,lightdm}-plymouth.service; do
    install -Dm644 "$srcdir/$i" "$pkgdir/usr/lib/systemd/system/$i"
  done

  install -Dm644 "$srcdir/$pkgname-deactivate.service" "$pkgdir/usr/lib/systemd/system/$pkgname-deactivate.service"
  install -Dm644 "$pkgdir/usr/share/$pkgname/${pkgname}d.defaults" "$pkgdir/etc/$pkgname/${pkgname}d.conf"
}
