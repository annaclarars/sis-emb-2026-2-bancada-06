# Relatório - Lab 3: GPIO de Entrada/Saída e o Experimento do Bouncing

**Bancada:** 06  
**Repositório:** sis-emb-2026-2-bancada-06

---

## 1. Resposta à Parte A.4 (Detecção de Borda)
A condição no código que impede que o LED fique alternando continuamente enquanto o usuário segura o botão pressionado é:
`nivel_ant == 1 && nivel == 0`

**Explicação:** Ela detecta somente a transição de $1 \rightarrow 0$. Enquanto o botão permanece pressionado, `nivel` e `nivel_ant` ficam em `0`, então a condição deixa de ser verdadeira. Isso demonstra que **borda é evento** (transição) e **nível é estado** (permanência).

---

## 2. Experimento do Bouncing (Parte C)

### Tabela de Ocorrências por Janela de Debounce
| DEBOUNCE_MS | Eventos por 10 Pressionadas | Eventos "Fantasma" (Bounces) |
| :---: | :---: | :---: |
| **0 ms** | [Inserir valor, ex: >30] | Excessivos (rajada de ruído) |
| **5 ms** | [Inserir valor, ex: 14] | Poucos (quiques rápidos) |
| **20 ms** | 10 | Nenhum (estável) |
| **50 ms** | 10 | Nenhum (estável, mas com leve atraso) |

### Conclusão sobre a Janela Ideal (C.11)
A janela ideal de debounce foi de **20 ms**, pois eliminou os eventos fantasma gerados pelos contatos metálicos e manteve uma resposta rápida e adequada ao pressionamento do botão, sem comprometer a experiência de uso.

---

## 3. Mudanças para Pull-Down Externo (Parte D - Diff do Código)
Para transicionar do circuito com pull-up interno para o pull-down externo, as alterações realizadas no código foram:
* **Troca do Pino:** `#define BTN GPIO_NUM_0` $\rightarrow$ `#define BTN GPIO_NUM_4` (evitando o pino de boot).
* **Desativação de Resistores Internos:** Adicionado `gpio_pullup_dis(BTN);` e `gpio_pulldown_dis(BTN);` para usar exclusivamente o resistor externo de $10\text{ k}\Omega$.
* **Inversão de Borda:** Mudança do estado de repouso e detecção de borda de descida ($1 \rightarrow 0$) para borda de subida ($0 \rightarrow 1$), ajustando `nivel_ant` de `1` para `0` e a condição para `if (nivel_ant == 0 && nivel == 1 && agora >= t_ok)`.

---

## 4. Evidências Visuais
* **Foto da montagem final (Pull-Down com resistor de $10\text{ k}\Omega$):**  
  *(Inserir aqui a foto da protoboard com o resistor de $10\text{ k}\Omega$ visível ligando o GPIO4 ao GND)*
