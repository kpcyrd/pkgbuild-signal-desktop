# Maintainer: kpcyrd <kpcyrd[at]archlinux[dot]org>
# Contributor: Jean Lucas <jean@4ray.co>

pkgname=signal-desktop
_pkgname=Signal-Desktop
pkgver=7.7.0
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
sha256sums=('360b0972836a75e3e14d711032a51c1aee6da1a6f699ec4919b11a0f860b65ce'
            'bfb24af869956ad2078adf0be6ba670c437b09193c1a368a76c2e22188c0a495'
            '913de2dc32db1831c9319ce7b347f51894e6fff0bf196118093a675dac874b91')
b2sums=('bb18e426fed87ae6ed6e525dcfcb85c3808b98f14b5ce619088268d2a8efcd9dc42f30291e9d75647c4de221f7243e0715fb90a4b08356c20f8a03fcce2de08a'
        '945b04a1b92dfe5ef3dd229227b8dfc687120bcb54c8526073724f685f2a7c05a9d093c2d18c7ebaf3040d1eae426d635518912d42b2e124c45cd3bf157c37a1'
        'e157cd0536b1b340c79385e99fcc27b9d48bef3c338562caaa78fe24bc7b8f00f6a757f6d4a47ee6c9e8c1138a1615dce7f1414dd1e6a9d1d06b682a7baa9130')

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
