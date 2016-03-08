SH     = $(shell which sh)
PWD    = $(shell pwd)

# Varan
VX     = $(shell which vx)

# Compilers
GCC    = $(shell which gcc)
CLANG  = $(shell which clang)

# For debugging
STRIP  = /usr/bin/true
CFLAGS = -O0 -g

# Where each version expects to be installed
INSTALL_DIR       = $(PWD)/install
INSTALL_BUILD_DIR = $(INSTALL_DIR)/mutt

# Where different versions are installed
INSTALL_GCC        = $(INSTALL_DIR)/gcc-system
INSTALL_GCC_ASAN   = $(INSTALL_DIR)/gcc-asan-system
INSTALL_CLANG      = $(INSTALL_DIR)/clang-system
INSTALL_CLANG_ASAN = $(INSTALL_DIR)/clang-asan-system

# Binaries inside each build directory
MUTT_GCC        = $(INSTALL_GCC)/bin/mutt
MUTT_GCC_ASAN   = $(INSTALL_GCC_ASAN)/bin/mutt
MUTT_CLANG      = $(INSTALL_CLANG)/bin/mutt
MUTT_CLANG_ASAN = $(INSTALL_CLANG_ASAN)/bin/mutt

# Script to run MUTT with Varan
MUTT_DIR = $(PWD)/bin
MUTT     = $(MUTT_DIR)/mutt

# Command lines to run MUTT with Varan inside script
RUN_GCC_GCC      = ASAN_OPTIONS="handle_segv=0" $(VX) $(MUTT_GCC) $(MUTT_GCC)      -- \$$@
RUN_GCC_GCC_ASAN = ASAN_OPTIONS="handle_segv=0" $(VX) $(MUTT_GCC) $(MUTT_GCC_ASAN) -- \$$@
RUN_CLANG_CLANG      = ASAN_OPTIONS="handle_segv=0" $(VX) $(MUTT_CLANG) $(MUTT_CLANG)      -- \$$@
RUN_CLANG_CLANG_ASAN = ASAN_OPTIONS="handle_segv=0" $(VX) $(MUTT_CLANG) $(MUTT_CLANG_ASAN) -- \$$@

# Main target
all: vx-gcc-gcc_asan

# ==============================================================================
# GCC targets
# ==============================================================================

# Set up script for native leader and native follower built with gcc
vx-gcc-gcc: $(MUTT_GCC)
	rm -rf $(INSTALL_BUILD_DIR) || true
	cp -r $(INSTALL_GCC) $(INSTALL_BUILD_DIR)
	rm -rf $(MUTT_DIR) || true
	mkdir -p $(MUTT_DIR)
	echo "#!$(SH)"        >  $(MUTT)
	echo "$(RUN_GCC_GCC)" >> $(MUTT)
	chmod a+x $(MUTT)

# Set up script for native leader and sanitized follower built with gcc
vx-gcc-gcc_asan: $(MUTT_GCC) $(MUTT_GCC_ASAN)
	rm -rf $(INSTALL_BUILD_DIR) || true
	cp -r $(INSTALL_GCC) $(INSTALL_BUILD_DIR)
	rm -rf $(MUTT_DIR) || true
	mkdir -p $(MUTT_DIR)
	echo "#!$(SH)"             >  $(MUTT)
	echo "$(RUN_GCC_GCC_ASAN)" >> $(MUTT)
	chmod a+x $(MUTT)

# Build using gcc and no sanitization
$(MUTT_GCC):
	rm -rf $(INSTALL_BUILD_DIR) || true
	make -C .. clean || true
	cd .. ; CC=$(GCC)         \
  	CFLAGS="$(CFLAGS)"      \
  	STRIP="$(STRIP)"        \
  	./configure             \
  	--prefix=$(INSTALL_BUILD_DIR)
	make -C ..
	make -C .. install
	rm -rf $(INSTALL_GCC) || true
	mv $(INSTALL_BUILD_DIR) $(INSTALL_GCC)

# Build using gcc and sanitization
$(MUTT_GCC_ASAN):
	rm -rf $(INSTALL_BUILD_DIR) || true
	make -C .. clean || true
	cd .. ; CC=$(GCC)                       \
  	CFLAGS="$(CFLAGS) -fsanitize=address" \
  	LDFLAGS="-fsanitize=address"          \
  	STRIP="$(STRIP)"                      \
  	./configure                           \
  	--prefix=$(INSTALL_BUILD_DIR)
	make -C ..
	make -C .. install
	rm -rf $(INSTALL_GCC_ASAN) || true
	mv $(INSTALL_BUILD_DIR) $(INSTALL_GCC_ASAN)

# ==============================================================================
# CLANG targets
# ==============================================================================

# Set up script for native leader and native follower built with clang
vx-clang-clang: $(MUTT_CLANG)
	rm -rf $(INSTALL_BUILD_DIR) || true
	cp -r $(INSTALL_CLANG) $(INSTALL_BUILD_DIR)
	rm -rf $(MUTT_DIR) || true
	mkdir -p $(MUTT_DIR)
	echo "#!$(SH)"            >  $(MUTT)
	echo "$(RUN_CLANG_CLANG)" >> $(MUTT)
	chmod a+x $(MUTT)

# Set up script for native leader and sanitized follower built with clang
vx-clang-clang_asan: $(MUTT_CLANG) $(MUTT_CLANG_ASAN)
	rm -rf $(INSTALL_BUILD_DIR) || true
	cp -r $(INSTALL_CLANG) $(INSTALL_BUILD_DIR)
	rm -rf $(MUTT_DIR) || true
	mkdir -p $(MUTT_DIR)
	echo "#!$(SH)"                 >  $(MUTT)
	echo "$(RUN_CLANG_CLANG_ASAN)" >> $(MUTT)
	chmod a+x $(MUTT)

# Build using clang and no sanitization
$(MUTT_CLANG):
	rm -rf $(INSTALL_BUILD_DIR) || true
	make -C .. clean || true
	cd .. ; CC=$(CLANG)       \
  	CFLAGS="$(CFLAGS)"      \
  	STRIP="$(STRIP)"        \
  	./configure             \
  	--prefix=$(INSTALL_BUILD_DIR)
	make -C ..
	make -C .. install
	rm -rf $(INSTALL_CLANG) || true
	mv $(INSTALL_BUILD_DIR) $(INSTALL_CLANG)

# Build using clang and sanitization
$(MUTT_CLANG_ASAN):
	rm -rf $(INSTALL_BUILD_DIR) || true
	make -C .. clean || true
	cd .. ; CC=$(CLANG)                     \
  	CFLAGS="$(CFLAGS) -fsanitize=address" \
  	LDFLAGS="-fsanitize=address"          \
  	STRIP="$(STRIP)"                      \
  	./configure                           \
  	--prefix=$(INSTALL_BUILD_DIR)
	make -C ..
	make -C .. install
	rm -rf $(INSTALL_CLANG_ASAN) || true
	mv $(INSTALL_BUILD_DIR) $(INSTALL_CLANG_ASAN)

# ==============================================================================
# misc targets
# ==============================================================================

clean:
	make -C .. clean || true
	rm -rf $(INSTALL_DIR) || true
	rm -rf $(MUTT_DIR) || true
