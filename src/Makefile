# yaourt: pacman frontend

VERSION       = $(shell git describe --abbrev=4)
DESTDIR       =
PREFIX        = /usr
SHELL         = /bin/bash
INSTALL       = /usr/bin/env install 
MSGFMT        = /usr/bin/msgfmt
SED           = /bin/sed
bindir        = $(PREFIX)/bin
libdir        = $(PREFIX)/lib
sysconfdir    = /etc
completiondir = ${PREFIX}/share/bash-completion/completions
localedir     = $(PREFIX)/share/locale
localstatedir = $(PREFIX)/var
mandir        = $(PREFIX)/share/man
dbpath        = ${localstatedir}/lib/pacman/
cachedir      = ${localstatedir}/cache/pacman/pkg/
aururl        = https://aur.archlinux.org

SCRIPTS = yaourt.sh pacdiffviewer.sh \
		  yaourtrc \
		  lib/util.sh lib/pkgbuild.sh lib/pacman.sh lib/abs.sh

all: $(SCRIPTS)

$(SCRIPTS): ${SCRIPTS:=.in}
	@echo '	GEN' $@
	@$(SED) -e 's|@VERSION@|$(VERSION)|g' \
	        -e 's|@localedir@|$(localedir)|g' \
	        -e 's|@sysconfdir@|$(sysconfdir)|g' \
	        -e 's|@libdir@|$(libdir)|g' \
	        -e 's|@localstatedir@|$(localstatedir)|g' \
	        -e 's|@dbpath@|$(dbpath)|g' \
	        -e 's|@cachedir@|$(cachedir)|g' \
	        -e 's|@aururl@|$(aururl)|g' \
	        $@.in > $@

install: $(SCRIPTS)
	$(INSTALL) -d $(DESTDIR)$(bindir)
	$(INSTALL) -d $(DESTDIR)$(libdir)/yaourt
	$(INSTALL) -d $(DESTDIR)$(sysconfdir)
	$(INSTALL) -d $(DESTDIR)$(completiondir)
	$(INSTALL) -d $(DESTDIR)$(mandir)/man{5,8}
	# Scripts
	$(INSTALL) -m755 yaourt.sh $(DESTDIR)$(bindir)/yaourt
	$(INSTALL) -m755 pacdiffviewer.sh $(DESTDIR)$(bindir)/pacdiffviewer
	# Configuration
	$(INSTALL) -m644 yaourtrc $(DESTDIR)$(sysconfdir)/yaourtrc
	$(INSTALL) -m644 bashcompletion $(DESTDIR)$(completiondir)/yaourt
	# Libs
	$(INSTALL) -m644 lib/alpm_backup.sh $(DESTDIR)$(libdir)/yaourt
	$(INSTALL) -m644 lib/alpm_query.sh $(DESTDIR)$(libdir)/yaourt
	$(INSTALL) -m644 lib/alpm_stats.sh $(DESTDIR)$(libdir)/yaourt
	$(INSTALL) -m644 lib/abs.sh $(DESTDIR)$(libdir)/yaourt
	$(INSTALL) -m644 lib/aur.sh $(DESTDIR)$(libdir)/yaourt
	$(INSTALL) -m644 lib/util.sh $(DESTDIR)$(libdir)/yaourt
	$(INSTALL) -m644 lib/io.sh $(DESTDIR)$(libdir)/yaourt
	$(INSTALL) -m644 lib/pacman.sh $(DESTDIR)$(libdir)/yaourt
	$(INSTALL) -m644 lib/pkgbuild.sh $(DESTDIR)$(libdir)/yaourt
	$(INSTALL) -m644 lib/misc.sh $(DESTDIR)$(libdir)/yaourt
	# Man
	$(INSTALL) -m644 man/*.5 $(DESTDIR)$(mandir)/man5
	$(INSTALL) -m644 man/*.8 $(DESTDIR)$(mandir)/man8
	# Locales
	test -x $(MSGFMT) && for file in po/*/*.po; \
	do \
	  package=$$(echo $$file | $(SED) -e 's#po/\([^/]\+\).*#\1#'); \
	  lang=$$(echo $$file | $(SED) -e 's#.*/\([^/]\+\).po#\1#'); \
	  $(INSTALL) -d $(DESTDIR)$(localedir)/$$lang/LC_MESSAGES; \
	  $(MSGFMT) -o $(DESTDIR)$(localedir)/$$lang/LC_MESSAGES/$$package.mo $$file; \
	done

