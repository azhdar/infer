# Copyright (c) 2016 - present Facebook, Inc.
# All rights reserved.
#
# This source code is licensed under the BSD style license found in the
# LICENSE file in the root directory of this source tree. An additional grant
# of patent rights can be found in the PATENTS file in the same directory.

TESTS_DIR = ../..

CMAKE_DIR = ../codetoanalyze/cmake
CMAKE_BUILD_DIR = $(CMAKE_DIR)/_build
CMAKE_ANALYZE_DIR = $(CMAKE_DIR)/_build_infer

ANALYZER = infer
CLEAN_EXTRA = $(CMAKE_BUILD_DIR) $(CMAKE_ANALYZE_DIR)
INFER_OPTIONS = --project-root $(TESTS_DIR) --no-failures-allowed
SOURCES = $(CMAKE_DIR)/hello.c
OBJECTS = $(CMAKE_BUILD_DIR)/CMakeFiles/Hello.dir/hello.c.o
INFERPRINT_OPTIONS = --issues-tests

include $(TESTS_DIR)/infer.make

$(CMAKE_BUILD_DIR)/Makefile: $(CMAKE_DIR)/CMakeLists.txt
	$(QUIET)$(MKDIR_P) $(CMAKE_BUILD_DIR)
	$(QUIET)cd $(CMAKE_BUILD_DIR) && \
	$(CMAKE) ..

$(OBJECTS): $(SOURCES) $(CMAKE_BUILD_DIR)/Makefile
	cd $(CMAKE_BUILD_DIR) && \
	$(MAKE)

infer-out/report.json: $(SOURCES) $(CMAKE_DIR)/CMakeLists.txt $(CLANG_DEPS)
#	nuke the build dir and recreate everything to avoid stale compilation/analysis results
	$(QUIET)$(REMOVE_DIR) $(CMAKE_ANALYZE_DIR)
	$(QUIET)$(MKDIR_P) $(CMAKE_ANALYZE_DIR)
	$(QUIET)cd $(CMAKE_ANALYZE_DIR) && \
	$(call silent_on_success,Testing CMake integration: Generate Makefiles,\
	  $(INFER_BIN) compile $(INFER_OPTIONS) -- cmake ..)
	$(QUIET)$(call silent_on_success,Testing CMake integration: Run Makefiles,\
	  $(INFER_BIN) -a $(ANALYZER) $(INFER_OPTIONS) -- $(MAKE) -C $(CMAKE_ANALYZE_DIR))
#	cmake makes all paths absolute
	$(QUIET)sed -i -e 's#$(abspath $(TESTS_DIR))/##g' $@
