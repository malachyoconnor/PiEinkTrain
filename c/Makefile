DIR_Config	 = ./lib/Config
DIR_EPD		 = ./lib/e-Paper
DIR_FONTS	 = ./lib/Fonts
DIR_GUI		 = ./lib/GUI
DIR_Examples = ./examples
DIR_BIN		 = ./bin

EPD = NULL

# I only care about this model:
EPD = epd7in5V2

# MY ONE
ifeq ($(EPD), epd7in5V2)
	OBJ_C_EPD = ${DIR_EPD}/EPD_7in5_V2.c
	OBJ_C_Examples = ${DIR_Examples}/EPD_7in5_V2_test.c
else ifeq ($(EPD), epd7in5V2_old)
	OBJ_C_EPD = ${DIR_EPD}/EPD_7in5_V2_old.c
	OBJ_C_Examples = ${DIR_Examples}/EPD_7in5_V2_test_old.c
else ifeq ($(EPD), epd7in5bV2)
	OBJ_C_EPD = ${DIR_EPD}/EPD_7in5b_V2.c
	OBJ_C_Examples = ${DIR_Examples}/EPD_7in5b_V2_test.c
else ifeq ($(EPD), epd7in5bV2_old)
	OBJ_C_EPD = ${DIR_EPD}/EPD_7in5b_V2_old.c
	OBJ_C_Examples = ${DIR_Examples}/EPD_7in5b_V2_test_old.c
else
    OBJ_C_EPD = NULL
    OBJ_C_Examples = NULL
endif

