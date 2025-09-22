# Central de Alarme — Guia de Projeto (EasyEDA Free)

Este repositório contém o passo a passo para construir uma **Central de Alarme** usando **PIC18F4550** no **EasyEDA (versão gratuita)**, seguindo as especificações do professor.

## Estrutura do guia (arquivos)
- Parte 01 — Base do projeto (alimentação, reset, clock, mapa de pinos)
- Parte 02 — LEDs e Display de 7 segmentos (cálculo de resistores, ligação correta)
- Parte 03 — Teclado matricial (linhas/colunas, diodos, pull-ups)
- Parte 04 — Sensores (entradas independentes, corrente alvo)
- Parte 05 — LCD1602 (modo de ligação, contraste, backlight)
- Parte 06 — Firmware (pinout, teste de IO, leitura do teclado)
- Parte 07 — Checklist (ERC/DRC, BoM, observações de layout)

> Requisitos (alvo do professor):
> - LEDs: Vf ≈ 1.2 V, If ≈ 11 mA
> - Diodos comuns: Vf ≈ 0.7 V
> - Teclado matricial: linhas ativas em nível baixo; corrente quando botão em curto ≈ 200 µA (usar diodos)
> - Sensores: corrente ≈ 200 µA; entradas independentes (não em série)
> - Display 7 segmentos: Vf ≈ 1.3 V por segmento; If ≈ 9 mA

## Como abrir no EasyEDA
1. File → New → Schematic.
2. Busque na **Libraries**: “PIC18F4550”, “LCD1602”, “1N4148”, “LED”, “CRYSTAL 8MHz”, “RES 1/4W”.
3. Salve o projeto e commite prints dos esquemas na pasta `Aulas/Imagens/`.

Boa prática: **inicie todos os pinos como ENTRADA** no firmware; só configure como SAÍDA quando o hardware do ramo (resistor, LED etc.) estiver correto — protege o microcontrolador.

