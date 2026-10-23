
idf_component_register(
    SRCS "control_brightness.c"
    INCLUDE_DIRS "include"
    PRIV_REQUIRES esp_adc display
)
