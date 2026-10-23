# Estrutura de Diretórios do EcoNode
## 1. Objetivo
Este documento apresenta a estrutura de diretórios proposta para o projeto EcoNode, desenvolvido com ESP-IDF. A organização pretende separar a lógica principal da aplicação dos componentes responsáveis pela configuração, aquisição de dados, armazenamento, apresentação, conectividade e comunicação.

> [!warning] Estrutura temporária, pode vir a ser alterada. 
> O cliente pode rever a especificação durante o desenvolvimento, por isso a granularidade dos componentes deve ser mantida baixa e só refinada quando algum módulo crescer.

## 1. Estrutura de diretórios proposta

```
econode/
├── CMakeLists.txt
├── sdkconfig.defaults
├── dependencies.lock
├── .gitignore
├── CHANGELOG.md
├── README.md
├── main/
│   ├── CMakeLists.txt
│   ├── idf_component.yml
│   ├── main.c
│   ├── app_tasks.c
│   └── app_tasks.h
├── components/
│   ├── hw_pins_spi_bus/
│   │   ├── CMakeLists.txt
│   │   ├── include/hw_pins_spi_bus.h
│   │   └── hw_pins_spi_bus.c
│   ├── system_state/
│   │   ├── CMakeLists.txt
│   │   ├── include/system_state.h
│   │   └── system_state.c
│   ├── config_manager/
│   │   ├── CMakeLists.txt
│   │   ├── include/config_manager.h
│   │   └── config_manager.c
│   ├── data_model/
│   │   ├── CMakeLists.txt
│   │   ├── include/econode_data.h
│   │   └── econode_data.c
│   ├── sensor_dht20/
│   │   ├── CMakeLists.txt
│   │   ├── include/sensor_dht20.h
│   │   └── sensor_dht20.c
│   ├── storage/
│   │   ├── CMakeLists.txt
│   │   ├── include/storage.h
│   │   └── storage.c
│   ├── display/
│   │   ├── CMakeLists.txt
│   │   ├── include/display.h
│   │   └── display.c
│   ├── control_brightness/
│   │   ├── CMakeLists.txt
│   │   ├── include/control_brightness.h
│   │   └── control_brightness.c
│   ├── status_led/
│   │   ├── CMakeLists.txt
│   │   ├── include/status_led.h
│   │   └── status_led.c
│   ├── network/
│   │   ├── CMakeLists.txt
│   │   ├── include/network.h
│   │   └── network.c
│   └── ble_services/
│       ├── CMakeLists.txt
│       ├── include/ble_services.h
│       ├── ble_services.c
│       ├── ble_ess.c
│       └── ble_config_svc.c
├── config/
│   └── config.example.json
├── docs/
│   ├── architecture.md
│   ├── adr/
│   │   ├── 0001-module-boundaries.md
│   │   ├── 0002-acquisition-queue.md
│   │   ├── 0003-spi-bus-access.md
│   │   ├── 0004-system-state-and-led-priority.md
│   │   ├── 0005-storage-format.md
│   │   ├── 0006-transport-mqtt-ws.md
│   │   └── 0007-ble-configuration.md
│   └── evidences/
└── README.md
```

## 2. Responsabilidade dos ficheiros e diretórios
### 2.1. Ficheiros da raiz

|Ficheiro ou diretório|Responsabilidade prevista|
|---|---|
|`CMakeLists.txt`|Define a configuração principal de compilação do projeto ESP-IDF e inclui os componentes necessários.|
|`sdkconfig.defaults`|Define opções de configuração predefinidas para a compilação do firmware, evitando depender exclusivamente de configurações locais.|
|`dependencies.lock`|Regista as versões concretas das dependências resolvidas, permitindo reproduzir o ambiente de compilação. Deve ser versionado.|
|`.gitignore`|Exclui do repositório `build/`, `sdkconfig`, `managed_components/` e qualquer `config.json` real com credenciais.|
|`CHANGELOG.md`|Registo das alterações de cada semana, associado à release semanal.|
|`main/`|Contém o ponto de entrada da aplicação e a lógica de inicialização e coordenação das tarefas.|
|`components/`|Agrupa os módulos funcionais da aplicação, separados por responsabilidade.|
|`config/`|Guarda ficheiros de configuração de referência e exemplos não sensíveis.|
|`docs/`|Contém documentação técnica, decisões de arquitetura e evidências do desenvolvimento.|
|`README.md`|Apresenta o projeto, as instruções de compilação e execução, e os registos semanais exigidos pela especificação.|

### 2.2. Aplicação principal — `main/`

|Ficheiro|Responsabilidade prevista|
|---|---|
|`CMakeLists.txt`|Declara a configuração de compilação do componente principal e as suas dependências.|
|`idf_component.yml`|Declara as dependências externas do projeto (`espressif/mqtt`, `espressif/cjson`), conforme o mecanismo de gestão de componentes do ESP-IDF.|
|`main.c`|Ponto de entrada do firmware. Chama a inicialização do `hw_pins_spi_bus` e dos restantes módulos pela ordem correta e arranca as tarefas.|
|`app_tasks.c`|Cria, configura e coordena as tarefas da aplicação, incluindo prioridades e períodos. Contém também a lógica de arranque do provisionamento: se o `config_manager` não tiver configuração de rede válida, ativa a flag `PROVISIONING` no `system_state`.|
|`app_tasks.h`|Expõe as funções e definições necessárias para iniciar ou gerir as tarefas da aplicação.|

