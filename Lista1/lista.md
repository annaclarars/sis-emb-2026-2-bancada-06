# Lista 1 — Sistemas Embarcados

## Parte A — Introdução a sistemas embarcados

### Q1. Defina sistema embarcado e cite as cinco restrições de projeto.

**Passo 1 — Definição**
Um sistema embarcado é um sistema computacional desenvolvido para executar uma função específica dentro de um equipamento ou produto eletrônico. Ele combina hardware e software para controlar, monitorar ou processar informações.

**Passo 2 — As cinco restrições de projeto**
* **Tempo real:** o sistema precisa responder dentro de um prazo determinado. *Exemplo:* sistema de airbag de um automóvel.
* **Energia:** precisa consumir pouca energia para funcionar por muito tempo. *Exemplo:* relógio inteligente alimentado por bateria.
* **Custo:** deve ter baixo custo de fabricação. *Exemplo:* controle remoto de televisão.
* **Memória:** precisa funcionar com pouca memória disponível. *Exemplo:* sensor de temperatura simples.
* **Confiabilidade:** deve funcionar corretamente e sem falhas. *Exemplo:* equipamento de monitoramento hospitalar.


---

### Q2. Classifique como MCU, MPU/SoC ou FPGA.

Primeiro, vamos entender:
* **MCU:** microcontrolador, indicado para controle de equipamentos, baixo consumo e tarefas específicas.
* **MPU/SoC:** processador ou sistema em chip, indicado para aplicações mais complexas e que exigem maior processamento.
* **FPGA:** circuito lógico programável, indicado para desenvolver e testar arquiteturas digitais personalizadas.

**Passo 1 — Classificação**

| Aplicação | Tecnologia | Justificativa |
| :--- | :--- | :--- |
| (a) Controle de airbag | **MCU** | Precisa de resposta rápida e confiável. |
| (b) Roteador Wi-Fi doméstico | **MPU/SoC** | Necessita de processamento de rede e várias funções simultâneas. |
| (c) Protótipo de codec de vídeo proprietário | **FPGA** | Permite implementar e testar circuitos digitais personalizados. |
| (d) Sensor de umidade a bateria por 2 anos | **MCU** | Apresenta baixo consumo de energia e custo reduzido. |



---

### Q3. Rastreador GPS: corrente média e autonomia.

**Dados do exercício:**
* Corrente durante a transmissão ($I_{\text{trans}}$): $120\text{ mA}$
* Tempo de transmissão ($t_{\text{trans}}$): $3\text{ segundos}$
* Intervalo entre transmissões ($T_{\text{int}}$): $10\text{ minutos}$
* Corrente em repouso ($I_{\text{repouso}}$): $40\text{ }\mu\text{A}$
* Bateria: $1200\text{ mAh}$

**Passo 1 — Converter as unidades**
$$40\text{ }\mu\text{A} = 0,04\text{ mA}$$
O intervalo entre transmissões é:
$$10 \times 60 = 600\text{ s}$$

**Passo 2 — Calcular o ciclo de transmissão**
$$D = \frac{3}{600}$$
$$D = 0,005$$

**Passo 3 — Calcular a corrente média**
Utilizamos a fórmula:
$$I_{\text{média}} = I_{\text{trans}} \times D + I_{\text{repouso}} \times (1 - D)$$

Substituindo:
$$I_{\text{média}} = 120(0,005) + 0,04(0,995)$$
$$I_{\text{média}} = 0,6 + 0,0398$$
$$I_{\text{média}} = 0,6398\text{ mA}$$

**Passo 4 — Calcular a autonomia**
A fórmula é:
$$T = \frac{\text{Capacidade}}{\text{Corrente Média}}$$
$$T = \frac{1200}{0,6398}$$
$$T \approx 1875,6\text{ horas}$$

Convertendo para dias:
$$T = \frac{1875,6}{24}$$
$$T \approx 78,15\text{ dias}$$

**Passo 5 — O que domina o consumo?**
Apesar de a transmissão durar apenas 3 segundos, sua corrente é muito elevada em comparação com a corrente de repouso. Portanto, ela representa a maior parte do consumo médio.

**Passo 6 — Como dobrar a autonomia por software?**
Uma possibilidade é aumentar o intervalo entre transmissões, por exemplo, de 10 para 20 minutos. Isso reduz a frequência de transmissão e o consumo médio.


---

### Q4. Diferença entre tempo real rígido (hard) e brando (soft).

