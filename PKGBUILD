# Maintainer: kpcyrd <kpcyrd[at]archlinux[dot]org>
# Contributor: Jean Lucas <jean@4ray.co>

pkgname=signal-desktop
_pkgname=Signal-Desktop
pkgver=7.10.0
pkgrel=1
pkgdesc="Signal Private Messenger for Linux"
license=('AGPL-3.0-only')
arch=('x86_64')
url="https://signal.org"
depends=(
  'gcc-libs'
  'glibc'
  'gtk3'
  'hicolor-icon-theme'
  'libasound.so'
  'libatk-bridge-2.0.so'
  'libcairo.so'
  'libcups'
  'libdbus-1.so'
  'libdrm'
  'libexpat.so'
  'libgio-2.0.so'
  'libpango-1.0.so'
  'libx11'
  'libxcb'
  'libxcomposite'
  'libxdamage'
  'libxext'
  'libxfixes'
  'libxkbcommon.so'
  'libxrandr'
  'mesa'
  'nspr'
  'nss'
)
makedepends=(
  'git'
  'git-lfs'
  'libxcrypt-compat'
  'node-gyp'
  'nodejs'
  'npm'
  'python'
  'yarn'
)
optdepends=('xdg-desktop-portal: Screensharing with Wayland')
source=(
  "${pkgname}-${pkgver}.tar.gz::https://github.com/signalapp/${_pkgname}/archive/v${pkgver}.tar.gz"
  "dns-fallback-${pkgver}.json::https://raw.githubusercontent.com/kpcyrd/signal-desktop-dns-fallback-extractor/${pkgver}/dns-fallback.json"
  "${pkgname}.desktop"
)
sha256sums=('4b695219fcd347ae41590633a7e4c461bf98bceeb5254b3cf744663ac2703ee0'
            'bcab0cfd42e44ea0a09ec3dde629d448d86ad1bc3ed2370aba6634437dd815c9'
            'bf388df4b5bbcab5559ebbf220ed4748ed21b057f24b5ff46684e3fe6e88ccce')
b2sums=('5791a8382b08899dd593859a17ce7069e9377303b7b6e68e0ff7c0e3aba79d7290bca0034fd853497c75c14c78b27872e4b974d6f53dd32e5d6f041574a7a4d6'
        '6b2409eb0e19358aa8d4f1222a540b6ba9d130bebea368e52272885164799d94f9fd175ab7e8de46fe15f83adfba0236408f7dca6601daf77def53fbbbe074fe'
        'ffb8f7bab4fd84aacf13e7b6d2835daf449b6650b4b3fa723456792ba7fb6cae352928fea11cb030510d558ce30036ff5a1513444f067b94c7fff0158b4f2265')

# https://lore.kernel.org/regressions/8979ae0b-c443-4b45-8210-6394471dddd1@kernel.dk/
# /bin/sh: /tmp/yarn--1716467297929-0.0038272490627193623/node: /bin/sh: bad interpreter: Text file busy
export UV_USE_IO_URING=0

prepare() {
  cd "${_pkgname}-${pkgver}"

  # git-lfs hook needs to be installed for one of the dependencies
  git lfs install

  # Allow higher Node versions
  sed 's#"node": "#&>=#' -i package.json

  # Install dependencies for sticker-creator
  yarn --cwd ./sticker-creator/ install

  # Install dependencies for signal-desktop
  yarn install --ignore-engines
}

build() {
  cd "${_pkgname}-${pkgver}"

  # Build the sticker creator
  yarn --cwd ./sticker-creator/ build

  # Fix reproducible builds, use official dns-fallback.json instead of the generated one
  # https://github.com/signalapp/Signal-Desktop/issues/6823
  cp "../dns-fallback-${pkgver}.json" build/dns-fallback.json
  > ts/scripts/generate-dns-fallback.ts

  # Build signal-desktop
  yarn build
}

package() {
  cd "${_pkgname}-${pkgver}"

  install -d "${pkgdir}/usr/"{lib,bin}
  cp -a release/linux-unpacked "${pkgdir}/usr/lib/${pkgname}"
  ln -s "/usr/lib/${pkgname}/${pkgname}" "${pkgdir}/usr/bin/"

  chmod u+s "${pkgdir}/usr/lib/signal-desktop/chrome-sandbox"

  install -Dm 644 "../${pkgname}.desktop" -t "${pkgdir}/usr/share/applications"
  for i in 16 24 32 48 64 128 256 512 1024; do
    install -Dm 644 "build/icons/png/${i}x${i}.png" \
      "${pkgdir}/usr/share/icons/hicolor/${i}x${i}/apps/${pkgname}.png"
  done
}

# vim: ts=2 sw=2 et:
