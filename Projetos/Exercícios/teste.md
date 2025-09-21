# 📘 Guia Completo — Microinstruções, Dois Clocks, Registradores e Mnemônicos
> Documento técnico **completo** em Markdown, pensado para quem está vendo o conteúdo pela primeira vez.
> Estruturado, descritivo e visual, com passos detalhados, algoritmos de execução e correções aplicadas.

---

## 0) Como usar este guia
- Leia a **Seção 1** para entender a arquitetura mínima (registradores, ULA, barramento, sinais).
- Leia a **Seção 2** para compreender a regra dos **dois clocks** (por que existem e como usamos).
- Use a **Seção 3** (Glossário de Sinais e Mnemônicos) como referência rápida.
- Na **Seção 4** você encontra **cada instrução**, com microciclos, sinais ativos e fluxos ASCII.
- A **Seção 5** traz **algoritmos de execução** (passo a passo) e dicas de depuração.
- As **Seções 6 e 7** reúnem a tabela e imagens de referência do exercício, e anotações finais.

> Imagens de referência (anexas):  
> • Tabela de microinstruções: `sandbox:/mnt/data/tabelaex1.png`  
> • Lista de comandos/mnemônicos: `sandbox:/mnt/data/comando.png`  

---

## 1) Arquitetura mínima do processador didático

### 1.1 Barramento (data bus)
- É a **“estrada” de dados** interna da CPU.
- Em um dado momento, **apenas uma fonte** pode dirigir o valor para a estrada (**regra Xout único**).
- Qualquer registrador/unidade **destino** pode ler o que está na estrada quando sua entrada é habilitada (Yin).

### 1.2 Registradores
- **ACC (Acumulador):** registrador central. Recebe resultados de operações. Participa fortemente da ULA.
- **AUX (Auxiliar):** guarda o **segundo operando** da ULA. Trabalha em dupla com o ACC.
- **R1, R2, R3 (propósito geral):** “gavetas rápidas” para valores temporários que não são o resultado final.
- Todos são **voláteis** (perdem conteúdo ao desligar).

### 1.3 ULA (Unidade Lógica e Aritmética)
- Realiza operações **ADD, SUB, OR** (neste conjunto mínimo).  
- Recebe operandos dos registradores (tipicamente `ACC` e `AUX`) e publica o **resultado** no barramento mediante sinal de saída da operação (`AddOut`, `SubOut`, `OrOut`).  
- Flags (quando implementadas): **Z** (zero), **C** (carry/borrow), **N** (negativo), etc.

### 1.4 Unidade de imediatos (ExtDataOut)
- Fornece o **valor literal** da própria instrução (ex.: `LDI R1,4` → `4`) para o barramento quando `ExtDataOut=1`.

---

## 2) Por que dois clocks? (modelo de microciclo)

**Regra de Ouro (sequenciamento):**
1. **Clock 1 (Produção):** uma **fonte** coloca o dado no barramento (`Xout = 1`).  
2. **Clock 2 (Consumo):** o **destino** captura o dado do barramento (`Yin = 1`).

**Motivo técnico:** separar **tempo de estabilização** (propagação) do ato de **amostragem** (captura). Assim:
- No **Clock 1** o valor aparece e estabiliza no barramento.
- No **Clock 2** a entrada do registrador é habilitada, garantindo captura **estável e previsível**.
- Se desligarmos a fonte antes do Clock 2, o destino pode ler **lixo**.

**Atenção a conflitos:** nunca ative **duas fontes** de saída (`Xout`) no mesmo clock → **briga de barramento**.

---

## 3) Glossário de sinais e mnemônicos

### 3.1 Sinais de saída (colocam dados no barramento)
- `ExtDataOut`: envia o **imediato** (constante embutida na instrução).
- `R1out`, `R2out`, `R3out`: enviam o conteúdo de R1, R2, R3.
- `AccOut`: envia o conteúdo de ACC.
- `AuxOut`: envia o conteúdo de AUX.
- Saídas da ULA: `AddOut`, `SubOut`, `OrOut` — publicam no barramento o resultado da operação selecionada.

### 3.2 Sinais de entrada (capturam dados do barramento)
- `R1in`, `R2in`, `R3in`: capturam no respectivo registrador.
- `AccIn`: captura no acumulador.
- `AuxIn`: captura no registrador auxiliar.