A lógica de negócio específica dos sensores, do armazenamento e da conectividade deverá permanecer nos respetivos componentes, evitando concentrar toda a implementação em `main.c`.
### 2.3. Componentes funcionais — `components/`
Cada componente deverá ter um `CMakeLists.txt`, um diretório `include/` para a interface pública e pelo menos um ficheiro `.c` para a implementação. Esta organização poderá ser adaptada caso algum módulo necessite de vários ficheiros de implementação.

|Componente|Ficheiro de interface|Ficheiro(s) de implementação|Responsabilidade prevista|
|---|---|---|---|
|`hw_pins_spi_bus/`|`include/hw_pins_spi_bus.h`|`hw_pins_spi_bus.c`|Define os pinos da placa (SPI, CS do TFT e do microSD, LED RGB, canal ADC do potenciómetro), inicializa o barramento SPI uma única vez (`hw_spi_bus_init()`) e expõe o mutex que protege os acessos longos ao barramento. `display` e `storage` dependem deste módulo, mas não um do outro.|
|`system_state/`|`include/system_state.h`|`system_state.c`|Guarda o estado global do sistema como flags (event group FreeRTOS), por exemplo `WIFI_OK`, `SERVER_OK`, `TX_ACTIVE`, `BLE_ADVERTISING`, `BLE_CONNECTED`, `PROVISIONING`, `SENSOR_ERR`, `SD_ERR`. Os módulos produtores escrevem flags; `status_led` e `display` leem-nas. Não depende de nenhum outro módulo funcional.|
|`config_manager/`|`include/config_manager.h`|`config_manager.c`|Carregar, validar e disponibilizar os parâmetros de configuração da aplicação (acesso via `config_get()`). Trata valores predefinidos e erros de leitura ou validação. Usa `espressif/cjson`.|
|`data_model/`|`include/econode_data.h`|`econode_data.c`|Definir as estruturas de dados comuns do EcoNode, incluindo as medições, a versão de formato e os metadados. **Não depende de nenhum outro módulo** (só tipos e constantes), para preservar a independência entre consumidores.|
|`sensor_dht20/`|`include/sensor_dht20.h`|`sensor_dht20.c`|Inicializar o sensor DHT20, efetuar leituras de temperatura e humidade e comunicar falhas de aquisição (`SENSOR_ERR`).|
|`storage/`|`include/storage.h`|`storage.c`|Gerir o armazenamento local no microSD, incluindo a inicialização do sistema de ficheiros sobre o barramento fornecido por `hw_pins_spi_bus`, a escrita e a leitura dos dados e o tratamento de erros de acesso (`SD_ERR`).|
|`display/`|`include/display.h`|`display.c`|Gerir a apresentação de informação no ecrã TFT: medições, estado do sistema (lido de `system_state`) e mensagens relevantes. Nunca mostra credenciais.|
|`control_brightness/`|`include/control_brightness.h`|`control_brightness.c`|Ler o potenciómetro (ADC), converter a leitura em nível de brilho e aplicá-lo ao TFT. Usa um nível predefinido se a leitura falhar (RF6).|
|`status_led/`|`include/status_led.h`|`status_led.c`|Controlar o LED RGB a partir das flags do `system_state`. Concentra a **prioridade entre estados simultâneos** (proposta inicial, a decidir e documentar em ADR: erro > provisionamento > transmissão > normal). Os restantes módulos não controlam os pinos do LED.|
|`network/`|`include/network.h`|`network.c`|Gerir a ligação Wi-Fi, a telemetria (`mqtt://` e `ws://` a partir de URI completo), reconexões e tratamento de falhas. Escreve flags no `system_state`. Usa `espressif/mqtt`.|
|`ble_services/`|`include/ble_services.h`|`ble_services.c`, `ble_ess.c`, `ble_config_svc.c`|Um único `.h` público. `ble_services.c` inicializa a pilha BLE e o anúncio; `ble_ess.c` implementa o Environmental Sensing Service (0x181A, consumidor de dados); `ble_config_svc.c` implementa o serviço GATT proprietário de configuração e provisionamento (fala com o `config_manager`, características de credenciais sem leitura).|

Os ficheiros `include/*.h` deverão expor apenas as funções, estruturas, constantes e tipos necessários aos restantes módulos. A implementação interna deverá permanecer nos ficheiros `.c`, reduzindo o acoplamento entre componentes.

O nível de granularidade apresentado é indicativo. Se um componente crescer, poderá ser dividido em vários ficheiros, por exemplo, para separar a inicialização, a gestão de erros e as operações específicas.
### 2.3.1. Dependências entre componentes (`REQUIRES`)
Regra geral (RNF7): os módulos consumidores de dados não se incluem uns aos outros. Comunicam por `data_model` (dados), Queue (amostras) e `system_state` (estado).

