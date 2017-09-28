pkgname=hws
pkgdesc="Hydronium Web Server"

arch=(i686 x86_64)
conflicts=(caddy nginx)
depends=(pcre zlib)
fancyindexver=0.4.2
makedepends=(git)
opensslver=1.1.0f
pkgrel=1
pkgver=1.13.5
provides=(caddy nginx)

sha384sums=(
	SKIP
	SKIP
	4e62fb4217cead679c49762c7f01d3c13222b060b651373530fc07441be2214ac35d4d4ce409408373396347b7dafb5b
	0f7403a1fd97ec05842573064496d4abf66ffaf9eb9afbad7636b7914940af2a1678a83b8436baabb3ddce4555fb9fbe
	eb89075d16cfbb117f2fcd96effa510c7fc1266e798478c699b730bf80450d1c6ab50aa475371cffa7a234b8852fc192
	54a1f247cddcaed1420920035bf9fe1a50dd41d283a6cf93e891878907adfae3ea683ddbe441ef19c8364926c65489e8
	5039fac50d6374ced88a2bbb60462833d6b8e643a73dbc73c5db94a9a1853dab7a5b09c95df2ffc4c0904db4ef5e9ade
	46a0bd7d28e7187eb138cc00dcd79caccbe42dba5ca19e07597403a52efb7557ed77944253c92c95a75da94f1856e1d7
	d24ab1e2b9aa079cb5f244a0bd8cdb8499401936d5c27637937a3593559846e43aa77b2bef1952052aa88babadaae19d
	affa0695127b998a69a1468a5fac0d821b9589c7142887bfb2d9a3d3f491b1047a0befd2448b51ac3eabcc941ca31a76
	2dc833b3c351e7f4afd1739f4188e9e6dd3931735d0bf38c11d9c53f46513a704994bb297ed467012658ea3a8fea9549
	815a2c882d79dae039a4c30b748586ccc78be56eee03ed1832463ab5dfd15a8443898b2dff84971419a79ab242828fb1
	a1312cf6a356a8ca03dc6e121e64593ae08126efcd9756d49a71238194e1eb321e16043566d2c824ccd36ffc9188a29a
	7fa169d119fcf11e2e85be1fccc7e0e68498e2ce000db0b03f856e5445b3352c1d8c485d66d3f272b45cd30354e91633
	63478e2a64e74002dfd3c8cb7d3460b12918782242649645654afc4ed29226c1663d4d47feb80ff49f1a76b1d122dc51
	9b428cf63071c3fbd7157a1c8abb01f01b26f822c7e6686ebe2e7396731f2670bcee9939b52ebba6a063f0607dd4669c
)

source=(
	git+https://github.com/ghedo/http2-push-nginx-module
	git+https://github.com/google/ngx_brotli.git
	https://caddyserver.com/download/linux/amd64?license=personal
	https://codeload.github.com/aperezdc/ngx-fancyindex/tar.gz/v$fancyindexver
	https://codeload.github.com/pagespeed/ngx_pagespeed/tar.gz/latest-beta
	https://nginx.org/download/nginx-$pkgver.tar.gz
	https://www.openssl.org/source/openssl-$opensslver.tar.gz
	logrotate
	nginx__204-headers.patch
	nginx__dynamic-tls-records.patch
	nginx__http2-hpack.patch
	nginx__http2-push.patch
	nginx__http2-spdy.patch
	nginx__server-header.patch
	openssl__equal-preference.patch
	service
)

_build_parameters=(
	--add-module=../http2-push-nginx-module
	--add-module=../ngx-fancyindex-$fancyindexver
	--add-module=../ngx_brotli
	--add-module=../ngx_pagespeed-latest-beta
	--conf-path=/etc/hws/nginx.conf
	--error-log-path=stderr
	--http-client-body-temp-path=/var/lib/hws/body
	--http-proxy-temp-path=/var/lib/hws/proxy
	--prefix=/etc/hws
	--sbin-path=/usr/bin/hws-nginx
	--with-cc-opt="-DTCP_FASTOPEN=23"
	--with-file-aio
	--with-http_addition_module
	--with-http_realip_module
	--with-http_spdy_module
	--with-http_ssl_module
	--with-http_v2_hpack_enc
	--with-http_v2_module
	--with-openssl=../openssl-$opensslver
	--with-threads
	--without-http_access_module
	--without-http_auth_basic_module
	--without-http_autoindex_module
	--without-http_browser_module
	--without-http_charset_module
	--without-http_empty_gif_module
	--without-http_fastcgi_module
	--without-http_geo_module
	--without-http_limit_conn_module
	--without-http_limit_req_module
	--without-http_map_module
	--without-http_memcached_module
	--without-http_mirror_module
	--without-http_referer_module
	--without-http_scgi_module
	--without-http_split_clients_module
	--without-http_ssi_module
	--without-http_upstream_hash_module
	--without-http_upstream_ip_hash_module
	--without-http_upstream_keepalive_module
	--without-http_upstream_least_conn_module
	--without-http_upstream_zone_module
	--without-http_userid_module
	--without-http_uwsgi_module
	--without-mail_imap_module
	--without-mail_pop3_module
	--without-mail_smtp_module
	--without-poll_module
	--without-select_module
	--without-stream_access_module
	--without-stream_geo_module
	--without-stream_limit_conn_module
	--without-stream_map_module
	--without-stream_return_module
	--without-stream_split_clients_module
	--without-stream_upstream_hash_module
	--without-stream_upstream_least_conn_module
	--without-stream_upstream_zone_module
)

prepare() {
	cd ngx_brotli
	git submodule update --init

	cd ../ngx_pagespeed-latest-beta
	curl "$(scripts/format_binary_url.sh PSOL_BINARY_URL)" | tar xz

	cd ../nginx-$pkgver
	cat ../nginx__*.patch | patch -p1

	cd ../openssl-$opensslver
	cat ../openssl__*.patch | patch -p1
}

build() {
	cd nginx-$pkgver
	./configure ${_build_parameters[@]}
	make
}

package() {
	cd nginx-$pkgver
	make DESTDIR="$pkgdir" install

	chmod 755 "$pkgdir"/usr/bin/hws-nginx
	rm -r "$pkgdir"/etc/hws

	install -D ../caddy "$pkgdir"/usr/bin/hws-caddy
	install -Dm644 ../logrotate "$pkgdir"/etc/logrotate.d/hws
	install -Dm644 ../service "$pkgdir"/usr/lib/systemd/system/hws.service

	install -dm700 -ghttp -ohttp "$pkgdir"/var/cache/hws "$pkgdir"/var/lib/hws/body "$pkgdir"/var/lib/hws/proxy "$pkgdir"/var/log/hws

	for i in contrib/vim/*; do
		install -Dm644 $i/nginx.vim "$pkgdir"/usr/share/vim/vimfiles/$i##*//nginx.vim
	done
}
