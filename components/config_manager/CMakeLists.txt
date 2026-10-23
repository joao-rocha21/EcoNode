
idf_component_register(
    SRCS "config_manager.c"
    INCLUDE_DIRS "include"
    REQUIRES espressif__cjson
    PRIV_REQUIRES fatfs
)
