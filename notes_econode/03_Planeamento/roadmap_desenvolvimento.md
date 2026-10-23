# Roadmap de desenvolvimento — EcoNode

## 1. Objetivo e estratégia

Este roadmap organiza o desenvolvimento do EcoNode por dependências técnicas: começar por validar o hardware e cada componente isoladamente, integrar as funcionalidades locais, acrescentar as comunicações e, por fim, testar a recuperação de falhas e o consumo energético.

A regra principal é **não avançar para a integração seguinte enquanto a etapa atual não tiver um teste reproduzível e um resultado conhecido**. Assim, quando surgir um problema, será mais fácil identificar o módulo responsável.

> **Versão do ambiente:** a especificação oficial do projeto indica ESP-IDF **v6.1.0**. A versão utilizada deve ser confirmada com a equipa docente e mantida consistente em todo o grupo.

> **Utilizar aquele exercicio do guiao que tinha o display, sdcard dht20 tudo integrado**

## 2. Fases de implementação

### Fase 0 — Preparar o repositório e o ambiente

**Objetivo:** garantir que existe uma base de desenvolvimento funcional antes de trabalhar nos periféricos.

- [ ] Criar o repositório e a estrutura inicial do projeto.
- [ ] Criar uma aplicação mínima para o ESP32-C6 DevKitC-1.
- [ ] Compilar, gravar e executar a aplicação na placa.
- [ ] Confirmar o funcionamento do monitor série e guardar os comandos de build, flash e monitor.
- [ ] Fixar a versão do ESP-IDF e declarar as dependências externas em `idf_component.yml`.
- [ ] Definir desde o início módulos separados, cada um com a sua interface pública `.h`.
- [ ] Criar `docs/adr/`, `docs/evidences/` e uma lista de requisitos com estado e evidência.

**Critério de conclusão:** qualquer elemento do grupo consegue compilar e executar a aplicação mínima seguindo as instruções do repositório, sem passos manuais não documentados.

### Fase 1 — Ler o sensor DHT20

**Objetivo:** obter medições fiáveis antes de acrescentar outras funcionalidades.

- [ ] Confirmar as ligações físicas e a interface de comunicação do DHT20.
- [ ] Implementar um módulo de sensor, por exemplo `sensor.c` e `sensor.h`.
- [ ] Ler temperatura e humidade e apresentá-las no monitor série.
- [ ] Verificar leituras repetidas, valores plausíveis, erros de comunicação e timeouts.
- [ ] Representar explicitamente uma leitura inválida; não tratar uma falha como se fosse uma medição válida.

**Critério de conclusão:** as leituras são coerentes durante um teste contínuo e os erros de leitura são detetados sem bloquear indefinidamente a aplicação.

> Pode-se reutilizar alguns ficheiros como leitura do sensor, escrita no sdcard que foram feitas na aulas práticas.

### Fase 2 — Colocar a informação no TFT

**Objetivo:** construir uma primeira versão funcional da interface local.

- [ ] Inicializar o TFT e mostrar um ecrã de arranque.
- [ ] Apresentar temperatura e humidade provenientes do módulo do sensor.
- [ ] Mostrar estados básicos, como `A iniciar`, `Sensor OK` e `Erro de sensor`.
- [ ] Atualizar apenas a informação necessária, evitando redesenhar o ecrã sem necessidade.
- [ ] Manter o código do ecrã independente dos detalhes internos do sensor.

**Critério de conclusão:** os valores apresentados correspondem às leituras reais e a falha do TFT não impede a aquisição do sensor.

### Fase 3 — LED RGB e potenciómetro

**Objetivo:** implementar os restantes elementos de interação local.

- [ ] Controlar o LED RGB e verificar cada cor individualmente.
- [ ] Definir os estados obrigatórios: verde — normal; vermelho — erro; amarelo — transmissão; azul — provisionamento.
- [ ] Ler o potenciómetro através do ADC e converter a leitura num nível de brilho.
- [ ] Aplicar o brilho ao TFT e definir um valor predefinido caso a leitura do potenciómetro falhe.
- [ ] Definir como a implementação representa estados simultâneos, sem contrariar os estados exigidos.

