
idf_component_register(
    SRCS "storage.c"
    INCLUDE_DIRS "include"
    REQUIRES fatfs
    PRIV_REQUIRES sdmmc hw_pins_spi_bus
)
