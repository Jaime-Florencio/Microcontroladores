# Parte 04 – Sensores Independentes

## Objetivo
- Integrar sensores de intrusão (PIR, magnéticos, etc.) com entradas independentes no PIC18F4550.
- Garantir isolamento elétrico e filtragem de ruídos provenientes dos sensores.

## Esquema
> Conteúdo pendente: criar esquemático com optoacopladores/ filtros RC conforme necessidade de cada sensor.
- Pendências: especificar conectores modulares e planejamento de alimentação auxiliar (12 V → 5 V se aplicável).
- Previsão de entrega: 19/07/2024.

## Cálculos
> Conteúdo pendente: dimensionar resistores de pull-up/pull-down e redes RC para debounce/filtragem.
- Pendências: calcular correntes máximas nos sensores e verificar compatibilidade com optoacopladores.
- Previsão de entrega: 19/07/2024.

## Checklist
- [ ] Listar sensores suportados e respectivas características elétricas.
- [ ] Definir estratégia de detecção de falha (sensor aberto/curto).
- [ ] Validar necessidade de proteções adicionais (TVS, fusíveis resetáveis).
