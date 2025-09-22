# 📘 Central de Alarme – Introdução

## 🎯 Objetivo do Projeto
Este projeto tem como objetivo desenvolver uma **Central de Alarme** baseada no microcontrolador **PIC18F4550**, utilizando o software **EasyEDA Free** para esquemáticos/PCB e documentando todo o processo neste repositório GitHub.

A Central de Alarme será composta por:
- Teclado matricial 4x3
- LEDs de indicação
- Display de 7 segmentos
- Display LCD1602
- Sensores independentes
- Circuitos básicos de alimentação, clock e reset

---

## 🛠️ Ferramentas Utilizadas
- **EasyEDA (Free)** – desenho de esquemáticos e layout PCB  
- **GitHub** – documentação do projeto em Markdown  
- **PIC18F4550** – microcontrolador principal  
- **Datasheets** dos componentes (LED, diodo, display, teclado)  

---

[📑 Ver Especificação](../../../06-assets/imagens/especificacaoprojeto.png)

## 📐 Especificações Técnicas (resumo)
Conforme referência do professor e cálculos iniciais:

- **LEDs**:  
  - Vf ≈ 1.2 V, If ≈ 11 mA  
  - Resistor calculado: **330 Ω**

- **Display de 7 segmentos**:  
  - Vf ≈ 1.3 V por segmento, If ≈ 9 mA  
  - Resistor calculado: **430 Ω**

- **Teclado matricial**:  
  - Corrente alvo ≈ 200 µA  
  - Diodos: 1N4148 (0.7 V)  
  - Resistores: **22 kΩ**

- **Sensores**:  
  - Corrente alvo ≈ 200 µA  
  - Resistores: **24 kΩ**  
  - Entradas **independentes** (não em série)

- **LCD1602**:  
  - Trimpot de 4.7 kΩ para ajuste de contraste  

- **Oscilador**: cristal 8 MHz + capacitores 27 pF  
- **Reset**: resistor 10 kΩ + capacitor 1 µF (power-on reset)  

---

## 📂 Organização da Documentação
O projeto será dividido em partes, cada uma em seu próprio arquivo `.md` dentro da pasta `Projetos/1.Central _de_Alarme/`:

1. **Introdução** (este arquivo)  
2. **Parte 01 – Base do Projeto** (alimentação, reset, clock, pinagem inicial)  
3. **Parte 02 – LEDs e Display de 7 segmentos**  
4. **Parte 03 – Teclado Matricial**  
5. **Parte 04 – Sensores**  
6. **Parte 05 – LCD1602**  
7. **Parte 06 – Firmware**  
8. **Parte 07 – Checklist Final**  

---

## ✅ Próximos Passos
- Criar **Parte 01** documentando alimentação, reset, clock e mapa de pinos.  
- Configurar o ambiente no EasyEDA e salvar os primeiros esquemáticos.  
