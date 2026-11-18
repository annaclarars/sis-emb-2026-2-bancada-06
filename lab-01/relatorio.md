# Relatório - Lab 1: Ambientação, ESP-IDF e Wokwi

**Bancada:** 06  
**Repositório:** sis-emb-2026-2-bancada-06

---

## 1. Simulação no Wokwi
* **Link do projeto:** [Wokwi - Lab 1 Bancada 6](https://wokwi.com/projects/475346102953590785)
* **Print da tela do Wokwi funcionando com o LED e o resistor de 220 Ω:** [wokwi-lab1.pdf](https://github.com/user-attachments/files/34706956/wokwi-lab1.pdf)

---

## 2. Tabela do Experimento 1 (Período vs. Percepção)

| Período Testado | Piscar Perceptível? (Sim/Não) | Observações |
| :--- | :--- | :--- |
| **500 ms** (padrão) | Sim | Piscar lento e regular (1 Hz). |
| **200 ms** | Sim | Fica visivelmente mais rápido. |
| **20 ms** | Sim (com flashes rápidos) | Percebe-se cintilação rápida. |
| **10 ms** | Não | O olho humano passa a integrar o brilho, parecendo continuamente aceso. |

---

## 3. Código do Experimento 2 (LED Assimétrico 900/100 ms)

Trecho do laço principal (`while(1)`) implementando a assimetria com dois retardos diferentes:

```c
while (1) {
    nivel = !nivel;
    gpio_set_level(PINO_LED, nivel);
    printf("LED = %d\n", nivel);
    vTaskDelay(pdMS_TO_TICKS(900)); // Acionado por 900 ms

    nivel = !nivel;
    gpio_set_level(PINO_LED, nivel);
    printf("LED = %d\n", nivel);
    vTaskDelay(pdMS_TO_TICKS(100)); // Apagado por 100 ms
}

```
## 4. Resposta ao Experimento 3 (Atraso Ocupado vs. `vTaskDelay`)

O uso de um laço for vazio (conhecido como atraso ocupado) muda completamente a forma como o microcontrolador lida com o tempo e com os recursos. Como vimos no Exemplo resolvido 1.1 da teoria, isso impacta a aplicação por três motivos principais:

1. **Uso excessivo da CPU:** O laço obriga a CPU a rodar a 100% da sua capacidade só executando instruções vazias e repetitivas para passar o tempo.
2. **Bloqueio de tarefas:** Ao contrário disso, a função vTaskDelay() avisa o sistema operacional que a tarefa pode pausar. Com isso, a CPU fica livre para entrar em modos de baixo consumo (como o sleep) ou se dedicar a outras rotinas importantes.
3. **Impacto na bateria:** Em um circuito real funcionando na bateria, desperdiçar ciclos de processamento desse jeito custa caro. Enquanto o uso correto do vTaskDelay poupa energia, um atraso ocupado pode reduzir drasticamente a vida útil de um equipamento autônomo, fazendo com que a bateria dure poucas horas em vez de meses.

## 5. Print do idf.py --version do PC da bancada

<img width="818" height="581" alt="Screenshot from 2026-11-18 15-07-07" src="https://github.com/user-attachments/assets/f8cc6740-ab32-440b-adad-d3137ebdfd8b" />
