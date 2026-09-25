SRC_NAME = nano-5.9
SRC_URL = https://www.nano-editor.org/dist/v5/nano-5.9.tar.gz
THISDIR = $(shell pwd)

all: download_test extract_test config_test
	$(MAKE) -j$(HOST_NCPU) -C $(SRC_NAME)

download_test:
	( if [ ! -f $(SRC_NAME).tar.gz ]; then \
		wget -t5 --timeout=20 --no-check-certificate -O $(SRC_NAME).tar.gz $(SRC_URL); \
	fi )

extract_test:
	( if [ ! -d $(SRC_NAME) ]; then \
		tar -xf $(SRC_NAME).tar.gz; \
	fi )

config_test:
	( if [ -f ./config_done ]; then \
		echo "the same configuration"; \
	else \
		make configure && touch config_done; \
	fi )

# uClibc's <sys/random.h> is unusable here: it uses size_t without including
# <stddef.h>, so gnulib's generated lib/sys/random.h fails with
# "unknown type name 'size_t'", and it declares getrandom() as returning int
# while this libc does not provide the function at all (configure reports
# "checking for getrandom... no").  Ignore both so that gnulib declares and
# implements getrandom() itself, reading from /dev/urandom.
configure:
	( cd $(SRC_NAME) ; \
	./configure \
		--prefix=/usr \
		--enable-tiny \
		--enable-utf8 \
		--disable-nls \
		PKG_CONFIG_LIBDIR="$(STAGEDIR)/lib/pkgconfig/" \
		--host=$(HOST_TARGET) \
		--build=$(HOST_BUILD) \
		ac_cv_header_sys_random_h=no \
		ac_cv_func_getrandom=no ; \
	)

clean:
	if [ -f $(SRC_NAME)/Makefile ] ; then \
		$(MAKE) -C $(SRC_NAME) distclean ; \
	fi ; \
	rm -f config_done

romfs:
	$(ROMFSINST) -p +x $(THISDIR)/$(SRC_NAME)/src/nano /usr/bin/nano