uninstall:
	# Locales - remove also the directories but only if they are empty
	for file in po/*/*.po; do \
	  package=$$(echo $$file | $(SED) -e 's#po/\([^/]\+\).*#\1#'); \
	  lang=$$(echo $$file | $(SED) -e 's#.*/\([^/]\+\).po#\1#'); \
	  rm -f $(DESTDIR)$(localedir)/$$lang/LC_MESSAGES/$$package.mo; \
	  if [ -d "$(DESTDIR)$(localedir)/$$lang/LC_MESSAGES" ]; then \
	    rmdir --ignore-fail-on-non-empty $(DESTDIR)$(localedir)/$$lang/LC_MESSAGES; \
	  fi; \
	  if [ -d "$(DESTDIR)$(localedir)/$$lang" ]; then \
	    rmdir --ignore-fail-on-non-empty $(DESTDIR)$(localedir)/$$lang; \
	  fi; \
	done
	# Man
	for file in man/*.5; do rm -f $(DESTDIR)$(mandir)/man5/$$(echo $$file | $(SED) -e 's#man/\([^/]\+\).*#\1#'); done
	for file in man/*.8; do rm -f $(DESTDIR)$(mandir)/man8/$$(echo $$file | $(SED) -e 's#man/\([^/]\+\).*#\1#'); done
	# Libs
	if [ -d "$(DESTDIR)$(libdir)/yaourt" ]; then rm $(DESTDIR)$(libdir)/yaourt -R; fi;
	# Configuration
	rm -f $(DESTDIR)$(completiondir)/yaourt;
	rm -f $(DESTDIR)$(sysconfdir)/yaourtrc;
	# Scripts
	rm -f $(DESTDIR)$(bindir)/pacdiffviewer
	rm -f $(DESTDIR)$(bindir)/yaourt
	# Directories (delete only if empty)
	if [ -d "$(DESTDIR)$(mandir)/man5" ]; then rmdir --ignore-fail-on-non-empty $(DESTDIR)$(mandir)/man5; fi;
	if [ -d "$(DESTDIR)$(mandir)/man8" ]; then rmdir --ignore-fail-on-non-empty $(DESTDIR)$(mandir)/man8; fi;
	if [ -d "$(DESTDIR)$(completiondir)" ]; then rmdir --ignore-fail-on-non-empty $(DESTDIR)$(completiondir); fi;
	if [ -d "$(DESTDIR)$(sysconfdir)" ]; then rmdir --ignore-fail-on-non-empty $(DESTDIR)$(sysconfdir); fi;
	if [ -d "$(DESTDIR)$(libdir)/yaourt" ]; then rmdir --ignore-fail-on-non-empty $(DESTDIR)$(libdir)/yaourt; fi;
	if [ -d "$(DESTDIR)$(libdir)" ]; then rmdir --ignore-fail-on-non-empty $(DESTDIR)$(libdir); fi;
	if [ -d "$(DESTDIR)$(bindir)" ]; then rmdir --ignore-fail-on-non-empty $(DESTDIR)$(bindir); fi;

dist:
	mkdir yaourt-$(VERSION) 
	cp -a AUTHORS INSTALL COPYING Makefile pacdiffviewer.sh.in yaourt.sh.in yaourtrc.in \
	  bashcompletion lib man po yaourt-$(VERSION)
	$(SED) -i -e 's|^VERSION.*|VERSION=$(VERSION)|g' yaourt-$(VERSION)/Makefile
	tar zcvf yaourt-$(VERSION).tar.gz yaourt-$(VERSION)
	rm -rf yaourt-$(VERSION)

clean:
	rm -f $(SCRIPTS)
	rm -f yaourt-$(VERSION).tar.gz
	rm -rf yaourt-$(VERSION)


.PHONY: all install clean dist
