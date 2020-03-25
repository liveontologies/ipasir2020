#-----------------------------------------------------------------------#
#- GLOBAL DEFS ---------------------------------------------------------#
#-----------------------------------------------------------------------#

# Keep this as generic as possible.

NAME=cryptominisat
VERSION=5.6.8

#-----------------------------------------------------------------------#
# Solver signatures have to be both valid file names and C symbols.
# Since Picosat uses a dash '-' for the package name, we have to
# differentiate between with (directory / package name) and without
# dash (the signature).

SIG=$(NAME)$(VERSION)
DIR=$(NAME)-$(VERSION)
#TARGET=libipasir$(SIG).a
TARGET=lib$(SIG).dylib

#-----------------------------------------------------------------------#

CC=g++
CFLAGS=-Wall -DNDEBUG -O3 -fPIC
export CFLAGS

#-----------------------------------------------------------------------#
#- REQUIRED TOP RULES --------------------------------------------------#
#-----------------------------------------------------------------------#

all: $(TARGET)

clean:
	rm -rf $(DIR)
	rm -f *.o *.a *.tar.gz *.dylib

#-----------------------------------------------------------------------#
#- INVISIBLE INTERNAL SUB RULES ----------------------------------------#
#-----------------------------------------------------------------------#

IPASIRGLUEOBJ=$(DIR)/build/cmsat5-src/CMakeFiles/ipasircryptominisat5.dir/ipasir.cpp.o

lib$(SIG).dylib: libipasir$(SIG).a $(IPASIRGLUEOBJ)
	$(CC) $(CFLAGS) -g -I. -dynamiclib -install_name lib$(NAME).dylib -o $@ $^

libipasir$(SIG).a: .FORCE
	@#
	@# get and extract library
	@#
	rm -rf $(DIR) rm *.tar.gz
	wget "https://github.com/msoos/$(NAME)/archive/$(VERSION).tar.gz"
	tar xvf $(VERSION).tar.gz
	@#
	@# configure and build library
	@#
	cd $(DIR); ../build.sh
	@#
	@# merge library and glue code into target
	@#
	cp $(DIR)/build/lib/libcryptominisat5.a $@
	ar r $@ $(IPASIRGLUEOBJ)

#-----------------------------------------------------------------------#

.FORCE:
.PHONY: all clean
