# Maintainer: Stefano Capitani <stefano@manjaro.org>
# Contributor: Piero Proietti <piero.proietti@gmail.com>

pkgname=penguins-eggs
pkgver=26.7.18
pkgrel=1
pkgdesc="penguins-eggs universal Linux remastering"
arch=('x86_64')
url="https://penguins-eggs.net"
license=('GPL3')
source=("$pkgname-$pkgver.tar.gz::https://github.com/pieroproietti/penguins-eggs/archive/refs/tags/v$pkgver.tar.gz")
sha256sums=('48fd92b9c145b654993dac25bef1adf0fbe377279438a917a79a6d88cb167832')
options=('!debug')
depends=(
    'manjaro-tools-iso'
    'arch-install-scripts'
    'bash-completion'
    'btrfs-progs'
    'curl'
    'dosfstools'
    'e2fsprogs'
    'efibootmgr'
    'glibc'
    'gnupg'
    'grub'
    'libarchive'
    'libisoburn'
    'mkinitcpio-nfs-utils'
    'mtools'
    'nbd'
    'pv'
    'rsync'
    'squashfs-tools'
    'sudo'
    'wget'
    'yq'
    'qemu-guest-agent'
)
makedepends=('go' 'make' 'git')

build() {
    cd "$pkgname-$pkgver"

    export OA_BUILD_DIR="$srcdir/oa-build-dir"
    export GOFLAGS="-trimpath" # used for remove reference to $srcdir

    # Compiling build_oa + build_coa + docs
    make build_oa build_coa docs
}

package() {
    cd "$pkgname-$pkgver"

    local build_dir="$srcdir/oa-build-dir"

    # --- binary ---
    install -Dm755 "$build_dir/oa"  "${pkgdir}/usr/bin/oa"
    install -Dm755 "$build_dir/coa" "${pkgdir}/usr/bin/coa"
    ln -s coa "${pkgdir}/usr/bin/eggs"

    # --- config ---
    install -Dm644 coa/pkg/assets/configs/custom.yaml         "${pkgdir}/etc/penguins-eggs.d/custom.yaml"
    install -Dm644 coa/pkg/assets/configs/custom.exclude.list "${pkgdir}/etc/penguins-eggs.d/custom.exclude.list"

    mkdir -p "${pkgdir}/etc/penguins-eggs.d/scripts"
    cp -a coa/pkg/assets/configs/scripts/. "${pkgdir}/etc/penguins-eggs.d/scripts/"

    mkdir -p "${pkgdir}/etc/penguins-eggs.d/brain.d"
    cp -a coa/brain.d/. "${pkgdir}/etc/penguins-eggs.d/brain.d/"

    # --- man page ---
    for f in "$build_dir"/docs/man/*.1; do
        [ -e "$f" ] && install -Dm644 "$f" "${pkgdir}/usr/share/man/man1/$(basename "$f")"
    done

    # --- completions ---
    install -Dm644 "$build_dir/docs/completion/coa.bash"  "${pkgdir}/usr/share/bash-completion/completions/coa.bash"
    install -Dm644 "$build_dir/docs/completion/coa.fish"  "${pkgdir}/usr/share/fish/vendor_completions.d/coa.fish"
    install -Dm644 "$build_dir/docs/completion/coa.zsh"   "${pkgdir}/usr/share/zsh/vendor-completions/_coa"
    install -Dm644 "$build_dir/docs/completion/eggs.bash" "${pkgdir}/usr/share/bash-completion/completions/eggs.bash"
    install -Dm644 "$build_dir/docs/completion/eggs.fish" "${pkgdir}/usr/share/fish/vendor_completions.d/eggs.fish"
    install -Dm644 "$build_dir/docs/completion/eggs.zsh"  "${pkgdir}/usr/share/zsh/vendor-completions/_eggs"
}
