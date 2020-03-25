#-----------------------------------------------------------------------#
#- GLOBAL DEFS ---------------------------------------------------------#
#-----------------------------------------------------------------------#

# Keep this as generic as possible.

NAME=glucose-syrup
VERSION=4.1

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

CXX=g++

CXXFLAGS=-std=c++11 -Wall -DNDEBUG -O3 -fPIC
export CXXFLAGS

#-----------------------------------------------------------------------#
#- REQUIRED TOP RULES --------------------------------------------------#
#-----------------------------------------------------------------------#

all: $(TARGET)

clean:
	rm -rf $(DIR)
	rm -f *.o *.a *.tgz *.dylib

#-----------------------------------------------------------------------#
#- INVISIBLE INTERNAL SUB RULES ----------------------------------------#
#-----------------------------------------------------------------------#

lib$(SIG).dylib: libipasir$(SIG).a ipasir$(NAME)glue.o
	$(CXX) $(CXXFLAGS) -g -I. -dynamiclib -install_name lib$(NAME).dylib -o $@ $^

libipasir$(SIG).a: .FORCE
	@#
	@# get and extract library
	@#
	rm -rf $(DIR) rm *.tgz
	wget "https://www.labri.fr/perso/lsimon/downloads/softwares/$(DIR).tgz"
	tar xvf $(DIR).tgz
	@# apply patch for set_terminate and learn_callback
	@#
	patch $(DIR)/core/Solver.h < glucose-syrup-h.patch
	patch $(DIR)/core/Solver.cc < glucose-syrup-cc.patch
	@#
	@# patch verbose output
	@#
	sed -i -e 's,"\([=|]\),"c [${SIG}] \1,' $(DIR)/core/Solver.cc
	sed -i -e 's,"\([=|]\),"c [${SIG}] \1,' $(DIR)/simp/SimpSolver.cc
	sed -i -e 's,"\([=|]\),"c [${SIG}] \1,' $(DIR)/simp/Main.cc
	@#
	@# configure and build library
	@#
	cd $(DIR)/simp; make -j4 VERBOSE=1 libr
	@#
	@# merge library and glue code into target
	@#
	cp $(DIR)/simp/lib_release.a $@
	@#ar r $@ $(IPASIRGLUEOBJ)

#-----------------------------------------------------------------------#
#- LOCAL GLUE RULES ----------------------------------------------------#
#-----------------------------------------------------------------------#

ipasir$(NAME)glue.o: ipasir$(NAME)glue.cc ipasir.h makefile
	$(CXX) -std=c++11 $(CXXLAGS) \
	  -DVERSION=\"$(VERSION)\" \
	  -I$(DIR) -I$(DIR)/core -c ipasir$(NAME)glue.cc

#-----------------------------------------------------------------------#

.FORCE:
.PHONY: all clean
