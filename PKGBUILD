# Maintainer: Mark Wagie <mark at manjaro dot org>
# Contributor: Stefano Capitani <stefano_at_manjaro_org>
# Contributor: Piero Proietti <piero.proietti_at_gmail.com>
# Contributor: Muflone https://www.muflone.com/contacts/english/
# Contributor: osiixy <osiixy at gmail dot com>

pkgname=penguins-eggs
pkgver=10.0.33
pkgrel=1
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
_commit=c5915f5f4840dce40518a0eff59f6f7d17750a34  # v10.0.33
source=("git+https://github.com/pieroproietti/penguins-eggs.git#commit=${_commit}")
sha256sums=('a47f1ea6ef70830441ab33231e1b1ace285f0724ddb3925fb2f2716d0fa9506c')

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
  cp -r addons assets bin conf ipxe dracut dist eui mkinitcpio mkinitfs node_modules scripts \
    "$pkgdir/usr/lib/$pkgname/"

  # Fix permissions
  chown root:root "$pkgdir/usr/lib/$pkgname/"{dist,node_modules}

  # Package contains reference to $srcdir
  find "$pkgdir" -name package.json -print0 | xargs -r -0 sed -i '/_where/d'

  local tmppackage="$(mktemp)"
  local pkgjson="$pkgdir/usr/lib/$pkgname/package.json"
  jq '.|=with_entries(select(.key|test("_.+")|not))' "${pkgjson}" > "${tmppackage}"
  mv "${tmppackage}" "${pkgjson}"
  chmod 644 "${pkgjson}"

  # Fix paths for node modules
  find node_modules -type f -print0 | xargs --null sed -i \
    "s#${srcdir}/$pkgname-${pkgver}/#/usr/lib/eggs/#"

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
