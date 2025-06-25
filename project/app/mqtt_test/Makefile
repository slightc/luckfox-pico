
ifeq ($(APP_PARAM), )
    APP_PARAM:=../Makefile.param
    include $(APP_PARAM)
endif

export LC_ALL=C
SHELL:=/bin/bash

CURRENT_DIR := $(shell pwd)

PKG_NAME := mqtt_test
PKG_BIN ?= out
PKG_BUILD ?= build

# debug: build cmake with more message
# PKG_CONF_OPTS += -DCMAKE_VERBOSE_MAKEFILE=ON
#
ifneq ($(RK_BUILD_VERSION_TYPE), DEBUG)
PKG_CONF_OPTS += -DCMAKE_BUILD_TYPE=Release
# PKG_CONF_OPTS += -DCMAKE_BUILD_TYPE=RelWithDebInfo
# PKG_CONF_OPTS += -DCMAKE_BUILD_TYPE=MinSizeRel
else
PKG_CONF_OPTS += -DCMAKE_BUILD_TYPE=Debug
endif
#

RK_APP_CFLAGS = -I $(RK_APP_MEDIA_INCLUDE_PATH) \
				-I $(RK_APP_MEDIA_INCLUDE_PATH)/libdrm \
				-I $(RK_APP_MEDIA_INCLUDE_PATH)/rkaiq \
				-I $(RK_APP_MEDIA_INCLUDE_PATH)/rkaiq/uAPI \
				-I $(RK_APP_MEDIA_INCLUDE_PATH)/rkaiq/uAPI2 \
				-I $(RK_APP_MEDIA_INCLUDE_PATH)/rkaiq/algos \
				-I $(RK_APP_MEDIA_INCLUDE_PATH)/rkaiq/common \
				-I $(RK_APP_MEDIA_INCLUDE_PATH)/rkaiq/xcore \
				-I $(RK_APP_MEDIA_INCLUDE_PATH)/rkaiq/iq_parser \
				-I $(RK_APP_MEDIA_INCLUDE_PATH)/rkaiq/iq_parser_v2 \
				-I $(RK_APP_OUTPUT)/include \
				-I $(RK_APP_OUTPUT)/include/freetype2 \
				-I $(RK_APP_MEDIA_INCLUDE_PATH)/easymedia

RK_APP_LDFLAGS = -L $(RK_APP_MEDIA_LIBS_PATH) -L$(RK_APP_OUTPUT)/lib

RK_APP_OPTS += -Wl,-rpath-link,$(RK_APP_MEDIA_LIBS_PATH):$(RK_APP_PATH_LIB_INCLUDE)/root/usr/lib:$(RK_APP_OUTPUT)/lib
PKG_CONF_OPTS += -DCMAKE_C_FLAGS="$(RK_APP_CFLAGS) $(RK_APP_LDFLAGS) $(RK_APP_OPTS)" \
				-DCMAKE_CXX_FLAGS="$(RK_APP_CFLAGS) $(RK_APP_LDFLAGS) $(RK_APP_OPTS)"

PKG_CONF_OPTS += -DRKIPC_CROSS_COMPILE="$(RK_APP_CROSS)"

PKG_TOOLCHAIN_SYSROOT := $(shell $(RK_APP_CROSS)-gcc -print-sysroot)
PKG_CONF_OPTS += -DCMAKE_FIND_ROOT_PATH="$(CURRENT_DIR)/$(PKG_BIN);$(RK_APP_OUTPUT);$(PKG_TOOLCHAIN_SYSROOT)"
PKG_CONF_OPTS += -DCMAKE_SYSROOT="$(PKG_TOOLCHAIN_SYSROOT)"

PKG_CONF_OPTS += -DCMAKE_FIND_ROOT_PATH_MODE_PROGRAM=NEVER
PKG_CONF_OPTS += -DCMAKE_FIND_ROOT_PATH_MODE_PACKAGE=ONLY
PKG_CONF_OPTS += -DCMAKE_FIND_ROOT_PATH_MODE_LIBRARY=ONLY
PKG_CONF_OPTS += -DCMAKE_FIND_ROOT_PATH_MODE_INCLUDE=ONLY
PKG_CONF_OPTS += -DCMAKE_NO_SYSTEM_FROM_IMPORTED=ON
PKG_CONF_OPTS += -DCMAKE_SYSTEM_NAME=Linux


ifeq ($(PKG_BIN),)
$(error ### $(CURRENT_DIR): PKG_BIN is NULL, Please Check !!!)
endif

# PKG_TARGET += mqtt-test-build

all: $(PKG_TARGET)
	@echo "build $(PKG_NAME) done"

mqtt-test-build: mqtt-build
	@echo "RK_APP_CHIP is $(RK_APP_CHIP)"
	@echo "RK_APP_TYPE is $(RK_APP_TYPE)"
	rm -rf $(PKG_BIN) $(PKG_BUILD); \
	mkdir -p $(PKG_BIN);
	mkdir -p $(PKG_BUILD);
	pushd $(PKG_BUILD)/; \
		rm -rf CMakeCache.txt; \
		cmake $(CURRENT_DIR)/$(PKG_NAME)/ \
			-DCMAKE_C_COMPILER=$(RK_APP_CROSS)-gcc \
			-DCMAKE_CXX_COMPILER=$(RK_APP_CROSS)-g++ \
			-DCMAKE_INSTALL_PREFIX="$(CURRENT_DIR)/$(PKG_BIN)" \
			-DCMAKE_COLOR_MAKEFILE=OFF \
			-DCMAKE_SYSTEM_NAME=Linux \
			$(RK_RKIPC_CONFIG) \
			$(PKG_CONF_OPTS) ;\
			make -j$(RK_APP_JOBS) || exit -1; \
			make install; \
	popd;
	$(call MAROC_COPY_PKG_TO_APP_OUTPUT, $(RK_APP_OUTPUT), $(PKG_BIN))

clean:
	@rm -rf $(PKG_BIN) $(PKG_BUILD)

distclean: clean

mqtt-build:
	@test ! -d $(RK_APP_COMPONENT)/mqtt || make -C $(RK_APP_COMPONENT)/mqtt
