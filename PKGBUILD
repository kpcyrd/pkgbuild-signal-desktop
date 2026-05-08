# Maintainer: kpcyrd <kpcyrd[at]archlinux[dot]org>
# Maintainer: Christian Heusel <gromit@archlinux.org>
# Contributor: Jean Lucas <jean@4ray.co>

pkgname=signal-desktop
_pkgname=Signal-Desktop
pkgver=8.9.1
pkgrel=2
pkgdesc="Signal Private Messenger for Linux"
license=('AGPL-3.0-only')
arch=('x86_64' 'aarch64')
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
  'libstdc++' 'libstdc++.so'
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
sha256sums=('ae8ab79405192a1007f06225a6457787e5a59ec456f77127258daa50a521baec'
            'bf388df4b5bbcab5559ebbf220ed4748ed21b057f24b5ff46684e3fe6e88ccce'
            '37701c610829ea3d0ae984b468ef83870fb75358396feb85b5f13f69cdbf1e68')
b2sums=('4acfabac3f79583f9fb42b4e74b4a0b36eaff6b2b7bd3e3a421c0341252c2f9ac05e128e6d0e316c236cbcdd12a3d5adbb371307ec78b77083ddfb88ab30d925'
        'ffb8f7bab4fd84aacf13e7b6d2835daf449b6650b4b3fa723456792ba7fb6cae352928fea11cb030510d558ce30036ff5a1513444f067b94c7fff0158b4f2265'
        '3b52b3e8530652472560fbc83f709cd1377210098c81b84cb9b14a985fbfcb349897843bb995cb772de31568517e038b497277b2fddca18b4a6dba5315d1a7c1')

prepare() {
  cd "${_pkgname}-${pkgver}"

  # git-lfs hook needs to be installed for one of the dependencies
  export GIT_CONFIG_GLOBAL="$HOME/.gitconfig"
  git lfs install

  # Allow higher Node versions
  sed 's#"node": "#&>=#' -i package.json

  # Upgrade electron-builder to support aarch64
  sed -i 's/"electron-builder": "26\.0\.14",/"electron-builder": "26.0.20",/' package.json

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

  case "${CARCH}" in
    "aarch64") folder="linux-arm64-unpacked" ;;
    *) folder="linux-unpacked" ;;
  esac

  cp -a release/${folder} "${pkgdir}/usr/lib/${pkgname}"
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