**Critério de conclusão:** cada estado pode ser demonstrado e o brilho varia de forma previsível com o potenciómetro.

### Fase 4 — Registar leituras e operações no microSD

**Objetivo:** preservar os dados localmente, mesmo quando não existe rede.

- [ ] Inicializar o cartão microSD e confirmar leitura e escrita.
- [ ] Criar um formato de registo para as amostras, incluindo versão do formato e metadados relevantes.
- [ ] Guardar temperatura e humidade em ficheiros.
- [ ] Criar um log separado para arranques, alterações de estado e erros.
- [ ] Testar cartão ausente, erro de inicialização, falha de escrita e falta de espaço.
- [ ] Garantir que uma falha do cartão não interrompe a aquisição nem as restantes funções.

**Critério de conclusão:** as amostras e os eventos ficam registados corretamente quando o cartão está disponível, e a aplicação continua a funcionar quando deixa de estar.

### Fase 5 — Configuração através de JSON

**Objetivo:** permitir alterar o comportamento do dispositivo sem recompilar.

- [ ] Criar no microSD um ficheiro de configuração JSON com `config_version`.
- [ ] Incluir, pelo menos, o período de amostragem, as credenciais Wi-Fi e o URI do servidor de telemetria, bem como os parâmetros adicionais exigidos.
- [ ] Implementar um módulo de configuração com interface pública, por exemplo `config_get()`.
- [ ] Validar a sintaxe, a versão e os valores dos parâmetros antes de os aceitar.
- [ ] Aplicar o período de amostragem configurado, respeitando o intervalo de 1 a 3600 segundos.
- [ ] Definir valores predefinidos documentados e o comportamento quando o ficheiro está ausente ou é inválido.
- [ ] Nunca mostrar credenciais no TFT nem incluí-las no log.

**Critério de conclusão:** alterar um parâmetro no JSON modifica o comportamento após reiniciar, sem recompilação; uma configuração inválida não é aplicada parcialmente.

### Fase 6 — Organizar a aplicação com FreeRTOS e passagem de amostras

**Objetivo:** separar as responsabilidades antes de acrescentar comunicações concorrentes.

- [ ] Criar uma tarefa FreeRTOS dedicada à aquisição do sensor.
- [ ] Enviar cada amostra, incluindo valores e metadados, através da `Queue` exigida para o grupo.
- [ ] Criar consumidores para as amostras, sem fazer os módulos dependerem diretamente uns dos outros.
- [ ] Separar, pelo menos, a aquisição, a apresentação e o registo, conforme a arquitetura adotada.
- [ ] Justificar a capacidade da Queue com base no período de amostragem e no tempo de processamento esperado.
- [ ] Definir e testar o comportamento quando o buffer fica cheio; não usar esperas indefinidas.
- [ ] Consultar os parâmetros através do módulo de configuração, em vez de os copiar para variáveis locais no arranque.

**Critério de conclusão:** as tarefas funcionam em simultâneo, a aquisição mantém a periodicidade prevista e a saturação do mecanismo de comunicação tem um comportamento definido e documentado.

### Fase 7 — Implementar BLE para consultar as leituras

**Objetivo:** permitir o acesso local aos dados sem depender do Wi-Fi.

- [ ] Inicializar o servidor GATT BLE.
- [ ] Implementar o Environmental Sensing Service, UUID `0x181A`.
- [ ] Expor Temperature `0x2A6E` e Humidity `0x2A6F` com leitura e notificação.
- [ ] Atualizar as características a partir das amostras produzidas pelo módulo de aquisição.
- [ ] Testar a leitura e as notificações com nRF Connect ou aplicação equivalente.
- [ ] Confirmar que a consulta BLE continua disponível quando o Wi-Fi está desligado.

