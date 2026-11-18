# Relatório - Lab 4: Botão por Interrupção, Timer Periódico e Watchdog

**Bancada:** 06  
**Repositório:** sis-emb-2026-2-bancada-06

---

## 1. Análise de Latência (Parte A)

### Tabela de 10 Latências (ISR → Tarefa)
| Evento | Latência ($\mu\text{s}$) |
| :---: | :---: |
| **1** | $600\,\mu\text{s}$ |
| **2** | $3175\,\mu\text{s}$ |
| **3** | $3539\,\mu\text{s}$ |
| **4** | $3546\,\mu\text{s}$ |
| **5** | $3556\,\mu\text{s}$ |
| **6** | $3558\,\mu\text{s}$ |
| **7** | $6001\,\mu\text{s}$ |
| **8** | $6547\,\mu\text{s}$ |
| **9** | $7579\,\mu\text{s}$ |
| **10** | $9549\,\mu\text{s}$ |
| **Média** | **$4765\,\mu\text{s}$** |
| **Máximo** | **$9549\,\mu\text{s}$** |

### Explicação (Capturar vs. Processar)
A ISR captura o evento imediatamente quando ocorre a mudança no GPIO, com resposta na ordem de $\mu\text{s}$. O processamento ocorre depois pela tarefa, quando o escalonador permite sua execução. Por isso, a latência medida inclui o tempo de espera da tarefa, inclusive o `vTaskDelay`, e não representa apenas o tempo de execução da ISR.

---

## 2. Cenário Numérico do Encoder (Parte B.2)

O polling do Lab 3 não conseguiria contar corretamente os pulsos do encoder ($3000\text{ RPM}$, $20\text{ pulsos/volta} \rightarrow 1000\text{ pulsos/s}$). Como cada pulso dura cerca de $1\text{ ms}$, mas o polling verifica o pino somente a cada $10\text{ ms}$, aproximadamente **9 de cada 10 pulsos** seriam perdidos, dependendo da fase entre o polling e o sinal.

---

## 3. Falhas Controladas e Comportamento do WDT (Parte C)

### Falha 1: `printf` dentro da ISR
* **Resultado observado:** O monitor serial exibiu um `panic_abort` acompanhado do *backtrace* de erro devido à chamada de uma função bloqueante (`printf`) dentro da rotina de interrupção.

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
| **1** | $150\text{ ms}$ | $149,8\text{ ms}$ | $\approx 0,2\text{ ms}$ (pequeno jitter do tick) |
| **2** | $1200\text{ ms}$ | $1200,4\text{ ms}$ | $\approx 0,4\text{ ms}$ (pequeno jitter do tick) |
| **3** | $150 / 1200\text{ ms}$ | $149,9 / 1200,2\text{ ms}$ | Validação cruzada com o gerador |

## 5. Por que printf dentro da ISR é proibido e como o firmware contorna (flag volatile lida pela tarefa)?
### Por que é proibido?
Uma interrupção (ISR) precisa ser relâmpago e rodar direto no nível do hardware. O printf faz coisas pesadas e demoradas, como gerenciar buffers de texto e travar recursos de sistema (mutexes) que exigem um ambiente seguro de tarefa do FreeRTOS. Se você bota um printf lá dentro, o sistema se perde e dá tela azul / panic na hora.

### Como o firmware contorna?
Para contornar a proibição do `printf` dentro de uma ISR, o firmware utiliza a tática da separação de tarefas: a interrupção acorda imediatamente, captura o tempo com `esp_timer_get_time()` e atualiza uma variável global marcada com a palavra-chave `volatile` para indicar o evento sem se bloquear, permitindo que uma tarefa normal de fundo monitore essa variável e execute o processamento pesado ou a exibição de logs de forma segura e com todo o suporte do sistema operacional.