|Componente|Pode depender de|
|---|---|
|`data_model`|nenhum|
|`system_state`|nenhum|
|`hw_pins_spi_bus`|nenhum|
|`config_manager`|`storage` (leitura do JSON), `espressif/cjson`|
|`sensor_dht20`|`data_model`, `system_state`|
|`storage`|`data_model`, `system_state`, `hw_pins_spi_bus`|
|`display`|`data_model`, `system_state`, `hw_pins_spi_bus`, `config_manager`|
|`control_brightness`|`hw_pins_spi_bus`|
|`status_led`|`system_state`, `hw_pins_spi_bus`|
|`network`|`data_model`, `system_state`, `config_manager`, `espressif/mqtt`|
|`ble_services`|`data_model`, `system_state`, `config_manager`|

> [!note] Tabela de partida, a confirmar na implementação. 
> O `REQUIRES` de cada `CMakeLists.txt` deve refletir exatamente esta tabela: se um componente local usa `cJSON` ou MQTT, deve declará-lo também no seu `REQUIRES`, além do `idf_component.yml`.

### 2.4. Configuração — `config/`

|Ficheiro|Responsabilidade prevista|
|---|---|
|`config.example.json`|Apresenta um exemplo da estrutura e dos parâmetros de configuração aceites pela aplicação, utilizando exclusivamente valores demonstrativos.|

Este ficheiro destina-se a documentar o formato esperado da configuração. Não se deve assumir que o firmware o consegue ler diretamente a partir do diretório `config/`, uma vez que o acesso depende do mecanismo de armazenamento e da montagem do sistema de ficheiros implementados.

A configuração efetivamente utilizada pelo firmware deverá estar disponível no local esperado pelo mecanismo de leitura implementado, por exemplo, no microSD. O formato, o caminho e o procedimento de carregamento deverão ser documentados no componente `config_manager`.

Não devem ser incluídas credenciais reais, palavras-passe, chaves ou outros dados sensíveis no ficheiro de exemplo nem no repositório. O `.gitignore` deve excluir qualquer `config.json` real.
### 2.5. Documentação — `docs/`

| Ficheiro ou diretório | Responsabilidade prevista                                                                                                                           |
| --------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- |
| `architecture.md`     | Descreve a arquitetura global do EcoNode, as responsabilidades dos componentes, as principais interações e os fluxos de dados.                      |
| `adr/`                | Guarda os registos das decisões de arquitetura (_Architecture Decision Records_), com contexto, alternativas consideradas, decisão e consequências. |
| `evidences/`          | Guarda evidências do desenvolvimento e da validação dos requisitos, como fotografias, capturas de ecrã, gráficos e resultados de testes.            |

Os ADR deverão ser numerados sequencialmente e documentar decisões arquiteturais efetivamente tomadas. Os sete ficheiros apresentados na árvore são exemplos de decisões a registar, não decisões que devam ser consideradas automaticamente aprovadas ou implementadas:

|ADR|Tema|
|---|---|
|`0001-module-boundaries`|Fronteiras entre módulos, interfaces públicas e regras de dependência.|
|`0002-acquisition-queue`|Queue de amostras: dimensionamento e comportamento quando fica cheia.|
|`0003-spi-bus-access`|Inicialização única do SPI em `hw_pins_spi_bus` e mutex de acesso partilhado.|
|`0004-system-state-and-led-priority`|Estado global por event group e prioridade entre estados do LED.|
|`0005-storage-format`|Formato dos ficheiros de leituras e log, versão de formato, rotação.|
|`0006-transport-mqtt-ws`|Escolha e implicações de `mqtt://` e `ws://`.|
|`0007-ble-configuration`|Serviço GATT de configuração, provisionamento e proteção das credenciais.|

A especificação exige pelo menos cinco ADR datados. Os títulos e o conteúdo finais deverão corresponder às decisões reais do grupo.
### 3. Dependências

> Cada ficheiro CMakeLists.txt tem as dependências usadas por cada módulo, podes ter de mudar, mas à partida será algo semelhante a isto.

| Componente           | Dependências principais propostas                                            |
| -------------------- | ---------------------------------------------------------------------------- |
| `hw_pins_spi_bus`    | `driver`                                                                     |
| `system_state`       | Nenhuma                                                                      |
| `config_manager`     | `espressif__cjson`, `fatfs`                                                  |
| `data_model`         | Nenhuma                                                                      |
| `sensor_dht20`       | `esp_driver_i2c`                                                             |
| `storage`            | `fatfs`, `sdmmc`, `hw_pins_spi_bus`                                          |
| `display`            | `esp_lcd`, `esp_driver_spi`, `hw_pins_spi_bus`                               |
| `control_brightness` | `esp_adc`, `display`                                                         |
| `status_led`         | `esp_driver_gpio`                                                            |
| `network`            | `esp_wifi`, `esp_event`, `esp_netif`,  `espressif__mqtt` ,`espressif__cjson` |
| `ble_services`       | `bt`                                                                         |