# Dicionário de dados

Este documento descreve os CSVs originais e as métricas definidas nas [perguntas de negócio](perguntas.md).
As decisões e correções de registros devem constar no [log de limpeza](log_limpeza.md).

## Sumário

- [1. Fontes](#1-fontes)
- [2. Convenções deste documento](#2-convenções-deste-documento)
- [3. Tabela turnos](#3-tabela-turnos-1-linha--1-turno)
- [4. Tabela despesas](#4-tabela-despesas-1-linha--1-lançamento)
- [5. Campos antigos × campos novos do formulário](#5-campos-antigos--campos-novos-do-formulário)
- [6. Receita: como evitar contar duas vezes](#6-receita-como-evitar-contar-duas-vezes)
- [7. Campos calculados](#7-campos-calculados)
- [8. Formatos, unidades e valores vazios](#8-formatos-unidades-e-valores-vazios)
- [9. Qualidade observada na exportação](#9-qualidade-observada-na-exportação)
- [10. Privacidade e publicação](#10-privacidade-e-publicação)
- [11. Pendências](#11-pendências)

## 1. Fontes

| Arquivo | Origem | Colunas | Registros | Exportado em |
| --- | --- | --- | --- | --- |
| `dados/bruto/turnos_2026-11-20.csv` | Google Forms / Sheets — turnos | 29 | 31 | 20/11/2026, conforme nome do arquivo |
| `dados/bruto/despesas_2026-11-20.csv` | Google Forms / Sheets — despesas | 20 | 61 | 20/11/2026, conforme nome do arquivo |

- Os arquivos brutos são privados e permanecem em `dados/bruto/`.
- Arquivo exportado ≠ recorte analítico. Ambos os CSVs abrangem 29/10/2026 a 20/11/2026 nas datas de turno/despesa. A v1 usa 29/10/2026 a 18/11/2026, com filtro a aplicar no Power Query.

O formulário mantém campos antigos e novos na mesma exportação. As seções 3 e 4 descrevem somente colunas originais; a seção 7 reúne cálculos.

## 2. Convenções deste documento

| Termo | Significado |
| --- | --- |
| Tipo | Tipo esperado após importar o texto do CSV. |
| Exemplo | Valor ilustrativo criado para explicar o formato; não representa registro real. Nas colunas privadas, `[omitido]`. |
| Público? — Sim | Campo destinado à análise pública, sujeito à revisão do conjunto. |
| Público? — Revisar | Avaliar exposição e anonimizar ou remover antes de publicar. |
| Público? — Não | Excluir da versão pública. |
| Antigo / Novo | Campos das versões do formulário; a consolidação está descrita na seção 5. |

## 3. Tabela `turnos` (1 linha = 1 turno)

### 3.1 Identificação e tempo

| Coluna | Tipo | Descrição | Exemplo | Público? |
| --- | --- | --- | --- | --- |
| Carimbo de data/hora | Data/hora | Carimbo do registro no formulário; não representa o início do trabalho. | 07/02/2025 14:23:17 | Revisar |
| Data do turno | Data | Dia de referência do turno e do filtro analítico. | 07/02/2025 | Sim |
| Turno | Categoria | Classificação declarada no formulário antigo; distinta do turno calculado. | Manhã | Sim |
| Hora de início | Hora | Início do período registrado, incluindo espera por corridas. | 06:17:00 | Sim |
| Hora de término | Hora | Fim do período registrado. | 11:43:00 | Sim |
| Pausas pessoais dentro do turno (minutos) | Decimal (min) | Pausas pessoais dentro do período; descontar quando informadas e válidas. | 17 | Sim |

### 3.2 Quilometragem

| Coluna | Tipo | Descrição | Exemplo | Público? |
| --- | --- | --- | --- | --- |
| KM inicial (odômetro) | Decimal (km) | Leitura acumulada do odômetro no início. | 123456 | Sim |
| KM final (odômetro) | Decimal (km) | Leitura acumulada do odômetro no fim. | 123579 | Sim |
| KM pessoais dentro do turno | Decimal (km) | Uso pessoal incluído na diferença do odômetro; descontar quando informado e válido. | 4,7 | Sim |

### 3.3 Corridas e ganhos

| Coluna | Tipo | Descrição | Exemplo | Público? |
| --- | --- | --- | --- | --- |
| Corridas Uber (quantidade) | Inteiro | Corridas realizadas pela Uber. | 17 | Sim |
| Ganhos Uber no turno (R$) | Decimal (R$) | Parte do motorista na Uber, após taxa da plataforma e antes dos custos operacionais. | 173,46 | Sim |
| Corridas 99 (quantidade) | Inteiro | Corridas realizadas pela 99. | 14 | Sim |
| Ganhos 99 no turno (R$) | Decimal (R$) | Parte do motorista na 99, após taxa da plataforma e antes dos custos operacionais. | 128,73 | Sim |
| Gorjetas / extras em dinheiro (R$) | Decimal (R$) | Extras externos; entram na receita somente se estiverem fora dos totais dos apps. | 6,37 | Sim |
| Corridas recusadas ou canceladas (quantidade) | Inteiro | Campo antigo que reúne recusas e cancelamentos; não permite distinguir os eventos nem calcular taxas. | 7 | Sim |

### 3.4 Detalhamento dos ganhos

Bônus e gorjetas destes campos já integram os ganhos. Não somar novamente.

| Coluna | Tipo | Descrição | Exemplo | Público? |
| --- | --- | --- | --- | --- |
| Bônus Uber já incluídos no ganho (R$) | Decimal (R$) | Parcela de bônus dentro do ganho Uber. | 13,27 | Sim |
| Gorjetas Uber já incluídas no ganho (R$) | Decimal (R$) | Parcela de gorjetas dentro do ganho Uber. | 4,83 | Sim |
| Bônus 99 já incluídos no ganho (R$) | Decimal (R$) | Parcela de bônus dentro do ganho 99. | 11,39 | Sim |
| Gorjetas 99 já incluídas no ganho (R$) | Decimal (R$) | Parcela de gorjetas dentro do ganho 99. | 3,71 | Sim |

### 3.5 Forma de recebimento

Espécie e Pix descrevem como a corrida foi recebida. Não são receita adicional.

| Coluna | Tipo | Descrição | Exemplo | Público? |
| --- | --- | --- | --- | --- |
| Uber: total recebido dos passageiros em espécie (R$) | Decimal (R$) | Antigo: dinheiro físico recebido de passageiros Uber. | [omitido] | Não |
| 99: total recebido dos passageiros em espécie (R$) | Decimal (R$) | Antigo: dinheiro físico recebido de passageiros 99. | [omitido] | Não |
| Uber: total recebido por Pix direto no Nubank (R$) | Decimal (R$) | Antigo: Pix direto de passageiros Uber; não é transferência entre contas. | [omitido] | Não |
| 99: total recebido por Pix direto no Nubank (R$) | Decimal (R$) | Antigo: Pix direto de passageiros 99; não é transferência entre contas. | [omitido] | Não |
| Recebido em dinheiro/Pix direto (R$) | Decimal (R$) | Novo: consolidado de recebimentos diretos das duas plataformas. | [omitido] | Não |

### 3.6 Contexto do turno

| Coluna | Tipo | Descrição | Exemplo | Público? |
| --- | --- | --- | --- | --- |
| Clima predominante | Categoria | Antigo: clima predominante declarado. | Chuva leve | Sim |
| Clima | Categoria | Novo: clima declarado no formulário simplificado. | Chuva leve | Sim |
| Região principal do turno | Texto/categoria | Região predominante; não descreve o trajeto completo. | Região ilustrativa A | Revisar |
| Veículo (apelido, sem placa) | Texto/categoria | Apelido operacional do veículo, sem placa. | Veículo ilustrativo A | Revisar |
| Observações | Texto livre | Contexto adicional; pode conter informações pessoais. | [omitido] | Não |

## 4. Tabela `despesas` (1 linha = 1 lançamento)

### 4.1 Identificação e valor

| Coluna | Tipo | Descrição | Exemplo | Público? |
| --- | --- | --- | --- | --- |
| Carimbo de data/hora | Data/hora | Carimbo do registro; distinto da data da compra ou pagamento. | 07/02/2025 15:26:19 | Revisar |
| Data da despesa | Data | Data da despesa; pode diferir da competência do custo. | 07/02/2025 | Sim |
| Categoria | Categoria | Antigo: classificação declarada da despesa. | Lavagem | Sim |
| Categoria (padronizada) | Categoria | Novo: classificação padronizada no formulário; é campo original, não cálculo. | Lavagem | Sim |
| Valor (R$) | Decimal (R$) | Valor total do lançamento; não duplicar a compra ao registrar pagamento da fatura. | 47,63 | Sim |
| Uso da despesa (Trabalho, Pessoal ou Misto) | Categoria | Destinação declarada: trabalho, pessoal ou mista. | Misto | Sim |
| Percentual profissional (0 a 100, somente se Misto) | Decimal (%) | Percentual profissional declarado para uso misto, na escala de 0 a 100. | 37,5 | Sim |

### 4.2 Combustível

| Coluna | Tipo | Descrição | Exemplo | Público? |
| --- | --- | --- | --- | --- |
| Litros (só para combustível) | Decimal (L) | Volume comprado; não mede sozinho o consumo do turno. | 8.437 | Sim |
| Odômetro no momento (só para combustível) | Decimal (km) | Odômetro acumulado no abastecimento. | 123567 | Sim |
| Combustível (Gasolina, Etanol, GNV, Diesel ou Elétrico) | Categoria | Tipo de combustível ou energia informado. | Etanol | Sim |
| Tanque cheio? (Sim ou Não) | Categoria binária | Indica se o tanque foi completado. Vazio não significa Não. | Sim | Sim |
| Veículo (apelido, sem placa) | Texto/categoria | Veículo associado à despesa; também se aplica a outros custos do carro. | Veículo ilustrativo A | Revisar |

### 4.3 Aluguel

Conforme perguntas.md, cada pagamento cobre os sete dias anteriores à data de pagamento: de D − 7 a D − 1, inclusive. Dividir o valor efetivo por sete, incluindo folgas, e arredondar somente na apresentação.

| Coluna | Tipo | Descrição | Exemplo | Público? |
| --- | --- | --- | --- | --- |
| Início do período do aluguel (dd/mm/aaaa) | Data | Início declarado da cobertura do aluguel. | 01/02/2025 | Sim |
| Fim do período do aluguel (dd/mm/aaaa) | Data | Fim declarado da cobertura do aluguel, inclusive. | 07/02/2025 | Sim |

### 4.4 Pagamento e onde foi gasto

| Coluna | Tipo | Descrição | Exemplo | Público? |
| --- | --- | --- | --- | --- |
| Forma de pagamento | Categoria | Meio usado para pagar a despesa. | Pix | Sim |
| Descrição | Texto livre | Detalhamento do lançamento; pode conter informações pessoais. | [omitido] | Não |
| Conta de pagamento | Texto/categoria | Conta ou carteira usada no pagamento. | [omitido] | Não |
| Link do comprovante (opcional) | Texto/URL | Referência privada ao comprovante. | [omitido] | Não |
| Estabelecimento | Texto/categoria | Novo: estabelecimento onde ocorreu a despesa. | Posto ilustrativo A | Revisar |
| ID_compra_mercado | Texto identificador | Identificador da compra de mercado; preservar como texto. | [omitido] | Não |

## 5. Campos antigos × campos novos do formulário

| Informação | Usar primeiro (novo) | Usar se o novo estiver vazio (antigo) | Cuidado |
| --- | --- | --- | --- |
| Clima | `Clima` | `Clima predominante` | Harmonizar rótulos e registrar discordâncias. |
| Categoria da despesa | `Categoria (padronizada)` | `Categoria` | Preservar a classificação original na fonte. |
| Recebimento direto | `Recebido em dinheiro/Pix direto (R$)` | Quatro campos de espécie/Pix por plataforma da seção 3.5 | Nunca somar o consolidado com os quatro antigos. Preenchimento parcial pode gerar total incompleto. |
| Estabelecimento | `Estabelecimento` | `Descrição`, somente quando a identificação for inequívoca | Não deduzir estabelecimento de texto ambíguo nem publicar a descrição. |

Zero é preenchimento válido. Usar o campo antigo apenas quando o novo estiver vazio. Quatro recebimentos antigos vazios representam total desconhecido, não zero.

## 6. Receita: como evitar contar duas vezes

```text
Receita total = Ganhos Uber + Ganhos 99 + Gorjetas / extras em dinheiro (fora dos apps)
```

| Soma? | Componente | Por quê |
| --- | --- | --- |
| ✅ | Ganhos Uber e 99 | Receita do motorista já líquida da taxa dos apps. |
| ✅ | Extras externos | Somente quando comprovadamente fora dos ganhos informados. |
| ❌ | Bônus e gorjetas já incluídos | São parcelas dos ganhos, não acréscimos. |
| ❌ | Recebimentos de corridas em espécie/Pix | São formas de recebimento da receita já registrada. |
| ❌ | Transferências e saldos | Não representam receita de corridas. |

## 7. Campos calculados

Esta seção especifica os cálculos para Power Query e DAX. Os rótulos abaixo não são cabeçalhos dos CSVs. Todos os exemplos numéricos são ilustrativos.

### 7.1 Colunas por turno (Power Query)

| Campo | Regra | Exemplo |
| --- | --- | --- |
| Turno calculado | Início antes de 12h: Manhã; a partir de 12h: Tarde/Noite. | 13:17 → Tarde/Noite |
| Horas trabalhadas | Duração entre início e término menos pausas pessoais válidas, convertidas de minutos para horas. | 6 h − 18/60 h = 5,7 h |
| KM em turno | Odômetro final − inicial − km pessoais válidos. | 123580 − 123450 − 4 = 126 km |
| KM fora de turno | Salto positivo entre o odômetro final de um turno e o inicial do seguinte, em ordem cronológica. | 123600 − 123580 = 20 km |
| Receita do turno | Ganhos dos dois apps + extras externos sem duplicação. | 173,46 + 128,73 + 6,37 = 308,56 reais |
| Corridas do turno | Corridas Uber + corridas 99. | 17 + 14 = 31 corridas |

### 7.2 Medidas (DAX)

| Métrica | Regra | Exemplo |
| --- | --- | --- |
| Receita dos aplicativos | Somar ganhos Uber e 99. | 720 + 480 = 1.200 reais |
| Extras externos | Somar somente extras fora dos totais dos apps. | 12 + 18 = 30 reais |
| Receita total | Receita dos aplicativos + extras externos. | 1.200 + 30 = 1.230 reais |
| Horas trabalhadas | Somar horas líquidas dos turnos. | 12 + 18 = 30 h |
| Intervalo entre turnos | Excluir do tempo trabalhado; é uma definição, não uma medida de produtividade. | 11h–15h = 4 h excluídas |
| KM em turno | Somar distâncias operacionais dos turnos. | 230 + 270 = 500 km |
| KM fora de turno | Somar saltos positivos entre turnos consecutivos. | 24 + 26 = 50 km |
| KM total observado | KM em turno + KM fora de turno. | 500 + 50 = 550 km |
| Custo de combustível | Somar abastecimentos do período; aproxima custo, não consumo exato. | 130 + 170 = 300 reais |
| Aluguel rateado | Somar parcelas diárias dos dias cobertos no período, inclusive folgas. | 560 / 7 × 3 dias = 240 reais |
| Custo total | Combustível + aluguel rateado + demais despesas de trabalho. | 300 + 240 + 60 = 600 reais |
| Resultado operacional estimado | Receita total − custo total. | 1.230 − 600 = 630 reais |
| Resultado por hora | Resultado do período / horas do período. | 630 / 30 = 21 reais/h |
| Margem operacional | Resultado / receita total. | 630 / 1.230 ≈ 51,22% |
| Receita por hora | Receita total / horas trabalhadas. | 1.230 / 30 = 41 reais/h |
| Ticket médio por plataforma | Receita da plataforma / corridas da plataforma. | 720 / 36 = 20 reais/corrida |
| Corridas por hora | Total de corridas / horas trabalhadas. | 60 / 30 = 2 corridas/h |
| Receita por km em turno | Receita total / KM em turno. | 1.230 / 500 = 2,46 reais/km |
| Custo operacional por km | Custo total / KM total observado. | 600 / 550 ≈ 1,09 reais/km |
| Preço médio do combustível | Soma dos valores abastecidos / soma dos litros correspondentes. | 300 / 50 = 6 reais/L |

Calcular razões pela **divisão dos totais, nunca pela média das razões**. Em DAX, usar `DIVIDE(numerador, denominador)` para tratar denominadores zero ou em branco sem fabricar um indicador zero. As horas não se dividem por plataforma: os aplicativos operam simultaneamente e não há medição de horas exclusivas.

Segundo perguntas.md, o combustível é distribuído entre turnos proporcionalmente aos km em turno; aluguel e demais custos, proporcionalmente às horas. São rateios analíticos. Separar resultado com custos registrados de resultado com provisão e substituir a provisão pelo custo confirmado, sem duplicação.

## 8. Formatos, unidades e valores vazios

| Padrão | Tratamento |
| --- | --- |
| Codificação | CSVs lidos como UTF-8; este documento usa UTF-8 sem BOM e LF. |
| Delimitador | Vírgula; respeitar aspas em campos delimitados. |
| Datas | `dd/mm/aaaa`, com dia primeiro. Data do evento não é carimbo nem competência. |
| Horas | `HH:mm` ou `HH:mm:ss`; duração calculada em horas decimais. |
| Fuso | Contexto do projeto: `America/Sao_Paulo`. |
| Moeda e decimais | Reais; conferir ponto ou vírgula decimal por campo. Não remover pontos indiscriminadamente. Manter precisão nos cálculos e arredondar a apresentação monetária. |
| Distância e volume | Odômetro e distância em km; abastecimento em L. GNV e eletricidade exigem unidades próprias, m³ e kWh. |
| Tempo | Pausas em minutos; converter para horas antes de subtrair. |
| Percentual | Escala de 0 a 100 no campo profissional; exemplo ilustrativo: 37,5 representa 37,5%. |
| Identificadores | Texto; preservar zeros à esquerda. |

- Vazio ≠ zero. Pode significar não informado, não aplicável ou não coletado naquela versão.
- Vazio em campo binário não significa Não. Ausência de turno não comprova folga.
- Não preencher automaticamente pausas, km pessoais ou componentes financeiros ausentes com zero.
- Não interpretar volume comprado como volume consumido sem controle do tanque.

## 9. Qualidade observada na exportação

Na leitura completa dos CSVs, as seguintes colunas estão 100% vazias:

**turnos:**

- `Bônus Uber já incluídos no ganho (R$)`
- `Bônus 99 já incluídos no ganho (R$)`
- `Gorjetas 99 já incluídas no ganho (R$)`
- `Recebido em dinheiro/Pix direto (R$)`

**despesas:**

- `Percentual profissional (0 a 100, somente se Misto)`
- `Início do período do aluguel (dd/mm/aaaa)`
- `Fim do período do aluguel (dd/mm/aaaa)`

Vazio não prova ausência de bônus, gorjetas, recebimentos, uso profissional ou cobertura de aluguel. Os erros pontuais e suas correções pertencem ao [log de limpeza](log_limpeza.md).

## 10. Privacidade e publicação

Excluir todos os campos marcados como **Não**: textos livres de observações e descrição, conta, comprovante, ID de compra e recebimentos em espécie/Pix. Revisar carimbos, região, veículo e estabelecimento; remover ou generalizar informações que revelem identidade ou rotina.

Somente despesas de trabalho entram na versão anonimizada. Excluir despesas pessoais, dívidas e transferências. Revisar também a combinação de campos marcados como Sim, pois datas e horários podem revelar rotina. Manter a fonte privada intacta e publicar apenas uma cópia tratada.

## 11. Pendências

| # | Pendência | Impacto |
| --- | --- | --- |
| 1 | Disponibilizar o tratamento Power Query e as medidas DAX. O projeto local não contém código, planilha de cálculo ou PBIX; o filtro e os cálculos acima são especificações, não execução comprovada. | Conferir nomes finais, implementação do recorte e resultados. |
| 2 | Confirmar as prioridades entre campos antigos e novos e a extração de estabelecimento descritas na seção 5. | Evitar consolidação incorreta ou perda de informação. |
| 3 | Confirmar cobertura efetiva do aluguel, exceções aos sete dias e situação da lacuna de 13 a 18/11 citada em perguntas.md. | Definir custos registrados e provisão sem duplicação. |
| 4 | Definir a classificação de despesas de trabalho e a precedência entre categoria, uso declarado e percentual profissional em despesas mistas. | Calcular custos e selecionar a base pública corretamente. |
| 5 | Definir tratamento de pausas/km pessoais ausentes, turnos que cruzam meia-noite, dia de atribuição e limites do recorte para saltos de odômetro. | Evitar distorções de horas e quilômetros. |
| 6 | Confirmar o fuso de origem, a procedência de carimbos manuais e o horário efetivo da exportação. | Interpretar datas e rastrear a versão dos arquivos. |
