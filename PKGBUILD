# Maintainer:  Sławomir Kowalski <suawekk+aur@gmail.com>
# Contributor: Gaetan Bisson <bisson@archlinux.org>
# Contributor: judd <jvinet@zeroflux.org>
# Contributor: Mario Vazquez <mario_vazq@hotmail.com>

pkgname="bind-threaded"
_pkgver=9.9.2-P1
pkgver=9.9.2.P1
pkgrel=3
pkgdesc='Berkeley Internet Name Daemon is the reference implementation of the DNS protocols - with threads enabled'
url='http://www.isc.org/software/bind/'
license=('custom:ISC')
arch=('i686' 'x86_64')
options=('!makeflags' '!libtool')
depends=('openssl' 'krb5' 'libxml2')
source=("http://ftp.isc.org/isc/bind9/${_pkgver}/bind-${_pkgver}.tar.gz"
        'root.hint::http://www.internic.net/zones/named.root'
        'remove-tools.patch'
        'tmpfiles.d'
        'named'
        'named.conf'
        'named.conf.d'
        'named.service'
        'named.logrotate'
        'localhost.zone'
        '127.0.0.zone')
sha1sums=('9d56db2a86dde8167f7adfb200beee0f51d771a5'
          'cbdecd1ea8e7b612b33b63d66bc57273795d1f54'
          '14264affa71bcfe7703d0fb7c3556c4c47efaa8b'
          'c5a2bcd9b0f009ae71f3a03fbdbe012196962a11'
          '47f9ac4f6f70bc9167c1cf7859a298531197ad20'
          'c71a7fc02d4bf0d55e8e29d1e014607ac1d58726'
          '7848edbfb9a848843f57c11c02b0289eefd42d00'
          '7de92fff4b90c4821b3b133a05affd186fb93951'
          '3fe1f0b5c1a51dc1db9ebe5e173d18c52c97169b'
          '76a0d4cd1b913db177a5a375bebc47e5956866ec'
          '53be0f1437ebe595240d8dbdd819939582b97fb9')

provides=('dns-server')

install=install
backup=('etc/logrotate.d/named'
        'etc/conf.d/named'
        'etc/named.conf')

build() {
	cd "${srcdir}/bind-${_pkgver}"

	patch -p1 -i ../remove-tools.patch

	./configure \
		--prefix=/usr \
		--sysconfdir=/etc \
		--localstatedir=/var \
		--enable-threads \
		--disable-static \
		--enable-linux-caps \
		--with-openssl \
		--with-libxml2 \
		--with-libtool \

	make
}

package() {
	cd "${srcdir}/bind-${_pkgver}"

	install -Dm644 COPYRIGHT "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"

	make DESTDIR="${pkgdir}" install

	rmdir "${pkgdir}/var/run"
	install -d "${pkgdir}"/usr/share/doc/bind
	install doc/arm/*.html "${pkgdir}"/usr/share/doc/bind

	install -D -m755 ../named "${pkgdir}"/etc/rc.d/named
	install -D -m644 ../tmpfiles.d "${pkgdir}"/usr/lib/tmpfiles.d/named.conf
	install -D -m644 ../named.conf.d "${pkgdir}"/etc/conf.d/named
	install -D -m644 ../named.service "${pkgdir}"/usr/lib/systemd/system/named.service
	install -D -m600 ../named.logrotate "${pkgdir}"/etc/logrotate.d/named
	install -D -m640 -o 0 -g 40 ../named.conf "${pkgdir}"/etc/named.conf

	install -d -m750 -o 0 -g 40 "${pkgdir}"/var/named
	install    -m640 -o 0 -g 40 ../root.hint "${pkgdir}"/var/named
	install    -m640 -o 0 -g 40 ../127.0.0.zone "${pkgdir}"/var/named
	install    -m640 -o 0 -g 40 ../localhost.zone "${pkgdir}"/var/named
}
