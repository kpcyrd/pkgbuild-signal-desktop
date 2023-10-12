# Maintainer: kpcyrd <kpcyrd[at]archlinux[dot]org>
# Contributor: Jean Lucas <jean@4ray.co>

pkgname=signal-desktop
_pkgname=Signal-Desktop
pkgver=6.34.0
pkgrel=1
pkgdesc="Signal Private Messenger for Linux"
license=('AGPL-3.0-only')
arch=('x86_64')
url="https://signal.org"
depends=(
  'gtk3'
  'hicolor-icon-theme'
  'libasound.so'
  'libatk-bridge-2.0.so'
  'libcairo.so'
  'libdbus-1.so'
  'libexpat.so'
  'libgio-2.0.so'
  'libpango-1.0.so'
  'libxkbcommon.so'
  'libxss'
)
makedepends=(
  'git'
  'git-lfs'
  'libxcrypt-compat'
  'nodejs'
  'npm'
  'python'
  'yarn'
)
optdepends=('xdg-desktop-portal: Screensharing with Wayland')
source=(
  "${pkgname}-${pkgver}.tar.gz::https://github.com/signalapp/${_pkgname}/archive/v${pkgver}.tar.gz"
  "${pkgname}.desktop"
)
sha256sums=('62cb1f6a3c835eca3a23fe379b43f93576e7d9a0e4d6098e8edab948fb671c10'
            '913de2dc32db1831c9319ce7b347f51894e6fff0bf196118093a675dac874b91')
b2sums=('f3e6bf401fc1470950ffee96a7a1f753d7748fcd9f07cd821d3714222399f12591d75f8acbd4b710147cfe7aa87d3f0298de427bf3a0f34a3a79ecfe9592cef5'
        'e157cd0536b1b340c79385e99fcc27b9d48bef3c338562caaa78fe24bc7b8f00f6a757f6d4a47ee6c9e8c1138a1615dce7f1414dd1e6a9d1d06b682a7baa9130')

prepare() {
  cd "${_pkgname}-${pkgver}"

  # temporary fix for openssl3
  export NODE_OPTIONS=--openssl-legacy-provider

  # git-lfs hook needs to be installed for one of the dependencies
  git lfs install

  # Allow higher Node versions
  sed 's#"node": "#&>=#' -i package.json

  yarn install --ignore-engines
}

build() {
  cd "${_pkgname}-${pkgver}"
  yarn generate
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
