# Modelo de Registo Semanal — EcoNode

## 1. Tabela de estado dos requisitos

Esta tabela deve ser atualizada em cada registo semanal, substituindo a versão anterior. Deve refletir o estado atual de todos os requisitos do projeto.

> [!note]
> A tabela fica sempre no topo e depois fica o registo semanal.

Os estados admissíveis são:

- **não iniciado** — o desenvolvimento ainda não começou;
- **em curso** — o requisito está a ser implementado, mas ainda não funciona integralmente;
- **funcional** — o requisito funciona nas condições testadas, mas a validação ainda não foi concluída;
- **validado** — o requisito foi testado, incluindo os cenários de erro relevantes, e cumpre os critérios definidos.

A coluna **Evidência de validação** só deve ser preenchida quando o requisito estiver no estado `funcional` ou `validado`. Deve conter uma referência para uma **imagem** ou **captura de ecrã** que demonstre o funcionamento do requisito. No estado `validado`, a evidência deve demonstrar também o tratamento dos erros relevantes.

Guardar as evidências na pasta `docs/evidences/`, utilizando nomes de ficheiro descritivos que permitam associá-las inequivocamente aos requisitos.

| Requisito | Descrição abreviada                          | Estado       | Evidência de validação |
| --------- | -------------------------------------------- | ------------ | ---------------------- |
| RF1       | Aquisição periódica do DHT20                 | não iniciado |                        |
| RF2       | Visualização no TFT                          | não iniciado |                        |
| RNF1      | Estratégia de redução do consumo energético  | não iniciado |                        |
| RNF2      | Distribuição por tarefas, sem bloqueio mútuo | não iniciado |                        |


**Nota:** confirmar as descrições e a lista de requisitos com a especificação oficial do projeto antes de utilizar a tabela como registo definitivo.

---

## 2. Registos de decisão de arquitetura (ADR)

O repositório deve conter o diretório `docs/adr/`, com pelo menos **cinco registos de decisão de arquitetura (Architecture Decision Records — ADR) datados**, conforme exigido no projeto.

Cada ADR documenta uma decisão arquitetural relevante, explicando o problema que a motivou, as alternativas consideradas, a escolha efetuada e as respetivas consequências. Deve ser criado um ficheiro Markdown por decisão, com numeração sequencial e um nome descritivo.

Exemplos de nomes:

- `docs/adr/0001-estrutura-de-modulos.md`
- `docs/adr/0002-gestao-de-tarefas.md`
- `docs/adr/0003-acesso-ao-barramento-spi.md`

Cada registo deve conter, pelo menos:

- **Data e estado:** data da decisão e estado atual, por exemplo, proposta, aceite ou substituída.
- **Contexto:** problema, requisitos e restrições que motivaram a decisão.
- **Alternativas consideradas:** opções relevantes, com as respetivas vantagens e desvantagens.
- **Decisão:** opção escolhida e justificação.
- **Consequências:** impactos positivos, limitações e compromissos assumidos.

Os ADR devem documentar decisões reais do grupo, não apenas descrever a implementação. São exemplos de temas relevantes a estrutura dos módulos, a comunicação entre tarefas, a sincronização de recursos partilhados, a gestão da conectividade e o tratamento de falhas.

Uma decisão já aceite não deve ser reescrita retroativamente quando a opção mudar. Deve ser criado um novo ADR que explique a alteração e referencie o registo anterior.

Na secção semanal «Decisões tomadas», devem ser referenciados os ADR criados ou afetados durante essa semana. Se não tiver sido tomada nenhuma decisão arquitetural relevante, indicar **nenhuma**.


> [!note]
> Sempre que ajudem a demonstrar os resultados, incluir capturas de ecrã, fotografias do protótipo, gráficos de medições ou diagramas que ilustrem o funcionamento observado. Cada imagem deve ter uma legenda. As evidências de validação dos requisitos devem também ser guardadas em `docs/evidences/` e referenciadas na tabela de estado dos requisitos.


