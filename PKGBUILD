_pkgname=gamescope
pkgname=handheld-${_pkgname}
pkgver=3.14.12
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
    libei
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
b2sums=('fec50850315c404e1805285038dd2715ac96de1132a412e03b2992c6fc11e8eacf1035d8012356c32a37fcaab02234ecf79e31169e48eb8599586a5c27207c84'
        'SKIP'
        'SKIP'
        '671573b584409c122786f34521cab934df00381cdf708714fa4db29b386b11216ba41758ec58e27b0bc4a522f2e5ca5535f2c173b92bfe22baa8bfbcd2a490b4'
        '95acbcc912cef41f0f388e0e5072e7faa40a144b08122c95a1a3b9580c339ddb9663e7d4efed4b7e649080e95bdb7335d4fb4c947438f3cd71d0a33d870742ee'
        '25322b4ace0d2d14480bd43ea031cf6b48cda4f2ee72a8064216d88b867876ed94d2259904f0ef071155deceb7722f6746385657191b9fad4035172be4e82421'
        'd072133be1334344b32891b44fdb970b10566d521150607962a4f34057690b08fe2ed03d17a8a622a247f75719cdd186fc1e5be63730f6b7c60f3eb47a8ec008'
        '9f33d880f8ea85e7b7740c0651d261767fe00ebe9dde4cfec7932b4d02628a8d14dc6460428f8a04e51404bab406d49c178a82d6f0dc3dbf53d2b48eff028ae4'
        '68ebfbd6bf33fb12c55ca8cac51e8eaa4111dc97a1a23bbc5ec8edaf3b80be0f9b94d815f61b4d98c6ff57026d453795e3bbeb138cf1bd85027d0a2a7258f4e6'
        'd336216b658d825f58ad2594c6139117a457431b33f6574b3caf188c3f8e7e9b60a2142a02a0eb0087ba984fe391f3b49fca241cd258aef17aa416da4bc71e4d')

provides=("$_pkgname")
conflicts=("$_pkgname")

prepare() {
    cd $_pkgname
    # This really should be a pacman feature...
    for src in "${source[@]}"; do
        src="${src%%::*}"
        src="${src##*/}"
        [[ $src = *.patch ]] || continue
        echo "Applying patch $src..."
        git apply "../$src"
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
    -Dpipewire=enabled
  meson compile -C build
}

package() {
  DESTDIR="${pkgdir}" meson install -C build \
    --skip-subprojects
  install -Dm 644 gamescope/LICENSE -t "${pkgdir}"/usr/share/licenses/gamescope/
}