### 3.3 Seleção de operação na ULA
- `Add=1`: seleciona **soma** (ADD).
- `Sub=1`: seleciona **subtração** (SUB).
- `OrOut` (como saída) implica seleção lógica **OR** na ULA (alguns projetos usam `Or=1` para selecionar e `OrOut` para publicar). No gabarito, a presença de `OrOut` no microciclo indica **publicação** do resultado OR.

> Observação prática: em muitos projetos há **dois tipos** de sinais na ULA: (1) seleção de operação (ex.: `Op[1:0]`) e (2) **saída** do resultado (`FOut`). No gabarito fornecido, as linhas já incluem a combinação “seleção + saída” indireta através dos campos de controle mostrados.

### 3.4 Mnemônicos (o que significa cada comando)
- **LDI d, k** (*Load Immediate*): carrega o **valor imediato `k`** no **destino `d`** (registrador).
- **MOV d, s** (*Move*): copia de `s` (**fonte**) para `d` (**destino**) via barramento.
- **ADD ACC, AUX**: ACC := ACC + AUX (resultado sempre **entra** em ACC).
- **SUB ACC, AUX**: ACC := ACC − AUX (resultado sempre **entra** em ACC).
- **OR ACC, AUX**: ACC := ACC OR AUX (bit a bit).
- **NOP** (*No Operation*): nenhum sinal relevante é ativado; útil para temporização/alinhamento.
- **LOOP**: **label** (pseudo‑instrução) usada como ponto de retorno em laços; requer uma instrução de salto (`JMP LOOP`) para efetivar a repetição.

---

## 4) Regras operacionais: algoritmo de microexecução (passo a passo)

**Algoritmo padrão para qualquer transferência simples (LDI/MOV):**
1. **Clock 1:** Habilite **somente** a **fonte** (`Xout=1`).  
2. **Clock 2:** Mantenha a fonte ativa **se necessário** e habilite **apenas** a **entrada** do destino (`Yin=1`).  
3. Garanta que **nenhuma outra fonte** está ativa nesses clocks.

**Algoritmo padrão para operações na ULA (ADD/SUB/OR):**
1. **Clock 1:** Habilite **AccOut** e **AuxOut** (fontes) para alimentar a ULA e **selecione a operação** (ex.: `Add=1` ou `Sub=1`).  
2. **Clock 2:** Publique o resultado da ULA no barramento (ex.: `AddOut=1` / `SubOut=1` / `OrOut=1`) e **habilite AccIn=1** para **capturar** o resultado.  
3. Não há necessidade de manter `AccOut/AuxOut` no Clock 2; a ULA já gerou o resultado.

> **Correção aplicada (gabarito):** no **segundo microciclo de `MOV AUX,R1`** o sinal correto de entrada é **`AuxIn=1`** (e **não** `AccOut=1`). Assim, a sequência certa é:  
> • Clock 1: `R1out=1`  
> • Clock 2: `R1out=1` (se necessário) e **`AuxIn=1`**  
> Esta correção evita confundir **saída do ACC** com **entrada do AUX** no passo de captura.

---

## 5) Instruções do exercício — detalhamento completo

> Abaixo, cada instrução é apresentada com: objetivo, microciclos (Clock 1 e 2), sinais em 1, fluxo ASCII e comentários didáticos.

### 5.1 `LDI ACC,6` — carregar imediato 6 no acumulador
**Objetivo:** ACC := 6

**Clock 1**
- **Sinais ativos:** `ExtDataOut=1`  
- **Efeito:** o valor **6** (00000110₂) aparece no barramento.

**Clock 2**
- **Sinais ativos:** `ExtDataOut=1`, `AccIn=1`  
- **Efeito:** ACC **captura 6**.

**Fluxo ASCII**
```
C1: [Imediato 6] --(ExtDataOut=1)--> [ BARRAMENTO ]
C2: [Imediato 6] --(ExtDataOut=1)--> [ BARRAMENTO ] --(AccIn=1)--> [ ACC := 6 ]
```

**Comentários**
- Mantemos `ExtDataOut` no C2 para garantir estabilidade no momento da captura.

---

### 5.2 `LDI R1,4` — carregar imediato 4 em R1
**Objetivo:** R1 := 4

**C1:** `ExtDataOut=1` → barramento=4  
**C2:** `ExtDataOut=1`, `R1in=1` → R1 captura 4

**Fluxo**
```
C1: [Imed 4] --(ExtDataOut)--> [Bus]
C2: [Imed 4] --(ExtDataOut)--> [Bus] --(R1in)--> [R1 := 4]
```