**Passo 1 — Tempo real rígido (Hard Real-Time)**
É um sistema no qual cumprir o prazo é obrigatório. Se o prazo não for cumprido, pode ocorrer uma falha grave.
* *Exemplo:* acionamento do airbag de um automóvel.

**Passo 2 — Tempo real brando (Soft Real-Time)**
É um sistema no qual atrasos ocasionais são tolerados, embora possam prejudicar o funcionamento.
* *Exemplo:* sistema de entretenimento e reprodução de áudio de um automóvel.

**Passo 3 — Classificação do projeto de malha de luminosidade**
O projeto de malha de luminosidade da semana 13 pode ser classificado como **tempo real brando**, pois pequenos atrasos na atualização da luminosidade normalmente não causam uma falha crítica.



---

### Q5. Papel do simulador Wokwi e suas limitações.

**Passo 1 — Papel do simulador**
O Wokwi é uma ferramenta que permite desenvolver e testar circuitos eletrônicos e programas antes de utilizar os componentes físicos. Ele permite:
* Testar códigos.
* Simular entradas e saídas digitais.
* Verificar o funcionamento de LEDs e botões.
* Identificar erros de programação.

**Passo 2 — Duas limitações**
1. Não representa perfeitamente os ruídos elétricos e as variações físicas dos componentes reais.
2. Não reproduz todas as características físicas do hardware, como quedas de tensão em fontes fracas e tolerâncias dos componentes.


---

## Parte B — Arquitetura de microcontroladores

### Q6. Arquiteturas Von Neumann e Harvard.

**Passo 1 — Arquitetura Von Neumann**
Possui uma memória compartilhada para armazenar instruções e dados. O processador utiliza o mesmo barramento para acessar ambos.

*(Nota: Adicione aqui o link da imagem da Arquitetura Von Neumann no seu repositório, ex: `![Arquitetura Von Neumann](caminho/para/von-neumann.png)`)*

Sua principal desvantagem é o **gargalo de Von Neumann**, pois instruções e dados precisam compartilhar o mesmo caminho de comunicação.

**Passo 2 — Arquitetura Harvard**
*(Nota: Adicione aqui o link da imagem da Arquitetura Harvard no seu repositório, ex: `![Arquitetura Harvard](caminho/para/harvard.png)`)*

Possui memórias e barramentos separados para instruções e dados. Isso permite buscar uma instrução e acessar dados simultaneamente.

**Passo 3 — Por que MCUs utilizam Harvard?**
Porque essa arquitetura permite maior eficiência de acesso à memória, melhor desempenho e separação entre programa e dados.

**Passo 4 — Onde o ESP32 se encaixa?**
O ESP32 utiliza uma **arquitetura Harvard modificada**, pois apresenta caminhos separados para instruções e dados, mas também permite determinados acessos compartilhados à memória, como a leitura de constantes armazenadas na Flash.



---

### Q7. Fração da CPU utilizada.

**Dados:**
* $12.000$ instruções por iteração.
* Frequência do controle: $1\text{ kHz}$.
* ESP32: $240\text{ MHz}$.
* AVR: $16\text{ MHz}$.
* $1$ instrução por ciclo.

**Passo 1 — Calcular a quantidade de instruções por segundo**
A cada segundo são executadas $1.000$ iterações:
$$12.000 \times 1.000 = 12.000.000\text{ instruções/s}$$

**Passo 2 — Calcular a utilização no ESP32**
$$U = \frac{12.000.000}{240.000.000} = 0,05$$
Convertendo para porcentagem:
$$U = 0,05 \times 100 = 5\%$$

**Passo 3 — Calcular a utilização no AVR**
$$U = \frac{12.000.000}{16.000.000} = 0,75 \rightarrow 75\%$$



---

### Q8. Diferença entre Flash, SRAM e memória RTC do ESP32.

**Passo 1 — Entender cada memória**
O ESP32 possui diferentes tipos de memória, cada uma com uma finalidade:

| Memória | Volátil? | Velocidade | Utilização |
| :--- | :--- | :--- | :--- |
| **Flash** | Não | Mais lenta que SRAM | Armazenar programa e constantes |
| **SRAM** | Sim | Rápida | Variáveis, pilha e dados temporários |
| **RTC Memory** | Depende da região | Baixo consumo | Dados que precisam ser preservados durante Deep Sleep |

**Passo 2 — Por que variáveis globais ficam na SRAM, mas seus valores iniciais vêm da Flash?**
Quando o programa é compilado, as variáveis globais inicializadas são armazenadas inicialmente na imagem do programa na Flash. Durante a inicialização do ESP32, os valores dessas variáveis são copiados para a SRAM.




### Q9. Utilização do registrador `GPIO_OUT_W1TS_REG`.

**Dados do exercício:**
* Endereço: `0x3FF44008`
* GPIO utilizado: `GPIO2`
* Operação: ligar o pino escrevendo `1` no bit correspondente.

**Passo 1 — Identificar o bit do GPIO2**
O `GPIO2` corresponde ao bit `2` do registrador.

**Passo 2 — Criar a máscara**
Utilizamos o deslocamento de bits:
$$\text{Máscara} = 1 \ll 2 = 00000100_2$$

**Passo 3 — Escrever o código em C**

#define GPIO_OUT_W1TS_REG 0x3FF44008
#define REG32(addr) (*(volatile uint32_t *)(addr))

REG32(GPIO_OUT_W1TS_REG) = (1U << 2);

### Q10. Dimensionamento da memória do acelerômetro.

**Dados do exercício:**
* $3$ eixos.
* $16\text{ bits}$ por eixo.
* Frequência: $1\text{ kHz}$.
* Tempo de gravação: $5\text{ segundos}$.
* SRAM disponível: aproximadamente $520\text{ KB}$.

**Passo 1 — Calcular a quantidade de bits por amostra**
Cada eixo possui $16\text{ bits}$ e temos $3$ eixos:
$$3 \times 16 = 48\text{ bits}$$
Convertendo para bytes:
$$48 \div 8 = 6\text{ bytes}$$
Cada amostra ocupa $6\text{ bytes}$.

**Passo 2 — Calcular a quantidade de amostras em 5 segundos**
A frequência é de $1.000$ amostras por segundo:
$$N = 1000 \times 5 = 5000\text{ amostras}$$

**Passo 3 — Calcular a memória necessária**
$$M = 5000 \times 6 = 30000\text{ bytes}$$
Convertendo para KB:
$$30000 \div 1024 \approx 29,30\text{ KB}$$

**Passo 4 — Verificar se cabe na SRAM**
A memória necessária é de aproximadamente $29,30\text{ KB}$. Como a SRAM possui aproximadamente $520\text{ KB}$:
$$29,30 < 520 \quad (\text{Cabe na SRAM})$$

**Passo 5 — Repetir o cálculo para 60 segundos**
$$N = 1000 \times 60 = 60000\text{ amostras}$$
Agora calculamos:
$$M = 60000 \times 6 = 360000\text{ bytes}$$
Convertendo para KB:
$$360000 \div 1024 \approx 351,56\text{ KB}$$

**Passo 6 — Propor uma solução**
Para 60 segundos, o armazenamento teórico ocupa aproximadamente $351,56\text{ KB}$. Embora seja inferior aos $520\text{ KB}$ nominais, pode não caber na SRAM efetivamente disponível, pois o sistema também utiliza memória para código, pilhas e outras variáveis.

Uma solução é utilizar **streaming com double buffering**:
* Um buffer recebe as novas amostras.
* O outro buffer envia os dados para armazenamento.
* Quando um buffer fica cheio, os papéis são invertidos.

### Q11. Utilização do qualificador `volatile`.

**Passo 1 — O que é `volatile`?**
O `volatile` é um qualificador utilizado para informar ao compilador que uma variável pode ser modificada por algo externo ao fluxo normal do programa. Isso impede que o compilador faça determinadas otimizações que poderiam ignorar alterações externas na variável.

**Passo 2 — Exemplo de utilização**
Imagine uma interrupção (ISR) que altera uma variável quando um botão é pressionado:

volatile int pronto = 0;

void ISR() {
    pronto = 1;
}

void loop() {
    while (!pronto) {
        // Aguarda a interrupção
    }
}

### Q12. Operações com máscaras de bits.

**Enunciado:**
Em um registrador de 8 bits, precisamos:
1. Zerar os bits 3–2.
2. Escrever o valor `01` nesses bits.
3. Inverter o bit 7.

**Passo 1 — Zerar os bits 3–2**
Criamos uma máscara:
$$\text{Máscara} = 00001100_2 \quad (3\text{U} \ll 2)$$
Aplicamos o operador `AND` com a máscara invertida:

R = R & ~(3U << 2);

A operação zera os bits 3 e 2, preservando os demais.

**Passo 2 — Escrever o valor `01` nos bits 3–2**
Primeiro, deslocamos o valor `01` para a posição correta:
$$01_2 \ll 2 = 00000100_2$$
Depois, utilizamos o operador `OR`:

R = R | (1U << 2);

Assim, o bit 2 recebe `1` e o bit 3 permanece em `0`.

