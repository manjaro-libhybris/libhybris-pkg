# Contributor: Michael Serpieri <mickybart@pygoscelis.org>

pkgname=libhybris
provides=('libhybris')
_pkgbase=libhybris
pkgver=1901.3f9afd7
pkgrel=1
_commit=3f9afd7acef22a1aefb807c4cf35d2bf73e905c4
arch=('armv7h' 'aarch64' 'x86_64')
url="https://github.com/libhybris/libhybris"
license=('Apache')
depends=('wayland' 'libglvnd')
makedepends=('wayland' 'android-headers' 'quilt' 'python3' 'egl-wayland' 'mesa'
             'patchelf' 'binutils' 'libglvnd' 'pkgconf' 'libxext' 'libxcb' 'git')
source=("libhybris::git+https://github.com/droidian/libhybris#commit=${_commit}")
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
  # Avoid conflict with ocl-icd
  mv libOpenCL.so* libhybris/
  rm pkgconfig/OpenCL.pc

  # Avoid conflict with libcamera
  mv libcamera.so* libhybris/
  rm -f ${pkgdir}/usr/lib/pkgconfig/libcamera.pc

  # Symlink eglhybris.h
  mkdir -p EGL
  cd EGL
  ln -s ../hybris/EGL/eglhybris.h .
}
