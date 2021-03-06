PKG_NAME    := libfixmath
PKG_VERSION := 91
PKG_BRANCH  := trunk
PKG_URL     := http://libfixmath.googlecode.com/svn/$(PKG_BRANCH)/

CHECKOUT_FOLDER := $(CURDIR)/checkout/$(PKG_BRANCH)-r$(PKG_VERSION)

.PHONY: all clean distclean

all: $(BINDIR)$(PKG_NAME).a

all-unittests: $(BINDIR)$(PKG_NAME)-unittests.a

ifneq (,$(filter libfixmath-unittests,$(USEMODULE)))
  all: all-unittests
endif

$(BINDIR)$(PKG_NAME).a: $(BINDIR)$(PKG_NAME)-src/Makefile $(BINDIR)$(PKG_NAME)-headers/fix16.h
	"$(MAKE)" -C $(<D)

$(BINDIR)$(PKG_NAME)-unittests.a: $(BINDIR)$(PKG_NAME)-unittests-src/Makefile $(BINDIR)$(PKG_NAME)-headers/fix16.h
	"$(MAKE)" -C $(<D)

$(BINDIR)$(PKG_NAME)-src/Makefile: $(CHECKOUT_FOLDER)/svn_info.xml
	@rm -rf $(@D)
	@mkdir -p $(@D)
	$(AD)cp $(CURDIR)/Makefile.template $@
	$(AD)cp $(CHECKOUT_FOLDER)/libfixmath/*.[ch] $(@D)
	$(AD)rm -f $(BINDIR)$(PKG_NAME)-src/fix16.h

	cd $(@D) && sed -i -e "s/1 <</(uint32_t) 1 <</g" uint32.c

$(BINDIR)$(PKG_NAME)-unittests-src/Makefile:
	@rm -rf $(@D)
	@mkdir -p $(@D)
	$(AD)cp $(CURDIR)/Makefile.template-unittests $@
	$(AD)cp $(CHECKOUT_FOLDER)/unittests/*.[ch] $(@D)

	$(AD)cd $(@D) && for C_FILE in *.[ch]; do \
		sed -e "s/int main()/int $$(basename $${C_FILE} .c)(void)/" \
		    -e '/fflush/d' \
		    -e 's/fprintf(std[^,]*,/printf(/' \
		    -i $${C_FILE}; \
	done

$(BINDIR)$(PKG_NAME)-headers/fix16.h: $(CHECKOUT_FOLDER)/svn_info.xml
	@rm -rf $(@D)
	@mkdir -p $(@D)
	$(AD)cp $(CHECKOUT_FOLDER)/libfixmath/fix16.h $(@D)
	@echo $(patsubst %,'extern int %(void);',$(shell basename -a -s .c $(CHECKOUT_FOLDER)/unittests/*.c)) \
	      $(patsubst %,'%();',$(shell basename -a -s .c $(CHECKOUT_FOLDER)/unittests/*.c)) | sed -e 's/;\s*/;\n/g' > $(@D)/fix16_unittests.inc

$(CHECKOUT_FOLDER)/svn_info.xml:
	@mkdir -p $(@D)
	svn checkout -r $(PKG_VERSION) $(PKG_URL) $(@D)
	@svn info --xml $(@D) > $@

clean::
	rm -rf $(BINDIR)$(PKG_NAME)-src/ $(BINDIR)$(PKG_NAME)-headers/

distclean:: clean
	rm -rf $(CHECKOUT_FOLDER)

Makefile.include:
	@true
