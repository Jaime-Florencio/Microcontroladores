# 🚀 Parte 01 – Circuito Mínimo do PIC18F4550

> Esta etapa define o circuito essencial para o PIC funcionar de forma estável: alimentação, reset, clock e desacoplamento.

---

## 🧩 1. Alimentação (Power Supply)

![Alimentação](../../06-assets/imagens/0PROJETO1.png)

O PIC18F4550 possui **dois pares de alimentação**:

| Tipo | Pinos | Conexão |
|------|--------|----------|
| VDD | 11 e 32 | +5 V |
| VSS | 12 e 31 | GND |

✅ **Todos devem ser ligados!**  
Eles alimentam diferentes blocos internos (núcleo, periféricos e módulo USB).  
Deixar algum par desligado pode causar **ruídos, travamentos ou resets aleatórios**.

---

### ⚡ Capacitores de desacoplamento

![Capacitor de desacoplamento](../../06-assets/imagens/3PROJETO1.png)

- Um **capacitor cerâmico de 100 nF** deve ser colocado **entre cada par VDD–VSS**, bem próximo ao chip.  
- Um **capacitor eletrolítico de 100 µF** próximo à entrada da fonte ajuda a filtrar oscilações maiores.

💬 *Função:* atuam como “mini baterias” locais — seguram variações rápidas e estabilizam o 5 V.

| Tipo | Valor | Frequência que filtra | Posição ideal |
|------|--------|------------------------|----------------|
| Cerâmico | 100 nF | Ruídos rápidos (alta frequência) | Colado nos pinos do PIC |
| Eletrolítico | 100 µF | Oscilações lentas (baixa frequência) | Perto da entrada da placa |

---

## 🔁 2. Reset (MCLR – Master Clear Reset)

![Circuito de Reset](../../06-assets/imagens/1PROJETO1.png)

O pino **MCLR (pino 1)** reinicia o PIC.  
Ele precisa ficar **em nível alto (5 V)** para o microcontrolador funcionar normalmente.

**Circuito recomendado:**
- **Resistor de 10 kΩ** entre MCLR e +5 V (mantém o pino alto).  
- **Capacitor de 1 µF** entre MCLR e GND (gera atraso na partida).  
- **Botão entre MCLR e GND** (reset manual).

💬 *Função do capacitor:* ao ligar o circuito, ele segura o MCLR em nível baixo por alguns milissegundos, garantindo que o PIC só comece a rodar quando a tensão estiver estável.

---

### 💭 Dúvidas Frequentes – Reset

**O MCLR reseta tudo?**  
Sim. Ele reinicia o programa desde o início, como o botão reset de um computador.

**O que significa pino “flutuando”?**  
É quando não está ligado a nada fixo, podendo oscilar sozinho e causar resets falsos.

**Por que usar resistor de 10 kΩ no MCLR?**  
Ele mantém o pino estável em 5 V (HIGH). Não é para limitar corrente, e sim para dar estabilidade.

**E o capacitor de 1 µF?**  
Cria um pequeno atraso no reset quando a fonte liga, garantindo que a tensão estabilize antes do PIC começar.

**Qual a função do botão no MCLR?**  
Ao apertar, liga o pino ao GND, forçando o reset manual. Ao soltar, o resistor puxa de volta para 5 V.

---

## ⏱️ 3. Clock (Oscilador Externo)

![Clock](../../06-assets/imagens/2PROJETO1.png)

O PIC precisa de um “coração” — o **oscilador** — para gerar o ritmo das instruções.

**Conexões:**
- **OSC1 (pino 13)** e **OSC2 (pino 14)** → conectam ao **cristal de 8 MHz**.  
- Cada perna do cristal vai para o **GND** através de **27 pF**.  

💬 *Como funciona:*  
OSC1 e OSC2 são as “mãos” que fazem o cristal vibrar continuamente.  
Os capacitores funcionam como “amortecedores”, equilibrando e filtrando o sinal de vibração.

---

### 🔧 Configuração no firmware

Se for usar o cristal de 8 MHz com PLL interno (para USB):

```c
#pragma config FOSC = HSPLL_HS
#pragma config PLLDIV = 2
#pragma config CPUDIV = OSC1_PLL2
#pragma config USBDIV = 2
```

Esses bits fazem o PIC multiplicar os 8 MHz do cristal até **48 MHz**, necessário para o USB full-speed.

---

## 💻 4. USB (opcional)

O pino **VBUS (20)** só deve ser ligado ao **+5 V** se você for usar USB.  
Caso contrário, **deixe desligado**.

**Para que serve o VBUS?**  
Ele detecta se o cabo USB está conectado (tensão de 5 V presente).  
Não alimenta o PIC — apenas informa ao firmware que há conexão USB.

---

## 🧠 5. Dúvidas Frequentes

**Posso ligar só um par de VDD/VSS?**  
Não! Os dois pares alimentam blocos diferentes do chip.

**Por que o PIC precisa de capacitores na alimentação?**  
Eles estabilizam a tensão — sem eles, o chip pode reiniciar sozinho.

**O que acontece se eu esquecer o capacitor de 1 µF do reset?**  
O PIC pode começar a rodar antes da tensão estabilizar, gerando falhas logo ao ligar.

**O que faz o cristal de 8 MHz?**  
Ele define o ritmo (clock) do micro. Sem ele, o PIC pode até usar o oscilador interno, mas com menos precisão.

---

## ✅ Conclusão

Se você montar **exatamente este circuito mínimo**, o PIC já:

- Liga com estabilidade  
- Gera clock preciso  
- Permite reset manual e automático  

➡️ A partir daqui, é só adicionar os periféricos do projeto (LCD, LEDs, teclado etc.).
