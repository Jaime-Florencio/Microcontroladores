# 🚀 Parte 01 – Circuito Mínimo do PIC18F4550

## 1. Alimentação (Power Supply)

O PIC não funciona só porque está no esquemático: ele precisa receber energia estável.

- **Pinos Vdd (11 e 32)** → ligam no **+5 V**  
- **Pinos Vss (12 e 31)** → ligam no **GND**  
- Cada par **Vdd–Vss** precisa de um **capacitor cerâmico de 100 nF** bem próximo (isso “filtra” ruído da fonte).  
- Um capacitor maior (**10 µF ou mais**) perto da entrada da fonte ajuda contra variações maiores.  

👉 Sem esses capacitores, o PIC pode **resetar sozinho** ou se comportar de forma estranha.

---

### 💭 Dúvidas Frequentes – Alimentação
**Por que usar capacitor cerâmico de 100 nF em cada par Vdd–Vss?**  
Porque ele atua como um reservatório instantâneo contra ruídos rápidos da fonte. O valor de 100 nF é padrão na indústria para filtrar frequências altas.

**Por que adicionar um capacitor maior (10 µF ou mais)?**  
Esse segura variações mais lentas na tensão, funcionando como uma “mini bateria”.

**Posso usar um diodo de proteção?**  
Sim, um diodo zener pode ser usado entre +5 V e GND para proteger contra sobretensão.

---

## 2. Reset (MCLR – Master Clear Reset)

O pino **MCLR (pino 1)** serve para reiniciar o PIC.

- Ele precisa ficar sempre em **nível alto (5 V)** para o PIC rodar o programa.  
- Se ficar “flutuando”, o PIC trava.  

Por isso usamos:  
- **Resistor de 10 kΩ** ligando MCLR ao +5 V (pull-up).  
- **Capacitor de 1 µF** ligando MCLR ao GND → cria um atraso quando liga a fonte.  
- **Opcional:** botão entre MCLR e GND para reset manual.  

👉 Isso garante que, ao ligar a fonte, o PIC **espera alguns milissegundos** antes de começar a rodar o programa, dando tempo para a tensão estabilizar.

---

### 💭 Dúvidas Frequentes – Reset
**O MCLR reseta tudo?**  
Sim, ele reinicia o programa desde o início, como um botão reset do PC.

**O que significa pino “flutuando”?**  
É quando não está ligado a nada fixo, podendo oscilar sozinho e causar resets falsos.

**Por que usar resistor de 10 kΩ no MCLR?**  
Ele mantém o pino estável em 5 V (HIGH). Não é para limitar corrente, e sim para dar estabilidade.

**E o capacitor de 1 µF?**  
Ele cria um pequeno atraso no reset quando a fonte liga, garantindo que a tensão estabilize antes do PIC começar.

**Qual a função do botão no MCLR?**  
Ao apertar, liga o pino ao GND, forçando o reset manual. Ao soltar, o resistor puxa de volta para 5 V.

---

## 3. Clock (Oscilador)

O PIC precisa de um **“coração” batendo**, que é o **oscilador**.

- **Pinos OSC1 (13) e OSC2 (14)** → ligamos um **cristal de 8 MHz**.  
- Cada perna do cristal vai a GND através de **27 pF**.  
- Esse clock externo pode ser multiplicado pelo **PLL interno do PIC** → chegando a **48 MHz** se necessário (por exemplo, para USB).  

👉 Se não colocar cristal, você pode usar o **oscilador interno** (configurado no firmware), mas em projetos sérios geralmente se usa cristal pela **precisão**.

---

### 💭 Dúvidas Frequentes – Clock
**Por que dois pinos para clock?**  
Porque o cristal tem dois terminais: OSC1 (entrada) e OSC2 (saída do oscilador interno).

**Qual frequência usar?**  
Você escolhe, mas deve estar dentro dos limites do datasheet. Para USB precisa de 48 MHz (via PLL).

---

## 4. USB (opcional)

- **Pino VBUS (20)** só deve ser ligado ao **+5 V** se você for usar USB.  
- Caso contrário, **deixe desligado**.  

---

### 💭 Dúvidas Frequentes – USB
**Para que serve o pino VBUS?**  
Ele detecta se a tensão de 5 V do cabo USB está presente. Não alimenta o PIC, mas informa ao firmware.

## ✅ Conclusão

Se você ligar apenas:
- **Vdd e Vss** corretamente,  
- Colocar **capacitores de desacoplamento**,  
- **Reset** com resistor + capacitor,  
- **Cristal** com capacitores de carga,  

➡️ O PIC já **liga, reseta e gera clock**.  
Ele estará pronto para rodar qualquer firmware que você gravar.

