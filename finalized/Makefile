# dwm - dynamic window manager
# See LICENSE file for copyright and license details.

include config.mk

SRC = drw.c dwm.c util.c
OBJ = ${SRC:.c=.o}

# FreeBSD users, prefix all ifdef, else and endif statements with a . for this to work (e.g. .ifdef)

ifdef YAJLLIBS
all: options clean dwm dwm-msg
else
all: options dwm
endif

options:
	@echo dwm build options:
	@echo "CFLAGS   = ${CFLAGS}"
	@echo "LDFLAGS  = ${LDFLAGS}"
	@echo "CC       = ${CC}"

.c.o:
	${CC} -c ${CFLAGS} $<

${OBJ}: config.h config.mk

config.h:
	cp config.def.h $@

dwm: ${OBJ}
	${CC} -o $@ ${OBJ} ${LDFLAGS}

ifdef YAJLLIBS
dwm-msg:
	${CC} -o $@ patch/ipc/dwm-msg.c ${LDFLAGS}
endif

clean:
	rm -f dwm ${OBJ} dwm-${VERSION}.tar.gz
	rm -f dwm-msg
	rm -rf patches.h config.h

dist: clean
	mkdir -p dwm-${VERSION}
	cp -R LICENSE Makefile README config.def.h config.mk\
		dwm.1 drw.h util.h ${SRC} dwm.png transient.c dwm-${VERSION}
	tar -cf dwm-${VERSION}.tar dwm-${VERSION}
	gzip dwm-${VERSION}.tar
	rm -rf dwm-${VERSION}


finalizer:
	# should be run from the main flexipatch repository
	set -e ;\
	git_dir=$$(mktemp -dt git.XXX) ;\
	final_dir=$$(mktemp -dt final.XXX) ;\
	cp -rf .git $$final_dir ;\
	rm -rf ./finalized ;\
	make -C . ;\
	./flexipatch-finalizer.sh -r -o $$final_dir ;\
	cp -rf $$git_dir .git ;\
	mkdir finalized ;\
	cp -rf $$final_dir/* finalized

install: all
	mkdir -p ${DESTDIR}${PREFIX}/bin
	cp -f dwm ${DESTDIR}${PREFIX}/bin
ifdef YAJLLIBS
	cp -f dwm-msg ${DESTDIR}${PREFIX}/bin
endif
	chmod 755 ${DESTDIR}${PREFIX}/bin/dwm
ifdef YAJLLIBS
	chmod 755 ${DESTDIR}${PREFIX}/bin/dwm-msg
endif
	mkdir -p ${DESTDIR}${MANPREFIX}/man1
	sed "s/VERSION/${VERSION}/g" < dwm.1 > ${DESTDIR}${MANPREFIX}/man1/dwm.1
	chmod 644 ${DESTDIR}${MANPREFIX}/man1/dwm.1
	mkdir -p ${DESTDIR}${PREFIX}/share/xsessions
	test -f ${DESTDIR}${PREFIX}/share/xsessions/dwm.desktop || cp -n dwm.desktop ${DESTDIR}${PREFIX}/share/xsessions
	chmod 644 ${DESTDIR}${PREFIX}/share/xsessions/dwm.desktop

uninstall:
	rm -f ${DESTDIR}${PREFIX}/bin/dwm\
		${DESTDIR}${MANPREFIX}/man1/dwm.1\
		${DESTDIR}${PREFIX}/share/xsessions/dwm.desktop

.PHONY: all options clean dist install uninstall