---

### 5.3 `LDI R2,5` — carregar imediato 5 em R2
**Objetivo:** R2 := 5

**C1:** `ExtDataOut=1` → 5 no barramento  
**C2:** `ExtDataOut=1`, `R2in=1` → R2 captura 5

**Fluxo**
```
C1: [5] -ExtDataOut-> [Bus]
C2: [5] -ExtDataOut-> [Bus] -R2in-> [R2 := 5]
```

---

### 5.4 `LDI R3,10` — carregar imediato 10 em R3
**Objetivo:** R3 := 10

**C1:** `ExtDataOut=1`  
**C2:** `ExtDataOut=1`, `R3in=1`

**Fluxo**
```
C1: [10] -ExtDataOut-> [Bus]
C2: [10] -ExtDataOut-> [Bus] -R3in-> [R3 := 10]
```

---

### 5.5 `MOV AUX,R1` — **(correção aplicada no C2)**
**Objetivo:** AUX := R1

**C1:** `R1out=1` → valor de R1 no barramento  
**C2:** **`R1out=1`, `AuxIn=1`** → AUX captura R1 (**correto**)

**Fluxo**
```
C1: [R1] -R1out-> [Bus]
C2: [R1] -R1out-> [Bus] -AuxIn-> [AUX := R1]
```

**Nota**  
- No gabarito original havia a marcação equivocada em C2. A entrada correta é **AuxIn** (não AccOut).

---

### 5.6 `ADD ACC,AUX` — soma
**Objetivo:** ACC := ACC + AUX

**C1:** `AccOut=1`, `AuxOut=1`, **selecione operação** `Add=1`  
**C2:** `AddOut=1`, `AccIn=1`

**Fluxo**
```
C1: [ACC] -AccOut-> [ULA.ADD]  &  [AUX] -AuxOut-> [ULA.ADD]
C2: [ULA(ACC+AUX)] -AddOut-> [Bus] -AccIn-> [ACC := ACC+AUX]
```

**Dicas**
- Não é preciso manter `AccOut/AuxOut` em C2.  
- Flags: Z, C, N (se existirem) são atualizadas pelo resultado.

---

### 5.7 `MOV AUX,R2`
**Objetivo:** AUX := R2

**C1:** `R2out=1`  
**C2:** `R2out=1`, `AuxIn=1`

**Fluxo**
```
C1: [R2] -R2out-> [Bus]
C2: [R2] -R2out-> [Bus] -AuxIn-> [AUX := R2]
```

---

### 5.8 `SUB ACC,AUX` — subtração
**Objetivo:** ACC := ACC − AUX

**C1:** `AccOut=1`, `AuxOut=1`, **operação** `Sub=1`  
**C2:** `SubOut=1`, `AccIn=1`

**Fluxo**
```
C1: [ACC] & [AUX] --> [ULA.SUB]
C2: [ULA(ACC−AUX)] -SubOut-> [Bus] -AccIn-> [ACC := ACC−AUX]
```

**Observação**  
- Em complemento de dois, “negativo” aparece quando MSB=1.  
- *Borrow/Carry* ajustados conforme projeto.

---

### 5.9 `MOV AUX,R3`
**Objetivo:** AUX := R3

**C1:** `R3out=1`  
**C2:** `R3out=1`, `AuxIn=1`

**Fluxo**
```
C1: [R3] -R3out-> [Bus]
C2: [R3] -R3out-> [Bus] -AuxIn-> [AUX := R3]
```

---

### 5.10 `OR ACC,AUX` — OU bit a bit
**Objetivo:** ACC := ACC OR AUX

**C1:** `AccOut=1`, `AuxOut=1` (seleção lógica OR na ULA)  
**C2:** `OrOut=1`, `AccIn=1`

**Fluxo**
```
C1: [ACC] & [AUX] --> [ULA.OR]
C2: [ULA(ACC OR AUX)] -OrOut-> [Bus] -AccIn-> [ACC := ACC OR AUX]
```

---

### 5.11 `LDI R1,0`, `LDI R2,0`, `LDI R3,0`, `LDI ACC,0`, `LDI AUX,0` — reset local
**Objetivo:** Zerar registradores

**Padrão por registrador d**
- **C1:** `ExtDataOut=1` (imediato 0)  
- **C2:** `ExtDataOut=1`, `dIn=1` (onde d∈{R1,R2,R3,ACC,AUX})