**Critério de conclusão:** um telemóvel consegue ler e receber notificações de temperatura e humidade por BLE sem depender da conectividade de rede.

### Fase 8 — Ligar o ESP32-C6 ao Wi-Fi

**Objetivo:** estabelecer conectividade de rede sem comprometer o funcionamento local.

- [ ] Ler SSID e palavra-passe a partir do módulo de configuração.
- [ ] Implementar a ligação Wi-Fi e apresentar o estado no TFT.
- [ ] Registar eventos de ligação, perda de ligação e reconexão, sem registar credenciais.
- [ ] Testar credenciais inválidas, ponto de acesso indisponível e perda de sinal.
- [ ] Implementar tentativas de reconexão controladas.
- [ ] Confirmar que a aquisição, o registo no microSD e o BLE continuam operacionais durante a falha de Wi-Fi.

**Critério de conclusão:** a ligação é estabelecida com credenciais válidas e a perda de Wi-Fi não bloqueia nem interrompe as funcionalidades locais.

### Fase 9 — Publicar telemetria no servidor

**Objetivo:** enviar as medições para um sistema remoto e confirmar a sua receção.

- [ ] Escolher e preparar um servidor de teste e uma ferramenta de visualização open source.
- [ ] Interpretar o endereço do servidor como um URI completo vindo do JSON.
- [ ] Implementar o transporte `mqtt://` e, conforme a especificação, `ws://`, selecionáveis através da configuração sem recompilação.
- [ ] Declarar as dependências necessárias em `idf_component.yml`, incluindo `espressif/mqtt` quando aplicável.
- [ ] Definir o formato das mensagens, incluindo versão, identificador do nó e dados medidos.
- [ ] Utilizar a base de tópicos/recursos atribuída ao grupo: `econode/<node_id>/`.
- [ ] Testar primeiro uma publicação simples e confirmar a receção no servidor.
- [ ] Integrar a publicação periódica das amostras.
- [ ] Indicar o estado de transmissão através do LED amarelo, respeitando a gestão global dos estados.
- [ ] Testar servidor indisponível e perda de Wi-Fi; a aquisição e o registo local devem continuar.

**Critério de conclusão:** os valores publicados são visíveis em tempo real na ferramenta escolhida, e a indisponibilidade do servidor não interrompe as outras funções.

### Fase 10 — Serviço BLE de configuração e provisionamento

**Objetivo:** configurar o dispositivo em campo, incluindo quando não existe uma rede configurada.

- [ ] Definir o serviço GATT proprietário e os UUIDs de acordo com o `node_id` atribuído.
- [ ] Permitir consultar e alterar, no mínimo, o período de amostragem, as credenciais Wi-Fi e o URI do servidor.
- [ ] Validar todos os valores recebidos antes de os aplicar.
- [ ] Garantir que as características que recebem credenciais não permitem a sua leitura.
- [ ] Persistir as alterações no JSON do microSD e só confirmar o sucesso depois de a gravação ser concluída.
- [ ] Se não existir configuração de rede válida, iniciar o modo de provisionamento BLE e indicar o estado através do LED azul.
- [ ] Testar o provisionamento com um telemóvel, incluindo dados inválidos e falha de escrita no cartão.
- [ ] Verificar se os parâmetros alterados passam a ser usados pelos módulos sem recompilação.

**Critério de conclusão:** é possível configurar o dispositivo por BLE, guardar a configuração e estabelecer a rede sem acesso físico ao cartão nem recompilação.

### Fase 11 — Robustez, integração e testes de falhas

**Objetivo:** validar o comportamento do sistema completo, sobretudo quando os componentes falham.

