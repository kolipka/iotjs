BUILD_TYPE ?= release

# For Nuttx target
NUTTX_ROOT=$(CURDIR)/deps/nuttx
export NUTTX_HOME=$(NUTTX_ROOT)/nuttx
NUTTX_BUILD_TYPE=1
ifneq ($(BUILD_TYPE),release)
  NUTTX_BUILD_TYPE=0
endif

# For Tizen target
export TIZEN_ROOTSTRAP=$(CURDIR)/deps/tizen

.PHONY: DEPS NUTTX_DEPS nuttx ARM_DEPS arm tizen

# Common deps
DEPS:
	sudo apt-get update -q
	sudo apt-get install -q -y cmake g++ valgrind

NUTTX_DEPS: DEPS
	sudo apt-get install -q -y \
		autoconf libtool gperf flex bison autoconf2.13 \
		cmake libncurses-dev libusb-1.0-0-dev \
		libsgutils2-dev gcc-arm-none-eabi

# prepare Nuttx home folder (Nuttx sysroot)
$(NUTTX_HOME):
	mkdir -p $(NUTTX_ROOT)
	git clone https://bitbucket.org/nuttx/nuttx.git $(NUTTX_HOME)
	git clone https://bitbucket.org/nuttx/apps.git $(NUTTX_ROOT)/apps

# build Nuttx dependency
nuttx: NUTTX_DEPS $(NUTTX_HOME)
	echo "nuttx"
	mkdir -p $(NUTTX_ROOT)/apps/system/iotjs
	cp -r ./targets/nuttx-stm32f4/app/ $(NUTTX_ROOT)/apps/system/iotjs/
	cd $(NUTTX_HOME)/tools && ./configure.sh stm32f4discovery/usbnsh
	cp ./targets/nuttx-stm32f4/nuttx/.config.travis .config
	make -C $(NUTTX_HOME) IOTJS_ROOT_DIR=$(CURDIR) R=$(NUTTX_BUILD_TYPE)


armhf: DEPS
	echo "Job armhf"
	sudo apt-get install -q -y g++-arm-linux-gnueabihf libc6-dev-armhf-cross

arm: DEPS
	sudo apt-get install -q -y g++-arm-linux-gnueabi

tizen: arm
	echo "Job Tizen"
	mkdir -p $(TIZEN_ROOTSTRAP)
	git clone https://github.com/pmarcinkiew/tizen3.0_rootstrap $(TIZEN_ROOTSTRAP)
	export OPTS="$(OPTS) --sysroot=$(TIZEN_ROOTSTRAP)"