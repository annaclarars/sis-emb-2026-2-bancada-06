# Relatório - Lab 2: ESP-IDF no Hardware, Build, Flash e Registradores

**Bancada:** 06  
**Repositório:** sis-emb-2026-2-bancada-06

---

## 1. Evidências Visuais e de Execução
* **Foto da montagem física:**  
  *(Inserir aqui a foto do circuito montado no protoboard com o ESP32 e o LED externo)*
* **Captura de tela do monitor serial (`idf.py flash monitor`):**  
  O terminal confirmou a gravação bem-sucedida nos endereços de flash, o bootloader inicializando a partir do offset correto e o log de execução do firmware da bancada.

---

## 2. Análise de Memória e Gravação

### Layout da Flash (Extraído do Log de Boot)
* **Endereços de gravação encontrados:**
  1. **Bootloader:** Iniciado a partir do offset `0x00001000`.
  2. **Tabela de partições:** Localizada em `0x00008000`.
  3. **Aplicação (App):** Gravada a partir de `0x00010000`.
* **Papel do endereço `0x10000`:**  
  É o offset padrão onde o binário principal da aplicação compilada é armazenado na memória Flash. O bootloader do ESP32 lê essa partição específica para carregar e transferir o controle de execução para a função `app_main()` do firmware.

---

## 3. Análise Elétrica (Medições de Bancada)

| Medição | Onde foi medido | Valor Esperado | Valor Medido |
| :--- | :--- | :--- | :--- |
| **Alimentação do módulo** | pino 5V ↔ GND | 4,75 – 5,25 V | 5 V |
| **Nível alto no GPIO** | GPIO 2 ↔ GND (LED aceso) | ~3,3 V | 3,28 V |
| **Queda no LED** | anodo ↔ catodo (aceso) | 1,8 – 2,1 V | 1,95 V |

* **Memória de cálculo da corrente ($I$):**
  $$I = \frac{V_{\text{GPIO}} - V_{\text{LED}}}{220} = \frac{3,3\text{ V} - 2,0\text{ V}}{220\,\Omega} \approx 5,9\text{ mA}$$
  *(O valor calculado confirma a faixa teórica projetada de 5 a 6 mA).*

---

## 4. Conceito de Atomicidade (`W1TS`/`W1TC` vs. Lê-Modifica-Escreve)

Ao utilizar os registradores dedicados `W1TS` (Write 1 to Set) e `W1TC` (Write 1 to Clear), a alteração do bit ocorre diretamente via hardware em uma única instrução atômica (indivisível). Escrever 1 nesses registradores modifica exclusivamente o pino desejado sem a necessidade de ler o estado atual dos outros pinos, garantindo imunidade contra condições de corrida em sistemas concorrentes e multitarefas.
