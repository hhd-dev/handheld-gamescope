_pkgname=gamescope
pkgname=handheld-${_pkgname}
pkgver=3.14.18
pkgrel=1
pkgdesc='Gamescope with reshade+legion_go+chimeraos patches. Based on the Bazzite version.'
arch=(x86_64)
url=https://github.com/ValveSoftware/gamescope
license=(BSD)
depends=(
  gcc-libs
  glibc
  glm
  libavif
  libcap.so
  libdecor
  libdisplay-info.so
  libdrm
  libliftoff.so
  libpipewire-0.3.so
  libvulkan.so
  libwlroots.so
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
  openvr
  sdl2
  vulkan-icd-loader
  wayland
  xorg-server-xwayland
)
makedepends=(
  benchmark
  git
  glslang
  meson
  ninja
  vulkan-headers
  wayland-protocols
)

source=(
    git+https://github.com/ValveSoftware/gamescope.git#tag=${pkgver}
    git+https://github.com/Joshua-Ashton/reshade.git
    git+https://github.com/KhronosGroup/SPIRV-Headers.git
    # Add regresh rates for the loki, legion go, and deckhd
    hardware.patch
    deckhd.patch
    # Add 720p patch for devices like the ally
    720p.patch
    # Add disable steam atom patch, which fixes a steam regression
    disable-steam-touch-click-atom.patch
    # Add --force-external-rotation used by devices with portrait screens + 
    external-rotation.patch
    panel-type.patch
    # Add touch gestures to open menus (currently broken)
    gestures.patch
)
b2sums=('bcdfcfdb7636d10c9d4450e03b252669a6ce9d5d21d241cde12bbf29feffaaf72bb785dce96682f43c25e0797cd6e5d13677e247d7c921e63ca1c27c8af04cb0'
        'SKIP'
        'SKIP'
        '671573b584409c122786f34521cab934df00381cdf708714fa4db29b386b11216ba41758ec58e27b0bc4a522f2e5ca5535f2c173b92bfe22baa8bfbcd2a490b4'
        '95acbcc912cef41f0f388e0e5072e7faa40a144b08122c95a1a3b9580c339ddb9663e7d4efed4b7e649080e95bdb7335d4fb4c947438f3cd71d0a33d870742ee'
        '25322b4ace0d2d14480bd43ea031cf6b48cda4f2ee72a8064216d88b867876ed94d2259904f0ef071155deceb7722f6746385657191b9fad4035172be4e82421'
        'd072133be1334344b32891b44fdb970b10566d521150607962a4f34057690b08fe2ed03d17a8a622a247f75719cdd186fc1e5be63730f6b7c60f3eb47a8ec008'
        '9f33d880f8ea85e7b7740c0651d261767fe00ebe9dde4cfec7932b4d02628a8d14dc6460428f8a04e51404bab406d49c178a82d6f0dc3dbf53d2b48eff028ae4'
        '68ebfbd6bf33fb12c55ca8cac51e8eaa4111dc97a1a23bbc5ec8edaf3b80be0f9b94d815f61b4d98c6ff57026d453795e3bbeb138cf1bd85027d0a2a7258f4e6'
        '71eca86fb6bdd36f8a142b68b76cb63f6301b8a111bfb2d9bf16c18afddf20d5f7fb2bfe0018d292908dae351365814ecda1ba0140a256bfcedffd252bc397c6')

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
  git submodule init src/reshade
  git config submodule.src/reshade.url ../reshade
  git submodule init thirdparty/SPIRV-Headers
  git config submodule.thirdparty/SPIRV-Headers.url ../SPIRV-Headers
  git -c protocol.file.allow=always submodule update
}

build() {
  arch-meson gamescope build \
    -Dforce_fallback_for=stb \
    -Dpipewire=enabled \
    -Dinput_emulation=enabled
  meson compile -C build
}

package() {
  DESTDIR="${pkgdir}" meson install -C build \
    --skip-subprojects
  install -Dm 644 gamescope/LICENSE -t "${pkgdir}"/usr/share/licenses/gamescope/
}

# vim: ts=2 sw=2 et:
