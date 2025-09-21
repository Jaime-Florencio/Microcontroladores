# 📘 Arquitetura Básica de Microprocessadores
## 1. Revisão de Circuitos Digitais

### 🔹 1.1 Flip-Flops
Os **flip-flops (FFs)** são os blocos básicos de **memória sequencial** — cada FF guarda **1 bit** (0 ou 1) até que ocorra uma mudança no sinal de controle (ex.: clock, set, reset).

> **Ideia central:** flip-flop é uma **célula de memória sequencial** que guarda **1 bit**.  
> “Sequencial” = a saída depende das **entradas atuais** **e** do **estado anterior**.

---

#### 🔸 1.1.1 Flip-Flop SR (Set–Reset)
**O que é.** O mais básico: guarda 1 bit usando **realimentação** (duas portas NOR ou NAND que “se alimentam” uma à outra).

- **Entradas:** **S** (set) e **R** (reset), em geral **ativas em nível alto** no modelo com NOR.  
- **Saídas:** **Q** (estado) e **Q̅** (inversa).

**Como funciona (NOR, entradas ativas em ‘1’)**

| S | R | Próximo Q | Lógica                               |
|---|---|-----------|--------------------------------------|
| 0 | 0 | Mantém    | memória (estado anterior permanece)  |
| 1 | 0 | 1         | set (força Q = 1)                    |
| 0 | 1 | 0         | reset (força Q = 0)                  |
| 1 | 1 | Proibido  | “loucura”: conflito/indeterminado    |

> ⚠️ **Estado proibido** (S = R = 1 no SR-NOR): as duas saídas tendem a 0 ao mesmo tempo; ao sair dessa condição, o circuito pode cair em qualquer estado — **indeterminação**.

**Por que usar.** Para “**lembrar**” um evento: **S** grava 1, **R** apaga. Muito útil como **latch de controle** (travas, sinalizadores simples).

**Por que pode dar “loucura”.** Se **S e R** chegam 1 **ao mesmo tempo**, há disputa na realimentação; devido a atrasos, o estado final é imprevisível (indeterminação/metastabilidade).

> 🧠 **Dica mental:** SR é ótimo para set/reset **mutuamente exclusivos**. Se há chance de S e R irem a 1 juntos, **não use SR puro**.

---

#### 🔸 1.1.2 Flip-Flop D (Data / Delay)
**O que é.** Evolução do SR que **remove o estado proibido**. “Copia” a entrada **D** para **Q**, mas **só no instante de clock** (edge-triggered) ou enquanto o clock permitir (latch por nível).

- **Entradas:** **D** (dado) e **CLK** (clock).
- **Saída:** **Q** (estado armazenado).

**Como funciona (na borda ativa do clock).** Na **borda** (ex.: subida), **Q ← D**; fora da borda, Q **permanece**.  
> **Resumo:** “**sample & hold**” — amostra D no instante do clock e **segura**.

**Por que usar.** É a **célula de memória padrão** de registradores e pipelines. Evita a “loucura” do SR e é simples de raciocinar: “**Q vira D na borda**”.

**Regras de ouro (temporização).**
- **Setup** (**t_setup**): D **estável antes** da borda por um tempo mínimo.  
- **Hold** (**t_hold**): D **permanece estável após** a borda por um tempo mínimo.  
- Violação → risco de **metastabilidade** (saída pode oscilar/atrasar para decidir entre 0/1).

> 🧠 **Dica mental:** pense no FF-D como um **fotógrafo**: o clock é o **clique**; D precisa estar “parado” um pouco **antes** e **depois** do clique.

---

#### 🔸 1.1.3 Flip-Flop JK (generalização do SR)
**O que é.** Resolve a proibição do SR e adiciona **toggle** (alternância). **J** lembra set; **K**, reset — sem estado proibido.

- **Entradas:** **J**, **K**, **CLK**.
- **Saída:** **Q**.

**Como funciona (na borda ativa).**

| J | K | Próximo Q | Lógica             |
|---|---|-----------|--------------------|
| 0 | 0 | Mantém    | memória            |
| 1 | 0 | 1         | set                |
| 0 | 1 | 0         | reset              |
| 1 | 1 | **inverte** | **toggle** (Q ← Q̅) |

**Por que usar.** Excelente para **contadores**: com **J=K=1**, cada clock **inverte** Q → divide a frequência por 2.

**Cuidado (race-around).** Em **latch sensível a nível**, se J=K=1 e o pulso de clock for “longo”, Q pode oscilar várias vezes dentro do **mesmo** pulso. Soluções: **master–slave** ou **edge-triggered**. Em FPGAs/ASICs modernos, o “JK” costuma ser implementado com **lógica ao redor de um FF-D**.

> 🧠 **Dica mental:** JK = SR “turbinado” com **toggle**. Para **dados**, use D; para **contar/dividir**, JK/T brilham.

---

#### 🔸 1.1.4 Flip-Flop T (Toggle)
**O que é.** Versão “só alterna”. Equivalente a JK com **J=K=1** ou a **D = T XOR Q**.

- **Entradas:** **T**, **CLK**.
- **Saída:** **Q**.

