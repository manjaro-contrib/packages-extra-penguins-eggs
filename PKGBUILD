# Maintainer: Stefano Capitani <stefano_at_manjaro_org>
# Contributor: Mark Wagie <mark at manjaro dot org>
# Contributor: Piero Proietti <piero.proietti_at_gmail.com>
# Contributor: Muflone https://www.muflone.com/contacts/english/
# Contributor: osiixy <osiixy at gmail dot com>

pkgname=penguins-eggs
pkgver=10.0.56
pkgrel=4
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
_commit=0f9ef35e657d3b918d750517c7794adee3bc0dad # v10.0.56
source=("git+https://github.com/pieroproietti/penguins-eggs.git#commit=${_commit}")
sha256sums=('2de095cfd83d7cc47c48e8e28717b9e1c5ae45a327a745b6daf2f2e903057e04')

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
  cp -r addons assets bin conf ipxe dracut dist eui mkinitcpio mkinitfs node_modules scripts syslinux \
    "$pkgdir/usr/lib/$pkgname/"

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
