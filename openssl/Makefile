#
# CDDL HEADER START
#
# The contents of this file are subject to the terms of the
# Common Development and Distribution License (the "License").
# You may not use this file except in compliance with the License.
#
# You can obtain a copy of the license at usr/src/OPENSOLARIS.LICENSE
# or http://www.opensolaris.org/os/licensing.
# See the License for the specific language governing permissions
# and limitations under the License.
#
# When distributing Covered Code, include this CDDL HEADER in each
# file and include the License file at usr/src/OPENSOLARIS.LICENSE.
# If applicable, add the following below this CDDL HEADER, with the
# fields enclosed by brackets "[]" replaced with your own identifying
# information: Portions Copyright [yyyy] [name of copyright owner]
#
# CDDL HEADER END
#
# Copyright (c) 2009, 2010, Oracle and/or its affiliates. All rights reserved.
# Copyright (c) 2012, Joyent, Inc.  All rights reserved.
#

VERSION=0.9.8x
BASE=$(PWD)
DESTDIR=$(BASE)/../proto

include Makefile.com

MANDIR = $(DESTDIR)/usr/share/man

CONFIG_STATUS = config.status
    
all: all32 all64

all32: $(VER)/$(CONFIG_STATUS)
	cd $(VER); env - PATH=$(PATH) \
	"CFLAGS=$(CFLAGS)" \
	"LDFLAGS=$(LDFLAGS)" \
	gmake 

all64: $(VER64)/$(CONFIG_STATUS)
	cd $(VER64); env - PATH=$(PATH) \
	"CFLAGS=$(CFLAGS)" \
	"LDFLAGS=$(LDFLAGS)" \
	gmake 

# opensslconf.h is patched to ensure suitability for both 64bit and 32bit
$(VER)/$(CONFIG_STATUS): $(VER)/configure
	cd $(VER); env - PATH=$(PATH) \
	ksh93 ./Configure $(CONFIGURE_OPTIONS) 
	gpatch -p1 $(VER)/crypto/opensslconf.h opensslconf.patch
	cd $(VER); gmake  depend
	touch $@

$(VER64)/$(CONFIG_STATUS): $(VER)/configure
	cd $(VER64); env - PATH=$(PATH) \
	ksh93 ./Configure $(CONFIGURE_OPTIONS64)
	cd $(VER64); gmake  depend
	touch $@

install: all install32 install64

install32: all32
	MACH32=i386 BASE=$(BASE) DESTDIR=$(DESTDIR) VERDIR=$(VER) VERSION=$(VERSION) ksh93 ./install-sfw

install64: all64
	MACH64=amd64 VERDIR=$(VER64) BASE=$(BASE) DESTDIR=$(DESTDIR) \
	    VERSION=$(VERSION) ksh93 ./install-sfw-64

$(VER)/configure: $(BVER)-src
	cp -r $(BVER)-src $(VER) 
	( cd $(VER) ; gpatch -p1 < ../configure.patch )
	( cd $(VER) ; gpatch -p1 < ../aesni.patch )
	cp engine_pkcs11/* $(VER)/crypto/engine/
	touch $(VER)/configure
	cp -r  $(VER) $(VER64)

clean:
	-rm -rf $(VER) $(VER64) $(VER)strap $(VER64)strap $(VER)strap-64
