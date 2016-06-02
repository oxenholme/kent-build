# Copyright 2016 Helm Solutions Ltd
# 
# This makefile is free software: you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation, either version 3 of the License, or
# (at your option) any later version.
# 
# This makefile is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
# 
# You should have received a copy of the GNU General Public License
# along with this makefile.  If not, see <http://www.gnu.org/licenses/>.

DEBUG_PROGS=$(PROGS:%=target/debug/%)
DEBUG_TESTS=$(TESTS:%=testTarget/debug/%)
DEBUG_LIBS=$(LIBS:%=target/debug/%)

RELEASE_PROGS=$(PROGS:%=target/release/%)
RELEASE_TESTS=$(TESTS:%=testTarget/release/%)
RELEASE_LIBS=$(LIBS:%=target/release/%)

CPPFLAGS+=-std=c++14 -Isrc/main/include -Isrc/main/cpp -I../include



debug-base debug-test:		CXXFLAGS+=-Og -g
release-base release-test:	CXXFLAGS+=-O2 -flto

debug-base debug-test:		LDFLAGS+=-L../debug
release-base release-test:	LDFLAGS+=-L../release

debug-test release-test:        INCLIBS+=-ltest

release-base release-test:	AR=gcc-ar



.PHONY:		debug debug-base debug-test release release-base release-test all includes clean

.PRECIOUS:	$(OBJS:%=target/debug/%) $(OBJS:%=target/release/%) $(TEST_OBJS:%=target/debug/%) $(TEST_OBJS:%=target/release/%)



debug:			debug-base debug-test

debug-base:		$(DEBUG_LIBS) $(DEBUG_PROGS)

debug-test:		$(DEBUG_TESTS)

release:		release-base release-test

release-base:	$(RELEASE_LIBS) $(RELEASE_PROGS)

release-test:	$(RELEASE_TESTS)

all:			debug release install

install:
	if [ -d src/main/include ]; then cp -r src/main/include ..; fi
	if [ -f "$(DEBUG_LIBS)" ]; then mkdir -p ../debug; cp -r $(DEBUG_LIBS) ../debug; fi
	if [ -f "$(DEBUG_PROGS)" ]; then mkdir -p ../debug; cp -r $(DEBUG_PROGS) ../debug; fi
	if [ -f "$(RELEASE_LIBS)" ]; then mkdir -p ../release; cp -r $(RELEASE_LIBS) ../release; fi
	if [ -f "$(RELEASE_PROGS)" ]; then mkdir -p ../release; cp -r $(RELEASE_PROGS) ../release; fi

clean:
	rm -rf target testTarget



$(DEBUG_PROGS):		$(OBJS:%=target/debug/%)
	mkdir -p `dirname $@`; \
	$(LINK.cpp) -o $@ $(OBJS:%=target/debug/%) $(INCLIBS)

$(DEBUG_LIBS):		$(OBJS:%=target/debug/%)
	mkdir -p `dirname $@`; \
	$(AR) rs $@ $(OBJS:%=target/debug/%)

$(DEBUG_TESTS):		$(TEST_OBJS:%=testTarget/debug/%) $(DEBUG_LIBS)
	mkdir -p `dirname $@`; \
	$(LINK.cpp) -o $@ $(TEST_OBJS:%=testTarget/debug/%) $(DEBUG_LIBS) $(INCLIBS)
	$@

$(RELEASE_PROGS):	$(OBJS:%=target/release/%)
	mkdir -p `dirname $@`; \
	$(LINK.cpp) -o $@ $(OBJS:%=target/release/%) $(INCLIBS)

$(RELEASE_LIBS):	$(OBJS:%=target/release/%)
	mkdir -p `dirname $@`; \
	$(AR) rs $@ $(OBJS:%=target/release/%)

$(RELEASE_TESTS):	$(TEST_OBJS:%=testTarget/release/%) $(RELEASE_LIBS)
	mkdir -p `dirname $@`; \
	$(LINK.cpp) -o $@ $(TEST_OBJS:%=testTarget/release/%) $(RELEASE_LIBS) $(INCLIBS)
	$@



target/debug/%.o		::	src/main/cpp/%.cpp target/%.dep
	mkdir -p `dirname $@`; \
	$(COMPILE.cpp) $(OUTPUT_OPTION)  $<

testTarget/debug/%.o	::	src/test/cpp/%.cpp testTarget/%.dep
	mkdir -p `dirname $@`; \
	$(COMPILE.cpp) $(OUTPUT_OPTION)  $<

target/release/%.o		::	src/main/cpp/%.cpp target/%.dep
	mkdir -p `dirname $@`; \
	$(COMPILE.cpp) $(OUTPUT_OPTION)  $<

testTarget/release/%.o	::	src/test/cpp/%.cpp testTarget/%.dep
	mkdir -p `dirname $@`; \
	$(COMPILE.cpp) $(OUTPUT_OPTION)  $<



target/%.dep	:	src/main/cpp/%.cpp
	mkdir -p `dirname $@`; \
	set -e; \
	$(CC) -MM $(CPPFLAGS) $< > $@.$$$$; \
	sed "s,^`basename $@ .dep`.o: ,$@: ," < $@.$$$$ > $@; \
	rm -f $@.$$$$

testTarget/%.dep	:	src/test/cpp/%.cpp
	mkdir -p `dirname $@`; \
	set -e; \
	$(CC) -MM $(CPPFLAGS) $< > $@.$$$$; \
	sed "s,^`basename $@ .dep`.o: ,$@: ," < $@.$$$$ > $@; \
	rm -f $@.$$$$



include $(OBJS:%.o=target/%.dep)
include $(TEST_OBJS:%.o=testTarget/%.dep)

