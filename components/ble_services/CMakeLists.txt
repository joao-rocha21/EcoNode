
idf_component_register(
    SRCS
        "ble_services.c"
        "ble_ess.c"
        "ble_config_svc.c"
    INCLUDE_DIRS "include"
    PRIV_REQUIRES bt
)
