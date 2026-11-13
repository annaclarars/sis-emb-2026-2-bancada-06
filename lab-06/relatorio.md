# Relatório - Lab 6: Produtor-Consumidor com Fila e ISR com Semáforo

**Bancada:** 06  
**Repositório:** sis-emb-2026-2-bancada-06

---

## 1. Números da Parte B

### Análise de Ocupação e Dimensionamento
* **Itens drenados por rajada (Item 14):** O consumidor drena tipicamente cerca de **30 itens** por rajada ($100\text{ Hz} \times 0,3\text{ s}$). Com a fila configurada para 64 itens, nenhuma perda foi registrada no apagão de $300\text{ ms}$.
* **Perdas com o apagão de 700 ms (Item 15):** Ao elevar o tempo de processamento para $700\text{ ms}$, o produtor acumulava cerca de 70 itens por ciclo, ultrapassando a capacidade limite da fila (64). O monitor serial registrou perdas consecutivas:
  <img width="629" height="475" alt="lab6" src="https://github.com/user-attachments/assets/13c55ec8-c453-4323-9c89-af72fca8e1fa" />

