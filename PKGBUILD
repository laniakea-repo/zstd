# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Contributor: Bartłomiej Piotrowski <bpiotrowski@archlinux.org>
# Contributor: Andrzej Giniewicz <gginiu@gmail.com>
# Contributor: Johan Förberg <johan@forberg.se>

pkgname=zstd
pkgver=1.5.2
pkgrel=8
pkgdesc='Zstandard - Fast real-time compression algorithm'
url='https://facebook.github.io/zstd/'
arch=(x86_64)
license=(BSD GPL2)
depends=(glibc gcc-libs zlib xz lz4)
makedepends=(cmake gtest ninja)
provides=(libzstd.so)
source=(https://github.com/facebook/zstd/releases/download/v${pkgver}/zstd-${pkgver}.tar.zst{,.sig})
sha256sums=('3ea06164971edec7caa2045a1932d757c1815858e4c2b68c7ef812647535c23f'
            'SKIP')
b2sums=('513e4526a92bcb59416b3457d186a30e554f9e0cf21d7114eb3e9fbcbd9d662c8d95cf0b06237f6fe3f756862c63de0aa146d6a23cb4111c16e6459608d115f1'
        'SKIP')
validpgpkeys=(4EF4AC63455FC9F4545D9B7DEF8FE99528B52FFD)

prepare() {
  cd ${pkgname}-${pkgver}
  # avoid error on tests without static libs, we use LD_LIBRARY_PATH
  sed '/build static library to build tests/d' -i build/cmake/CMakeLists.txt
  sed 's/libzstd_static/libzstd_shared/g' -i build/cmake/tests/CMakeLists.txt
}

build() {
  cd ${pkgname}-${pkgver}
  export CFLAGS+=' -ffat-lto-objects'
  export CXXFLAGS+=' -ffat-lto-objects'

  cmake -S build/cmake -B build -G Ninja \
    -DCMAKE_BUILD_TYPE=None \
    -DCMAKE_INSTALL_PREFIX=/usr \
    -DZSTD_ZLIB_SUPPORT=ON \
    -DZSTD_LZMA_SUPPORT=ON \
    -DZSTD_LZ4_SUPPORT=ON \
    -DZSTD_BUILD_CONTRIB=ON \
    -DZSTD_BUILD_STATIC=OFF \
    -DZSTD_BUILD_TESTS=ON \
    -DZSTD_PROGRAMS_LINK_SHARED=ON
  cmake --build build
}

check() {
  cd ${pkgname}-${pkgver}
  export LD_LIBRARY_PATH="$(pwd)/build/lib"
  ctest -VV --test-dir build
}

package() {
  cd ${pkgname}-${pkgver}
  DESTDIR="${pkgdir}" cmake --install build
  ln -sf /usr/bin/zstd "${pkgdir}/usr/bin/zstdmt"
  install -Dm 644 LICENSE -t "${pkgdir}/usr/share/licenses/${pkgname}"
}

# vim: ts=2 sw=2 et:
