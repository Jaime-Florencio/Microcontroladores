# Parte 03 – Teclado Matricial 4x3

## Objetivo
- Documentar a interface de leitura de um teclado matricial 4x3 utilizado para armar/desarmar a central.
- Definir hardware anti-ghosting e rotina de varredura para o firmware.

## Esquema
> Conteúdo pendente: desenhar matriz com resistores pull-down/pull-up, diodos de proteção e conector para o teclado.
- Pendências: verificar necessidade de buffers para isolamento e organizar rotas de sinal.
- Previsão de entrega: 12/07/2024.

## Cálculos
> Conteúdo pendente: confirmar dimensionamento dos resistores de 22 kΩ e impacto na corrente de varredura.
- Pendências: avaliar tempos RC e debouncing por hardware.
- Previsão de entrega: 12/07/2024.

## Checklist
- [ ] Escolher topologia final (pull-up vs. pull-down) conforme firmware planejado.
- [ ] Testar diodos 1N4148 no simulador para validar anti-ghosting.
- [ ] Gerar planilha de mapeamento tecla ↔ endereço lógico.
