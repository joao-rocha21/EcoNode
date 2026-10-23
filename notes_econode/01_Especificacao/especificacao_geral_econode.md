# Projeto Final – EcoNode (Dispositivos Conectados 2026/2027)

## 1. Visão geral

**EcoNode**: nó de monitorização ambiental autónomo, baseado no **ESP32-C6 DevKitC-1**, para locais sem infraestrutura de rede fiável (estufas, arquivos, armazéns, salas técnicas).

| Componente    | Função                                                             |
| ------------- | ------------------------------------------------------------------ |
| DHT20         | Temperatura e humidade                                             |
| Ecrã TFT      | Valores, estado da rede e do BLE                                   |
| LED RGB       | Estado do sistema                                                  |
| microSD       | Leituras, log de operações e ficheiro de configuração JSON         |
| Wi-Fi         | Publicação em servidor de telemetria (MQTT / WebSocket)            |
| BLE           | Consulta local de leituras e provisionamento/configuração em campo |
| Potenciómetro | Controlo do brilho do ecrã                                         |

> [!warning] Requisitos instáveis
> O cliente pode rever a especificação durante o desenvolvimento (comunicado via eLearning, passa a fazer parte do âmbito). A arquitetura deve absorver mudanças sem reescrever o sistema.

- **Grupos:** 2 estudantes, contributo equitativo; ambos têm de participar na defesa (não participar = nota nula nessa componente).
- **Ambiente:** ESP-IDF **v6.1.0**.

---

## 2. Requisitos funcionais

| ID   | Descrição                                                                                                                                  |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------ |
| RF1  | Leitura de temperatura e humidade com período configurável **[1 s – 3600 s]**                                                              |
| RF2  | TFT: valores medidos, estado da ligação de rede, estado do BLE                                                                             |
| RF3  | LED RGB: **verde** normal · **vermelho** erro · **amarelo** transmissão · **azul** provisionamento                                         |
| RF4  | Registo das leituras e do log de operações em ficheiros no microSD                                                                         |
| RF5  | Publicação das leituras num servidor remoto, com visualização em tempo real                                                                |
| RF6  | Brilho do ecrã controlado por potenciómetro                                                                                                |
| RF7  | Leitura, interpretação e *enforcing* da configuração JSON do microSD                                                                       |
| RF8  | GATT server BLE com **Environmental Sensing Service (0x181A)**: Temperature (0x2A6E) e Humidity (0x2A6F), em *read* e *notify*             |
| RF9  | Serviço GATT proprietário de configuração: consultar/alterar no mínimo período, credenciais de rede e endereço do servidor                 |
| RF10 | Provisionamento em campo: sem configuração de rede válida, entra em modo de provisionamento, aceita config por BLE e persiste-a no microSD |
| RF11 | Estratégia de redução de consumo energético, implementada **e** documentada                                                                |

## 3. Requisitos não funcionais

| ID   | Descrição                                                                                                                                                    |
| ---- | ------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| RNF1 | Código modular e documentado; funcionalidades em **tarefas FreeRTOS** individuais; nenhuma tarefa bloqueia outra                                             |
| RNF2 | Tolerância a falhas de rede: perda de Wi-Fi/servidor **não** interrompe amostragem, registo em cartão nem BLE                                                |
| RNF3 | Tempo de resposta adequado à monitorização                                                                                                                   |
| RNF4 | Interface simples e intuitiva                                                                                                                                |
| RNF5 | Alterar parâmetros sem recompilar                                                                                                                            |
| RNF6 | Segurança: credenciais **nunca** no ecrã nem no log; características BLE que as recebem **não legíveis**                                                     |
| RNF7 | Cada módulo com interface pública `.h` bem definida; consumidores de dados **não dependem uns dos outros** (ex.: visualização não inclui cabeçalhos da rede) |

---

## 4. Aspetos de implementação

### 4.1 Build e dependências

- Descomprimir o repositório e fazer *build* com ESP-IDF v6.1.0 **sem passos manuais**.
- Todas as dependências externas declaradas em `idf_component.yml`.
- Desde o IDF 6.0, alguns componentes saíram da árvore principal e vêm do gestor de componentes, nomeadamente `espressif/mqtt` e `espressif/cjson`. Sem estas declarações o projeto não compila noutra máquina.

### 4.2 Tarefa de aquisição

- Tarefa FreeRTOS dedicada à leitura do DHT20, com período lido do JSON no arranque.
- A amostra (valores + metadados) é entregue ao resto do sistema por **Queue**
- A documentação deve **justificar o dimensionamento** (profundidade da fila / tamanho do buffer) e descrever o **comportamento quando a capacidade se esgota** (será discutido na defesa).

### 4.3 Parâmetros atribuídos ao grupo


| Parâmetro                                 | Fórmula                                                                    |
| ----------------------------------------- | -------------------------------------------------------------------------- |
| `node_id`                                 | $1000 + 10 \times T + D$ (T = nº da turma prática)                         |
| Base dos tópicos/recursos                 | `econode/<node_id>/`                                                       |
| UUID base do serviço GATT de configuração | `E0C0DE00-0000-4000-8000-00000000XXXX` (XXXX = `node_id` em 4 dígitos hex) |
| UUIDs das características                 | Mesmo UUID, primeiro grupo `E0C0DE01`, `E0C0DE02`, …                       |
> Ver [[parametros_atribuídos_ao_grupo]]

### 4.4 Transporte de telemetria

- Servidor indicado na config como **URI completo** (não campos separados de endereço e porto).
- Suportar no mínimo `mqtt://` e `ws://`, selecionáveis **apenas** pela configuração.
- Documentar a escolha e implicações: sobrecarga por mensagem, comportamento em falha, custo energético.

### 4.5 Formato de dados

- Todas as estruturas trocadas com o exterior (telemetria, registos no microSD, valores das características GATT proprietárias) incluem um **campo de versão de formato**.
- O JSON de configuração inclui `config_version`.

### 4.6 Acesso à configuração

- Os módulos **não copiam** valores de configuração para variáveis locais no arranque.
- O acesso faz-se sempre pela interface pública do módulo de configuração (ex.: `config_get()`).

### 4.7 Decisões de arquitetura (ADR)

- Diretório `docs/adr/` com ADRs **datados**, incluindo alternativas consideradas.
- Todas as decisões arquiteturais documentadas; **mínimo de 5 ADRs**.

### 4.8 Demonstração

- Visualização em tempo real das medidas publicadas (qualquer ferramenta open source, p. ex. MQTT Tiles).
- Leitura BLE demonstrável com app genérica (nRF Connect, LightBlue ou equivalente).

---

## 5. Avaliação

ver [[avaliacao_projeto]]

---

## 6. Registo de progresso semanal (`README.md`)

ver [[modelo_registo_semanal]]

---

