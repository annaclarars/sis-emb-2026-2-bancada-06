# Relatório - Lab 1: Ambientação, ESP-IDF e Wokwi

**Bancada:** 06  
**Repositório:** sis-emb-2026-2-bancada-06

---

## 1. Simulação no Wokwi
* **Link do projeto:** [Wokwi - Lab 1 Bancada 6](https://wokwi.com/projects/475346102953590785)
* **Captura de tela do circuito:**  
  *(Adicionar aqui o print da tela do Wokwi funcionando com o LED e o resistor de 220 Ω)*

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

