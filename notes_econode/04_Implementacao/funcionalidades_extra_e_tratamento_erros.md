# Funcionalidades extra

Para além dos requisitos obrigatórios da especificação, propõem-se funcionalidades complementares para melhorar a robustez, o diagnóstico e a utilidade do EcoNode. 

> **Estas ideias não são obrigatórias e podem ser implementadas conforme o tempo disponível.**

### A. Tratamento de erros (*Error Handling*)

O dispositivo deve identificar situações anormais, informar o utilizador e recuperar automaticamente quando possível. Uma falha deve afetar apenas as funcionalidades que dependem do componente afetado.

> **Em relação às falhas testar posteriormente na prática, remove sensor, desconecta dispositivo da internet...**

Define códigos consistentes, como `ERR_SENSOR_TIMEOUT`, `ERR_SD_WRITE` e `ERR_SERVER_UNAVAILABLE`, para facilitar os testes, os registos e a demonstração prática.

**Registo de diagnóstico (**_**Logging**_**)**

Utilizar o sistema de logging do ESP-IDF para registar ocorrências relevantes durante a execução do EcoNode. Devem ser utilizadas as macros adequadas à gravidade do evento:
- `ESP_LOGE()`: erros que impedem ou comprometem uma operação, como falhas de leitura do sensor ou de escrita no microSD.
- `ESP_LOGW()`: situações anormais potencialmente recuperáveis, como perda temporária de Wi-Fi ou tentativas de recuperação.
- `ESP_LOGI()`: informação sobre o funcionamento normal, inicialização de componentes e recuperação após uma falha.
- `ESP_LOGD()` e `ESP_LOGV()`: informação detalhada de diagnóstico durante o desenvolvimento.

Cada módulo deve utilizar um `TAG` identificativo, como `SENSOR`, `SD_CARD`, `WIFI` ou `BLE`. Sempre que aplicável, as mensagens devem incluir o código do erro, a operação afetada e o resultado da tentativa de recuperação.

Exemplos:
- `ESP_LOGE(TAG, "ERR_SENSOR_TIMEOUT: timeout na leitura do DHT20");`
- `ESP_LOGE(TAG, "ERR_SD_WRITE: falha ao escrever no cartão");`
- `ESP_LOGW(TAG, "Wi-Fi desligado; a tentar restabelecer ligação");`
- `ESP_LOGI(TAG, "Ligação Wi-Fi recuperada");`

Os logs de consola destinam-se ao diagnóstico e não garantem o armazenamento persistente dos eventos. Se for necessário guardar os registos no microSD, esse mecanismo deve ser implementado separadamente e não pode comprometer o funcionamento das restantes tarefas quando o cartão estiver indisponível. A sinalização através do LED e do TFT deve continuar a funcionar de acordo com os estados definidos na especificação.

#### A.1. Falha do sensor DHT20

**Situação:** o sensor é desligado, deixa de responder ou devolve leituras inválidas.

**Comportamento proposto:**

- Detetar timeouts, erros de comunicação e valores inválidos.
- Registar o erro e o número de tentativas no log, quando o microSD estiver disponível.
- Acender o LED vermelho e apresentar `ERRO: SENSOR` no TFT.
- Repetir a leitura após um intervalo controlado, sem bloquear outras tarefas.
- Retomar as medições quando o sensor voltar a funcionar.

A última leitura válida pode ser conservada para referência, mas deve ser identificada como desatualizada.

#### A.2. Falha do cartão microSD

**Situação:** o cartão é removido, não inicializa, fica sem espaço ou ocorre um erro de leitura/escrita.

**Comportamento proposto:**

- Sinalizar a falha através do LED vermelho e do TFT.
- Continuar a aquisição, a interface e as comunicações, se os respetivos componentes estiverem operacionais.
- Tentar recuperar o acesso ao cartão de forma controlada.
- Retomar os registos quando o cartão voltar a estar disponível.

Como melhoria, pode manter-se temporariamente um número limitado de amostras em memória. Se o armazenamento falhar, o sistema não deve afirmar que os dados foram persistidos.

#### A.3. Perda de Wi-Fi ou indisponibilidade do servidor

**Situação:** o dispositivo perde a ligação à rede ou o servidor não responde.

**Comportamento proposto:**

- Distinguir a perda de Wi-Fi da falha do servidor.
- Atualizar o estado no TFT e registar a ocorrência e a recuperação.
- Tentar restabelecer a ligação com intervalos progressivos.
- Continuar a adquirir e guardar localmente as leituras (**cache**) e manter o BLE disponível.
- Retomar a publicação quando a ligação for recuperada.

Opcionalmente, pode ser criado um buffer limitado de mensagens por enviar. A política de reenvio deve evitar consumo ilimitado de memória.

#### A.4. Configuração JSON inválida ou inexistente

**Situação:** o ficheiro não existe, contém erros ou possui parâmetros inválidos.

**Comportamento proposto:**

- Validar a configuração antes de a aplicar e registar a causa da rejeição sem expor credenciais.
- Não aplicar parcialmente uma configuração inválida.
- Utilizar valores predefinidos apenas quando estiverem documentados.
- Se não existir uma configuração de rede válida, entrar em modo de provisionamento BLE.
- Apresentar uma mensagem de erro no TFT.

As alterações recebidas por BLE devem ser validadas antes de substituir a configuração ativa. Se a gravação falhar, o dispositivo deve indicar que a alteração não foi persistida.

#### A.5. Falha do BLE

**Situação:** o BLE não inicializa, perde a ligação ou ocorre um erro numa operação GATT.

**Comportamento proposto:**

