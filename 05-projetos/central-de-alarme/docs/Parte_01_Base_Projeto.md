# 🚀 Parte 01 – Circuito Mínimo do PIC18F4550

## 1. Alimentação (Power Supply)

O PIC não funciona só porque está no esquemático: ele precisa receber energia estável.

- **Pinos Vdd (11 e 32)** → ligam no **+5 V**  
- **Pinos Vss (12 e 31)** → ligam no **GND**  
- Cada par **Vdd–Vss** precisa de um **capacitor cerâmico de 100 nF** bem próximo (isso “filtra” ruído da fonte).  
- Um capacitor maior (**10 µF ou mais**) perto da entrada da fonte ajuda contra variações maiores.  

👉 Sem esses capacitores, o PIC pode **resetar sozinho** ou se comportar de forma estranha.

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

## 3. Clock (Oscilador)

O PIC precisa de um **“coração” batendo**, que é o **oscilador**.

- **Pinos OSC1 (13) e OSC2 (14)** → ligamos um **cristal de 8 MHz**.  
- Cada perna do cristal vai a GND através de **27 pF**.  
- Esse clock externo pode ser multiplicado pelo **PLL interno do PIC** → chegando a **48 MHz** se necessário (por exemplo, para USB).  

👉 Se não colocar cristal, você pode usar o **oscilador interno** (configurado no firmware), mas em projetos sérios geralmente se usa cristal pela **precisão**.

---

## 4. USB (opcional)

- **Pino VBUS (20)** só deve ser ligado ao **+5 V** se você for usar USB.  
- Caso contrário, **deixe desligado**.  

---

## ✅ Conclusão

Se você ligar apenas:
- **Vdd e Vss** corretamente,  
- Colocar **capacitores de desacoplamento**,  
- **Reset** com resistor + capacitor,  
- **Cristal** com capacitores de carga,  

➡️ O PIC já **liga, reseta e gera clock**.  
Ele estará pronto para rodar qualquer firmware que você gravar.

