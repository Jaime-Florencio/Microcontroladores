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
Os arquivos atualmente disponíveis na pasta `Projetos/Central de Alarme/` são:

1. [Introdução](./Introducao.md)
2. [Parte 01 – Base do Projeto](./Parte_01_Base_Projeto.md)
3. [Parte 02 – LEDs e Display de 7 Segmentos](./Parte_02_LEDs_7Segmentos.md)
4. [Parte 03 – Teclado Matricial 4x3](./Parte_03_Teclado_Matricial.md)
5. [Parte 04 – Sensores Independentes](./Parte_04_Sensores.md)
6. [Parte 05 – Display LCD1602](./Parte_05_LCD1602.md)
7. [Parte 06 – Firmware da Central de Alarme](./Parte_06_Firmware.md)
8. [Parte 07 – Checklist Final](./Parte_07_Checklist_Final.md)

Cada parte possui seções padronizadas (Objetivo, Esquema, Cálculos e Checklist) com notas técnicas, pendências e datas previstas para conclusão.

---

## ✅ Próximos Passos
- Criar **Parte 01** documentando alimentação, reset, clock e mapa de pinos.
- Configurar o ambiente no EasyEDA e salvar os primeiros esquemáticos.