- Atualizar o estado BLE no TFT e registar a falha, quando possível.
- Tentar recuperar o serviço ou permitir que o cliente volte a ligar-se.
- Manter a aquisição, o armazenamento e a comunicação Wi-Fi operacionais.
- Não confirmar alterações de configuração que não tenham sido aplicadas e guardadas com sucesso.

#### A.6. Saturação da Queue

**Situação:** os consumidores não processam as amostras à velocidade a que são produzidas.

**Comportamento proposto:**

- Detetar timeouts e contabilizar ocorrências de saturação.
- Definir uma política explícita: aguardar por um período limitado, descartar a amostra mais antiga ou descartar a nova amostra.
- Dimensionar a capacidade com base no período de amostragem e no tempo esperado de indisponibilidade dos consumidores.
- Evitar esperas indefinidas que bloqueiem a aquisição.

A política escolhida deve ser documentada, e as amostras descartadas não podem ser consideradas armazenadas ou transmitidas.

#### A.7. Falha do TFT ou do potenciómetro

**Situação:** o ecrã não inicializa ou a leitura do potenciómetro é inválida.

**Comportamento proposto:**

- Registar o erro e manter as restantes funcionalidades operacionais.
- Se o TFT falhar, permitir o diagnóstico pelo log ou BLE.
- Se o potenciómetro falhar, utilizar um nível de brilho predefinido.
- Evitar que a falha da interface local provoque o reinício do dispositivo.

#### A.8. Princípios gerais de recuperação

- **Isolamento:** uma falha afeta apenas as funcionalidades dependentes do componente.
- **Diagnóstico:** identificar os erros com um código, descrição e gravidade.
- **Registo e indicação:** usar o microSD quando disponível e o TFT/LED para informar o utilizador.
- **Notificação remota:** enviar eventos de erro quando a rede estiver disponível.
- **Recuperação controlada:** repetir operações recuperáveis sem ciclos excessivos.
- **Ausência de bloqueios:** diagnóstico, registo e recuperação não devem bloquear indefinidamente outras tarefas.

O LED deve respeitar os estados obrigatórios definidos na especificação. O vermelho pode indicar uma falha ativa, mas o diagnóstico detalhado deve ficar no TFT ou no log. A prioridade entre estados simultâneos deve ser definida na implementação.

### B. Outras funcionalidades úteis

#### B.1. Monitorização do estado interno

Manter indicadores de diagnóstico, como tempo de funcionamento (*uptime*), número de reinícios, leituras falhadas, erros de escrita e tentativas de reconexão. Estes dados podem ser consultados no TFT ou através de uma característica GATT de diagnóstico.

#### B.2. Alertas ambientais configuráveis

Permitir definir limites de temperatura e humidade por JSON ou BLE. Ao ultrapassar um limite, o sistema pode apresentar um aviso no TFT, registar o evento e enviar uma notificação remota quando existir conectividade. Um alerta ambiental deve ser distinguido de uma falha técnica do sensor.

#### B.3. Estatísticas e atualidade das leituras

Calcular valores mínimos, máximos e médios num intervalo definido. Além disso, indicar se a última leitura é recente, antiga ou inválida, evitando que uma amostra desatualizada seja interpretada como atual.

#### B.4. Integridade e recuperação dos registos

Implementar rotação de ficheiros, limites de espaço, números de sequência e recuperação controlada após reinícios inesperados. A política deve equilibrar preservação do histórico, capacidade do cartão e frequência de escrita.

#### B.5. Diagnóstico remoto por BLE

Permitir consultar, através de uma aplicação BLE genérica, o estado do sensor, armazenamento, rede, tempo de funcionamento e últimos códigos de erro. A interface não deve expor credenciais nem permitir alterações sem validação.

### C. Priorização sugerida

| Prioridade | Funcionalidade                                 | Justificação                                                          |
| ---------- | ---------------------------------------------- | --------------------------------------------------------------------- |
| Alta       | Tratamento de falhas do sensor e microSD       | Mantém a monitorização e evita apresentar ou guardar dados inválidos. |
| Alta       | Recuperação de Wi-Fi e servidor                | Reforça a tolerância a falhas sem interromper o registo local.        |
| Alta       | Validação do JSON                              | Evita configurações inválidas e estados incoerentes.                  |
| Alta       | Gestão da saturação da Queue                   | Previne bloqueios e perdas não controladas de amostras.               |
| Média      | Monitorização do estado interno                | Facilita o diagnóstico e a demonstração.                              |
| Média      | Indicadores de atualidade e alertas ambientais | Melhora a interpretação dos dados e a utilidade prática.              |
| Média      | Rotação e recuperação de registos              | Melhora a utilização do cartão a longo prazo.                         |
| Baixa      | Estatísticas ambientais locais                 | Acrescenta análise sem ser essencial ao funcionamento base.           |

Esta priorização é uma proposta, não uma classificação oficial. A seleção final deve considerar o tempo disponível e a capacidade de testar cada funcionalidade.

### D. Recomendação de implementação

As melhorias iniciais mais úteis são o tratamento de falhas do sensor e do microSD, a recuperação da rede, a validação da configuração e a gestão da saturação da Queue. Em seguida, pode ser acrescentado um mecanismo centralizado de diagnóstico e consulta de estado por BLE.

A gestão de erros não deve depender exclusivamente do LED ou do microSD: se um destes componentes falhar, o sistema deve continuar a diagnosticar e sinalizar problemas pelos meios ainda disponíveis. Recomenda-se ainda definir códigos consistentes, como `ERR_SENSOR_TIMEOUT`, `ERR_SD_WRITE` e `ERR_SERVER_UNAVAILABLE`, para facilitar os testes, os registos e a demonstração prática.

