# 1. Introdução à ULA (Unidade Lógica e Aritmética)

## 1.1 O que é a ULA?
A Unidade Lógica e Aritmética (ULA) é o bloco responsável por executar **operações matemáticas e lógicas** dentro do microprocessador.  
Ela é o "coração" do processamento: a parte que realmente **calcula, compara e manipula dados**.

📌 *Na figura abaixo (Figura 1), vemos a ULA destacada dentro de uma arquitetura simplificada de microprocessador, interligada ao acumulador, registradores e unidade de controle.*  

![Figura 1 – ULA dentro da arquitetura de um microprocessador](Imagens/ULA.png)

---

## 1.2 Origem e Importância
Antes da ULA, computadores eram apenas conjuntos de circuitos de comutação.  
Com a introdução das **portas lógicas**, surgiram os primeiros cálculos digitais.  
A ULA surge como uma **integração sistemática dessas operações**, permitindo que qualquer instrução de alto nível (como em Assembly ou C) seja traduzida em operações simples realizadas pelo hardware.

### 1.2.1 Evolução Histórica
- **Década de 1940-50**: Circuitos de válvulas, UNIVAC e ENIAC.  
- **1971**: Intel 4004 – primeiro microprocessador comercial, já com ULA integrada.  
- **Atualmente**: ULAs modernas realizam bilhões de operações por segundo, com suporte a operações de inteiros, ponto flutuante e vetoriais.

---

## 1.3 Funções Básicas da ULA
A ULA pode executar:
- **Operações aritméticas**: soma, subtração, incremento, decremento.  
- **Operações lógicas**: AND, OR, XOR, NOT.  
- **Deslocamentos e rotações**: manipulação de bits para ajustes rápidos.  

---

## 1.4 Papel dentro do Processador
A ULA não trabalha sozinha:  
- Recebe dados dos **registradores** e do **acumulador**.  
- Executa a operação determinada pela **unidade de controle**.  
- Retorna o resultado para um registrador ou memória.  
- Atualiza **flags** (Zero, Carry, Negativo) que influenciam decisões futuras.

---

## 1.5 Analogia Didática
Podemos imaginar a ULA como a **“calculadora interna”** da CPU:  
- Os **registradores** funcionam como a memória da calculadora (onde você digita os números).  
- A **unidade de controle** é a pessoa que decide qual operação apertar (somar, subtrair, comparar).  
- A **ULA** é o botão da calculadora que executa a conta.  

---

# 2. Contexto na Arquitetura da ULA

A ULA não trabalha isolada. Ela está conectada a diversos blocos da arquitetura interna do microprocessador, como o **acumulador**, os **registradores de propósito geral**, a **unidade de controle** e a **unidade de deslocamento**.  
Esses elementos, em conjunto, permitem que o processador execute instruções de forma organizada e eficiente.

📌 *Na Figura 1, já vista na introdução, é possível observar a ULA no centro da arquitetura, ligada ao acumulador, registradores e unidade de deslocamento.*  

---

## 2.1 Flags (Sinalizadores)
Os **flags** são pequenos flip-flops usados como sinalizadores do estado do resultado produzido pela ULA.  
Após cada operação, a ULA atualiza automaticamente esses bits, permitindo que a **unidade de controle** tome decisões.

### Exemplos comuns:
- **Z (Zero)** → indica que o resultado da operação foi zero.  
- **C (Carry)** → indica um transporte (vai-um) em somas ou um empréstimo em subtrações.  
- **N (Negativo)** → indica se o resultado foi negativo (bit mais significativo = 1).  
- **O ou V (Overflow)** → indica se houve estouro aritmético.

📌 **Exemplo prático:**  
Num microcontrolador de 8 bits, ao calcular `200 + 100 = 300`:  
- O valor real (300) não cabe em 8 bits (máximo 255).  
- O resultado armazenado será **44** (300 - 256).  
- O **flag Carry** será ativado, indicando que houve um estouro de capacidade.

---

## 2.2 Registrador de Deslocamento
Um **registrador de deslocamento (shift register)** é formado por flip-flops ligados em série e usados para **mover os bits** de um número para a esquerda ou para a direita.

### Funções principais:
1. **Multiplicação/Divisão por 2**  
   - Deslocar para a esquerda = multiplicar por 2.  
   - Deslocar para a direita = dividir por 2.  

2. **Rotação de bits**  
   - Muito usado em algoritmos de criptografia e compressão.  

3. **Comunicação serial**  
   - Protocolos como SPI e UART enviam/recebem dados bit a bit usando registradores de deslocamento.

