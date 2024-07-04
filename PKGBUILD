_pkgname=gamescope
pkgname=handheld-${_pkgname}
pkgver=3.13.19
pkgrel=3
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
    glm_exper.patch
    # Chimeraos patches, mostly --force-panel-type external which is useful for devices like the ally
    chimeraos.patch
    crashfix.patch
    0001-disable-steam-touch-click-atom.patch
    # 0001-prevent-entering-trackpad-mode-on-internal-display.patch
    # Allows for using 720p resolutions
    add_720p_var.patch
    # Adds swipe gestures from left and right
    touch_gestures_env.patch
    # Pins the legion go refresh rates to be 60, 144 allowing the unified
    # refresh rate slider to work correctly
    legion_go.patch
)
b2sums=('88be5615a626ad2ed57053dbb8e9589d7c661996e5945aee6483f09f7a0ccffa1d79a69ed16542c46aa556f408e5531b86e756c9d8d6569adb31e2b4404f28e8'
        'SKIP'
        'SKIP'
        '07f2fe1886abe30c97b33876be8a8f2074e0611bc2b459ec2c9e9691e84bd38168a7232011d5264568f02a46a43ac9931632281bc9ef8c9ed094f689bc29b72b'
        'db2ff02be12f496da06c01a05e203a6370eb2e71270be21d253dcabb137fd24a5664cf68677fc940065cf7c82b9927caf21577840ca72c0db6b61794c66c133c'
        'e45d9a03102c92eaadf374cf4fd452d840fae145ce3efdce8b6e7f3e2c94f784282b1017cdb33953d692e59359ebe2813da19aa888a736ac2286f4cb8b884038'
        '022a84d51ab8abfba118fc9177b4a0e50b21f4d494a2694e0cd9de1f1fbc6bb1579e66c7171fd890175baa3d7875ccfa8e0cc15403ff8c583fa9431d38ea85f0'
        '14b38277cd9d48e321a974ec52f2a2236b3c0d44525b0a689b5b0e18c2c50f060959ffe0fdd541b3cf3b3f2bdc81d8064e1bd443a39c9315f8e22e5d3478b9d5'
        '445ff0bf630553e7636428b22f23f526067c1f52b32ca3809b232544ee8c989ef63df1b04751648a26327e667d3dba25c7c88ef0b7d6fc8dd2e58d509ef495d6'
        'd6c99ce3054a47680d7f9c0193cd6b65426fae9459ecc1a4ee7be5b8e6ffe1beef0082db68b7417b0da3e85f7efc64136d4683107f2229386aa2e5b6ab91b001')

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
  # FIXME: Fix werror failing on transposed-args
  arch-meson gamescope build \
    -Dforce_fallback_for=stb,wlroots,libliftoff,vkroots,libinput \
    -Denable_openvr_support=false \
    -Dlibliftoff:werror=false \
    -Dwlroots:werror=false \
    -Dpipewire=enabled
  meson compile -C build
}

package() {
  DESTDIR="${pkgdir}" meson install -C build \
    --skip-subprojects
  install -Dm 644 gamescope/LICENSE -t "${pkgdir}"/usr/share/licenses/gamescope/
}