OBJ_C = $(wildcard ${OBJ_C_EPD} ${DIR_GUI}/*.c ${OBJ_C_Examples} ${DIR_Examples}/main.c ${DIR_Examples}/ImageData2.c ${DIR_Examples}/ImageData.c ${DIR_FONTS}/*.c )
OBJ_O = $(patsubst %.c,${DIR_BIN}/%.o,$(notdir ${OBJ_C}))
RPI_DEV_C = $(wildcard $(DIR_BIN)/dev_hardware_SPI.o $(DIR_BIN)/RPI_gpiod.o $(DIR_BIN)/DEV_Config.o )
JETSON_DEV_C = $(wildcard $(DIR_BIN)/sysfs_software_spi.o $(DIR_BIN)/sysfs_gpio.o $(DIR_BIN)/DEV_Config.o )


DEBUG = -D DEBUG

# USELIB_RPI = USE_BCM2835_LIB
# USELIB_RPI = USE_WIRINGPI_LIB
USELIB_RPI = USE_LGPIO_LIB
# USELIB_RPI = USE_DEV_LIB

LIB_RPI=-Wl,--gc-sections
ifeq ($(USELIB_RPI), USE_BCM2835_LIB)
	LIB_RPI += -lbcm2835 -lm 
else ifeq ($(USELIB_RPI), USE_WIRINGPI_LIB)
	LIB_RPI += -lwiringPi -lm 
else ifeq ($(USELIB_RPI), USE_LGPIO_LIB)
	LIB_RPI += -llgpio -lm
else ifeq ($(USELIB_RPI), USE_DEV_LIB)
	LIB_RPI += -lgpiod -lm 
endif
DEBUG_RPI = -D $(USELIB_RPI) -D RPI

USELIB_JETSONI = USE_DEV_LIB
# USELIB_JETSONI = USE_HARDWARE_LIB
ifeq ($(USELIB_JETSONI), USE_DEV_LIB)
	LIB_JETSONI = -lm 
else ifeq ($(USELIB_JETSONI), USE_HARDWARE_LIB)
	LIB_JETSONI = -lm 
endif
DEBUG_JETSONI = -D $(USELIB_JETSONI) -D JETSON

.PHONY : RPI JETSON clean


CFLAGS += -I $(DIR_Config) -I $(DIR_GUI) -I $(DIR_EPD) -I $(DIR_FONTS) -I $(DIR_Examples)

# We only want RPI
all:RPI_DEV RPI_epd

JETSON: JETSON_DEV JETSON_epd

TARGET = epd
CC = gcc
MSG = -g -O -ffunction-sections -fdata-sections -Wall
CFLAGS += $(MSG) -D $(EPD)

RPI_epd:${OBJ_O}
	echo $(@)
	@echo '$(CC) $(CFLAGS) -D RPI $(OBJ_O) $(RPI_DEV_C) -o $(TARGET) $(LIB_RPI) $(DEBUG)'
	$(CC) $(CFLAGS) -D RPI $(OBJ_O) $(RPI_DEV_C) -o $(TARGET) $(LIB_RPI) $(DEBUG)

#Example (simplified) output:
# gcc -g -O -Wall -D RPI ./binEPD_7in5_V2.o $(DIR_BIN)/dev_hardware_SPI.o -o epd
# -D RPI is equivalent to #define RPI

#Full output:
#gcc -g -O -ffunction-sections -fdata-sections -Wall -D epd7in5bV2 -D RPI ./bin/GUI_BMPfile.o ./bin/GUI_Paint.o
#      ./bin/main.o ./bin/ImageData2.o ./bin/ImageData.o ./bin/font12.o ./bin/font12CN.o ./bin/font16.o ./bin/font20.o
#      ./bin/font24.o ./bin/font24CN.o ./bin/font8.o ./bin/dev_hardware_SPI.o ./bin/DEV_Config.o -o epd -Wl,--gc-sections
#		-llgpio -lm -D DEBUG


RPI_DEV:
ifeq ($(USELIB_RPI), USE_DEV_LIB)
	$(CC) $(CFLAGS) $(DEBUG_RPI) -c	 $(DIR_Config)/dev_hardware_SPI.c -o $(DIR_BIN)/dev_hardware_SPI.o $(LIB_RPI) $(DEBUG)
	$(CC) $(CFLAGS) $(DEBUG_RPI) -c	 $(DIR_Config)/DEV_Config.c -o $(DIR_BIN)/DEV_Config.o $(LIB_RPI) $(DEBUG)
	$(CC) $(CFLAGS) $(DEBUG_RPI) -c	 $(DIR_Config)/RPI_gpiod.c -o $(DIR_BIN)/RPI_gpiod.o $(LIB_RPI) $(DEBUG)
else
	$(CC) $(CFLAGS) $(DEBUG_RPI) -c	 $(DIR_Config)/dev_hardware_SPI.c -o $(DIR_BIN)/dev_hardware_SPI.o $(LIB_RPI) $(DEBUG)
	$(CC) $(CFLAGS) $(DEBUG_RPI) -c	 $(DIR_Config)/DEV_Config.c -o $(DIR_BIN)/DEV_Config.o $(LIB_RPI) $(DEBUG)
endif

JETSON_epd:${OBJ_O}
	echo $(@)
	$(CC) $(CFLAGS) $(OBJ_O) $(JETSON_DEV_C) -o $(TARGET) $(LIB_JETSONI) $(DEBUG)

$(shell mkdir -p $(DIR_BIN))

${DIR_BIN}/%.o:$(DIR_Examples)/%.c
	$(CC) $(CFLAGS) -c	$< -o $@ -I $(DIR_Config) -I $(DIR_GUI) -I $(DIR_EPD) $(DEBUG)
	
${DIR_BIN}/%.o:$(DIR_EPD)/%.c
	$(CC) $(CFLAGS) -c	$< -o $@ -I $(DIR_Config) $(DEBUG)

${DIR_BIN}/%.o:$(DIR_FONTS)/%.c 
	$(CC) $(CFLAGS) -c	$< -o $@ $(DEBUG)
	
${DIR_BIN}/%.o:$(DIR_GUI)/%.c
	$(CC) $(CFLAGS) -c	$< -o $@ -I $(DIR_Config) $(DEBUG)

JETSON_DEV:
	$(CC) $(CFLAGS) $(DEBUG_JETSONI) -c	 $(DIR_Config)/sysfs_software_spi.c -o $(DIR_BIN)/sysfs_software_spi.o $(LIB_JETSONI) $(DEBUG)
	$(CC) $(CFLAGS) $(DEBUG_JETSONI) -c	 $(DIR_Config)/sysfs_gpio.c -o $(DIR_BIN)/sysfs_gpio.o $(LIB_JETSONI) $(DEBUG)
	$(CC) $(CFLAGS) $(DEBUG_JETSONI) -c	 $(DIR_Config)/DEV_Config.c -o $(DIR_BIN)/DEV_Config.o $(LIB_JETSONI) $(DEBUG)

clean :
	rm $(DIR_BIN)/*.* 
	rm $(TARGET) 