- [ ] Testar separadamente falhas do sensor, TFT, potenciómetro, microSD, Wi-Fi, servidor e BLE.
- [ ] Verificar que a falha de um módulo não bloqueia tarefas não dependentes desse módulo.
- [ ] Testar JSON ausente, malformado, com versão não suportada ou parâmetros fora dos limites.
- [ ] Testar saturação da Queue/Message Buffer e contabilizar amostras perdidas.
- [ ] Definir códigos de erro consistentes e registar os eventos quando o microSD estiver disponível.
- [ ] Verificar que dados desatualizados ou inválidos não são apresentados como medições atuais.
- [ ] Confirmar que as credenciais não aparecem no TFT, logs ou características BLE legíveis.
- [ ] Executar um teste prolongado com aquisição, TFT, registo, BLE e telemetria ativos em simultâneo.

**Critério de conclusão:** os testes documentados demonstram que as falhas são detetadas e isoladas, e que os requisitos de tolerância a falhas e segurança são respeitados.

### Fase 12 — Consumo energético, documentação e demonstração final

**Objetivo:** preparar uma entrega reproduzível e demonstrar os requisitos cumpridos.

- [ ] Documentar e testar uma estratégia de redução de consumo: brilho, atualizações do TFT, período de amostragem e intervalos de reconexão.
- [ ] Comparar medições de consumo em condições de teste descritas e reproduzíveis, se existir equipamento disponível.
- [ ] Completar pelo menos cinco ADRs datados em `docs/adr/`, registando alternativas e justificação das decisões.
- [ ] Guardar evidências dos testes em `docs/evidences/` e associá-las aos requisitos.
- [ ] Atualizar a tabela de requisitos com os estados `não iniciado`, `em curso`, `funcional` ou `validado`.
- [ ] Registar semanalmente o progresso no README, acrescentando entradas sem apagar as anteriores; seguir a convenção de commits `log: semana N` e tags `semana-N`.
- [ ] Confirmar que o repositório compila numa instalação limpa com a versão de ESP-IDF definida e sem passos manuais adicionais.
- [ ] Preparar a demonstração: valores no TFT, estados do LED, registos no cartão, visualização remota em tempo real, leitura BLE e provisionamento BLE.

**Critério de conclusão:** os requisitos obrigatórios têm evidências, a compilação é reproduzível e a demonstração permite verificar as principais funcionalidades e cenários de falha.



## 3. Regras de trabalho para cada fase

Para cada fase, seguir sempre este ciclo:

1. **Implementar o mínimo necessário** para cumprir o objetivo da fase.
2. **Compilar e testar isoladamente**, antes de integrar mais módulos.
3. **Testar pelo menos um caso de erro** relevante para a funcionalidade.
4. **Guardar evidência**: saída do monitor série, fotografia, registo, teste ou captura da ferramenta remota.
5. **Atualizar a documentação e o estado dos requisitos**.
6. **Criar um commit funcional** antes de iniciar alterações de maior dimensão.

Evitar desenvolver todos os módulos em paralelo sem interfaces acordadas. O trabalho pode ser dividido pela equipa, mas cada módulo deve definir previamente a sua API, os dados que recebe e os resultados que produz.

## 4. Prioridades se o tempo for limitado

A prioridade é cumprir primeiro todos os requisitos obrigatórios, não acumular funcionalidades opcionais. A sequência mais segura é:

- **Prioridade 1:** aquisição, TFT, LED, microSD e configuração JSON.
- **Prioridade 2:** arquitetura FreeRTOS e passagem de amostras com comportamento de saturação documentado.
- **Prioridade 3:** BLE de leitura e ligação Wi-Fi.
- **Prioridade 4:** publicação remota com os dois esquemas de URI exigidos.
- **Prioridade 5:** serviço BLE de configuração e provisionamento.
- **Prioridade 6:** testes de falhas, segurança, energia, documentação e demonstração.

As funcionalidades adicionais — por exemplo, estatísticas mínimas/máximas/médias ou alertas ambientais — só devem ser implementadas depois de os requisitos obrigatórios estarem funcionais e testados.
