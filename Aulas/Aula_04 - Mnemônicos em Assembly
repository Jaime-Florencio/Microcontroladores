# 📘 Aula 04 – Mnemônicos em Assembly

## 🧩 Introdução
Nesta aula, vamos explorar os **mnemônicos** da linguagem Assembly.  
Mnemônico é uma palavra reservada que **representa uma instrução de máquina** em formato legível para humanos.  
Enquanto o processador entende apenas códigos binários (0 e 1), o programador escreve instruções como `MOV`, `ADD` ou `SUB`, que o montador (assembler) traduz para a linguagem de máquina.

➡️ **Referência:**  
- Microchip – *PIC16F87X Data Sheet* (capítulo de instruções).  
- Atmel (Microchip) – *AVR Instruction Set Manual*.  
- Stallings, W. – *Arquitetura e Organização de Computadores*.  

---

## 🧾 O que são Registradores?
Um **registrador** é uma pequena memória dentro do processador, extremamente rápida, usada para guardar valores temporários.  
Eles são o **coração da ULA (Unidade Lógica e Aritmética)**, pois é neles que os números ficam armazenados para as operações.

- Registradores podem ter **propósito geral** (`R0`, `R1`, …, `Rn`)  
- Ou **funções específicas**, como o **Acumulador**, **Program Counter (PC)**, **Stack Pointer (SP)** etc.  

📌 Exemplo visual:  
Imagine que cada registrador é uma **gaveta** em um armário.  
Você pode guardar números, mover de uma gaveta para outra, somar valores de duas gavetas e armazenar o resultado em uma delas.

---

## 🔹 Instrução `MOV` – Move
O comando `MOV` copia o conteúdo de um registrador para outro ou de memória para registrador.

📌 **Sintaxe:**
```asm
MOV destino, origem
```

📌 **Exemplo 1:**
```asm
MOV R2, R1    ; Copia o valor de R1 para R2
```
👉 Se `R1 = 5`, após essa instrução, `R2 = 5`.

📌 **Exemplo 2 (com variável auxiliar):**
```asm
MOV AUX, R3   ; Coloca na variável AUX o valor de R3
```

📖 **Referência:** AVR Instruction Set – MOV.

---

## 🔹 Instrução `LDI` – Load Immediate
Carrega um **valor imediato (constante)** diretamente para um registrador.

📌 **Sintaxe:**
```asm
LDI registrador, valor
```

📌 **Exemplo:**
```asm
LDI R16, 25   ; R16 = 25
```

⚠️ Aqui não copiamos de outro registrador, mas sim de um número literal.

📖 **Referência:** AVR Instruction Set – LDI.

---

## 🔹 Instrução `ADD` – Adição
Realiza a soma de dois registradores e armazena o resultado no destino.  
É realizada pela **ULA** e pode alterar **flags** (sinalizadores) como Carry (C) e Zero (Z).

📌 **Sintaxe:**
```asm
ADD destino, origem
```

📌 **Exemplo:**
```asm
LDI R17, 10   ; R17 = 10
LDI R18, 20   ; R18 = 20
ADD R17, R18  ; R17 = 30
```

📖 **Referência:** AVR Instruction Set – ADD.

---

## 🔹 Instrução `SUB` – Subtração
Subtrai o conteúdo de um registrador de outro e armazena o resultado no destino.

📌 **Sintaxe:**
```asm
SUB destino, origem
```

📌 **Exemplo:**
```asm
LDI R20, 50   ; R20 = 50
LDI R21, 30   ; R21 = 30
SUB R20, R21  ; R20 = 20
```

📖 **Referência:** AVR Instruction Set – SUB.

---

## 🔹 Instrução `OR` – Operação Lógica OU
Realiza o **OU lógico bit a bit** entre dois registradores.

📌 **Sintaxe:**
```asm
OR destino, origem
```

📌 **Exemplo:**
```asm
LDI R22, 0b00001111
LDI R23, 0b11110000
OR R22, R23   ; Resultado: 11111111
```

📖 **Referência:** AVR Instruction Set – OR.

---

## 🔹 Instrução `NOP` – No Operation
Executa **nenhuma operação**, apenas consome 1 ciclo de máquina.  
É usada para ajustes de temporização e espera de hardware.

📌 **Exemplo:**
```asm
NOP    ; Espera 1 ciclo
```

📖 **Referência:** AVR Instruction Set – NOP.

---

## 🔹 Instrução `RJMP` / `JMP` – Desvio
Desvia a execução do programa para outro endereço (salto).

📌 **Exemplo:**
```asm
RJMP LOOP
```

```asm
LOOP: NOP
```

📖 **Referência:** AVR Instruction Set – JMP, RJMP.

---

## 🧠 Comparação Geral

| Instrução | Função | Exemplo |
|-----------|--------|---------|
| `MOV`     | Copia dados | `MOV R2, R1` |
| `LDI`     | Carrega constante | `LDI R16, 25` |
| `ADD`     | Soma registradores | `ADD R17, R18` |
| `SUB`     | Subtrai registradores | `SUB R20, R21` |
| `OR`      | Operação lógica OU | `OR R22, R23` |
| `NOP`     | Não faz nada (1 ciclo) | `NOP` |
| `RJMP`    | Salto relativo | `RJMP LOOP` |

---

## 📌 Conclusão
- Cada **mnemônico** é uma tradução direta para uma operação de máquina.  
- **Registradores** são os locais de trabalho do processador.  
- Com poucas instruções já é possível montar algoritmos básicos.  

➡️ Estude sempre a documentação oficial do fabricante, pois cada família de processadores pode ter pequenas variações.

📖 **Referências finais:**
- [AVR Instruction Set Manual](https://ww1.microchip.com/downloads/en/DeviceDoc/AVR-Instruction-Set-Manual-DS40002198A.pdf)  
- [PIC16F87X Data Sheet – Instruções](https://ww1.microchip.com/downloads/en/DeviceDoc/30292c.pdf)  
- Stallings, W. – Arquitetura e Organização de Computadores.
