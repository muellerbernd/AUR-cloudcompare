#!/hint/bash
# Maintainer  : bartus <arch-user-repo(at)bartus.33mail.com>
# Contributor : Johannes Sauer <joh.sauer(at)gmail(dot)com>
# Contributor : Danilo Bargen <aur at dbrgn dot ch>
# Contributor : Olaf Leidinger <oleid@mescharet.de>
# shellcheck disable=SC2034,SC2154 # allow unused/uninitialized variables.

name=cloudcompare
_fragment="#tag=v2.11.3"
pkgname=${name}
pkgver="${_fragment###tag=v}"
pkgrel=6
pkgdesc="A 3D point cloud (and triangular mesh) processing software"
arch=('i686' 'x86_64')
url="http://www.danielgm.net/cc/"
license=('GPL2')
depends=('cgal' 'dlib' 'fbx-sdk' 'ffmpeg4.4' 'glew' 'glu' 'mesa' 'mpir' 'pdal' 'qt5-base' 'qt5-tools' 'qt5-svg' 'shapelib' 'tbb' 'gdal')
makedepends=('clang' 'cmake' 'doxygen' 'git' 'laz-perf' 'libharu' 'ninja' 'pcl' 'proj' 'python')
optdepends=('pcl')
source=("${name}::git+https://github.com/CloudCompare/CloudCompare.git${_fragment}"
        "${name}-cork::git+https://github.com/CloudCompare/cork.git"
        constexpr.patch
        CloudCompare.desktop
        ccViewer.desktop
        pdal_230.patch
        tbb.patch
        )
sha256sums=('SKIP'
            'SKIP'
            '984e6186f6483534a52cb153b65dee016904eb9efdb89211c2c0042eea2417ff'
            '14096df9cf7aca3099d5df1585d1cf669544e9b10754dce3d2507100dd7034fe'
            '821ac2540e1196774e26f8033946ce7b36223dae7a2a7c78f4a901b4177f68cc'
            '70e5c6e932c1cf61dc9add064c2e165737db26a5fdda696c2e9cf92cbfd257c6'
            'SKIP')

prepare() {
  git -C "${srcdir}/${name}" submodule update --init --recursive
  git -C "${srcdir}/${name}" apply -v "${srcdir}"/{constexpr,pdal_230}.patch
  git -C "${srcdir}/${name}" apply -v "${srcdir}"/tbb.patch
  #fix gcc:11 porting
  sed '1 i\#include <limits>' -i "${srcdir}/${name}"/plugins/core/IO/qE57IO/extern/libE57Format/src/E57XmlParser.cpp
  #fix pcl
  git -C "${srcdir}/${name}" cherry-pick -n 535c5017604b69b48f7a03de47d9a0c35cbdbb65 5bc453a08a4760cf63788461e5160b2644d4588c 1b5f2a710e10138aa39f5779ce66f4b0321f5d4b  6a398d53cd1c3f960aa0f9ffa748374aa518d89b
}

