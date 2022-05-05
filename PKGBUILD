# Contributor: Michael Serpieri <mickybart@pygoscelis.org>
pkgname=libhybris-glvnd
provides=('libhybris')
_pkgbase=libhybris
pkgver=1569.03c5ec6
pkgrel=1
_commit=03c5ec656cd164963de3ac0570c7f59b659f861f
arch=('armv7h' 'aarch64' 'x86_64')
url="https://github.com/libhybris/libhybris"
license=('Apache')
depends=('wayland' 'libglvnd')
makedepends=('git' 'mesa' 'android-headers-30' 'vulkan-headers')
conflicts=('ocl-icd' 'libhybris-28-glvnd' 'libhybris-29-glvnd')
provides=('libhybris')
source=("libhybris::git+https://github.com/manjaro-libhybris/libhybris#commit=${_commit}")
md5sums=('SKIP')
options=(debug !strip)

pkgver() {
  cd "${srcdir}/${_pkgbase}"
  echo $(git rev-list --count HEAD).$(git rev-parse --short HEAD)
}

build() {
  cd "${srcdir}/${_pkgbase}/hybris"

  if [ "$CARCH" = "x86_64" ] ; then
    HYBRIS_ARCH="x86-64"
  elif [ "$CARCH" = "armv7h" ] ; then
    HYBRIS_ARCH="arm"
  elif [ "$CARCH" = "aarch64" ]; then
    HYBRIS_ARCH="arm64"
  else
    echo "Unsupported arch: $CARCH"
    exit 1
  fi

  ./autogen.sh \
    --prefix=/usr \
    --enable-wayland \
    --enable-trace \
    --enable-debug \
    --enable-experimental \
    --with-android-headers=/usr/include/android \
    --enable-arch=$HYBRIS_ARCH \
    --enable-property-cache \
    --enable-glvnd
  make
}

package() {
  cd "${srcdir}/${_pkgbase}/hybris"

  # lib dependency issue: workaround with -j1
  make -j1 DESTDIR="${pkgdir}" install

  rm -f ${pkgdir}/usr/lib/pkgconfig/{egl,glesv1_cm,glesv2,wayland-egl}.pc

  cd "${pkgdir}/usr/include"
  # Move libhybris-provided headers into hybris dir
  mv CL EGL GLES GLES2 GLES3 KHR hybris

  cd "${pkgdir}/usr/lib"
  # Avoid conflict with vulkan-icd-loader
  mv libvulkan.so* libhybris/

  # Symlink eglhybris.h
  mkdir -p EGL
  cd EGL
  ln -s ../hybris/EGL/eglhybris.h .
}
