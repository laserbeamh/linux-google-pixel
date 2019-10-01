# Reference: <https://postmarketos.org/vendorkernel>
# Kernel config based on: arch/arm64/configs/lineageos_crosshatch_defconfig

pkgname="linux-google-pixel"
pkgver=3.18.70
pkgrel=1
pkgdesc="Google Pixel kernel fork"
arch="aarch64"
_carch="arm64"
_flavor="google-pixel"
url="https://kernel.org"
license="GPL-2.0-only"
options="!strip !check !tracedeps"
makedepends="perl sed installkernel bash gmp-dev bc linux-headers elfutils-dev devicepkg-dev dtbtool openssl-dev lz4 dtc"

# Compiler: latest GCC from Alpine
HOSTCC="${CC:-gcc}"
HOSTCC="${HOSTCC#${CROSS_COMPILE}}"

#if [ "${CC:0:5}" != "gcc6-" ]; then
#	CC="gcc6-$CC"
#	HOSTCC="gcc6-gcc"
#	CROSS_COMPILE="gcc6-$CROSS_COMPILE"
#fi

# Source
#_repository="android_kernel_google_pixel"
#_commit="61cfc9713975b21c42a3ba93963a25f5b9c454d6"

# AOSP Source
_repository="android_kernel_msm"
_commit="2513d2388ed10f8dbb80088426c6e402b2e9eb6b"
_config="config-${_flavor}.${arch}"
source="
	$pkgname-$_commit.tar.gz::https://github.com/laserbeamh/${_repository}/archive/${_commit}.tar.gz
	$pkgname-$_commit.tar.gz::https://android.googlesource.com/kernel/msm/+archive/${_commit}.tar.gz
	$_config
"
builddir="$srcdir/${_repository}-${_commit}"

prepare() {
	default_prepare
	downstreamkernel_prepare "$srcdir" "$builddir" "$_config" "$_carch" "$HOSTCC"
}

build() {
	unset LDFLAGS
	make ARCH="$_carch" CC="${CC:-gcc}" \
		KBUILD_BUILD_VERSION="$((pkgrel + 1 ))-postmarketOS"
}

package() {
	# kernel.release
	install -D "$builddir/include/config/kernel.release" \
		"$pkgdir/usr/share/kernel/$_flavor/kernel.release"

	# zImage (find the right one)
	# Pixel 3 XL uses lz4 to compress kernel.
	cd "$builddir/arch/$_carch/boot"
	_target="$pkgdir/boot/vmlinuz-$_flavor"
	for _zimg in Image.lz4-dtb zImage-dtb Image.gz-dtb *zImage Image; do
		[ -e "$_zimg" ] || continue
		msg "zImage found: $_zimg"
		install -Dm644 "$_zimg" "$_target"
		break
	done
	if ! [ -e "$_target" ]; then
		error "Could not find zImage in $PWD!"
		return 1
	fi
}

sha512sums="521a3b003c10da2c4166bc3dee7160de147b1a872e0f2bafc3e5f819fe4339a226ebb2ee8031118e9e6bdacd4a945b2bbf1c9f3505081aa70bc8060600c3d3e5  linux-google-pixel-2513d2388ed10f8dbb80088426c6e402b2e9eb6b.tar.gz
06e5c6340589287aa6856d218515b613695480afae208b024158c451aa473b55a5889f1f3fb335c1af40b106a6f8048534de35f60ae94e018a31bc60c5263e55  config-google-pixel.aarch64"