build() {
  export CCACHE_BASEDIR="$srcdir"
# shellcheck disable=SC2191
  CMAKE_FLAGS=(
        -Wno-dev
        -DCMAKE_CXX_STANDARD=14
        -DCMAKE_CXX_FLAGS="$CXXFLAGS -fpermissive -DSUPPORT_TOPO_STREAM_OPERATORS -Wno-deprecated-declarations"
        -DCMAKE_INSTALL_PREFIX=/usr
        -DCMAKE_INSTALL_LIBDIR=lib
        -DCMAKE_BUILD_TYPE=Release
        -DCOMPILE_CC_CORE_LIB_WITH_CGAL=ON
        -DCOMPILE_CC_CORE_LIB_WITH_TBB=ON
        -DWITH_FFMPEG_SUPPORT:BOOL=ON
        -DFFMPEG_INCLUDE_DIR:PATH=/usr/include
        -DFFMPEG_LIBRARY_DIR:PATH=/usr/lib
        -DPOISSON_RECON_WITH_OPEN_MP:BOOL=ON
        -DPLUGIN_EXAMPLE_GL:BOOL=ON
        -DPLUGIN_EXAMPLE_IO:BOOL=ON
        -DPLUGIN_EXAMPLE_STANDARD:BOOL=ON
        -DPLUGIN_GL_QEDL:BOOL=ON
        -DPLUGIN_GL_QSSAO:BOOL=ON
        -DPLUGIN_IO_QADDITIONAL:BOOL=ON
        -DPLUGIN_IO_QCORE:BOOL=ON
        -DPLUGIN_IO_QCSV_MATRIX:BOOL=ON
        -DPLUGIN_IO_QE57:BOOL=ON
        -DPLUGIN_IO_QFBX:BOOL=ON # requires update of AUR/fbx-sdk (https://www.autodesk.com/content/dam/autodesk/www/adn/fbx/2020-1-1/fbx202011_fbxsdk_linux.tar.gz)
        -DFBX_SDK_INCLUDE_DIR:PATH=/usr/include
        -DFBX_SDK_LIBRARY_FILE:FILEPATH=/usr/lib/libfbxsdk.so
        -DPLUGIN_IO_QPDAL:BOOL=ON
        -DPLUGIN_IO_QPHOTOSCAN:BOOL=ON
        -DPLUGIN_IO_QRDB:BOOL=OFF # requires rdblib (package for AUR from http://www.riegl.com/products/software-packages/rdblib/)
        -DPLUGIN_STANDARD_QANIMATION:BOOL=OFF
        -DPLUGIN_STANDARD_QBROOM:BOOL=ON
        -DPLUGIN_STANDARD_QCANUPO:BOOL=ON # requires dlib
        -DDLIB_ROOT:PATH="/usr" # required by qcanupo plugin
        -DPLUGIN_STANDARD_QCOMPASS:BOOL=ON
        -DPLUGIN_STANDARD_QCORK:BOOL=ON # require mpir, cork (cork-git is not enough)
        -DMPIR_INCLUDE_DIR:PATH=/usr/include # required by qcork plugin
        -DCORK_INCLUDE_DIR:PATH="${srcdir}/${name}-cork/src" # required by qcork plugin
        -DCORK_RELEASE_LIBRARY_FILE:FILEPATH="${srcdir}/${name}-cork/lib/libcork.a" # required by qcork plugin
        -DMPIR_RELEASE_LIBRARY_FILE:FILEPATH=/usr/lib/libmpir.so # require by qcork plugin
        -DPLUGIN_STANDARD_QCSF:BOOL=ON
        -DPLUGIN_STANDARD_QFACETS:BOOL=ON # requires shapelib
        -DOPTION_USE_SHAPE_LIB:BOOL=ON
        -DOPTION_USE_GDAL=ON
        -DPLUGIN_STANDARD_QHOUGH_NORMALS:BOOL=ON
        -DPLUGIN_STANDARD_QHPR:BOOL=ON
        -DPLUGIN_STANDARD_QM3C2:BOOL=ON
        -DPLUGIN_STANDARD_QPCL:BOOL=ON
        -DPLUGIN_STANDARD_QPCV:BOOL=ON
        -DPLUGIN_STANDARD_QPOISSON_RECON:BOOL=ON
        -DPLUGIN_STANDARD_QRANSAC_SD:BOOL=ON
        -DPLUGIN_STANDARD_QSRA:BOOL=ON
        -DOPTION_USE_DXF_LIB:BOOL=ON # required by qsra plugin
        -DEIGEN_ROOT_DIR=/usr/include/eigen3
  )
  msg2 "Build Cork lib"
  make -C "${srcdir}/${name}-cork" CXXFLAGS="$CXXFLAGS -DSUPPORT_TOPO_STREAM_OPERATORS"
  msg2 "Build CloudCompare"
  cmake -B build -S "${srcdir}/${name}" -G Ninja "${CMAKE_FLAGS[@]}"
# shellcheck disable=SC2086 # allow slitting for MAKEFLAGS carrying multiple flags.
  ninja -C build ${MAKEFLAGS:--j1}
}

package() {
  DESTDIR="$pkgdir" ninja -C build install

  # install *.desktop files
  install -D -m 644 "${srcdir}"/*.desktop -t "${pkgdir}"/usr/share/applications/

  # copy icons for *.desktop files
  for size in 16 32 64 256; do
    install -D -m 644 ${name}/qCC/images/icon/cc_icon_${size}.png "${pkgdir}"/usr/share/icons/hicolor/${size}x${size}/apps/cc_icon.png
    install -D -m 644 ${name}/qCC/images/icon/cc_viewer_icon_${size}.png "${pkgdir}"/usr/share/icons/hicolor/${size}x${size}/apps/cc_viewer_icon.png
  done
  install -D -m 644 ${name}/qCC/images/icon/cc_icon.svg "${pkgdir}"/usr/share/icons/hicolor/scalable/apps/cc_icon.svg
  install -D -m 644 ${name}/qCC/images/icon/cc_viewer_icon.svg "${pkgdir}"/usr/share/icons/hicolor/scalable/apps/cc_viewer_icon.svg
}
# vim:set sw=2 ts=2 et:
