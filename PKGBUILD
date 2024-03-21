# Maintainer: kpcyrd <kpcyrd[at]archlinux[dot]org>
# Contributor: Jean Lucas <jean@4ray.co>

pkgname=signal-desktop
_pkgname=Signal-Desktop
pkgver=7.3.0
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
sha256sums=('4684e4402cdec95565caba1d9ba742520214374473851ca772729f998faa5aa3'
            'c48e12530864cd77abae1890f69fdc2646533dd1a42c89db1482a45cd2c01b62'
            '913de2dc32db1831c9319ce7b347f51894e6fff0bf196118093a675dac874b91')
b2sums=('741b7cd7ee0cedff72d0698eefa7f7e112889b8b9a46d21665cf849516be6ef24c46e29a7136425c9fe91755af83ac3d66f6b53584bbc07149b5a9b71a427e69'
        '991cce5b26cae0048165748fd7ae086d7373ff84e06d25bc270b30ef8c46e8405b129fe25049066d3debdbe30a8f0d8ee275bcca85c36f74cd9fb863151834b7'
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
