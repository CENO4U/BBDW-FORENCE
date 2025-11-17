# Plano de UI/UX para o Dashboard Forense

Este documento traduz a arquitetura funcional do sistema em diretrizes de interface modernas e focadas em filtros inteligentes. O objetivo é apoiar designers e desenvolvedores na criação de uma experiência visual consistente, responsiva e fácil de usar.

## Princípios Gerais

1. **Design system coeso**
   - Paleta reduzida: fundo em cinza escuro, azul vibrante para ações, verde para confirmações e vermelho/laranja para alertas.
   - Tipografia: fonte sem serifa (Inter ou Roboto) para textos gerais e fonte monoespaçada para códigos/dados brutos.
   - Ícones: utilizar uma única biblioteca (Material ou Feather) para manter consistência.
2. **Hierarquia visual clara**
   - Destacar elementos críticos usando tamanho, peso e cor.
   - Agrupar informações em cards com títulos concisos.
3. **Feedback imediato**
   - Mudança de estado em botões e filtros (spinners, validações de upload).
   - Resultados atualizados em tempo real sempre que filtros forem aplicados.

## Tela Inicial – Upload e Configuração

- **Upload progressivo** com barra de progresso segmentada: parsing do ZIP, indexação, transcrição, análise.
- **Validação visual** do arquivo (borda verde com check em caso válido, vermelha com X em caso inválido).
- **Configurações automáticas**: pré-selecionar fuso horário e idioma com base no navegador, com opção de ajuste manual.

## Dashboard Principal – Painéis Cruzados

- Painéis interligados: selecionar uma pessoa no Painel 2 filtra automaticamente os demais.
- Resumo rápido com links: números (contradições, pessoas, grupos) são clicáveis e aplicam filtros.
- Botão "Expandir" em cada painel para visualização focada.

## Painel 1 – Busca Cruzada

- **Busca semântica** com alternância entre palavra-chave literal e significado aproximado.
- **Filtro por intenção**: perguntas (❓), afirmações (❗), links/mídia (🔗).
- **Filtro por sentimento** (positivo, neutro, negativo) e por mensagens não revisadas.
- Possibilidade de **salvar conjuntos de filtros** para reutilização.

## Painel 2 – Rastreamento de Pessoa

- **Gráfico horário interativo** mostrando volume de mensagens por hora; clicar na barra aplica filtro temporal.
- **Nuvem de tópicos** clicável para refinar a timeline.
- Nas contradições detectadas, incluir botão "Ver na timeline" para rolar até o contexto correspondente.

## Painel 3 – Detector de Contradições

- Visualização lado a lado das mensagens conflitantes.
- Slider de confiança mínima (50%–100%) com feedback visual.
- Modal de "Contexto Completo" mostrando mensagens ao redor das declarações conflitantes.
- Tags automáticas categorizando contradições (Financeira, Prazo, Localização, Opinião).

## Painel 4 – Cronologia Cruzada

- Linha do tempo gráfica com pistas (lanes) por grupo/chat e blocos representando mensagens.
- Controles de zoom para alternar entre hora, dia, semana ou mês.
- Insights (pressão, cadeia de eventos, lacunas) sinalizados diretamente na timeline com ícones dedicados.

## Próximos Passos

1. Criar wireframes de baixa fidelidade seguindo este guia.
2. Validar a interação dos filtros com protótipos clicáveis.
3. Implementar o design system em componentes reutilizáveis.
