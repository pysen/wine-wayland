# Created by: varmd

RELEASE=5.22
pkgname=('wine-wayland')



pkgver=$RELEASE
pkgrel=1
_winesrcdir="wine-wine-$pkgver"

pkgdesc='Wine wayland'

url=''
arch=('x86_64')

options=('!staticlibs' '!strip' '!docs')
license=('LGPL')

export LANG=en_US.utf8
LANG=en_US.utf8
export PKGEXT='.pkg.tar.zst'

depends=(
  'adwaita-icon-theme'
  'fontconfig'
  'libxml2'
  'freetype2'
  'gcc-libs'
  'desktop-file-utils'
  'libpng'
  'mpg123'
  'openal'
  'alsa-lib'
  'mesa'
  'vulkan-icd-loader'
  'faudio'
  'sdl2'
  'wayland'
  'wayland-protocols'  
)

makedepends=(
    'autoconf'
    'ncurses'
    'bison'
    'perl'
    'flex'
    'gcc'
    'vulkan-headers'
    'gettext'
    'zstd'
)


source=(
  "https://github.com/wine-mirror/wine/archive/wine-$pkgver.zip"
  "https://github.com/civetweb/civetweb/archive/v1.12.zip"
)

sha256sums=('SKIP' 'SKIP')


STRBUILD32="builder"


if [ "$USER" = "$STRBUILD32" ]; then
  WINE_BUILD_32=1
fi


if [ -z "${WINE_BUILD_32:-}" ]; then
  #msg2 "Not building wine 32"
  D=1
else
  source ./PKGBUILD-32
  #msg2 "Also building wine 32"
fi



OPTIONS=(!strip !docs !libtool !zipman !purge !debug)
makedepends=(${makedepends[@]} ${depends[@]})



prepare() {

  if [ -e "${srcdir}"/"${_winesrcdir}"/server/esync.c ]; then
    msg2 "Stale src/ folder. Delete src/ folder or run makepkg --noextract."
    exit;
  else
    cd ..
    rm -f "${srcdir}"/"${_winesrcdir}"/dlls/winewayland*

    ln -s $PWD/winewayland* "${srcdir}"/"${_winesrcdir}"/dlls/

    cd "${srcdir}"/"${_winesrcdir}"

    patch -Np1 < '../../enable-wayland.patch'


    patch dlls/user32/driver.c < ../../winewayland.drv/patch/user32-driverc.patch
    patch dlls/user32/sysparams.c < ../../winewayland.drv/patch/0002-user32-sysparamsc-new2.patch
    patch dlls/user32/sysparams.c < ../../winewayland.drv/patch/0004-user32-sysparams-fix-valid-adapter.patch
    patch programs/explorer/desktop.c < ../../winewayland.drv/patch/explorer-desktopc.patch


    cd "${srcdir}"/"${_winesrcdir}"


    cp ../../esync2/esync-copy/ntdll/* dlls/ntdll/unix/
    cp ../../esync2/esync-copy/server/* server/

    cp ../../esync2/fsync-copy/ntdll/* dlls/ntdll/unix/
    cp ../../esync2/fsync-copy/server/* server/



    for _f in ../../esync2/ok/server/*.patch; do
      msg2 "Applying ${_f}"
      patch -Np1 < ${_f}
    done

    for _f in ../../esync2/ok/*.patch; do
      msg2 "Applying ${_f}"
      patch -Np1 < ${_f}
    done


    for _f in ../../esync2/fsync/*.patch; do
      msg2 "Applying ${_f}"
      patch -Np1 < ${_f}
    done

    rm -rf programs/explorer
    rm -rf programs/iexplore

    # speed up
    sed -i '/programs\/explorer/d' configure.ac
    sed -i '/programs\/iexplore/d' configure.ac
    #sed -i '/programs\/dxdiag/d' configure.ac
    sed -i '/programs\/hh/d' configure.ac
    sed -i '/programs\/powershell/d' configure.ac
    sed -i '/programs\/winemenubuilder/d' configure.ac
    sed -i '/programs\/wordpad/d' configure.ac
    sed -i '/programs\/conhost/d' configure.ac
    sed -i '/programs\/winedbg/d' configure.ac
    
    sed -i '/\/tests/d' configure.ac
    #sed -i '/dlls\/d3d8/d' configure.ac
    sed -i '/dlls\/d3d12/d' configure.ac
    sed -i '/dlls\/jscript/d' configure.ac


    rm configure
    autoconf

    mkdir -p "${srcdir}"/"${pkgname}"-64-build

  fi

}




build() {

  #build civetweb for wineland
  cd civetweb-1.12
  make build WITH_IPV6=0 USE_LUA=0 PREFIX="$pkgdir/usr"


	cd "${srcdir}"


  export CC=cc

  msg2 'Building Wine-64...'
	cd  "${srcdir}"/"${pkgname}"-64-build


  if [ -e Makefile ]; then
    echo "Already configured"
  else
  ../${_winesrcdir}/configure \
		--prefix='/usr' \
		--libdir='/usr/lib' \
		--without-x \
		--without-capi \
		--without-dbus \
		--without-gphoto \
		--without-gssapi \
		--without-netapi \
		--without-hal \
    --without-gsm \
    --without-opencl \
    --without-opengl \
    --without-cups \
    --without-cms \
    --without-vkd3d \
    --without-xinerama \
    --without-xrandr \
    --without-sane \
    --without-osmesa \
    --without-gettext \
    --without-fontconfig \
    --without-cups \
    --disable-win16 \
    --without-gphoto \
    --without-glu \
    --without-xcomposite \
    --without-xcursor \
    --without-xfixes \
    --without-xshape \
    --without-xrender \
    --without-xinput \
    --without-xinput2 \
    --without-xrender \
    --without-xxf86vm \
    --without-xshm \
    --without-v4l2 \
    --without-usb \
    --with-vulkan \
    --with-faudio \
    --disable-win16 \
		--enable-win64 \
		--disable-tests
  fi

  CPUS=$(getconf _NPROCESSORS_ONLN)

	make -s -j $CPUS

}

package_wine-wayland() {
  depends=(
    'adwaita-icon-theme'
    'fontconfig'
    'libxml2'
    'freetype2'
    'gcc-libs'
    'desktop-file-utils'
    'libpng'
    'mpg123'
    'openal'
    'alsa-lib'
    'mesa'
    'vulkan-icd-loader'
    'faudio'
    'sdl2'
  )

  conflicts=('wine' 'wine-staging' 'wine-esync')
  provides=('wine')


	cd "${srcdir}/${pkgname}"-64-build
	make -s	prefix="${pkgdir}/usr" \
			libdir="${pkgdir}/usr/lib" \
			dlldir="${pkgdir}/usr/lib/wine" install


  mkdir -p ${pkgdir}/usr/lib/wineland
  cp ${srcdir}/civetweb*/civetweb ${pkgdir}/usr/lib/wineland/wineland-civetweb
  cd ${srcdir}
  cp -r ../wineland ${pkgdir}/usr/lib/wineland/ui
  cp -r ../wineland/joystick.svg ${pkgdir}/usr/lib/wineland/ui/joystick.svg
  cp -r ../wineland/wineland ${pkgdir}/usr/bin/wineland
  chmod +x ${pkgdir}/usr/bin/wineland

  mkdir -p ${pkgdir}/usr/share/applications
  cp -r ../wineland/wineland.desktop ${pkgdir}/usr/share/applications/wineland.desktop
}
