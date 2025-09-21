# 📘 Guia (Organizado) — Microciclos e Registradores
> Documento em Markdown estruturado. **Conteúdo original mantido**; apenas organizado.

## 1. Sumário
- [Destrinchando a Instrução `LDI ACC,6`](#-destrinchando-a-instrução-ldi-acc6)
  - [1. O que é `LDI ACC,6`](#1-o-que-é-ldi-acc6)
  - [2. A regra de ouro (barramento)](#2-a-regra-de-ouro-barramento)
  - [3. O que é o `ExtDataOut`](#3-o-que-é-o-extdataout)
  - [4. O `ExtDataOut` é o barramento?](#4-o-extdataout-é-o-barramento)
  - [5. Eu “ligo” o `ExtDataOut`?](#5-eu-ligo-o-extdataout)
  - [6. O que é o ACC](#6-o-que-é-o-acc)
  - [7. O acumulador guarda instruções?](#7-o-acumulador-guarda-instruções)
  - [8. Sinais relevantes do projeto](#8-sinais-relevantes-do-projeto)
  - [9. Linha do tempo da execução (2 clocks)](#9-linha-do-tempo-da-execução-2-clocks)
  - [10. Fluxo Visual](#10-fluxo-visual)
  - [11. Perguntas frequentes (FAQ)](#11-perguntas-frequentes-faq)
  - [12. Erros clássicos](#12-erros-clássicos)
  - [13. Checklist mental para `LDI ACC,6`](#13-checklist-mental-para-ldi-acc6)
- [Destrinchando a Instrução `LDI R1,4`](#-destrinchando-a-instrução-ldi-r14)
- [Destrinchando as Instruções `LDI R2,5` e `LDI R3,10`](#-destrinchando-as-instruções-ldi-r25-e-ldi-r310)
- [Registradores no Projeto Didático](#-registradores-no-projeto-didático)
- [Destrinchando a Instrução `MOV AUX,R1`](#-destrinchando-a-instrução-mov-auxr1)
- [Destrinchando a Instrução `ADD ACC,AUX`](#-destrinchando-a-instrução-add-accaux)

---

# 🔹Destrinchando a Instrução `LDI ACC,6`

## 2. 1. O que é `LDI ACC,6`
- **LDI** = *Load Immediate* → carregar um valor imediato.  
- **ACC** = acumulador (registrador central da CPU).  
- **6** = valor literal (imediato) que vem dentro da própria instrução.  

👉 Objetivo: colocar o número **6** dentro do **acumulador**.

---

## 3. 2. A regra de ouro (barramento)
Dentro da CPU, dados **sempre** se movem assim:
1. **Alguém escreve no barramento** (um `Xout = 1`).  
2. **Alguém lê do barramento** (um `Yin = 1`).  

Esses dois atos **não** acontecem “ao mesmo tempo” — a gente divide em **dois clocks** para garantir que:
- No **Clock 1** o dado esteja **presente** no barramento;
- No **Clock 2** o destino **ainda veja** esse dado e possa **capturar**.

---

## 4. 3. O que é o `ExtDataOut`
- **ExtDataOut** = *External Data Out* → saída dos dados imediatos da instrução.  
- Não é um “componente universal de todo microcontrolador”, mas faz parte desse projeto didático.  
- Função: **liberar o campo imediato da instrução para o barramento de dados**.

👉 Analogia:  
- Barramento = estrada.  
- ExtDataOut = portão que abre e deixa o carro (valor imediato) entrar na estrada.

---

## 5. 4. O `ExtDataOut` é o barramento?
- ❌ Não.  
- O **barramento** é a estrada por onde os dados circulam.  
- O **ExtDataOut** é só o **interruptor** que coloca o valor imediato na estrada.

---

## 6. 5. Eu “ligo” o `ExtDataOut`?
- ✅ Sim.  
- Quando `ExtDataOut = 1`, o valor imediato (ex.: `6`, em binário `00000110`) é colocado no barramento.  
- No próximo clock, outro registrador (como o ACC) habilita sua entrada (`AccIn = 1`) e captura esse valor.

---

## 7. 6. O que é o ACC
- **ACC = Acumulador**.  
- É um **registrador especial**, usado como mesa de trabalho da CPU.  
- Funções:  
  1. Guardar **resultados parciais** (ex.: 6+4=10, que ainda será usado depois).  
  2. Guardar **resultados finais** (ex.: resultado pronto = 8).  
  3. Servir de ponto central para operações da ULA.

👉 Analogia: ACC é a **tábua de corte**: tudo passa por ela, vai sendo transformado, até chegar ao prato final.

---

## 8. 7. O acumulador guarda instruções?
- ❌ Não.  
- Ele não acumula instruções (estas ficam na memória de programa).  
- Ele acumula **dados/resultados** das operações para continuar o processamento.

---

## 9. 8. Sinais relevantes do projeto
- `ExtDataOut`: habilita a **saída do valor imediato** da instrução para o barramento.  
- `AccIn`: habilita a **entrada** do acumulador para capturar o que está no barramento.  

> Dica mental: `Xout` → **coloca** dado na estrada; `Yin` → **pega** o dado da estrada.

---

## 10. 9. Linha do tempo da execução (2 clocks)

| Clock | Sinais em **1**                         | Quem **escreve** no barramento | Quem **lê** | Valor no barramento | Efeito do ciclo |
|------:|-----------------------------------------|---------------------------------|-------------|---------------------|-----------------|
|   1   | `ExtDataOut`                            | Imediato da instrução           | —           | **6**               | O 6 aparece na “estrada” |
|   2   | `ExtDataOut` **(mantém!)**, `AccIn`     | Imediato da instrução           | ACC         | **6**               | ACC captura 6   |

**Por que manter `ExtDataOut` no 2º clock?**  
Porque o ACC só consegue ler se o 6 **ainda estiver** no barramento quando `AccIn` for ativado. Se você desligasse `ExtDataOut` antes, o ACC não veria nada.

---

## 11. 10. Fluxo Visual

Clock 1:
[ Imediato 6 ] --(ExtDataOut=1)--> [ BARRAMENTO ] --(ninguém lê)--> [ ACC ]

Clock 2:
[ Imediato 6 ] --(ExtDataOut=1)--> [ BARRAMENTO ] --(AccIn=1)-----> [ ACC := 6 ]


Resultado final após os 2 clocks: **ACC = 6**.

---

## 12. 11. Perguntas frequentes (FAQ)

**1) “LDI pega de onde?”**  
Do **campo imediato** da **própria instrução** (codificado em bits dentro do registrador de instrução). `ExtDataOut` é o “porteiro” que libera esse campo para o barramento.

**2) “E se eu esquecer de manter `ExtDataOut` no 2º clock?”**  
O ACC vai tentar ler, mas não haverá dado estável no barramento → **lixo** ou **zero** (dependendo do hardware).  
Regra prática: **Quem escreve no clock 1, mantém no clock 2** se alguém for ler.

**3) “Por que duas metas separadas (1º escreve, 2º lê)?”**  
Para evitar disputa de tempo e garantir estabilidade dos dados. Essa separação é o coração do microciclo.

**4) “LDI altera flags (Zero, Carry)?”**  
Depende do design. Em muitos projetos didáticos, **LDI pode atualizar o flag Z** se o imediato for 0, mas **não mexe em Carry**. Se não implementado, LDI só carrega.

**5) “E se o imediato for maior que 8 bits?”**  
Você pode:
- Carregar em **duas partes** (alto/baixo) com duas instruções LDI, ou  
- Ter uma ULA/barramento mais largos.  
No projeto didático, o imediato tipicamente é **1 byte**.

**6) “E se eu acionar 2 saídas (Xout) no mesmo clock?”**  
Dá **briga no barramento** (contenção). Regra: **apenas uma fonte Xout** por clock.

---

## 13. 12. Erros clássicos
- ❌ Esquecer `AccIn` no 2º clock → ACC não muda.  
- ❌ Não manter `ExtDataOut` no 2º clock → ACC lê valor instável.  
- ❌ Ligar algum `R?out` junto com `ExtDataOut` → disputa no barramento (comportamento imprevisível).

---

## 14. 13. Checklist mental para `LDI ACC,6`
- [ ] No Clock 1, **liguei** `ExtDataOut`.  
- [ ] No Clock 2, **mantive** `ExtDataOut` **e** liguei `AccIn`.  
- [ ] Só **uma** fonte no barramento por clock.  
- [ ] ACC mudou para 6 ao final do Clock 2.

---

# 🔹Destrinchando a Instrução `LDI R1,4`

## 15. 1. O que é `LDI R1,4`
- **LDI** = *Load Immediate* → carregar um valor imediato.  
- **R1** = registrador de propósito geral (uma das “gavetas” rápidas da CPU).  
- **4** = valor literal (imediato) que vem dentro da própria instrução.  

👉 Objetivo: colocar o número **4** dentro do **registrador R1**.

---

## 16. 2. A regra de ouro (barramento)
Dentro da CPU, dados **sempre** se movem assim:
1. **Alguém escreve no barramento** (um `Xout = 1`).  
2. **Alguém lê do barramento** (um `Yin = 1`).  

Esses dois atos são separados em **dois clocks** para garantir estabilidade:
- **Clock 1:** o dado aparece no barramento.  
- **Clock 2:** o dado continua lá, e o destino captura.

---

## 17. 3. O que é o `ExtDataOut`
- É o sinal que libera o campo imediato da instrução para o barramento.  
- No caso do `LDI R1,4`, o valor **4** (em binário `00000100`) vai para o barramento.

👉 Analogia: portão que abre e coloca o carro “4” na estrada.

---

## 18. 4. O que é o `R1in`
- É o sinal que habilita a **entrada** do registrador R1.  
- Quando está em `1`, R1 captura o valor que está no barramento.

---

## 19. 5. Linha do tempo da execução (2 clocks)

| Clock | Sinais em **1**                         | Quem **escreve** no barramento | Quem **lê** | Valor no barramento | Efeito do ciclo |
|------:|-----------------------------------------|---------------------------------|-------------|---------------------|-----------------|
|   1   | `ExtDataOut`                            | Imediato da instrução           | —           | **4**               | O 4 aparece na “estrada” |
|   2   | `ExtDataOut` **(mantém!)**, `R1in`      | Imediato da instrução           | R1          | **4**               | R1 captura 4    |

**Por que manter `ExtDataOut` no 2º clock?**  
Porque o R1 só consegue ler se o 4 **ainda estiver** no barramento quando `R1in` for ativado.  

---

## 20. 6. Fluxo Visual

Clock 1:
[ Imediato 4 ] --(ExtDataOut=1)--> [ BARRAMENTO ] --(ninguém lê)--> [ R1 ]

Clock 2:
[ Imediato 4 ] --(ExtDataOut=1)--> [ BARRAMENTO ] --(R1in=1)-----> [ R1 := 4 ]


Resultado final após os 2 clocks: **R1 = 4**.

---

## 21. 7. Perguntas frequentes (FAQ)

**1) “De onde vem o 4?”**  
Do campo imediato da própria instrução (bits guardados no registrador de instrução).

**2) “O ACC participa?”**  
❌ Não. Aqui o destino é R1, então só `R1in` é usado.  

**3) “E se eu ligar `R1in` no primeiro clock?”**  
Ele pode ler valor instável, porque o barramento ainda não tinha certeza do dado. Por isso a entrada sempre vem **no clock seguinte**.

**4) “Isso vale para R2, R3, etc.?”**  
✅ Sim! `LDI R2,X` e `LDI R3,Y` seguem exatamente o mesmo padrão, só trocando o registrador de destino.

---

## 22. 8. Checklist mental para `LDI R1,4`
- [ ] No Clock 1, **liguei** `ExtDataOut`.  
- [ ] No Clock 2, **mantive** `ExtDataOut` **e** liguei `R1in`.  
- [ ] Só **uma** fonte no barramento por clock.  
- [ ] R1 mudou para 4 ao final do Clock 2.

---

# 🔹 Destrinchando as Instruções `LDI R2,5` e `LDI R3,10`

## 23. 1. O que são
- **LDI** = *Load Immediate* → carregar um valor imediato.  
- **R2** e **R3** = registradores de propósito geral (gavetas rápidas da CPU).  
- **5** e **10** = valores literais (imediatos) que vêm dentro da própria instrução.  

👉 Objetivo: colocar os números **5** e **10** nos registradores **R2** e **R3**, respectivamente.  

---

## 24. 2. A lógica é a mesma do `LDI ACC,6` e `LDI R1,4`
Dentro da CPU, dados sempre se movem em **dois clocks**:  
1. **Clock 1:** o valor imediato vai para o barramento (`ExtDataOut = 1`).  
2. **Clock 2:** o valor se mantém no barramento (`ExtDataOut = 1`) e o registrador de destino habilita a entrada (`R2in` ou `R3in`).  

---

## 25. 3. Linha do tempo da execução

### `LDI R2,5`

| Clock | Sinais em **1**                         | Quem **escreve** | Quem **lê** | Valor no barramento | Efeito |
|------:|-----------------------------------------|------------------|-------------|---------------------|--------|
|   1   | `ExtDataOut`                            | Imediato (5)     | —           | **5**               | 5 aparece na estrada |
|   2   | `ExtDataOut` (mantém), `R2in`           | Imediato (5)     | R2          | **5**               | R2 captura 5 |

👉 Resultado final: **R2 = 5**.

---

### `LDI R3,10`

| Clock | Sinais em **1**                         | Quem **escreve** | Quem **lê** | Valor no barramento | Efeito |
|------:|-----------------------------------------|------------------|-------------|---------------------|--------|
|   1   | `ExtDataOut`                            | Imediato (10)    | —           | **10**              | 10 aparece na estrada |
|   2   | `ExtDataOut` (mantém), `R3in`           | Imediato (10)    | R3          | **10**              | R3 captura 10 |

👉 Resultado final: **R3 = 10**.

---

## 26. 4. Fluxo Visual (igual para R2 e R3, só muda o destino)

Clock 1:
[ Imediato X ] --(ExtDataOut=1)--> [ BARRAMENTO ] --(ninguém lê)--> [ R? ]

Clock 2:
[ Imediato X ] --(ExtDataOut=1)--> [ BARRAMENTO ] --(R?in=1)-----> [ R? := X ]


---

## 27. 5. Resumindo
- **LDI R2,5** → coloca o valor imediato 5 em R2.  
- **LDI R3,10** → coloca o valor imediato 10 em R3.  
- Ambos seguem exatamente a mesma lógica já explicada para `LDI ACC,6` e `LDI R1,4`.  
- O que muda é apenas o **registrador de destino** (`R2in` ou `R3in`).

---

# 🔹 Registradores no Projeto Didático

## 28. 1. O que é um registrador
- Um **registrador** é uma **pequena memória dentro da CPU**.  
- Ele armazena uma **palavra** (um conjunto de bits, ex.: 8 bits = 1 byte).  
- Os registradores são **voláteis** → ou seja, perdem o conteúdo quando a CPU é desligada.  
- Função principal: **guardar dados temporários para operações**.

👉 Pense nos registradores como **gavetas muito rápidas** que a CPU usa durante o processamento.

---

## 29. 2. ACC (Acumulador)
- **Nome:** ACC = *Accumulator*.  
- **Função:** registrador **principal** para guardar resultados das operações.  
- Chamamos de “acumulador” porque ele **acumula os resultados parciais e finais**.  
- Exemplo:  
  - `LDI ACC,6` → ACC = 6  
  - `ADD ACC,AUX` → ACC = ACC + AUX  

👉 Ele é o **coração da ULA**, quase toda operação passa por ele.

---

## 30. 3. AUX (Auxiliar)
- **Nome:** AUX = registrador auxiliar.  
- **Função:** segurar o **outro operando** para operações da ULA.  
- Trabalha sempre em conjunto com o ACC.  
- Exemplo:  
  - `MOV AUX,R1` → AUX = R1  
  - `ADD ACC,AUX` → ACC = ACC + AUX  

👉 Diferença básica:
- **ACC** → recebe sempre o resultado.  
- **AUX** → serve como apoio (segundo valor da operação).

---

## 31. 4. R1, R2, R3 (Registradores de Propósito Geral)
- São registradores comuns, tipo **gavetas extras** para guardar valores.  
- Eles não têm papel “especial” como ACC e AUX, mas são usados como **fonte de dados**.  
- Exemplo:  
  - `LDI R1,4` → R1 = 4  
  - `MOV AUX,R1` → AUX = R1  

👉 Eles dão flexibilidade: você pode armazenar dados intermediários sem precisar sobrecarregar o ACC.

---

## 32. 5. Comparação ACC x AUX x R1/R2/R3

| Registrador | Função principal | Papel típico |
|-------------|-----------------|--------------|
| **ACC**    | Acumular resultados (parciais e finais) | Saída da maioria das operações |
| **AUX**    | Guardar o segundo operando da ULA | Apoio para somas, subtrações, operações lógicas |
| **R1,R2,R3** | Armazenar dados de propósito geral | Fonte de valores para mover/copiar |

---

## 33. 6. Registrador de deslocamento?
- ❌ Não.  
- O **AUX não é um registrador de deslocamento** (shift register).  
- Ele é só mais um registrador de uso interno para operações.  
- Registradores de deslocamento são outro tipo de circuito (servem para mover bits para esquerda/direita).

---

## 34. 7. Resumindo
- Todos (ACC, AUX, R1, R2, R3) são **registradores voláteis** (perdem o conteúdo ao desligar).  
- **ACC** = onde o resultado das operações vai parar.  
- **AUX** = apoio, segura o outro valor que a ULA precisa.  
- **R1, R2, R3** = armazenam valores gerais, servem de fonte para mover/copiar.

---

# 🔹 Destrinchando a Instrução `MOV AUX,R1`

## 35. 1. O que é `MOV AUX,R1`
- **MOV** = *Move* → copiar dados de uma fonte para um destino.  
- **AUX** = registrador auxiliar (apoio para operações com ACC).  
- **R1** = registrador de propósito geral (fonte do dado).  

👉 Objetivo: **copiar o conteúdo de R1 para o AUX**.  
Diferente do `LDI`, aqui o dado **não vem da instrução** (imediato), mas sim de **outro registrador**.

---

## 36. 2. A lógica do MOV
- Toda operação dentro da CPU passa pelo **barramento**.  
- Para mover dados:  
  1. **Clock 1:** a fonte coloca o valor no barramento (`R1out = 1`).  
  2. **Clock 2:** a fonte mantém o valor, e o destino habilita a entrada (`AuxIn = 1`).  

👉 Resultado: AUX recebe o valor que estava em R1.

---

## 37. 3. Linha do tempo da execução (2 clocks)

| Clock | Sinais em **1**                         | Quem **escreve** | Quem **lê** | Valor no barramento | Efeito |
|------:|-----------------------------------------|------------------|-------------|---------------------|--------|
|   1   | `R1out`                                 | R1               | —           | valor de R1         | Valor de R1 aparece na estrada |
|   2   | `R1out` (mantém), `AuxIn`               | R1               | AUX         | valor de R1         | AUX captura valor de R1 |

👉 Resultado final: **AUX = R1**.  

---

## 38. 4. Diferença entre `LDI` e `MOV`
- **LDI** → pega um **valor imediato** escrito na instrução (`ExtDataOut`).  
- **MOV** → copia de um **registrador fonte** (`R1out`, `R2out`, `R3out`) para um **registrador destino** (`AuxIn`, `AccIn`, etc.).  

👉 Em resumo:  
- `LDI` = carrega número fixo.  
- `MOV` = transfere valor já existente entre registradores.

---

## 39. 5. Fluxo Visual

Clock 1:
[ R1 ] --(R1out=1)--> [ BARRAMENTO ] --(ninguém lê)--> [ AUX ]

Clock 2:
[ R1 ] --(R1out=1)--> [ BARRAMENTO ] --(AuxIn=1)-----> [ AUX := R1 ]


---

## 40. 6. Perguntas Frequentes (FAQ)

**1) O que é o AUX?**  
É um registrador auxiliar que trabalha em conjunto com o ACC para operações aritméticas e lógicas.  
Exemplo: em um `ADD ACC,AUX`, a ULA soma o valor do ACC com o do AUX.

**2) O MOV altera o valor de R1?**  
❌ Não.  
`MOV AUX,R1` apenas **copia** → o R1 continua com o mesmo valor, só o AUX passa a ter uma cópia.

**3) Por que preciso do AUX se já tenho o ACC?**  
Porque muitas operações da ULA são **binárias** (precisam de dois operandos).  
- Exemplo: ACC + AUX.  
- O ACC guarda o primeiro valor, o AUX guarda o segundo, e a ULA faz a operação.

---

## 41. 7. Checklist mental para `MOV AUX,R1`
- [ ] No Clock 1, **liguei** `R1out`.  
- [ ] No Clock 2, **mantive** `R1out` **e** liguei `AuxIn`.  
- [ ] Só **uma** fonte no barramento por clock.  
- [ ] AUX mudou para o valor de R1 ao final do Clock 2.

---

# 🔹 Destrinchando a Instrução `ADD ACC,AUX`

## 42. 1. O que é `ADD ACC,AUX`
- **ADD** = *Addition* → soma.  
- **ACC** = acumulador (registrador central, destino do resultado).  
- **AUX** = registrador auxiliar (segundo operando).  

👉 Objetivo: **somar o valor do ACC com o valor do AUX, e guardar o resultado no ACC**.  

---

## 43. 2. A lógica do ADD
- Diferente do `LDI` ou `MOV`, aqui entra em cena a **ULA (Unidade Lógica e Aritmética)**.  
- A ULA precisa de **dois operandos**:  
  - Um vem do **ACC**.  
  - O outro vem do **AUX**.  
- No final, o resultado da soma volta para o **ACC**.

---

## 44. 3. Linha do tempo da execução (2 clocks)

| Clock | Sinais em **1**                          | Quem **escreve** | Quem **lê** | Valor no barramento | Efeito |
|------:|------------------------------------------|------------------|-------------|---------------------|--------|
|   1   | `AccOut`, `AuxOut` (interno à ULA)       | ACC + AUX → ULA  | —           | operandos prontos   | A ULA recebe os dois valores |
|   2   | `AddOut`, `AccIn`                        | ULA (resultado)  | ACC         | ACC + AUX           | ACC captura a soma |

👉 Resultado final: **ACC = ACC + AUX**.  

---

## 45. 4. Fluxo Visual

Clock 1:
[ ACC ] --(AccOut=1)--> [ ULA (ADD) ]  
[ AUX ] --(AuxOut=1)--> [ ULA (ADD) ]  
[ ULA ] --(processa)--> [ resultado interno ]

Clock 2:
[ ULA resultado ] --(AddOut=1)--> [ BARRAMENTO ] --(AccIn=1)--> [ ACC := ACC + AUX ]

---

## 46. 5. Diferença em relação ao MOV/LDI
- **LDI** → pega valor imediato.  
- **MOV** → transfere valor de um registrador para outro.  
- **ADD** → envolve **processamento na ULA** (não é só copiar, é calcular).  

👉 O resultado **não vem direto da fonte**, mas sim da **operação feita pela ULA**.  

---

## 47. 6. Perguntas Frequentes (FAQ)

**1) O ACC participa duas vezes?**  
✅ Sim.  
- Ele fornece o **primeiro operando** no Clock 1.  
- Ele recebe o **resultado da soma** no Clock 2.  

**2) O valor do AUX muda?**  
❌ Não.  
O AUX apenas fornece o segundo operando, mas continua com o mesmo valor.  

**3) O ADD altera as *flags*?**  
Depende do projeto. Normalmente:  
- Atualiza **Zero flag (Z)** se o resultado for 0.  
- Atualiza **Carry flag (C)** se houver estouro (overflow).  

**4) Preciso manter AccOut e AuxOut no 2º clock?**  
Não.  
- No Clock 1, os operandos já entraram na ULA.  
- No Clock 2, só a saída da ULA (`AddOut`) precisa estar ativa junto com `AccIn`.  

---

## 48. 7. Checklist mental para `ADD ACC,AUX`
- [ ] No Clock 1, **liguei** `AccOut` e `AuxOut` para fornecer operandos à ULA.  
- [ ] No Clock 2, **liguei** `AddOut` (resultado da ULA) e `AccIn` (destino).  
- [ ] ACC mudou para **ACC + AUX** ao final do Clock 2.



---
## 49. MOV AUX,R2

### Descrição
Transfere o conteúdo de R2 para o registrador auxiliar (AUX).

### Microciclos
- **Clock 1:** R2 coloca o valor no barramento (R2out=1).
- **Clock 2:** AUX captura o valor do barramento (AuxIn=1).

### Fluxo Visual
```
Clock 1: [ R2 ] --(R2out=1)--> [ Barramento ]
Clock 2: [ Barramento ] --(AuxIn=1)--> [ AUX := R2 ]
```

### FAQ
- **R2 é alterado?** Não, apenas lê o valor e envia para o barramento.
- **AUX sobrescreve valor anterior?** Sim, o conteúdo antigo é perdido.

---
## 50. SUB ACC,AUX

### Descrição
Subtrai o valor de AUX do ACC: ACC := ACC - AUX.

### Microciclos
- **Clock 1:** ACC libera seu valor para a ULA (AccOut=1).
- **Clock 2:** AUX libera seu valor (AuxOut=1), ULA configurada para SUB gera resultado e ACC recebe (AccIn=1).

### Fluxo Visual
```
Clock 1: [ ACC ] --(AccOut=1)--> [ ULA SUB ]
Clock 2: [ AUX ] --(AuxOut=1)--> [ ULA SUB ] --(SubOut=1)--> [ Barramento ] --(AccIn=1)--> [ ACC := ACC - AUX ]
```

### FAQ
- **Flags são atualizados?** Sim, Carry/Borrow e Zero são ajustados.
- **Pode causar underflow?** Sim, se AUX > ACC, resultado será negativo (em complemento de dois).

---
## 51. MOV AUX,R3

### Descrição
Transfere o conteúdo de R3 para o registrador auxiliar (AUX).

### Microciclos
- **Clock 1:** R3 coloca valor no barramento (R3out=1).
- **Clock 2:** AUX captura valor (AuxIn=1).

### Fluxo Visual
```
Clock 1: [ R3 ] --(R3out=1)--> [ Barramento ]
Clock 2: [ Barramento ] --(AuxIn=1)--> [ AUX := R3 ]
```

### FAQ
- **Perco o valor anterior de AUX?** Sim, é sobrescrito.
- **R3 é alterado?** Não, apenas lido.

---
## 52. OR ACC,AUX

### Descrição
Realiza operação lógica OR entre ACC e AUX: ACC := ACC OR AUX.

### Microciclos
- **Clock 1:** ACC envia valor à ULA (AccOut=1).
- **Clock 2:** AUX envia valor (AuxOut=1), ULA configurada em OR gera resultado e ACC recebe (AccIn=1).

### Fluxo Visual
```
Clock 1: [ ACC ] --(AccOut=1)--> [ ULA OR ]
Clock 2: [ AUX ] --(AuxOut=1)--> [ ULA OR ] --(OrOut=1)--> [ Barramento ] --(AccIn=1)--> [ ACC := ACC OR AUX ]
```

### FAQ
- **Modifica ACC?** Sim, resultado da operação lógica substitui o valor anterior.
- **Flags são afetados?** Sim, principalmente o Zero Flag se o resultado for 0.


---
## 53. LDI R1,0

### Descrição
Carrega o valor imediato 0 no registrador R1.

### Microciclos
- **Clock 1:** Unidade de Imediatos coloca 0 no barramento (ExtDataOut=1).
- **Clock 2:** R1 captura o valor (R1in=1).

### Fluxo Visual
```
Clock 1: [ Imediato=0 ] --(ExtDataOut=1)--> [ Barramento ]
Clock 2: [ Barramento ] --(R1in=1)--> [ R1 := 0 ]
```

---
## 54. LDI R2,0

### Descrição
Carrega o valor imediato 0 no registrador R2.

### Microciclos
- **Clock 1:** Unidade de Imediatos coloca 0 no barramento (ExtDataOut=1).
- **Clock 2:** R2 captura o valor (R2in=1).

### Fluxo Visual
```
Clock 1: [ Imediato=0 ] --(ExtDataOut=1)--> [ Barramento ]
Clock 2: [ Barramento ] --(R2in=1)--> [ R2 := 0 ]
```

---
## 55. LDI R3,0

### Descrição
Carrega o valor imediato 0 no registrador R3.

### Microciclos
- **Clock 1:** Unidade de Imediatos coloca 0 no barramento (ExtDataOut=1).
- **Clock 2:** R3 captura o valor (R3in=1).

### Fluxo Visual
```
Clock 1: [ Imediato=0 ] --(ExtDataOut=1)--> [ Barramento ]
Clock 2: [ Barramento ] --(R3in=1)--> [ R3 := 0 ]
```

---
## 56. LDI ACC,0

### Descrição
Carrega o valor imediato 0 no acumulador (ACC).

### Microciclos
- **Clock 1:** Unidade de Imediatos coloca 0 no barramento (ExtDataOut=1).
- **Clock 2:** ACC captura o valor (AccIn=1).

### Fluxo Visual
```
Clock 1: [ Imediato=0 ] --(ExtDataOut=1)--> [ Barramento ]
Clock 2: [ Barramento ] --(AccIn=1)--> [ ACC := 0 ]
```

---
## 57. LDI AUX,0

### Descrição
Carrega o valor imediato 0 no registrador auxiliar (AUX).

### Microciclos
- **Clock 1:** Unidade de Imediatos coloca 0 no barramento (ExtDataOut=1).
- **Clock 2:** AUX captura o valor (AuxIn=1).

### Fluxo Visual
```
Clock 1: [ Imediato=0 ] --(ExtDataOut=1)--> [ Barramento ]
Clock 2: [ Barramento ] --(AuxIn=1)--> [ AUX := 0 ]
```

---
## 58. NOP

### Descrição
Instrução "No Operation". Não altera nenhum registrador ou barramento.

### Microciclos
- **Clock 1 e 2:** Nenhum sinal é ativado, barramento permanece em repouso.

### Observação
- Usada para alinhamento de instruções, temporizações ou espera de hardware.

---
## 59. LOOP (Pseudo-instrução)

### Descrição
Marca um ponto do programa para repetição.

### Implementação
- Geralmente acompanhado de uma instrução de salto (ex.: `JMP LOOP`).
- Não é uma instrução física, mas uma **label** para controle de fluxo.

### Exemplo
```
LOOP:
    NOP
    NOP
    JMP LOOP
```

