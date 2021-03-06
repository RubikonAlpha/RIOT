APPLICATION = unittests
include ../Makefile.tests_common

BOARD_INSUFFICIENT_MEMORY := airfy-beacon chronos msb-430 msb-430h pca10000 \
                          pca10005 spark-core stm32f0discovery \
                          telosb wsn430-v1_3b wsn430-v1_4 z1 nucleo-f103 \
                          nucleo-f334 yunjia-nrf51822 samr21-xpro \
                          arduino-mega2560 airfy-beacon nrf51dongle nrf6310 weio

USEMODULE += embunit

ifeq (, $(filter tests-%, $(MAKECMDGOALS)))
    UNIT_TESTS := $(foreach d,$(wildcard tests-*/Makefile),$(shell dirname $(d)))
else
    UNIT_TESTS := $(filter tests-%, $(MAKECMDGOALS))
endif

AVR_BOARDS := arduino-mega2560
DISABLE_TEST_FOR_AVR := tests-relic

MSP430_BOARDS :=  chronos msb-430 msb-430h telosb wsn430-v1_3b wsn430-v1_4 z1
DISABLE_TEST_FOR_MSP430 := tests-relic

ifneq (, $(filter $(AVR_BOARDS), $(BOARD)))
UNIT_TESTS := $(filter-out $(DISABLE_TEST_FOR_AVR), $(UNIT_TESTS))
endif

ifneq (, $(filter $(MSP430_BOARDS), $(BOARD)))
UNIT_TESTS := $(filter-out $(DISABLE_TEST_FOR_AVR), $(UNIT_TESTS))
endif

DISABLE_MODULE += auto_init

# Pull in `Makefile.include`s from the test suites:
-include $(UNIT_TESTS:%=$(RIOTBASE)/tests/unittests/%/Makefile.include)

DIRS += $(UNIT_TESTS)
BASELIBS += $(UNIT_TESTS:%=$(BINDIR)%.a)

INCLUDES += -I$(RIOTBASE)/tests/unittests/common

include $(RIOTBASE)/Makefile.include

.PHONY: FORCE $(UNIT_TESTS)

FORCE:
	touch $(CURDIR)/main.c

all: FORCE

$(UNIT_TESTS): FORCE all

charCOMMA := ,

ifeq (, $(UNIT_TESTS))
    CFLAGS += -DNO_TEST_SUITES
    $(warning There was no test suite specified!)
else
    CFLAGS += -DTEST_SUITES='$(subst $() $(),$(charCOMMA),$(UNIT_TESTS:tests-%=%))'
endif

test: SHELL=bash
test:
	@exec 5>&1 && \
	LOG=$$("$(MAKE)" -s term | tee >(cat - >&5)) && \
	grep 'OK ([1-9][0-9]* tests)' <<< $${LOG} > /dev/null
