### Requisitos funcionais

Os requisitos funcionais definem as operações que o dispositivo deve executar, ou seja, as funções concretas que o sistema deve cumprir para atingir os objetivos propostos. Para este projeto, estabelecem-se os seguintes requisitos funcionais:

- **RF1.** Leitura da temperatura e humidade com período configurável entre 1 e 3600 segundos.
- **RF2.** Visualização de dados no ecrã TFT, incluindo os valores medidos, o estado da ligação de rede e o estado da interface BLE.
- **RF3.** Indicação de estado através do LED RGB:
  - Normal: verde;
  - Erro: vermelho;
  - Transmissão: amarelo;
  - Provisionamento: azul.
- **RF4.** Registo das leituras e do log de operações em ficheiros no cartão microSD.
- **RF5.** Publicação das leituras de temperatura e humidade num servidor de telemetria remoto, com visualização em tempo real.
- **RF6.** Controlo do brilho do ecrã através de um potenciómetro.
- **RF7.** Leitura, interpretação e aplicação de configurações a partir de um ficheiro JSON armazenado no cartão microSD.
- **RF8.** Exposição das leituras através de um servidor GATT BLE, implementando o *Environmental Sensing Service* (UUID `0x181A`), com as características *Temperature* (`0x2A6E`) e *Humidity* (`0x2A6F`), em modo de leitura e notificação.
- **RF9.** Exposição de um serviço GATT proprietário de configuração, que permita consultar e alterar, no mínimo, a periodicidade de amostragem, as credenciais da rede sem fios e o endereço do servidor de telemetria.
- **RF10.** Provisionamento em campo: na ausência de uma configuração de rede válida, o dispositivo entra em modo de provisionamento e aceita a configuração por BLE, persistindo-a no cartão microSD.
- **RF11.** Implementação e documentação de uma estratégia de redução do consumo energético.

### Requisitos não funcionais

Os requisitos não funcionais definem como o sistema deve funcionar, sem descrever funcionalidades específicas. Para este projeto, estabelecem-se os seguintes requisitos não funcionais:

- **RNF1.** Código modular e documentado. As diferentes funcionalidades devem ser distribuídas por tarefas FreeRTOS individuais. Nenhuma tarefa deve bloquear a execução de outra.
- **RNF2.** Tolerância a falhas de rede. A perda de conectividade Wi-Fi ou do servidor de telemetria não pode interromper a amostragem, o registo no cartão nem a interface BLE.
- **RNF3.** Tempo de resposta adequado ao contexto de monitorização.
- **RNF4.** Interface simples e intuitiva.
- **RNF5.** Alteração dos parâmetros de funcionamento sem necessidade de recompilação.
- **RNF6.** Adoção de práticas de segurança robustas. Em particular, as credenciais de rede não podem ser apresentadas no ecrã nem escritas no ficheiro de log, e as características BLE que as recebem não podem ser legíveis.
- **RNF7.** Cada módulo deve expor uma interface pública (`.h`) bem definida. Os módulos consumidores de dados não devem depender uns dos outros. A título de exemplo, o módulo de visualização não deve incluir cabeçalhos do módulo de rede.
