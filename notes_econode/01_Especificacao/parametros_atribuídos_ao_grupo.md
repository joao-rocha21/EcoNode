# Parâmetros Atribuídos ao Grupo
## 1. Identificação do grupo

|Parâmetro|Valor|
|---|---|
|Turma prática|P1|
|Número mecanográfico — Estudante 1|`125302`|
|Número mecanográfico — Estudante 2|`125855`|

## 2. Soma dos algarismos ((S))
Calcula-se a soma dos algarismos de cada número mecanográfico.

**Estudante 1:**

$$
S_1 = 1 + 2 + 5 + 3 + 0 + 2 = 13
$$

**Estudante 2:**

$$
S_2 = 1 + 2 + 5 + 8 + 5 + 5 = 26
$$

A soma total é:

$$
S = S_1 + S_2 = 13 + 26 = 39
$$
## 3. Cálculo de (D)
O parâmetro (D) corresponde ao resto da divisão de (S) por 10.

$$
D = S \bmod 10
$$

$$
D = 39 \bmod 10 = 9
$$
## 4. Identificador do nó (`node_id`)
O identificador do nó é calculado através da seguinte fórmula:

$\text{node\_id} = 1000 + 10 \times T + D$

Em que:
- (T = 1), correspondente à turma prática P1;
- (D = 9), calculado na secção anterior.

Substituindo os valores:

$$
\begin{aligned}
\text{node\_id} &= 1000 + 10 \times 1 + 9 \
&= 1019
\end{aligned}
$$

**Resultado:** `node_id = 1019`
## 5. Base dos tópicos de telemetria
A base utilizada para os tópicos ou recursos de telemetria é:

```
econode/1019/
```

O identificador do nó permite distinguir a telemetria deste grupo da dos restantes grupos.
## 6. Conversão do `node_id` para hexadecimal
O identificador decimal `1019` é convertido para hexadecimal:

$$
1019_{10} = 03FB_{16}
$$

**Sufixo hexadecimal do UUID:** `03FB`
## 7. UUIDs do serviço GATT de configuração
### 7.1. UUID do serviço
O UUID do serviço GATT proprietário de configuração é:

```
E0C0DE00-0000-4000-8000-0000000003FB
```

### 7.2. UUIDs das características

|Característica|UUID|
|---|---|
|Característica 1|`E0C0DE01-0000-4000-8000-0000000003FB`|
|Característica 2|`E0C0DE02-0000-4000-8000-0000000003FB`|
|Característica 3|`E0C0DE03-0000-4000-8000-0000000003FB`|

Os UUIDs partilham o mesmo sufixo `03FB`, associado ao identificador do grupo. O prefixo distingue o serviço (`00`) das três características (`01`, `02` e `03`).
## 8. Resumo dos parâmetros

|Parâmetro|Resultado|
|---|---|
|(S_1) — Soma do estudante 1|13|
|(S_2) — Soma do estudante 2|26|
|(S) — Soma total|39|
|(D)|9|
|(T)|1|
|`node_id` (decimal)|`1019`|
|`node_id` (hexadecimal)|`03FB`|
|Base de telemetria|`econode/1019/`|
|UUID do serviço GATT|`E0C0DE00-0000-4000-8000-0000000003FB`|