**Passo 3 — Inverter o bit 7**
Criamos a máscara:
$$1 \ll 7 = 10000000_2$$
Utilizamos o operador `XOR`:

R = R ^ (1U << 7);

O `XOR` inverte o bit 7 sem alterar os outros bits.

**Código completo:**

// Zerar os bits 3 e 2
R = R & ~(3U << 2);

// Escrever 01 nos bits 3 e 2
R = R | (1U << 2);

// Inverter o bit 7
R = R ^ (1U << 7);

### Q13. Cálculo do resistor série para um LED azul.

**Dados do exercício:**
* Tensão do GPIO ($V_{\text{gpio}}$): $3,3\text{ V}$
* Tensão do LED ($V_F$): $3,0\text{ V}$
* Corrente desejada ($I$): $4\text{ mA}$

**Passo 1 — Converter a corrente**
$$4\text{ mA} = 0,004\text{ A}$$

**Passo 2 — Aplicar a Lei de Ohm**
A fórmula é:
$$R = \frac{V}{I}$$
Como precisamos considerar a tensão que sobra depois do LED:
$$R = \frac{V_{\text{gpio}} - V_F}{I}$$
Substituindo:
$$R = \frac{3,3 - 3,0}{0,004} = \frac{0,3}{0,004} = 75\text{ }\Omega$$

**Passo 3 — Verificar o resultado prático**
A margem de tensão é pequena, pois temos apenas $0,3\text{ V}$ disponíveis para o resistor. Se a tensão do LED aumentar para $3,2\text{ V}$:
$$I = \frac{3,3 - 3,2}{75} = \frac{0,1}{75} \approx 0,00133\text{ A} = 1,33\text{ mA}$$
Isso demonstra que pequenas variações na tensão do LED podem alterar bastante a corrente.

**Passo 4 — Repetir para uma fonte de 5 V**
Considerando uma fonte de $5\text{ V}$ alimentando o conjunto LED e resistor:
$$R = \frac{5 - 3}{0,004} = 500\text{ }\Omega$$

**Passo 5 — Por que não ligar 5 V diretamente ao GPIO de 3,3 V?**
Porque o GPIO do ESP32 trabalha com níveis lógicos de $3,3\text{ V}$ e não é tolerante a uma entrada de $5\text{ V}$. Uma tensão superior ao limite permitido pode danificar o microcontrolador.

### Q14. Diferença entre pull-up e pull-down.

**Passo 1 — Entender o pull-up**
O resistor pull-up mantém a entrada em nível lógico alto (`HIGH`) quando o botão está solto. Quando o botão é pressionado, a entrada é conectada ao `GND` e passa para nível lógico baixo (`LOW`).

**Passo 2 — Entender o pull-down**
O resistor pull-down mantém a entrada em nível lógico baixo (`LOW`) quando o botão está solto. Quando o botão é pressionado, a entrada recebe nível lógico alto (`HIGH`).

*(Nota: Adicione aqui o link da imagem do circuito pull-up no seu repositório, ex: `![Circuito Pull-Up](caminho/para/pull-up.png)`)*

**Passo 3 — Desenho do circuito com pull-up interno**

       ESP32 (3,3 V)
             |
       Pull-up interno
             |
             +-------- GPIO
             |
          [BOTÃO]
             |
            GND


### Q15. Debounce por software.

**Passo 1 — Entender o problema**
Quando pressionamos um botão mecânico, seus contatos podem gerar várias transições rápidas entre `HIGH` e `LOW`. Esse fenômeno é chamado de *bouncing*. No exercício, as bordas espúrias podem durar até $6\text{ ms}$.

**Passo 2 — Escolher o tempo de confirmação**
Podemos escolher uma janela de confirmação de $10\text{ ms}$. Como o *bouncing* dura até $6\text{ ms}$, aguardamos $10\text{ ms}$ para verificar se o sinal permaneceu estável.

**Passo 3 — Explicar o funcionamento**
O algoritmo funciona da seguinte maneira:
* Detecta uma mudança no estado do botão.
* Registra o instante da mudança.
* Aguarda $10\text{ ms}$.
* Lê novamente o botão.
* Se o estado continuar igual, confirma a mudança.
* Se o estado for diferente, reinicia a confirmação.

**Passo 4 — Compromisso entre latência e robustez**
Uma janela muito pequena pode não eliminar todos os ruídos do botão. Uma janela muito grande pode provocar demora na resposta do sistema. Escolhemos $10\text{ ms}$ porque esse tempo é superior aos $6\text{ ms}$ de *bouncing* apresentados no exercício.