**Referência:** [Architecture Decision Records — repositório oficial no GitHub](https://github.com/architecture-decision-record/architecture-decision-record#how-to-start-using-adrs).

---
## 3. Estrutura de docs


|Local|Exemplos úteis|
|---|---|
|`docs/adr/`|Diagrama da arquitetura escolhida, alternativas de comunicação entre tarefas, esquema do acesso partilhado ao SPI.|
|Registos semanais|Fotografia do protótipo, captura do terminal com logs, gráfico de periodicidade do sensor, teste de desconexão Wi-Fi.|
|`docs/evidences/`|Capturas ou fotografias que demonstrem diretamente o funcionamento e a validação dos requisitos.|

Para manter o repositório organizado, sugiro esta estrutura:

```
docs/
├── adr/
│   ├── 0001-estrutura-de-modulos.md
│   ├── 0002-gestao-de-tarefas.md
│   └── images/
│       └── arquitetura-geral.png
├── evidences/
│   ├── RF1-aquisicao-dht20.png
│   └── RNF3-reconexao-wifi.png
└── weekly-records/
    ├── semana-01.md
    └── semana-02.md
```


## 4. Registo semanal

> Isto é colocado no README.md e numa pasta registos semanais para não ter que andar a ver as versões antigas do histórico.

Duplicar esta secção para cada nova semana de trabalho, mantendo os registos anteriores.

### Semana N (DD/MM a DD/MM)

#### 4.1. Requisitos trabalhados

Identificar os requisitos funcionais e não funcionais abordados durante a semana, utilizando os respetivos identificadores.

**Requisitos:** RFx, RFy, RNFx

#### 4.2. Trabalho realizado

Descrever, em duas a quatro frases, o trabalho concluído durante a semana. Identificar os módulos criados ou alterados pelos respetivos nomes de ficheiros ou caminhos no repositório.

Distinguir claramente o trabalho concluído do trabalho que permanece em curso.

#### 4.3. Decisões tomadas

Registar brevemente as decisões relevantes de arquitetura, estrutura, implementação ou interface, explicando a justificação e as consequências.

Referenciar os ADR correspondentes em `docs/adr/`, incluindo o identificador e o nome do ficheiro. Se não tiver sido tomada nenhuma decisão relevante, indicar **nenhuma**.

#### 4.4. Dificuldades e resolução

Descrever os problemas encontrados, incluindo os que permanecem por resolver. Para cada problema, indicar:

- **Problema:** descrição e contexto em que ocorreu.
- **Estado:** resolvido, contornado ou pendente.
- **Resolução:** solução aplicada ou próximos passos necessários.

Esta secção não deve ficar vazia numa semana em que tenha existido trabalho efetivo. Se não tiverem surgido dificuldades, indicar explicitamente esse facto.

#### 4.5. Verificações e testes efetuados

Descrever o que foi testado, o procedimento utilizado e os resultados obtidos. Sempre que aplicável, incluir os cenários de erro testados e o comportamento observado.

Registar os valores medidos relevantes — por exemplo, periodicidade de aquisição, desvios temporais, consumo energético, utilização de memória ou ocupação da pilha — juntamente com as condições de medição, para que os resultados possam ser reproduzidos.

Distinguir os testes executados dos que ainda estão planeados. Não considerar um requisito validado apenas porque o código foi compilado ou executado uma vez.

#### 4.6. Divisão do trabalho

Identificar o trabalho realizado por cada elemento do grupo através do respetivo número mecanográfico. Quando uma tarefa tiver sido desenvolvida em conjunto, indicar os elementos envolvidos.

- **125302:** trabalho realizado.
- **125855:** trabalho realizado.
- **Trabalho conjunto:** tarefas realizadas em colaboração, quando aplicável.

#### 4.7. Utilização de ferramentas de geração assistida

Identificar as ferramentas de geração assistida utilizadas e as atividades em que foram aplicadas, incluindo geração ou revisão de código, depuração, documentação e pesquisa técnica.

Descrever sucintamente a utilização efetuada e o contributo da ferramenta. Se não tiver sido utilizada nenhuma ferramenta deste tipo, indicar **nenhuma**.

A utilização destas ferramentas é permitida, mas deve ser declarada de forma transparente, de acordo com as regras de integridade académica aplicáveis.

#### 4.8. Trabalho previsto para a semana seguinte

Descrever, em duas ou três linhas, as principais tarefas planeadas para a semana seguinte. Sempre que possível, associar cada tarefa aos requisitos a implementar ou validar e aos módulos que se prevê criar ou alterar.

---

## 4. Exemplo de registo preenchido

### Semana 2 (06/10 a 12/10)

#### 4.1. Requisitos trabalhados

RF1, RF4 e RF7.

#### 4.2. Trabalho realizado

Implementada a tarefa de aquisição do DHT20 com periodicidade lida do ficheiro de configuração (`main/sensor_task.c`). Criado o módulo de configuração, com a interface `config_get()`, responsável pela leitura do JSON no cartão (`components/config/`). Implementada a escrita de registos em CSV no microSD, utilizando o barramento SPI partilhado com o TFT e protegido por mutex (`components/storage/`).

#### 4.3. Decisões tomadas

O acesso ao SPI é protegido por um mutex único, em vez de uma tarefa árbitro dedicada, por simplicidade e por não existirem, nesta fase, requisitos temporais concorrentes que justifiquem uma solução mais complexa. Decisão registada em `docs/adr/0003-acesso-ao-barramento-spi.md`.

#### 4.4. Dificuldades e resolução

- **Problema:** a montagem do sistema de ficheiros falhava intermitentemente durante o arranque.
- **Estado:** resolvido.
- **Resolução:** identificada uma interação entre a inicialização do cartão e a sequência de arranque do TFT, que partilham o barramento SPI. A inicialização foi serializada.
- **Problema adicional:** comportamento indefinido quando o cartão não é detetado.
- **Estado:** pendente.
- **Impacto:** o sistema arranca atualmente com a configuração por omissão compilada, o que não cumpre o comportamento esperado para o RF7.

#### 4.5. Verificações e testes efetuados

A aquisição foi verificada com períodos de 1 s, 60 s e 3600 s, através da inspeção dos tempos registados no CSV. Para o período de 1 s, o desvio observado foi inferior a 20 ms. Foi também testada a remoção do cartão durante o funcionamento: a aquisição manteve-se e o erro foi sinalizado através do LED.

#### 4.6. Divisão do trabalho

- **125302:** desenvolvimento do módulo de configuração e leitura do JSON.
- **125855:** desenvolvimento da tarefa de aquisição e escrita no cartão.
- **Ambos os elementos:** depuração do conflito no barramento SPI.

#### 4.7. Utilização de ferramentas de geração assistida

Consultado um assistente de IA para esclarecer a sequência de inicialização da função `esp_vfs_fat_sdspi_mount()` e interpretar mensagens de erro do driver SPI. O código dos módulos foi escrito pelo grupo.

#### 4.8. Trabalho previsto para a semana seguinte

Desenvolvimento do cliente de telemetria com mecanismo de reconexão. Implementação da primeira versão do servidor MAP com o Sensing Service.

---

## 5. Critérios de preenchimento

- Atualizar a tabela de estado dos requisitos em cada registo semanal.
- Manter os registos das semanas anteriores para preservar o histórico de desenvolvimento.
- Manter pelo menos cinco ADR datados em `docs/adr/`, documentando decisões arquiteturais reais e as alternativas consideradas.
- Associar as decisões relevantes de cada semana aos respetivos ADR.
- Registar dificuldades e resultados de testes de forma verificável, sem apresentar resultados esperados como se já tivessem sido obtidos.
- Guardar as evidências na pasta `docs/evidences/` e referenciá-las na tabela apenas nos estados permitidos.
- Declarar a utilização de ferramentas de geração assistida, mesmo quando o seu contributo se limitou à pesquisa, documentação ou depuração.