**Como funciona.**
- **T=0** → mantém Q.  
- **T=1** → **Q inverte** a cada borda (0,1,0,1…).

**Por que usar.** **Contadores binários** e **divisão de clock**:  
1 FF-T divide por 2; 2 em cascata, por 4; n FF-T → por **2ⁿ**.

> 🧠 **Dica mental:** T é o **piscador** (blink) e **divisor de frequência** clássico.

---

#### 🔸 1.1.5 Latch × Flip-Flop (nível × borda)
- **Latch (por nível):** transparente quando **CLK=1** (ou 0); se a entrada muda nesse período, a saída **acompanha**.  
- **Flip-flop (por borda):** amostra **apenas na borda** (↑/↓); fora da borda, a saída **trava**.

> **Por que preferimos borda?** Facilita o **controle temporal** e evita **race-around**. A maioria dos sistemas modernos é **sincronizada por borda**.

---

#### 🔸 1.1.6 Por que o **clock** surgiu (e por que usamos até hoje)
**Problema sem clock (assíncrono).** Trocas de sinais a qualquer momento → **disputas de tempo**, **hazards**, realimentações que entram em “**loucura**” (metastabilidade longa, corridas).

**O que o clock resolve.**  
- **Discretiza o tempo**: define **instantes oficiais** de amostragem (bordas) e janelas de **estabilização**.  
- Permite **pipeline**, **previsibilidade** (timing) e **verificação de setup/hold**.  
- Escala para **sistemas grandes** com muitos registradores passo-a-passo.

**Conceitos práticos.**  
- **Borda:** subida (↑) ou descida (↓).  
- **Duty cycle:** fração do período em nível alto.  
- **Jitter:** variação de período/borda — piora margens de timing.  
- **Clock enable:** preferir habilitar o FF, em vez de “cortar” o clock com lógica (**evita glitches**).

> 🧠 **Dica mental:** o clock é o **metrônomo** da orquestra; sem ele, cada músico toca num tempo → vira caos.

---

#### 🔸 1.1.7 Metastabilidade
Quando o FF **amostra um sinal em transição** (violou setup/hold), a saída pode demorar a decidir 0/1 ou oscilar.

**Como mitigar.**  
- Projetar **tempos** (respeitar setup/hold).  
- Para sinais **assíncronos** que entram no domínio de clock: usar **sincronizadores** (duplo FF-D em série).

---

#### 🔸 1.1.8 Onde cada um é mais usado (na prática moderna)
- **D:** registradores, pipelines, armazenamento — **onipresente**.  
- **SR:** latches de controle, **preset/clear assíncronos** de FF-D.  
- **JK/T:** contadores/divisores em lógica discreta; em FPGAs/ASICs, geralmente **sintetizados com D**.

---

#### 🔸 1.1.9 Regras de ouro (prova/projeto)
1. **SR-NOR:** **nunca** S=R=1 → estado proibido/indeterminado.  
2. **FF-D:** “**Q vira D na borda**”; cuide de **setup/hold**.  
3. **JK:** J=K=1 → **toggle**; evite **race-around** (use borda/master-slave).  
4. **T:** com T=1 divide por 2; em cascata, por 2ⁿ.  
5. **Clock:** é o **metrônomo**; prefira **clock enable** a “gating” com lógica.  
6. **Metastabilidade:** evite violar setup/hold; use **sincronizadores**.

---

### 🔹 1.2 Buffer Tri-State
Um **buffer** permite/bloqueia a passagem de dados. O **tri-state** tem três estados:
1. **0** (nível baixo)  
2. **1** (nível alto)  
3. **Alta impedância (Hi-Z)** → **desconectado** do barramento

> **Para que serve.** Quando **vários dispositivos compartilham o mesmo barramento**, o Hi-Z impede que dois drivers forcem **0 e 1 ao mesmo tempo** (curto lógico / conflito).  
**Sinal de habilitação** (EN) decide quando o dispositivo fala no barramento.

---

### 🔹 1.3 Registradores Bidirecionais
Conjunto de **flip-flops D** em paralelo para armazenar **n bits** (8/16/32…). São “**bidirecionais**” pois podem **ler (OUT)** e **escrever (IN)** no barramento quando habilitados.

- Exemplos no processador: `RA`, `RB`, `RC` (operandos para a ULA).  
- **Sinais típicos:** `RAOUT`, `RAIN`, `RBOUT`, `RBIN`…  
- **Comportamento:** quando `RAOUT`=1, RA dirige o barramento; quando `RAIN`=1, o dado do barramento é **carregado** em RA (na borda do clock).

> **Ideia chave:** registradores + sinais IN/OUT + tri-state = **movimentação de dados** controlada sem conflito.

---

### 🔹 1.4 Relação com o Microprocessador
- **Flip-flops** → célula de **memória sequencial** elementar.  
- **Buffers tri-state** → **arbitram** quem fala no barramento.  
- **Registradores** → **memória temporária** para a ULA e para fluxo interno.  

➡️ Juntos, formam os **blocos essenciais** que permitem ao processador **armazenar e mover dados** de forma ordenada e sincronizada pelo **clock**.
