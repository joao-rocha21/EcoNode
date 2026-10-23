idf_component_register(
    SRCS "network.c"
    INCLUDE_DIRS "include"
    REQUIRES esp_wifi esp_event esp_netif
    PRIV_REQUIRES espressif__mqtt espressif__cjson
)
