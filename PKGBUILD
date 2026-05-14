# Maintainer: Stefano Capitani <stefano_at_manjaro_org>
# Contributor: Mark Wagie <mark at manjaro dot org>
# Contributor: Piero Proietti <piero.proietti_at_gmail.com>
# Contributor: Muflone https://www.muflone.com/contacts/english/
# Contributor: osiixy <osiixy at gmail dot com>

pkgname=penguins-eggs
pkgver=26.5.12
pkgrel=1
_bootloadersver=25.9.8
pkgdesc="A console tool that allows you to remaster your system and redistribute it as live images on USB sticks or via PXE"
arch=('any')
url="https://penguins-eggs.net"
license=('GPL-2.0-or-later')
depends=(
  'arch-install-scripts'
  'dosfstools'
  'erofs-utils'
  'findutils'
  'grub'
  'jq'
  'libarchive'
  'libisoburn'
  'lsb-release'
  'lvm2'
  'manjaro-tools-iso'
  'mkinitcpio-nfs-utils'
  'mtools'
  'nbd'
  'nodejs'
  'pacman-contrib'
  'parted'
  'procps-ng'
  'pv'
  'python'
  'rsync'
  'squashfs-tools'
  'sshfs'
  'syslinux'
  'xdg-utils'
)
makedepends=(
  'git'
  'pnpm'
)
optdepends=(
  'bash-completion: eggs autocomplete'
  'calamares: system installer GUI'
  'zsh-completions: eggs autocomplete'
)
options=('!strip')
source=("git+https://github.com/pieroproietti/penguins-eggs.git#tag=v$pkgver"
  	"bootloaders.tar.gz::https://github.com/pieroproietti/penguins-bootloaders/releases/download/v$_bootloadersver/bootloaders.tar.gz")
sha256sums=('867d8081052167790d450071c1d6d55836ea566b85f94e5b2784c4b6be191ff2'
            'c5dcfd82a8e65160af5c93365f07776a00d44e8b531d6641f4cadbbe7b4b5baf')

pkgver() {
  cd "$pkgname"
  node -pe "require('./package.json').version"
}

build() {
  cd "$pkgname"
  export PNPM_HOME="$srcdir/pnpm-home"
  pnpm i
  pnpm build
}

package() {
  cd "$pkgname"
  install -Dm644 .oclif.manifest.json package.json -t "$pkgdir/usr/lib/$pkgname/"
  cp -r addons assets bin conf dracut dist eui mkinitcpio node_modules scripts \
    "$pkgdir/usr/lib/$pkgname/"
  cp -r "$srcdir/bootloaders" "$pkgdir/usr/lib/$pkgname/"

  # Fix permissions
  chown root:root "$pkgdir/usr/lib/$pkgname/"{dist,node_modules}

  # Package contains reference to $srcdir
  find "$pkgdir" -name package.json -print0 | xargs -r -0 sed -i '/_where/d'

  # Install documentation
  install -Dm644 README.md -t "$pkgdir/usr/share/doc/$pkgname/"

  # Install shell completion files
  install -d "$pkgdir/usr/share/bash-completion/completions"
  mv "$pkgdir/usr/lib/$pkgname/scripts/eggs.bash" \
    "$pkgdir/usr/share/bash-completion/completions/"
  install -d "$pkgdir/usr/share/zsh/functions/Completion/Zsh/"
  mv "$pkgdir/usr/lib/$pkgname/scripts/_eggs" \
    "$pkgdir/usr/share/zsh/functions/Completion/Zsh/"

  # Install man page
  install -Dm644 manpages/doc/man/eggs.1.gz -t "$pkgdir/usr/share/man/man1/"

  # Install desktop file
  install -Dm644 "assets/$pkgname.desktop" -t "$pkgdir/usr/share/applications/"

  # Install icon
  install -Dm644 assets/eggs.png -t "$pkgdir/usr/share/pixmaps/"

  # Script permissions
  chmod +x "$pkgdir/usr/lib/$pkgname/scripts/mom.sh"
  chmod +x "$pkgdir/usr/lib/$pkgname/eui/eui-create-image.sh"
  chmod +x "$pkgdir/usr/lib/$pkgname/eui/eui-start.sh"
  chmod 0400 "$pkgdir/usr/lib/$pkgname/eui/eui-users"

  # Symlink executable
  install -d "$pkgdir/usr/bin"
  ln -s "/usr/lib/$pkgname/bin/run.js" "$pkgdir/usr/bin/eggs"

  # Symlink to adapt
  ln -s "/usr/lib/$pkgname/addons/eggs/adapt/bin/adapt" "$pkgdir/usr/bin/"
}
