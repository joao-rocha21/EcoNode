# Índice do projeto DC — EcoNode

Esta pasta reúne a documentação de apoio ao projeto de Dispositivos Conectados (DC). A organização separa gestão, especificação, arquitetura, planeamento, implementação e ideias exploratórias.

## Estrutura recomendada

- `00_Gestao/` — avaliação, modelo de registo semanal e lembretes operacionais.
- `01_Especificacao/` — visão geral, requisitos oficiais de trabalho e parâmetros específicos do grupo.
- `02_Arquitetura/` — arquitetura proposta e organização dos módulos.
- `03_Planeamento/` — roadmap e acompanhamento dos requisitos/evidências.
- `04_Implementacao/` — decisões e notas complementares sobre implementação e tratamento de erros.
- `05_Referencias/` — ideias iniciais que podem ser consultadas, mas não devem ser confundidas com requisitos aprovados.

## Por onde começar

1. [`01_Especificacao/especificacao_geral_econode.md`](01_Especificacao/especificacao_geral_econode.md) — compreender o âmbito e os requisitos do projeto.
2. [`01_Especificacao/parametros_atribuídos_ao_grupo.md`](01_Especificacao/parametros_atribuídos_ao_grupo.md) — consultar os identificadores e UUIDs específicos.
3. [`02_Arquitetura/arquitetura_e_estrutura_do_projeto.md`](arquitetura_do_projeto.md) — perceber como dividir o código.
4. [`03_Planeamento/roadmap_desenvolvimento.md`](03_Planeamento/roadmap_desenvolvimento.md) — seguir as fases de desenvolvimento.
5. [`03_Planeamento/estado_evidencias_requisitos.md`](estado_evidencias_requisitos.md) — atualizar o progresso e as evidências.
6. [`00_Gestao/modelo_registo_semanal.md`](00_Gestao/modelo_registo_semanal.md) — usar como base para os registos semanais.

## Descrição de cada ficheiro

### 00_Gestao/

- [`avaliacao_projeto.md`](00_Gestao/avaliacao_projeto.md) — Critérios e pesos da avaliação do projeto, incluindo progresso no GitHub, documentação, defesa e discussão.
- [`modelo_registo_semanal.md`](00_Gestao/modelo_registo_semanal.md) — Modelo para os registos semanais: requisitos trabalhados, trabalho realizado, decisões, dificuldades, testes, divisão de trabalho e uso de ferramentas assistidas.
- [`notas_operacionais.md`](00_Gestao/notas_operacionais.md) — Lembretes curtos sobre releases semanais, documentação das alterações e tratamento de erros.

### 01_Especificacao/

- [`especificacao_geral_econode.md`](01_Especificacao/especificacao_geral_econode.md) — Visão geral do EcoNode, componentes, requisitos funcionais e não funcionais e parâmetros gerais da implementação.
- [`requisitos_funcionais_e_nao_funcionais.md`](01_Especificacao/requisitos_funcionais_e_nao_funcionais.md) — Lista de requisitos RF e RNF em formato mais direto, útil como referência durante implementação e validação.
- [`parametros_atribuídos_ao_grupo.md`](01_Especificacao/parametros_atribuídos_ao_grupo.md) — Cálculos específicos do grupo: node_id, tópico/base de telemetria e UUIDs do serviço e características GATT.

### 02_Arquitetura/

- [`arquitetura_do_projeto.md`](arquitetura_do_projeto.md) — Proposta de arquitetura e integração dos componentes do EcoNode.
- [`arvore_ficheiros.md`](arvore_ficheiros.md) — Estrutura de diretórios e responsabilidades dos módulos.

### 03_Planeamento/

- [`roadmap_desenvolvimento.md`](03_Planeamento/roadmap_desenvolvimento.md) — Plano faseado de desenvolvimento, dependências, tarefas e critérios de conclusão/teste.
- [`estado_evidencias_requisitos.md`](estado_evidencias_requisitos.md) — Tabela para acompanhar o estado de cada requisito e associar evidências de validação.

### 04_Implementacao/

- [`funcionalidades_extra_e_tratamento_erros.md`](04_Implementacao/funcionalidades_extra_e_tratamento_erros.md) — Ideias e orientações adicionais de implementação, incluindo tratamento de falhas e comportamentos de recuperação.

### 05_Referencias/

- [`ideias_iniciais_projeto_iot.md`](05_Referencias/ideias_iniciais_projeto_iot.md) — Brainstorming inicial de ideias IoT e utilizações possíveis dos componentes físicos; manter como referência histórica, não como especificação oficial.

## Notas de organização

- Os nomes foram normalizados para serem descritivos e consistentes; os ficheiros originais foram preservados quanto ao conteúdo.
- `especificacao_geral_econode.md` e `requisitos_funcionais_e_nao_funcionais.md` têm alguma sobreposição: o primeiro dá contexto global; o segundo é uma referência rápida aos requisitos. Ao alterar requisitos, manter ambos sincronizados ou escolher um como fonte principal.
- A tabela de estado dos requisitos contém entradas abreviadas (`...`) no original; completar antes de a usar como acompanhamento definitivo.
- Confirmar com a equipa docente a versão do ESP-IDF e quaisquer alterações posteriores à especificação antes de tomar os documentos de planeamento como definitivos.
- As ideias em `05_Referencias/` são exploratórias e não fazem parte automaticamente do âmbito aprovado.
