OS := $(shell uname)

GREEN := \033[0;32m
RED := \033[0;31m
NC := \033[0m # No Color
ifeq ($(findstring bash, $(shell readlink $(SHELL))), bash)
ECHO = echo -e
else
ECHO = echo
endif

GIT_VERSION := $(shell git describe --tags --dirty=-Dev)
ARM_GCC_TOOLCHAIN = ../../tools/gcc-arm-none-eabi-10.3-2021.10/bin

CROSS_COMPILE = $(ARM_GCC_TOOLCHAIN)/arm-none-eabi-
.config: beken378/app/config/sys_config.h
	@echo 'GEN  .config'
	@-rm -f $@
	@echo '#include "beken378/app/config/sys_config.h"' > config.c
	@sed -n '/^#define/p' beken378/app/config/sys_config.h | awk '{print $$2}' | sort -d | uniq | awk '{print "valueOf_"$$1"="$$1}' >> config.c
	@echo "# Autogenerated by Makefile, DON'T EDIT" > .config
	@$(ARM_GCC_TOOLCHAIN)/arm-none-eabi-gcc -E -I ../../../sdk/include/ config.c | grep '^valueOf_' | sed 's/valueOf_//' >> .config
	@sed -i '/_SYS_CONFIG_H_/d' .config
	@-rm -f config.c

.PHONY: mp
mp: .config
	@$(MAKE) -f application.mk CFG_GIT_VERSION=\"$(GIT_VERSION)\"
	
all: .config	
	@$(MAKE) -f application.mk CFG_GIT_VERSION=\"$(GIT_VERSION)\"
	
.PHONY: beken
beken: .config
	@$(MAKE) -f application.mk application

.PHONY: clean
clean:
	@$(MAKE) -f application.mk clean
	
.PHONY: flash debug ramdebug setup
setup:
	@$(MAKE) -f application.mk $(MAKECMDGOALS)

flash: toolchain
	@$(MAKE) -f application.mk flashburn
	
debug: toolchain
	@$(MAKE) -f application.mk debug	

ramdebug: toolchain
	@$(MAKE) -f application.mk ramdebug	