**Fluxo (genérico)**
```
C1: [0] -ExtDataOut-> [Bus]
C2: [0] -ExtDataOut-> [Bus] -dIn-> [d := 0]
```

---

### 5.12 `NOP` — No Operation (3 repetições no exercício)
**Objetivo:** não alterar estado

**C1 e C2:** sem sinais relevantes (tudo 0)  
**Uso:** temporização, alinhamento de fases, espera de hardware.

---

### 5.13 `LOOP` — pseudo‑instrução (label)
**Objetivo:** marcar um ponto no código para saltos repetidos

**Implementação:** requer uma instrução de salto, ex.: `JMP LOOP`.  
Não há microciclos próprios; é apenas um **rótulo de endereço**.

---

## 6) Tabela, imagens e alinhamento com o exercício

- **Tabela de microinstruções** usada como referência (linhas da planilha/PDF):  
  `sandbox:/mnt/data/tabelaex1.png`  
- **Lista de comandos/mnemônicos** (legenda dos termos):  
  `sandbox:/mnt/data/comando.png`

> Os microciclos apresentados acima foram alinhados à tabela; o **erro único indicado** foi **corrigido** (no C2 de `MOV AUX,R1`, usar `AuxIn=1`).

---

## 7) Dicas de estudo, depuração e boas práticas

1. **Faça um “mapa de sinais”**: para cada instrução, liste **apenas** os sinais que precisam ir a 1 em cada clock.  
2. **Cheque conflitos**: verifique se **só uma fonte** está conectada ao barramento em cada clock.  
3. **Verifique o destino**: no **Clock 2** garanta que **o destino certo** está com `*In=1`.  
4. **ULA em dois tempos**: C1 fornece operandos, **C2 publica resultado** e ACC captura.  
5. **Resets locais** com `LDI d,0` são úteis para iniciar estados conhecidos.  
6. **NOPs**: bons para sincronização com periféricos ou estágios internos.  
7. **Comente seu microcódigo**: anote **o porquê** de cada sinal; ajuda a detectar erros de lógica.

---

## 8) Perguntas frequentes

- **“Por que não capturar no mesmo clock em que publico?”**  
  Porque precisamos de **tempo de estabilização** do barramento e da ULA. Dividir em C1/C2 evita leituras metastáveis.

- **“Posso ligar `AccOut` e `R1out` juntos?”**  
  **Não.** Duas fontes ativas no barramento geram **contenção** (corrente alta, dados inválidos).

- **“As flags mudam em LDI/MOV?”**  
  Tipicamente **não** (depende do design). Em operações ULA (ADD/SUB/OR) sim, conforme implementado.

- **“Qual a diferença entre `OrOut` e `Or`?”**  
  Em alguns projetos há `Or` (seleção) separado de `OrOut` (publicação do resultado). No gabarito, `OrOut` já sinaliza a **saída** da operação OR no C2.

---

## 9) Conclusões
- A lógica de **dois clocks** garante previsibilidade e segurança do dado.
- **ACC** recebe resultados; **AUX** guarda o segundo operando da ULA; **R1, R2, R3** são armazenamentos auxiliares.
- **LDI/MOV** seguem o padrão “**fonte no C1** → **destino no C2**”.
- **Operações ULA** seguem “**forneça operandos no C1** → **publique e capture no C2**”.
- Com esses padrões, você consegue **derivar novas instruções** e **validar tabelas de controle** rapidamente.

---

### Anexo A — Pseudocódigo de geração de microciclos (referência)
```text
function microsteps(instruction):
  switch instruction.type:
    case LDI(d, k):
      C1: ExtDataOut=1
      C2: ExtDataOut=1, dIn=1
    case MOV(d, s):
      C1: sout(s)=1           // R1out/R2out/R3out/AccOut/AuxOut
      C2: sout(s)=1, din(d)=1 // R?out mantido se precisar estabilidade
    case ADD:
      C1: AccOut=1, AuxOut=1, Add=1
      C2: AddOut=1, AccIn=1
    case SUB:
      C1: AccOut=1, AuxOut=1, Sub=1
      C2: SubOut=1, AccIn=1
    case OR:
      C1: AccOut=1, AuxOut=1, Op=OR
      C2: OrOut=1, AccIn=1
    case NOP:
      C1: (nenhum sinal)
      C2: (nenhum sinal)
```