📌 **Exemplo prático em C:**
```c
#include <stdint.h>

uint8_t valor = 5;   // 00000101 em binário

uint8_t a = valor << 1;  // Resultado: 00001010 (10 em decimal)
uint8_t b = valor >> 1;  // Resultado: 00000010 (2 em decimal)

## 2.3 Registradores de Propósito Geral
São como **caixinhas rápidas de memória** dentro da CPU.  
Eles guardam valores temporários que a ULA vai usar nas operações.  

📌 *Exemplo prático:* se você vai somar 5 + 3, esses números ficam guardados primeiro em registradores antes de ir para a ULA.

---

## 2.4 Acumulador
É um **registrador especial**, usado quase sempre como ponto principal de cálculo.  
A ULA costuma colocar os resultados nele.  

📌 *Exemplo prático:* pense no acumulador como a **tela da calculadora**, que sempre mostra o resultado mais recente.

---

## 2.5 ULA (Unidade Lógica e Aritmética)
É a **calculadora interna do processador**.  
Faz contas (soma, subtração) e também operações lógicas (AND, OR, NOT, XOR).  

📌 *Exemplo prático:* quando você escreve em C `c = a + b;`, é a ULA que faz a soma de `a` e `b`.

---

## 2.6 PC (Program Counter)
É o **contador de programa**.  
Guarda o endereço da próxima instrução que o processador deve executar.  

📌 *Exemplo prático:* pense no PC como o **marcador de páginas de um livro**: ele sempre mostra onde o processador deve ler a próxima linha.

---

## 2.7 Pilha (Stack)
É uma área de memória organizada como uma **pilha de pratos**: o último que entra é o primeiro que sai (LIFO).  
Serve para guardar dados temporários e endereços de retorno de funções.  

📌 *Exemplo prático:* quando uma função é chamada, o endereço para continuar o programa depois dela é guardado na pilha.

---

## 2.8 SP (Stack Pointer)
É o **dedo indicador da pilha**.  
Aponta para o topo da pilha, mostrando onde será colocado ou retirado o próximo dado.  

📌 *Exemplo prático:* se a pilha tem 5 pratos, o SP aponta para o quinto. Ao empilhar mais um, o SP passa a apontar para o sexto.

---

## 2.9 Registrador de Instrução (IR)
Guarda a **instrução que está sendo executada** no momento.  
Trabalha junto com o PC e a unidade de controle.  

📌 *Exemplo prático:* se o programa manda somar dois números, essa instrução fica no IR até ser concluída.

---

## 2.10 Unidade de Decodificação e Controle
É o **maestro da CPU**.  
Lê a instrução no IR e gera sinais de controle para coordenar a ULA, registradores, memória etc.  

📌 *Exemplo prático:* se a instrução for `ADD A, B`, essa unidade manda a ULA somar os valores e guardar no acumulador.

---

## 2.11 Registradores de Configuração do Sistema
Registradores especiais que permitem **ajustar o funcionamento do processador** e dos periféricos.  
Com eles você pode ativar timers, configurar portas de entrada/saída, definir modos de interrupção etc.  

📌 *Exemplo prático:* em microcontroladores, você usa esses registradores para escolher se um pino será entrada ou saída digital.

---

# 3. Representação de 8 bits (1 byte)
Um microcontrolador de 8 bits consegue manipular diretamente valores de até **8 dígitos binários**.  
Isso equivale a números entre **0 e 255** em decimal, ou de **-128 a +127** se usar representação com sinal.

| Binário (8 bits) | Decimal | Hexadecimal | ASCII (Caractere) |
|------------------|---------|-------------|-------------------|
| 01000001         | 65      | 0x41        | A                 |
| 01000010         | 66      | 0x42        | B                 |
| 00110000         | 48      | 0x30        | 0 (zero)          |
| 00110001         | 49      | 0x31        | 1                 |
| 00110010         | 50      | 0x32        | 2                 |
| 01111111         | 127     | 0x7F        | DEL (controle)    |
| 11111111         | 255     | 0xFF        | (sem caractere ASCII padrão) |

---

📌 **Observações importantes:**
- O mesmo conjunto de bits pode ser interpretado como **número** ou como **letra**, dependendo do uso.  
- `01000001` pode ser o número **65** (decimal) ou a letra **A** (na tabela ASCII).  
- `11111111` (255) é o **máximo valor sem sinal** que um processador de 8 bits manipula diretamente.  
- Se usar com sinal (complemento de 2), esse mesmo `11111111` representaria o valor **-1**.

