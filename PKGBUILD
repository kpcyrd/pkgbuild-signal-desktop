# Maintainer: kpcyrd <kpcyrd[at]archlinux[dot]org>
# Maintainer: Christian Heusel <gromit@archlinux.org>
# Contributor: Jean Lucas <jean@4ray.co>

pkgname=signal-desktop
_pkgname=Signal-Desktop
pkgver=8.1.0
pkgrel=2
pkgdesc="Signal Private Messenger for Linux"
license=('AGPL-3.0-only')
arch=('x86_64')
url="https://signal.org"
install="${pkgname}.install"
depends=(
  'alsa-lib' 'libasound.so'
  'at-spi2-core' 'libatk-bridge-2.0.so'
  'cairo' 'libcairo.so'
  'dbus' 'libdbus-1.so'
  'expat' 'libexpat.so'
  'glib2' 'libgio-2.0.so'
  'glibc'
  'gtk3'
  'hicolor-icon-theme'
  'libcups'
  'libdrm'
  'libgcc'
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
  "signal.desktop"
  "${pkgname}.sh"
)
sha256sums=('f9e42d00ef3981c044eaffd320ba53097109426de9f4b3005a3baaeae9e621d7'
            'bf388df4b5bbcab5559ebbf220ed4748ed21b057f24b5ff46684e3fe6e88ccce'
            '37701c610829ea3d0ae984b468ef83870fb75358396feb85b5f13f69cdbf1e68')
b2sums=('f5d44b544990cb1ccd0034220324e44ce420125231569ccdeb2467beb7e59c1447bb23584e9a55d6a7be4dcb0b84d5d157bf065a69ecced620a5f7149b519283'
        'ffb8f7bab4fd84aacf13e7b6d2835daf449b6650b4b3fa723456792ba7fb6cae352928fea11cb030510d558ce30036ff5a1513444f067b94c7fff0158b4f2265'
        '3b52b3e8530652472560fbc83f709cd1377210098c81b84cb9b14a985fbfcb349897843bb995cb772de31568517e038b497277b2fddca18b4a6dba5315d1a7c1')

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
  # Launcher
  install -Dm755 "${srcdir}/${pkgname}.sh" "${pkgdir}/usr/bin/${pkgname}"

  chmod u+s "${pkgdir}/usr/lib/signal-desktop/chrome-sandbox"

  install -Dm 644 "../signal.desktop" -t "${pkgdir}/usr/share/applications"
  for i in 16 24 32 48 64 128 256 512 1024; do
    install -Dm 644 "build/icons/png/${i}x${i}.png" \
      "${pkgdir}/usr/share/icons/hicolor/${i}x${i}/apps/${pkgname}.png"
  done
}

# vim: ts=2 sw=2 et:
