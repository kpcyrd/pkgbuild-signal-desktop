# Maintainer: kpcyrd <kpcyrd[at]archlinux[dot]org>
# Contributor: Jean Lucas <jean@4ray.co>

pkgname=signal-desktop
_pkgname=Signal-Desktop
pkgver=7.11.1
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
sha256sums=('944acb384c51a9b5b4dcf3838c3d05572e90ed8bb16a9ada0f9aa7c6d622713c'
            '82b6382057f303bb53244efe82d5fa8163eee201d2ac1b2deb6409247d5b92c5'
            'bf388df4b5bbcab5559ebbf220ed4748ed21b057f24b5ff46684e3fe6e88ccce')
b2sums=('fb1b8fd1d1217f8d32b97af317d526c9bd61a2542bd7aa6a6a3ae4a7efb3713153ba5c4cd2fc7b924b9832ae39fa28ab7eaa6db63cc0751377d66c58433ca412'
        'bc09e66434bf0a9b5ea4008c72a220beafde3f90ae0ac99b5f44da81519ac0e0c7b74605e721e82379d82a5fb60c7a7663e35d1f5fb4e3f479aa1f32f6e73c58'
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
