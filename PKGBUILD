# This PKGBUILD is part of the VDR4Arch project [https://github.com/vdr4arch]
pkgbase=vdr-softhdcuvid
pkgname=(vdr-softhddrm)
pkgver=3.12
pkgrel=2
_vdrapi=2.6.5
pkgdesc="VDR output plugin with CUDA and Opengl"
url="https://github.com/jojo61/vdr-plugin-softhdcuvid"
arch=('x86_64' 'aarch64')
license=('AGPL3')
makedepends=('ffmpeg' 'freeglut' 'glew' 'mesa' "vdr-api=${_vdrapi}" 'xcb-util-wm' 'xorg-server' 'libplacebo>=3.120.0' 'glm' 'glu' 'vulkan-headers' 'ffnvcodec-headers' 'freetype2')
_plugname=${pkgbase//vdr-/}
source=("${pkgbase}-${pkgver}.tar.gz::${url}/archive/refs/tags/V${pkgver}.tar.gz"
        "50-$_plugname.conf")
sha512sums=('be70e6fc66bffb34e7fff067661341a786d3bfbbd0ca118fc32b4b88c7eabe26b31f3663cbf74d08f037b5cc840e49fb2aa67be7b403467026c05d5edde90935'
            'a6d038f645de6936487b18452bdda3ca17545d6168289fdd144f40e4eada37949c27a28a810108ad5e6e5cba63ee1b7c9f3891fd8ae53a5d7d0e4b3675327c58')

if [ "$CARCH" == "x86_64" ] ; then
  pkgname+=(vdr-softhdcuvid vdr-softhdvaapi)
  makedepends+=( 'nvidia>=410.48' )
fi

prepare() {
  cd "${srcdir}/vdr-plugin-${_plugname}-${pkgver}"

  # Disable OSS. Arch Linux doesn't ship OSS
  sed -i '/OSS /d' Makefile

  # Don't clean built plugin binary
  sed -i 's/\*\.so//' Makefile
}

build() {
  cd "${srcdir}/vdr-plugin-${_plugname}-${pkgver}"
  if [ "$CARCH" == "x86_64" ] ; then
    make CUVID=1 libvdr-softhdcuvid.so
    make clean
    make VAAPI=1 libvdr-softhdvaapi.so
    make clean
  fi
  make DRM=1 LIBPLACEBO=0 libvdr-softhddrm.so
  make clean
}

package_vdr-softhdcuvid() {
  depends=('freeglut' 'glew' 'mesa' "vdr-api=${_vdrapi}" 'xcb-util-wm' 'xorg-server' 'nvidia>=410.48' 'glu'
           'libavcodec.so' 'libavutil.so' 'libplacebo.so' 'libswresample.so')
  optdepends=('vdr-xorg: Recommended way to start X.org server together with VDR')
  backup=("etc/vdr/conf.avail/50-$_plugname.conf")

  cd "${srcdir}/vdr-plugin-${_plugname}-${pkgver}"
  install -Dm755 "lib${pkgname}.so" "${pkgdir}$(pkg-config --variable=libdir vdr)/lib${pkgname}.so.$(pkg-config --variable=apiversion vdr)"
  make CUVID=1 DESTDIR="${pkgdir}" install-i18n

  install -Dm644 "$srcdir/50-$_plugname.conf" "$pkgdir/etc/vdr/conf.avail/50-softhdcuvid.conf"
}

package_vdr-softhdvaapi() {
  depends=('freeglut' 'glew' 'mesa' "vdr-api=${_vdrapi}" 'xcb-util-wm' 'xorg-server' 'glu'
           'libavcodec.so' 'libavfilter.so' 'libavutil.so' 'libplacebo.so' 'libswresample.so')
  optdepends=('vdr-xorg: Recommended way to start X.org server together with VDR')
  backup=("etc/vdr/conf.avail/50-softhdvaapi.conf")

  cd "${srcdir}/vdr-plugin-${_plugname}-${pkgver}"
  install -Dm755 "lib${pkgname}.so" "${pkgdir}$(pkg-config --variable=libdir vdr)/lib${pkgname}.so.$(pkg-config --variable=apiversion vdr)"
  make VAAPI=1 DESTDIR="${pkgdir}" install-i18n

  install -Dm644 "$srcdir/50-$_plugname.conf" "$pkgdir/etc/vdr/conf.avail/50-softhdvaapi.conf"
  sed -i 's/softhdcuvid/softhdvaapi/' "$pkgdir/etc/vdr/conf.avail/50-softhdvaapi.conf"
}

package_vdr-softhddrm() {
  depends=('freeglut' 'glew' 'mesa' "vdr-api=${_vdrapi}" 'xcb-util-wm' 'glu' 'freetype2'
           'libavcodec.so' 'libavfilter.so' 'libavutil.so' 'libswresample.so')
  conflicts=('vdr-xorg')
  backup=("etc/vdr/conf.avail/50-softhddrm.conf")

  cd "${srcdir}/vdr-plugin-${_plugname}-${pkgver}"
  install -Dm755 "lib${pkgname}.so" "${pkgdir}$(pkg-config --variable=libdir vdr)/lib${pkgname}.so.$(pkg-config --variable=apiversion vdr)"
  make DRM=1 LIBPLACEBO=0 DESTDIR="${pkgdir}" install-i18n

  install -Dm644 "$srcdir/50-$_plugname.conf" "$pkgdir/etc/vdr/conf.avail/50-softhddrm.conf"
  sed -i 's/softhdcuvid/softhddrm/' "$pkgdir/etc/vdr/conf.avail/50-softhddrm.conf"
}
