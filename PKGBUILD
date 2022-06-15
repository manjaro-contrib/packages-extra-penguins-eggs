# Maintainer: Stefano Capitani <stefano_at_manjaro_org>

pkgname=penguins-eggs
pkgver=9.1.31
pkgrel=1
_commit='c9439ad6203abe311089c7730a09f0ce093aa3ea'
pkgdesc="A console utility that allows you to remaster your system and redistribute it as ISO images or via remote boot PXE."
arch=('any')
url="https://penguins-eggs.net"
license=('GPL2')
depends=('arch-install-scripts' 'erofs-utils' 'manjaro-tools-iso' 'mtools' 'nodejs' 'python' 'syslinux' 'xdg-utils')
makedepends=('git' 'pnpm')
conflicts=('penguins-eggs-dev')
replaces=('penguins-eggs-dev')
options=('!strip')
source=("git+https://github.com/pieroproietti/penguins-eggs.git#commit=${_commit}")
sha256sums=('SKIP')

pkgver() {
	cd "${srcdir}/${pkgname}"
	grep 'version' package.json | awk 'NR==1 {print $2 }' | awk -F '"' '{print $2}'
}

build() {
	cd "${srcdir}/${pkgname}"
	pnpm i
	pnpm run build
}

package() {
	cd ${srcdir}/${pkgname}
	install -Dm644 package.json -t "${pkgdir}/usr/lib/${pkgname}/"
	cp -r node_modules "${pkgdir}/usr/lib/${pkgname}/"

	install -d "${pkgdir}/opt/${pkgname}"
	cp -r addons "${pkgdir}/opt/${pkgname}/"
	cp -r bin "${pkgdir}/opt/${pkgname}/"
	cp -r conf "${pkgdir}/opt/${pkgname}/"
	cp -r lib "${pkgdir}/opt/${pkgname}/"
	cp -r mkinitcpio "${pkgdir}/opt/${pkgname}/"
	cp -r scripts "${pkgdir}/opt/${pkgname}/"

	# Symlink executable
	install -d "${pkgdir}/usr/bin"
	ln -s "/opt/${pkgname}/bin/run" "${pkgdir}/usr/bin/eggs"

	# Install bash completions
	install -d "${pkgdir}/usr/share/bash-completion/completions"
	mv "${pkgdir}/opt/${pkgname}/scripts/eggs.bash" "${pkgdir}/usr/share/bash-completion/completions/"

	# Install man page
	install -Dm644 manpages/doc/man/eggs.roll.gz "${pkgdir}/usr/share/man/man1/eggs.1.gz"

	install -Dm644 "assets/${pkgname}.desktop" -t "${pkgdir}/usr/share/applications/"
	install -Dm644 assets/eggs.png -t "${pkgdir}/usr/share/pixmaps/"
}

