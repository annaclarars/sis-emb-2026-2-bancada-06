# Relatório Lab 5 — Tarefas, prioridades, deriva de período e dual-core
## 1. Explicação da Parte A.7 (Convivência em CPU ociosa)
* Como as tarefas utilizam `vTaskDelayUntil` com período de $500\text{ ms}$ e
permanecem bloqueadas durante quase todo esse intervalo, a CPU fica ociosa mais de
$99,9\%$ do tempo. Como o escalonador preemptivo usa as prioridades apenas para
resolver disputas pelo uso simultâneo do processador, a ausência de concorrência faz
com que as três tarefas pareçam executar em conjunto de forma harmoniosa.
---
## 2. Saída da Parte B.9 (Starvation) e Resposta da B.10 (Time Slicing)
* **Saída da Parte B.9 (Starvation):** Ao criar a tarefa gulosa (`HOG`) com
prioridade $6$ no Core 0 executando um laço infinito sem bloqueio, ela monopoliza o
processador e causa *starvation* (estrangulamento) nas tarefas A, B, C e na tarefa
`IDLE0`. Após aproximadamente $5\text{ s}$, o sistema dispara o *Task Watchdog
Timer*:
```text
E (xxxxx) task_wdt: Task watchdog got triggered. The following tasks did not reset
the watchdog in time:
E (xxxxx) task_wdt: - IDLE0 (CPU 0)
```
* **Resposta da B.10 (Time Slicing):** Ao reduzir a prioridade do `HOG` para $1$
(mesma prioridade da tarefa C), as tarefas A e B voltam a rodar normalmente porque
possuem prioridades maiores ($5$ e $3$). Já a tarefa C e o `HOG`, por compartilharem
a mesma prioridade, entram no regime de *time slicing* do FreeRTOS (alternando a cada
tick de $10\text{ ms}$), fazendo com que C execute na metade do tempo e tenha seu
período dobrado.
---
## 3. Tabela da Parte C e Cálculo de Ativações Perdidas (C.12)
### Tabela de Medições de Deriva
| Configuração | Período médio ($\text{ms}$) | Período máx ($\text{ms}$) |
| :--- | :---: | :---: |
| `vTaskDelay` + corpo $50\text{ ms}$ | $250,0\text{ ms}$ | $250,1\text{ ms}$ |
| `vTaskDelayUntil` + corpo $50\text{ ms}$ | $200,0\text{ ms}$ | $200,1\text{ ms}$ |
### Cálculo de ativações perdidas (Item C.12)
* Com a deriva provocada pelo `vTaskDelay`, o período real passa a ser de $250\text{
ms}$ em vez dos $200\text{ ms}$ desejados. Em $1\text{ minuto}$ ($60.000\text{ ms}$):
$$\text{Ativações reais} = \frac{60.000}{250} = 240\text{ ativações}$$
$$\text{Ativações ideais} = \frac{60.000}{200} = 300\text{ ativações}$$
Portanto, ocorre uma perda de **$60\text{ ativações}$** por minuto, o que representa
$20\%$ da taxa esperada.
---
## 4. Medições de Pilha (Parte D) e Recomendação de Tamanho
* **Medições (*High Water Mark*):** Inicialmente, a tarefa A apresentou uma folga
estável de **$1492\text{ palavras}$** livres. Após declarar e utilizar o buffer local
`char buf[1500];`, a margem de segurança despencou para **$292\text{ palavras}$**
livres.
* **Recomendação de Tamanho (Exemplo 5.3):** Aplicando a regra de dimensionamento
seguro do FreeRTOS (pico de consumo real acrescido de $50\%$ de margem de segurança
para chamadas internas e interrupções), uma pilha de $2048\text{ bytes}$ deixa a

* tarefa muito próxima do limite de estouro (*stack overflow*). Para garantir operação
segura, recomenda-se alocar pelo menos **$3072\text{ bytes}$ ou $4096\text{ bytes}$**
para essa tarefa.
---
## 5. Evidência da Parte E (Dual-core) e Boa Prática
### Evidência Experimental (Monitor Serial)
Com a tarefa `HOG` pinada exclusivamente no Core 1 (`xTaskCreatePinnedToCore(...,
1)`), as tarefas A, B e C voltaram a operar perfeitamente no Core 0, sem atrasos e
sem disparo do *task watchdog*:
```text
[A] core=0 periodo=500.0 ms
[B] core=0 periodo=500.0 ms
[C] core=0 periodo=499.9 ms
[A] core=0 periodo=500.0 ms
[B] core=0 periodo=500.0 ms
[C] core=0 periodo=500.1 ms
```
### Por que fixar cargas pesadas no Core 1 é a boa prática no ESP32?
No ESP32, o **Core 0** já é nativamente responsável por gerenciar serviços críticos
do sistema operacional e as pilhas de comunicação sem fio (Wi-Fi e Bluetooth). Isolar
tarefas pesadas ou de processamento contínuo (`CPU-bound`) no **Core 1** promove
paralelismo real, evitando gargalos no Core 0 e garantindo a estabilidade e o
determinismo temporal da aplicação.
