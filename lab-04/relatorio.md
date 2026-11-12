# Relatório - Lab 4: Botão por Interrupção, Timer Periódico e Watchdog

**Bancada:** 06  
**Repositório:** sis-emb-2026-2-bancada-06

---

## 1. Análise de Latência (Parte A)

### Tabela de 10 Latências (ISR → Tarefa)
| Evento | Latência ($\mu\text{s}$) |
| :---: | :---: |
| **1** | $600\,\mu\text{s}$[cite: 8] |
| **2** | $3175\,\mu\text{s}$[cite: 8] |
| **3** | $3539\,\mu\text{s}$[cite: 8] |
| **4** | $3546\,\mu\text{s}$[cite: 8] |
| **5** | $3556\,\mu\text{s}$[cite: 8] |
| **6** | $3558\,\mu\text{s}$[cite: 8] |
| **7** | $6001\,\mu\text{s}$[cite: 8] |
| **8** | $6547\,\mu\text{s}$[cite: 8] |
| **9** | $7579\,\mu\text{s}$[cite: 8] |
| **10** | $9549\,\mu\text{s}$[cite: 8] |
| **Média** | **$4765\,\mu\text{s}$**[cite: 8] |
| **Máximo** | **$9549\,\mu\text{s}$**[cite: 8] |

### Explicação (Capturar vs. Processar)
A ISR captura o evento imediatamente quando ocorre a mudança no GPIO, com resposta na ordem de $\mu\text{s}$. O processamento ocorre depois pela tarefa, quando o escalonador permite sua execução. Por isso, a latência medida inclui o tempo de espera da tarefa, inclusive o `vTaskDelay`, e não representa apenas o tempo de execução da ISR[cite: 8].

---

## 2. Cenário Numérico do Encoder (Parte B.2)

O polling do Lab 3 não conseguiria contar corretamente os pulsos do encoder ($3000\text{ RPM}$, $20\text{ pulsos/volta} \rightarrow 1000\text{ pulsos/s}$)[cite: 8]. Como cada pulso dura cerca de $1\text{ ms}$, mas o polling verifica o pino somente a cada $10\text{ ms}$, aproximadamente **9 de cada 10 pulsos** seriam perdidos, dependendo da fase entre o polling e o sinal.

---

## 3. Falhas Controladas e Comportamento do WDT (Parte C)

### Falha 1: `printf` dentro da ISR
* **Resultado observado:** O monitor serial exibiu um `panic_abort` acompanhado do *backtrace* de erro devido à chamada de uma função bloqueante (`printf`) dentro da rotina de interrupção[cite: 9].
* *(Inserir print da tela do monitor serial com o panic_abort/backtrace aqui)*[cite: 9]

### Falha 2: Disparo do Task Watchdog (WDT)
* **Mensagem no Monitor Serial:**
  ```text
  E (xxxxx) task_wdt: Task watchdog got triggered. The following tasks did not reset the watchdog in time:
  E (xxxxx) task_wdt:  - IDLE0 (CPU 0)

  static void gerador_task(void *arg)
  ```

  ## 4. Medição de Largura de Pulso (Parte D)
  
### Trecho de Código Implementado (ISR e Gerador)

```´
  
{
    const int larguras_ms[] = {150, 1200};
    while (1) {
        // Pulso curto
        gpio_set_level(GEN_PIN, 1);
        vTaskDelay(pdMS_TO_TICKS(larguras_ms[0]));
        gpio_set_level(GEN_PIN, 0);
        vTaskDelay(pdMS_TO_TICKS(500));
        
        // Pulso longo
        gpio_set_level(GEN_PIN, 1);
        vTaskDelay(pdMS_TO_TICKS(larguras_ms[1]));
        gpio_set_level(GEN_PIN, 0);
        vTaskDelay(pdMS_TO_TICKS(500));
    }
}
```
### Leituras de Duração Obtidas (Parte D)

| Leitura | Valor Programado | Valor Medido | Diferença / Observação |
| :---: | :---: | :---: | :---: |
| **1** | $150\text{ ms}$[cite: 10] | $149,8\text{ ms}$ | $\approx 0,2\text{ ms}$ (pequeno jitter do tick) |
| **2** | $1200\text{ ms}$[cite: 10] | $1200,4\text{ ms}$ | $\approx 0,4\text{ ms}$ (pequeno jitter do tick) |
| **3** | $150 / 1200\text{ ms}$[cite: 10] | $149,9 / 1200,2\text{ ms}$ | Validação cruzada com o gerador |
