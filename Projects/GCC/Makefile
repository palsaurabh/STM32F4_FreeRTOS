CC                     =    arm-none-eabi-gcc
SIZE                   =    arm-none-eabi-size
BIN                   :=    RTOSDemo.elf

BUILD_DIR             :=    build

FREERTOS_DIR_REL      :=    ../../../../../../FreeRTOS/FreeRTOS/
FREERTOS_DIR          :=    $(abspath $(FREERTOS_DIR_REL))
KERNEL_DIR            :=    $(FREERTOS_DIR)/Source
ST_CODE_DIR			  :=	$(abspath ../../ST_Code)
# Startup sources

ASMS = Startup/startup_stm32f407vg.S

# SOURCE_FILES          +=    Startup/syscalls.c  Startup/sysmem.c
SOURCE_FILES		  +=	$(wildcard $(ST_CODE_DIR)/Core/Src/*.c)
SOURCE_FILES          +=	$(wildcard $(ST_CODE_DIR)/Drivers/STM32F4xx_HAL_Driver/Src/*.c)
SOURCE_FILES          +=	$(wildcard $(ST_CODE_DIR)/Drivers/BSP/STM32F4-Discovery/*.c)
# platform portable source file
SOURCE_FILES          +=    $(KERNEL_DIR)/portable/GCC/ARM_CM4_MPU/port.c
SOURCE_FILES          +=    $(KERNEL_DIR)/portable/GCC/ARM_CM4_MPU/mpu_wrappers_v2_asm.c

# Kernel source files
SOURCE_FILES          +=    $(KERNEL_DIR)/portable/Common/mpu_wrappers.c
SOURCE_FILES          +=    $(KERNEL_DIR)/portable/Common/mpu_wrappers_v2.c
SOURCE_FILES          +=    $(KERNEL_DIR)/tasks.c
SOURCE_FILES          +=    $(KERNEL_DIR)/list.c
SOURCE_FILES          +=    $(KERNEL_DIR)/queue.c
SOURCE_FILES          +=    $(KERNEL_DIR)/timers.c
SOURCE_FILES          +=    $(KERNEL_DIR)/event_groups.c
SOURCE_FILES          +=    ${KERNEL_DIR}/portable/MemMang/heap_4.c
SOURCE_FILES          +=    $(KERNEL_DIR)/stream_buffer.c

# application source files
SOURCE_FILES          +=    $(wildcard ../../Demo/*.c)
# SOURCE_FILES          +=    mpu_demo.c

INCLUDE_DIRS          +=    -I$(FREERTOS_DIR)/Demo/STM32F4_FreeRTOS/
INCLUDE_DIRS          +=    -I$(FREERTOS_DIR)/Demo/STM32F4_FreeRTOS/Demo
INCLUDE_DIRS          +=    -I$(FREERTOS_DIR)/Demo/STM32F4_FreeRTOS/Config
INCLUDE_DIRS          +=    -I$(FREERTOS_DIR)/Demo/STM32F4_FreeRTOS/ST_Code/Core/Inc
INCLUDE_DIRS          +=    -I$(FREERTOS_DIR)/Demo/STM32F4_FreeRTOS/ST_Code/Drivers/CMSIS/Include
INCLUDE_DIRS          +=    -I$(FREERTOS_DIR)/Demo/STM32F4_FreeRTOS/ST_Code/Drivers/STM32F4xx_HAL_Driver/Inc
INCLUDE_DIRS          +=    -I$(FREERTOS_DIR)/Demo/STM32F4_FreeRTOS/ST_Code/Drivers/CMSIS/Device/ST/STM32F4xx/Include
INCLUDE_DIRS          +=    -I$(FREERTOS_DIR)/Demo/STM32F4_FreeRTOS/ST_Code/Drivers/BSP/STM32F4-Discovery

INCLUDE_DIRS          +=    -I$(KERNEL_DIR)/include
INCLUDE_DIRS          +=    -I$(KERNEL_DIR)/portable/GCC/ARM_CM4_MPU

DEFINES               :=    -DHEAP4
CPPFLAGS              +=    $(DEFINES)

CFLAGS                +=    -mthumb -mcpu=cortex-m4 -mfloat-abi=hard -mfpu=fpv4-sp-d16
CFLAGS                +=    -Wall -Wextra -Wshadow -Wno-unused-parameter
#CFLAGS                +=    -Wpedantic -fanalyzer
CFLAGS                +=    $(INCLUDE_DIRS)

LDFLAGS                =    -T ./STM32L475VGTX_FLASH.ld
LDFLAGS               +=    -Xlinker -Map=${BUILD_DIR}/output.map
# LDFLAGS               +=    -Xlinker --gc-sections
LDFLAGS               +=    -nostdlib -nolibc -nodefaultlibs
# LDFLAGS               +=    -lc 

ifeq ($(DEBUG), 1)
    CFLAGS                +=     -g3 -Og -ffunction-sections -fdata-sections -save-temps=obj
else
    CFLAGS                +=     -Os -ffunction-sections -fdata-sections
endif

# ifeq ($(PICOLIBC), 1)
#     CFLAGS                +=     --specs=picolibc.specs -DPICOLIBC_INTEGER_PRINTF_SCANF
#    LDFLAGS                +=     --specs=picolibc.specs -DPICOLIBC_INTEGER_PRINTF_SCANF
# endif

OBJ_FILES             :=    $(SOURCE_FILES:%.c=$(BUILD_DIR)/%.o) #$(ASMS:.S=.o)

.PHONY: clean

$(BUILD_DIR)/$(BIN) : $(OBJ_FILES)
	$(CC) $(CFLAGS) $(LDFLAGS) $+ -o $(@)
	$(SIZE) $(@)

%.d: %.c
	@set -e; rm -f $@; \
	$(CC) -M $(CPPFLAGS) $< > $@.$$$$; \
	sed 's,\($*\)\.o[ :]*,\1.o $@ : ,g' < $@.$$$$ > $@; \
	rm -f $@.$$$$

INCLUDES              :=     $(SOURCE_FILES:%.c=$(BUILD_DIR)/%.d)
-include $(INCLUDES)

${BUILD_DIR}/%.o : %.c Makefile
	-mkdir -p $(@D)
	$(CC) $(CFLAGS) $(CPPFLAGS) -MMD -c $< -o $@

clean:
	-rm -rf build

