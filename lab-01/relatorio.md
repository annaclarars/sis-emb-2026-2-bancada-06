# Relatório - Lab 1: Ambientação, ESP-IDF e Wokwi

**Bancada:** 06  
**Repositório:** sis-emb-2026-2-bancada-06

---

## 1. Simulação no Wokwi
* **Link do projeto:** [Wokwi - Lab 1 Bancada 6](https://wokwi.com/projects/475346102953590785)


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

O uso de um laço `for` vazio (atraso ocupado) impacta diretamente o funcionamento e o consumo do microcontrolador por três motivos principais:

1. **Uso excessivo da CPU:** O laço mantém a CPU operando a 100% de capacidade executando instruções totalmente inúteis apenas para passar o tempo.
2. **Bloqueio de tarefas:** Diferente disso, a função `vTaskDelay()` bloqueia a tarefa atual de forma eficiente e devolve o controle do processador ao sistema operacional (RTOS), permitindo que ele entre em estado de baixo consumo ou execute outras rotinas.
3. **Impacto na bateria:** Em dispositivos embarcados reais alimentados a bateria, desperdiçar ciclos de processamento significa desperdiçar carga preciosa — o software define diretamente o consumo energético, podendo ser a diferença entre um equipamento durar meses ou apenas poucas horas.

## 5. Print da tela do Wokwi funcionando com o LED e o resistor de 220 Ω
[wokwi-lab1.pdf](https://github.com/user-attachments/files/34706956/wokwi-lab1.pdf)



