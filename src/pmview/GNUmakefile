TOPDIR = ../..
COMMAND = pmview
PROJECT = $(COMMAND).pro
include $(TOPDIR)/src/include/builddefs

WRAPPER = $(COMMAND).sh
QRCFILE = $(COMMAND).qrc
ICNFILE = $(COMMAND).icns
ICOFILE = $(COMMAND).ico
XMLFILE = $(COMMAND).info
DESKTOP = $(COMMAND).desktop
UIFILES = $(shell echo *.ui)
CLASSES = main.h pmview.h colorlist.h \
	  barmod.h barobj.h baseobj.h \
	  defaultobj.h gridobj.h labelobj.h stackobj.h \
	  text.h viewobj.h pipeobj.h link.h xing.h \
	  scenefileobj.h scenegroup.h \
	  colorscalemod.h colormod.h colorscale.h \
	  metriclist.h modlist.h modulate.h \
	  scalemod.h stackmod.h togglemod.h \
	  yscalemod.h pcpcolor.h launch.h
SOURCES = $(CLASSES:.h=.cpp) error.cpp
HEADERS = $(CLASSES) modobj.h
GENERATED = gram.cpp lex.cpp 
LFILES = lex.l
YFILES = gram.y
LDIRT = $(COMMAND) $(WRAPPER) $(XMLFILE) $(GENERATED) gram.h gram.c images

SUBDIRS = front-ends

default: build-me

include $(BUILDRULES)

ifeq "$(ENABLE_QT3D)" "true"
build-me:: images wrappers $(GENERATED)
	+$(QTMAKE)
	$(LNMAKE)

build-me:: $(SUBDIRS)
	$(SUBDIRS_MAKERULE)

lex.cpp:	lex.l
	$(LEX) -t lex.l > $@

gram.c gram.h:	gram.y
	$(YACC) -d -o `basename $< .y`.c $<

gram.cpp:	gram.c
	cp gram.c $@

lex.o:	gram.h

ifeq ($(WINDOW),mac)
MACBUILD = $(COMMAND).app/Contents
PKG_MAC_DIR = /usr/local/Cellar/pcp/$(PACKAGE_VERSION)/$(COMMAND).app/Contents
wrappers: $(WRAPPER)
else
wrappers:
endif

$(WRAPPER): $(WRAPPER).IN
	$(SED) -e '/\# .*/b' -e 's;PKG_MAC_DIR;$(PKG_MAC_DIR);g' < $< > $@
$(XMLFILE): $(XMLFILE).in
	$(SED) -e 's;PACKAGE_VERSION;$(PACKAGE_VERSION);g' < $< > $@

install: default $(SUBDIRS)
	$(SUBDIRS_MAKERULE)
	$(INSTALL) -m 755 $(BINARY) $(PCP_BIN_DIR)/$(COMMAND)

# skip this stuff for the moment
futures: default
	$(SUBDIRS_MAKERULE)
ifeq ($(WINDOW),win)
	$(INSTALL) -m 755 $(BINARY) $(PCP_BIN_DIR)/$(COMMAND)
endif
ifeq ($(WINDOW),x11)
	$(INSTALL) -m 755 $(BINARY) $(PCP_BIN_DIR)/$(COMMAND)
	$(INSTALL) -m 755 -d $(PCP_DESKTOP_DIR)
	$(INSTALL) -m 644 $(DESKTOP) $(PCP_DESKTOP_DIR)/$(DESKTOP)
endif
ifeq ($(WINDOW),mac)
	$(INSTALL) -m 755 $(WRAPPER) $(PCP_BIN_DIR)/$(COMMAND)
	$(call INSTALL,$(PKG_MAC_DIR),/Applications)
	$(INSTALL) -m 644 $(MACBUILD)/PkgInfo $(PKG_MAC_DIR)/PkgInfo
	$(INSTALL) -m 644 $(XMLFILE) $(PKG_MAC_DIR)/Info.plist
	$(INSTALL) -m 755 -d $(PKG_MAC_DIR)/MacOS
	$(call INSTALL_QT_FRAMEWORKS,$(BINARY))
	$(INSTALL) -m 755 $(BINARY) $(PKG_MAC_DIR)/MacOS/$(COMMAND)
	rm $(BINARY)
	$(INSTALL) -m 755 -d $(PKG_MAC_DIR)/Resources
	$(INSTALL) -m 644 $(ICNFILE) $(PKG_MAC_DIR)/Resources/$(ICNFILE)
	$(call INSTALL_QT_RESOURCES,$(PKG_MAC_DIR)/Resources)
endif

else
build-me:
install:
endif

default_pcp:	default

install_pcp:	install

images: $(ICNFILE)
	$(LN_S) $(TOPDIR)/images images

$(ICNFILE):
	$(LN_S) $(TOPDIR)/images/$(ICNFILE) $(ICNFILE)
