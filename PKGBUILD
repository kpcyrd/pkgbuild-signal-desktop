# Maintainer: kpcyrd <kpcyrd[at]archlinux[dot]org>
# Contributor: Jean Lucas <jean@4ray.co>

pkgname=signal-desktop
_pkgname=Signal-Desktop
pkgver=7.20.0
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
)
optdepends=('xdg-desktop-portal: Screensharing with Wayland')
source=(
  "${pkgname}-${pkgver}.tar.gz::https://github.com/signalapp/${_pkgname}/archive/v${pkgver}.tar.gz"
  "dns-fallback-${pkgver}.json::https://raw.githubusercontent.com/kpcyrd/signal-desktop-dns-fallback-extractor/${pkgver}/dns-fallback.json"
  "${pkgname}.desktop"
)
sha256sums=('f78c1f94b0f0b66ded08df625ac0373d52e2d380b8ade91265b7f77b95c977e1'
            'bce4baf878a12e8edaa3720a02886d6abbe892d23322075ba88c04b05053208f'
            'bf388df4b5bbcab5559ebbf220ed4748ed21b057f24b5ff46684e3fe6e88ccce')
b2sums=('e739d735c940945c810f8102ceac9c10c539a61625c83d3173d1eee095cc196c1002a17fce5bddd44075f696f9139349dac42ab0ab22599259dcf95b6b6adcc1'
        '73561482255cb7fa8f72748bf78064b4f248aedeb9f9fb5291994a6a6e96fd0bd40a08c268a3989824f3d881012248926ee0b92c7c7d76ac508370a7e5539a1d'
        'ffb8f7bab4fd84aacf13e7b6d2835daf449b6650b4b3fa723456792ba7fb6cae352928fea11cb030510d558ce30036ff5a1513444f067b94c7fff0158b4f2265')

prepare() {
  cd "${_pkgname}-${pkgver}"

  # git-lfs hook needs to be installed for one of the dependencies
  git lfs install

  # Allow higher Node versions
  sed 's#"node": "#&>=#' -i package.json

  # Install dependencies for sticker-creator
  npm --prefix ./sticker-creator/ install

  # Install dependencies for signal-desktop
  npm install --ignore-engines
}

build() {
  cd "${_pkgname}-${pkgver}"

  # Build the sticker creator
  npm --prefix ./sticker-creator/ run build

  # Fix reproducible builds, use official dns-fallback.json instead of the generated one
  # https://github.com/signalapp/Signal-Desktop/issues/6823
  cp "../dns-fallback-${pkgver}.json" build/dns-fallback.json
  > ts/scripts/generate-dns-fallback.ts

  # Build signal-desktop
  npm run build
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
