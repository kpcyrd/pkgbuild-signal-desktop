# Maintainer: kpcyrd <kpcyrd[at]archlinux[dot]org>
# Contributor: Jean Lucas <jean@4ray.co>

pkgname=signal-desktop
_pkgname=Signal-Desktop
pkgver=5.22.0
pkgrel=1
pkgdesc="Signal Private Messenger for Linux"
license=('AGPL3')
arch=('x86_64')
url="https://signal.org"
depends=('gtk3' 'libvips' 'libxss' 'hicolor-icon-theme')
makedepends=('yarn' 'git' 'git-lfs' 'nodejs-lts-fermium' 'npm6' 'python')
source=(
  "${pkgname}-${pkgver}.tar.gz::https://github.com/signalapp/${_pkgname}/archive/v${pkgver}.tar.gz"
  "${pkgname}.desktop"
  "expire-from-source-date-epoch.patch"
)
sha512sums=('c30ce478dbd26bc1a5d5d5dcc9c867a8dbbad0289eef86c7fa06ed5a717241815aa182dd7b58fb77833d7c46dfa973af755f68d4682ad7e75df0ea0aba1bb47c'
            '90cfee563a985bc73c4e7984715d190ae0b5c7aa887a7dc15c665980ca5cc8420b02f6c7a54e032c29e18876d5d51cfbe5027a9f0a59de3903f50fd469d73ce0'
            '4bdb1812575072a838ce2183bab0b407e3f31bcdb81ee62f6a6424c459779bf7739e3c6c65cb06a191e5a10f940e140f51ddf50ab094023a8e8ce9497ed72e82')
b2sums=('80479d315c15917d916a581d1cd1405bd46b47aac8a1c79bc1b21df23f1a077f9e900d824c7aa37eea46f399f1d880c2388357ec9b6646316dfd09a52c7c6445'
        'b0974f1409bf77e917a3ecce68a9d066eaf24a6cd35e0d52b0d61fcf2fb014f19962bb445b6eaf668081d370c69c01b08420c056902907574a29a71a4f0f2457'
        '4d1c85e4f5b0c797a679ad33f685c4ec1693a6d73b23dd10565d1b1a560558ca50c283e7e652dbab891150c70f0accb1d5dd011c7c87a11efb9714ef1ab5e8c5')

prepare() {
  cd "${_pkgname}-${pkgver}"

  # git-lfs hook needs to be installed for one of the dependencies
  git lfs install

  # Allow higher Node versions
  sed 's#"node": "#&>=#' -i package.json

  # patch better-sqlite3 to encapsulate sqlcipher
  # https://bugs.archlinux.org/task/69980
  # https://github.com/signalapp/better-sqlite3/pull/3
  grep -q 32828e03be0489572ab334239c5768c86697989f package.json
  sed 's|https://github\.com/signalapp/better-sqlite3#32828e03be0489572ab334239c5768c86697989f|https://github.com/signalapp/better-sqlite3#917a6f5cf8b84d5ef4b8fe6dc0f4b8f59ca45bea|' -i package.json

  yarn install --ignore-engines

  # We can't read the release date from git so we use SOURCE_DATE_EPOCH instead
  patch --forward --strip=1 --input="${srcdir}/expire-from-source-date-epoch.patch"
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
