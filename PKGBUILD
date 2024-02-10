_pkgname=gamescope
pkgname=${_pkgname}-plusplus
pkgver=3.13.19
pkgrel=1
pkgdesc='Gamescope with reshade+legion_go+chimeraos patches. Based on the Bazzite version.'
arch=(x86_64)
url=https://github.com/ValveSoftware/gamescope
license=(BSD)
depends=(
    gcc-libs
    glibc
    glm
    hwdata
    libavif
    libcap.so
    libdisplay-info.so
    libdrm
    libliftoff.so
    libinput
    libpipewire-0.3.so
    libvulkan.so
    libx11
    libxcb
    libxcomposite
    libxdamage
    libxext
    libxfixes
    libxkbcommon
    libxmu
    libxrender
    libxres
    libxtst
    libxxf86vm
    seatd
    sdl2
    vulkan-icd-loader
    wayland
    xcb-util-wm
    xcb-util-errors
    xorg-server-xwayland
)
makedepends=(
    benchmark
    cmake
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
    # Upstream library has a version mismatch
    # Gamescope uses an experimental config which causes the compiler to complain
    'glm_exper.patch'
    # Chimeraos patches, mostly --force-panel-type external which is useful for devices like the ally
    'chimeraos.patch'
    'crashfix.patch'
    # Allows for using 720p resolutions
    'add_720p_var.patch'
    # Adds swipe gestures from left and right
    'touch_gestures_env.patch'
    # Pins the legion go refresh rates to be 60, 144 allowing the unified
    # refresh rate slider to work correctly
    'legion_go.patch'
)
b2sums=('SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP' 'SKIP')

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
