_pkgname=gamescope
pkgname=handheld-${_pkgname}
pkgver=3.14.24
pkgrel=2
pkgdesc='The Bazzite gamescope forked for Arch. May have extra fixes or a tiny bit held back.'
arch=(x86_64)
url=https://github.com/ValveSoftware/gamescope
license=(BSD-2-Clause BSD-3-Clause)
depends=(
  gcc-libs
  glibc
  libavif
  libcap.so
  libdecor
  libdrm
  libinput
  libpipewire-0.3.so
  libx11
  libxcb
  libxcomposite
  libxdamage
  libxext
  libxfixes
  libxkbcommon.so
  libxmu
  libxrender
  libxres
  libxtst
  libxxf86vm
  sdl2
  seatd
  vulkan-icd-loader
  wayland
  xcb-util-errors
  xcb-util-wm
  xorg-server-xwayland
)
makedepends=(
  benchmark
  cmake # for openvr
  git
  glslang
  meson
  ninja
  vulkan-headers
  wayland-protocols
)
_tag=${pkgver}
source=(
  git+https://github.com/ValveSoftware/gamescope.git#tag=${_tag}
  git+https://github.com/Joshua-Ashton/wlroots.git
  git+https://gitlab.freedesktop.org/emersion/libliftoff.git
  git+https://github.com/Joshua-Ashton/vkroots.git
  git+https://gitlab.freedesktop.org/emersion/libdisplay-info.git
  # git+https://github.com/ValveSoftware/openvr.git
  git+https://github.com/Joshua-Ashton/reshade.git
  git+https://github.com/KhronosGroup/SPIRV-Headers.git
  # Add regresh rates for the loki, legion go, and deckhd
  chimeraos.patch
  # hhd-dev
  disable-steam-touch-click-atom.patch
  v3-0001-always-send-ctrl-1-2-to-steam-s-wayland-session.patch
  # https://github.com/ValveSoftware/gamescope/pull/1281
  deckhd.patch
  # https://github.com/ValveSoftware/gamescope/issues/1398
  drm-Separate-BOE-and-SDC-OLED-Deck-panel-rates.patch
  # https://github.com/ValveSoftware/gamescope/issues/1369
  revert-299bc34.patch
  # https://github.com/ValveSoftware/gamescope/pull/1231
  1231.patch
  # Temporary until newer tag than 3.14.24
  upstream.patch
)

b2sums=('272b4df0782c6dfe3f428e396f4e2ab93594e6b086bba25d0116995883e84944788760a24feb0d8a61d615c4d4ecee9d7648a5cdcee128620aaa9d24d2606284'
        'SKIP'
        'SKIP'
        'SKIP'
        'SKIP'
        'SKIP'
        'SKIP'
        '9abb6ce2d34687506ff6536d6e40fbe23ca358317863ff3d086864a4be0396a27b5cc00661953e653d2e354a58632ddd59a7bf41cf20124b35fee05aa1b97807'
        '058e134c17f6701e3c8f3df76c33062b8a53f38ed1f1b7de3c93158be83bf2f7f6bc0a1bea1dd66e45369fd22284832e6e9b8d92c70d95694c881ffec999fd50'
        'e07e9c6675fe732ea517a87724441f2d71a698aadd99a356043f51391841c0b05730f578f951efc3452c7de7eca61153855f3628515283b42b26e65e9ce7f3b8'
        '3eae10a5244500ac00e9cd2f79a48923c024d79210dfe73295da69b4e300507cf80511852350d1c9177f79774036f2924fd8330a4ff6cc43046b794f059a0f86'
        '548bdbc47c3d802127318cfcb83347c30907cc849a32896d6dc6e49d4641bc619e741f81869a44d3c0e063beba6c21a9be5a56e9679220414c7bc16b2698794d'
        '4fcd400f117051398c5d4130a9fb5c1668e17c2ccf050a69b8eb931f1b827d74cc24cc90bf150b5c7712f8d9d7bcfe082aa09719a27b36454c9927320593b922'
        '27c3ced2ae92055bcd43744708be3810f8560d0bf509e9c003cffbe87dfe31439b5452334d0c05e27c1a0f5b2b89eb94f6419546ed8a84cf8f5e937aceddf75d'
        'e913fc720c3d8d9ee6d6496a80239c6a41af8da32693b6efc4c2817a238d86a6257a82524bdd8dca47948ff6568f7c67ce3f8bcc484dbbc00d791feaa9882325')

provides=("$_pkgname")
conflicts=("$_pkgname")

prepare() {
  cd $_pkgname

  local src
  for src in "${source[@]}"; do
    src="${src%%::*}"
    src="${src##*/}"
    src="${src%.zst}"
    [[ $src = *.patch ]] || continue
    echo "----- Applying patch ./$src"
    # Allow going to reject path through vs code terminal
    patch -Np1 < "../$src" | sed -e "s/saving rejects to file /saving rejects to file ${PWD//\//\\/}\//g"
    if [ ${PIPESTATUS[0]} -ne 0 ]; then
      exit ${PIPESTATUS[0]}
    fi
  done
    
  meson subprojects download

  git submodule init subprojects/wlroots
  git config submodule.subprojects/wlroots.url ../wlroots

  git submodule init subprojects/libliftoff
  git config submodule.subprojects/libliftoff.url ../libliftoff

  git submodule init subprojects/vkroots
  git config submodule.subprojects/vkroots.url ../vkroots

  git submodule init subprojects/libdisplay-info
  git config submodule.subprojects/libdisplay-info.url ../libdisplay-info

  # git submodule init subprojects/openvr
  # git config submodule.subprojects/openvr.url ../openvr

  git submodule init src/reshade
  git config submodule.src/reshade.url ../reshade

  git submodule init thirdparty/SPIRV-Headers
  git config submodule.thirdparty/SPIRV-Headers.url ../SPIRV-Headers

  git -c protocol.file.allow=always submodule update
}

# pkgver() {
#   cd gamescope
#   git describe --tags | sed 's/-//'
# }

build() {
  arch-meson gamescope build \
    -Dforce_fallback_for=wlroots,libliftoff,vkroots,glm,stb,libdisplay-info,SPVR-Headers,reshade \
    -Denable_openvr_support=false \
    -Dlibliftoff:werror=false \
    -Dwlroots:werror=false \
    -Dpipewire=enabled
  meson compile -C build
}

package() {
  DESTDIR="${pkgdir}" meson install -C build \
    --skip-subprojects
}

# vim: ts=2 sw=2 et:
