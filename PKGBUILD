# Maintainer: kpcyrd <kpcyrd[at]archlinux[dot]org>
# Maintainer: Christian Heusel <gromit@archlinux.org>
# Contributor: Jean Lucas <jean@4ray.co>

pkgname=signal-desktop
_pkgname=Signal-Desktop
pkgver=7.88.0
pkgrel=1
pkgdesc="Signal Private Messenger for Linux"
license=('AGPL-3.0-only')
arch=('x86_64')
url="https://signal.org"
depends=(
  'alsa-lib' 'libasound.so'
  'at-spi2-core' 'libatk-bridge-2.0.so'
  'cairo' 'libcairo.so'
  'dbus' 'libdbus-1.so'
  'expat' 'libexpat.so'
  'gcc-libs'
  'glib2' 'libgio-2.0.so'
  'glibc'
  'gtk3'
  'hicolor-icon-theme'
  'libcups'
  'libdrm'
  'libnotify'
  'libpulse'
  'libx11'
  'libxcb'
  'libxcomposite'
  'libxdamage'
  'libxext'
  'libxfixes'
  'libxkbcommon' 'libxkbcommon.so'
  'libxrandr'
  'mesa'
  'nspr'
  'nss'
  'pango' 'libpango-1.0.so'
  'systemd-libs' 'libudev.so'
)
makedepends=(
  'git'
  'git-lfs'
  'libxcrypt-compat'
  'node-gyp'
  'nodejs-lts-jod'
  'pnpm'
  'python'
)
optdepends=('xdg-desktop-portal: Screensharing with Wayland')
source=(
  "${pkgname}-${pkgver}.tar.gz::https://github.com/signalapp/${_pkgname}/archive/v${pkgver}.tar.gz"
  "${pkgname}.desktop"
)
sha256sums=('6d0b85e6acf4e586d85e44d5bd1559beaa4d147e76eee00774cbe2acae7595e8'
            'bf388df4b5bbcab5559ebbf220ed4748ed21b057f24b5ff46684e3fe6e88ccce')
b2sums=('8d1a93202d882b14683d8356f7dcdcd82df4afad908d6dee940e31eec2727943d6612b39a1e102b25faaa85fc8605a541aeeb62dc49ebd6905b67d893d0ac86b'
        'ffb8f7bab4fd84aacf13e7b6d2835daf449b6650b4b3fa723456792ba7fb6cae352928fea11cb030510d558ce30036ff5a1513444f067b94c7fff0158b4f2265')

prepare() {
  cd "${_pkgname}-${pkgver}"

  # git-lfs hook needs to be installed for one of the dependencies
  export GIT_CONFIG_GLOBAL="$HOME/.gitconfig"
  git lfs install

  # Allow higher Node versions
  sed 's#"node": "#&>=#' -i package.json

  # Install dependencies for sticker-creator
  pnpm install --dir sticker-creator

  # Install dependencies for signal-desktop
  pnpm install
}

build() {
  cd "${_pkgname}-${pkgver}"

  # Build the sticker creator
  pnpm --prefix ./sticker-creator/ run build

  # Build signal-desktop
  pnpm run build
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
