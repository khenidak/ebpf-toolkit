LINUX_HEADERS		=$(shell rpm -q kernel-devel --last | head -n 1 | awk -F'kernel-devel-' '{print "/usr/src/kernels/"$$2}' | cut -d " " -f 1)
LINUX_CORE_INCLUDE	=$(foreach path,$(LINUX_HEADERS), -I $(path)/arch/x86/include -I $(path)/arch/x86/include/generated -I $(path)/include -I $(path)/include/generated/uapi -I $(path)/arch/x86/include/uapi -I $(path)/include/uapi)

#inputs sources and outputs binaries
SRC_FILES		?=$(wildcard $(SRC_DIR)/*.c)
OUTPUT_NAME		?=ebpf_output.o
OUTPUT_DIR		?=/tmp


define CLANG_FLAGS_INTERNAL =
-D__KERNEL__ \
-D__ASM_SYSREG_H \
-Wno-unused-value \
-Wno-pointer-sign \
-Wno-compare-distinct-pointer-types \
-Wunused \
-Wall \
-Werror \
-O2
endef
CLANG			=clang
CLANG_FLAGS 		?=$(CLANG_FLAGS_INTERNAL)
CLANG_ADD_FLAGS		?=
CLANG_ADD_INCLUDE       =$(foreach add_include,$(CLANG_ADD_INCLUDE), -I add_include)

# clang -target bpf or clang -emit-llvm | llc -march bpf
CLANG_EMIT		=-emit-llvm
CLANG_EMIT_LLC		=llc -march=bpf -filetype=obj -o $(OUTPUT_DIR)/$(OUTPUT_NAME)
CLANG_BPF_TARGET	=-target bpf

CLANG_BUILD_STYLE	?=emit

#command line generation
CLANG_LINE		=$(CLANG) $(CLANG_FLAGS) $(CLANG_ADD_FLAGS)

# are we building for -target or emit?
ifeq ($(CLANG_BUILD_STYLE), emit)
CLANG_LINE+=$(CLANG_EMIT)  $(LINUX_CORE_INCLUDE) -c $(SRC_FILES) -o - | $(CLANG_EMIT_LLC)
else
CLANG_LINE="echo '-target is UNSUPPORTED YET'"
endif

help: ## Show this help.
	@echo "Targets:"
	@fgrep -h "##" $(MAKEFILE_LIST) | fgrep -v fgrep | sed -e 's/\\$$//' | sed -e 's/##//'
	@echo
	@echo -e "Arguments \n\
- SRC_DIR: directory path, where the source files are. default all *.c will be built\n\
- SRC: instead of directory a list of files\n\
- OUTPUT_NAME: ouput file name. default: ebpf_output.o\n\
- OUTPUT_DIR: directory for output. default: /tmp"
	@echo
	@echo -e "Wielding clang arguments. You can:\n\
- make CLANG_FLAGS="--foo=bar" ... --> replaces all clang flags\n\
- make CLANG_ADD_FLAGS="--add_foo=add_bar" ... --> runs with original flags *and* yours\n\
- make CLANG_ADD_INCLUDE="/path/to/my/kickass/code /path/to/my/other/kickass/code" ... --> adds -I *foreach*\n\
- mix and match"


dry-run: ##displays the clang command but will not run it
	@echo "Running Target: $@"
	@echo -e "Will Execute:\n$(CLANG_LINE)"

build: ## Builds ebpf out of c files
	@echo "Running $@"
	@echo "Sources: $(SRC_FILES)"
	$(shell $(CLANG_LINE))
	@echo "Done."
	@echo "Output: $(OUTPUT_DIR)/$(OUTPUT_NAME)"
