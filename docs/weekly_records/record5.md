## Requisitos funcionais

| Requisito | Descrição abreviada                         | Estado       | Evidência de validação |
| --------- | ------------------------------------------- | ------------ | ---------------------- |
| RF1       | Aquisição periódica do DHT20                | não iniciado |                        |
| RF2       | Visualização de dados e estados no TFT      | não iniciado |                        |
| RF3       | Indicação de estado através do LED RGB      | não iniciado |                        |
| RF4       | Registo de dados e logs no microSD          | não iniciado |                        |
| RF5       | Publicação de telemetria em tempo real      | não iniciado |                        |
| RF6       | Controlo do brilho através de potenciómetro | não iniciado |                        |
| RF7       | Gestão de configurações através de JSON     | não iniciado |                        |
| RF8       | Serviço BLE Environmental Sensing           | não iniciado |                        |
| RF9       | Serviço BLE de configuração                 | não iniciado |                        |
| RF10      | Provisionamento de rede por BLE             | não iniciado |                        |
| RF11      | Estratégia de redução do consumo energético | não iniciado |                        |

## Requisitos não funcionais

| Requisito | Descrição abreviada                                     | Estado       | Evidência de validação |
| --------- | ------------------------------------------------------- | ------------ | ---------------------- |
| RNF1      | Modularidade e concorrência através de tarefas FreeRTOS | não iniciado |                        |
| RNF2      | Tolerância a falhas de rede                             | não iniciado |                        |
| RNF3      | Tempo de resposta adequado                              | não iniciado |                        |
| RNF4      | Interface simples e intuitiva                           | não iniciado |                        |
| RNF5      | Configuração sem recompilação                           | não iniciado |                        |
| RNF6      | Proteção das credenciais e segurança BLE                | não iniciado |                        |
| RNF7      | Interfaces públicas e baixo acoplamento entre módulos   | não iniciado |                        |

### Semana N (DD/MM a DD/MM)

#### Requisitos trabalhados

Identificar os requisitos funcionais e não funcionais abordados durante a semana, utilizando os respetivos identificadores.

**Requisitos:** RFx, RFy, RNFx

#### Trabalho realizado

Descrever, em duas a quatro frases, o trabalho concluído durante a semana. Identificar os módulos criados ou alterados pelos respetivos nomes de ficheiros ou caminhos no repositório.

Distinguir claramente o trabalho concluído do trabalho que permanece em curso.

#### Decisões tomadas

Registar brevemente as decisões relevantes de arquitetura, estrutura, implementação ou interface, explicando a justificação e as consequências.

Referenciar os ADR correspondentes em `docs/adr/`, incluindo o identificador e o nome do ficheiro. Se não tiver sido tomada nenhuma decisão relevante, indicar **nenhuma**.

#### Dificuldades e resolução

Descrever os problemas encontrados, incluindo os que permanecem por resolver. Para cada problema, indicar:

- **Problema:** descrição e contexto em que ocorreu.
- **Estado:** resolvido, contornado ou pendente.
- **Resolução:** solução aplicada ou próximos passos necessários.

Esta secção não deve ficar vazia numa semana em que tenha existido trabalho efetivo. Se não tiverem surgido dificuldades, indicar explicitamente esse facto.

#### Verificações e testes efetuados

Descrever o que foi testado, o procedimento utilizado e os resultados obtidos. Sempre que aplicável, incluir os cenários de erro testados e o comportamento observado.

Registar os valores medidos relevantes — por exemplo, periodicidade de aquisição, desvios temporais, consumo energético, utilização de memória ou ocupação da pilha — juntamente com as condições de medição, para que os resultados possam ser reproduzidos.

Distinguir os testes executados dos que ainda estão planeados. Não considerar um requisito validado apenas porque o código foi compilado ou executado uma vez.

#### Divisão do trabalho

Identificar o trabalho realizado por cada elemento do grupo através do respetivo número mecanográfico. Quando uma tarefa tiver sido desenvolvida em conjunto, indicar os elementos envolvidos.

- **125302:** trabalho realizado.
- **125855:** trabalho realizado.
- **Trabalho conjunto:** tarefas realizadas em colaboração, quando aplicável.

#### Utilização de ferramentas de geração assistida

Identificar as ferramentas de geração assistida utilizadas e as atividades em que foram aplicadas, incluindo geração ou revisão de código, depuração, documentação e pesquisa técnica.

Descrever sucintamente a utilização efetuada e o contributo da ferramenta. Se não tiver sido utilizada nenhuma ferramenta deste tipo, indicar **nenhuma**.

A utilização destas ferramentas é permitida, mas deve ser declarada de forma transparente, de acordo com as regras de integridade académica aplicáveis.

#### Trabalho previsto para a semana seguinte

Descrever, em duas ou três linhas, as principais tarefas planeadas para a semana seguinte. Sempre que possível, associar cada tarefa aos requisitos a implementar ou validar e aos módulos que se prevê criar ou alterar.
