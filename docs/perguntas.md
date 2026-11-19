# Perguntas de negócio — Rentabilidade como motorista de aplicativo

> Documento elaborado **antes** da análise específica das perguntas abaixo, após uma
> exploração inicial e uma auditoria dos dados.
>
> **Transparência:** indicadores gerais de receita, horas, quilômetros e despesas já
> haviam sido consultados. As **hipóteses originais** de cada pergunta foram escritas a
> partir da minha experiência como motorista. Os **cenários de planejamento** (seção 5)
> foram calibrados com o histórico e não são hipóteses formuladas sem conhecimento prévio.
>
> As respostas e as conclusões finais ficam no README, não aqui.

---

## 1. Contexto

Trabalho como motorista de Uber e 99 no Rio de Janeiro, com um Sandero alugado. O aluguel
habitual é de **R$ 600 por semana**, com descontos excepcionais (por exemplo, dias em que o
carro ficou na oficina).

Minha rotina normalmente tem dois turnos: **manhã (~05h–11h)** e **tarde/noite (~16h–21h)**.
Os aplicativos mostram quanto eu **faturo**, mas não descontam combustível nem aluguel.

Este projeto usa registros da minha própria operação para estimar **quanto sobra depois dos
custos de trabalho** e como esse resultado varia entre turnos, plataformas e semanas.

## 2. Objetivo e métrica principal

**Objetivo:** estimar o resultado operacional por hora trabalhada e identificar oportunidades
de melhorar a relação entre tempo trabalhado, faturamento e custos.

**Métrica principal (North Star):** **resultado operacional estimado por hora trabalhada.**

Neste projeto, "lucro" é usado como abreviação desse resultado:

```text
Resultado operacional estimado = receita total − custos de trabalho considerados
Resultado por hora             = resultado operacional estimado ÷ horas trabalhadas
```

O resultado é calculado **antes** dos gastos pessoais e dos pagamentos de dívidas e não
representa, necessariamente, o saldo disponível nas contas bancárias.

## 3. Escopo da primeira versão (v1)

| Item | Definição |
|---|---|
| Período | 29/10/2026 a 18/11/2026 (21 dias de calendário) |
| Local | Rio de Janeiro e deslocamentos registrados na operação |
| Fontes | Google Forms de turnos e de despesas, vinculados ao Google Sheets |
| Formato de análise | Exportações em CSV |
| Unidade principal | Um turno de trabalho |
| Base no recorte | 28 turnos, distribuídos em 18 dias com trabalho registrado |
| Despesas consideradas | Todas as classificadas como trabalho: combustível, aluguel, pedágio, lavagem, manutenção, equipamentos e demais categorias operacionais |
| Fora do resultado | Gastos pessoais, amortização de dívidas e transferências entre contas |
| Semana de comparação | Segunda a domingo, identificando semanas incompletas |

As semanas de início e de fim do recorte são **parciais**. Seus totais não serão comparados
diretamente com os de semanas completas sem destacar essa diferença.

## 4. Definições das métricas

| Métrica | Definição |
|---|---|
| Receita dos aplicativos | Parte do motorista mostrada pela Uber e pela 99, após a taxa da plataforma, incluindo bônus e gorjetas já incorporados nesses valores |
| Extras externos | Gorjetas ou outros extras registrados fora dos totais dos apps; apresentados separadamente |
| Receita total | Receita dos aplicativos + extras externos, sem duplicação |
| Horas trabalhadas | Diferença entre início e fim do turno, descontando pausas pessoais quando informadas |
| Intervalo entre turnos | Não integra as horas trabalhadas |
| KM em turno | Odômetro final − inicial, descontando km pessoais quando informados |
| KM fora de turno | Salto positivo entre o odômetro final de um turno e o inicial do turno seguinte |
| KM total observado | KM em turno + KM fora de turno |
| Custo de combustível | Valor dos abastecimentos registrados no período (aproxima o custo, mas não mede exatamente o combustível consumido) |
| Aluguel rateado | Valor efetivo do aluguel dividido pelos dias cobertos, incluindo folgas |
| Custo total | Combustível + aluguel rateado + demais despesas de trabalho |
| Resultado operacional estimado | Receita total − custo total |
| Resultado por hora | Resultado do período ÷ horas trabalhadas no período |
| Margem operacional | Resultado operacional estimado ÷ receita total |
| Receita por hora | Receita total ÷ horas trabalhadas |
| Ticket médio por plataforma | Receita da plataforma ÷ corridas da plataforma |
| Corridas por hora | Total de corridas ÷ horas trabalhadas |
| Receita por km em turno | Receita total ÷ KM em turno |
| Custo operacional por km | Custo total ÷ KM total observado |
| Preço médio do combustível | Soma dos valores abastecidos ÷ soma dos litros comprados |

**Regra de agregação:** indicadores como R$/hora e R$/km são calculados pela **divisão dos
totais** do período. Não se usa a média simples dos indicadores de cada turno.

### 4.1. Tratamento do aluguel

- O aluguel habitual equivale a 600 ÷ 7 por dia (≈ R$ 85,71). O cálculo mantém a precisão
  completa e arredonda somente a apresentação.
- Pagamentos com desconto usam o valor **efetivamente registrado**, não R$ 600 automaticamente.
- Pela regra atual, cada pagamento cobre os **sete dias anteriores** à data do pagamento.
  No recorte da v1, há uma **lacuna de cobertura entre 13 e 18/11**.

Enquanto o pagamento correspondente não estiver registrado, serão apresentados:

- **Resultado com custos registrados:** considera apenas os períodos cobertos pelos
  lançamentos existentes.
- **Resultado ajustado com provisão:** acrescenta o aluguel estimado dos dias sem cobertura,
  identificado separadamente.

A provisão será substituída pelo valor efetivo quando confirmado, sem contar o mesmo aluguel
duas vezes.

### 4.2. Tratamento dos custos por turno

Os custos não são medidos diretamente em cada turno. Para permitir a análise:

- O combustível comprado no período é distribuído **proporcionalmente aos km em turno**.
- O aluguel e os demais custos de trabalho são distribuídos **proporcionalmente às horas
  trabalhadas**.
- Os custos de folgas e de deslocamentos fora dos turnos continuam incluídos no total distribuído.

Esses valores são **rateios analíticos**, não medições exatas do custo de cada turno. Por isso,
a comparação principal entre manhã e tarde usa receita por hora, ticket médio e corridas por hora.

---

## 5. Referências iniciais para planejamento

Ainda não defini uma meta pessoal definitiva de lucro. Os valores abaixo são **cenários** para
avaliação, calibrados com o histórico disponível e com a provisão do aluguel pendente. Como
referência preliminar, o resultado ajustado fica **próximo de R$ 15 por hora**.

| Cenário | Resultado semanal desejado | Horas aproximadas no modelo inicial |
|---|---|---|
| Menor volume de trabalho | R$ 400 | 35 h |
| Cenário-base proposto | R$ 600 | 42 h |
| Cenário de avanço | R$ 800 | 49 h |

- As horas são **estimativas condicionais**: pressupõem desempenho e custos semelhantes aos observados.
- As duas semanas completas registradas tiveram aproximadamente **39 h e 51 h** de trabalho.
  Os cenários-base e de avanço estão próximos de jornadas já observadas, mas isso não garante
  que o resultado financeiro se repita.
- Não será fixada uma meta diária única nesta versão. Como exemplo, R$ 600 distribuídos em
  seis dias de trabalho equivalem a R$ 100 por dia trabalhado, mas a avaliação principal
  continua **semanal**, incluindo os custos das folgas.

---

## 6. Perguntas de negócio

### P1. Quanto sobra por hora de trabalho depois dos custos operacionais?

- **Tipo:** descritiva.
- **Hipótese original:** acho que **faturo entre R$ 30 e R$ 40 por hora**. Considero R$ 30/h o
  mínimo para valer a pena rodar, evito corridas abaixo de R$ 1,50/km e acho difícil manter mais
  de R$ 40/h todos os dias no UberX. Como acredito que mais da metade do faturamento vai para
  combustível e aluguel (P2), o **resultado deve ficar abaixo de R$ 20 por hora**.
- **Expectativa após a exploração inicial:** há uma diferença relevante entre faturamento por hora
  e resultado por hora. O resultado ajustado parece inferior a R$ 20/h, mas sua estabilidade
  entre semanas ainda precisa ser avaliada.
- **Decisão apoiada:** compreender a remuneração efetiva do trabalho e estabelecer uma referência
  para planejamento.
- **Como medir:**
  - Receita por hora.
  - Resultado operacional estimado por hora.
  - Margem operacional.
  - Diferença entre o resultado com custos registrados e o resultado ajustado com provisão.
- **Cortes:** período completo e semanas, identificando recortes parciais.
- **Visual previsto:** cartões de indicadores e comparação semanal entre receita/h e resultado/h.

### P2. Quais custos mais reduzem o resultado?

- **Tipo:** descritiva e diagnóstica.
- **Hipótese original:** acho que **mais da metade** do faturamento vai para despesas:
  - **Combustível: cerca de 25%**, pela minha conta mental de que, a cada R$ 50 abastecidos,
    faturo uns R$ 200.
  - **Aluguel: perto de 30%**, já que ≈ R$ 86 por dia equivalem a cerca de 30% de um dia de
    faturamento de uns R$ 290. Em dias mais fracos, o peso do aluguel é maior.
- **Decisão apoiada:** identificar quais custos merecem investigação e acompanhamento prioritários
  (posto, forma de dirigir, contrato de aluguel).
- **Como medir:**
  - Participação de combustível, aluguel e demais custos na receita.
  - Custo operacional por km total observado.
  - Custo de combustível por km total observado.
  - Preço médio ponderado do combustível por estabelecimento.
- **Cortes:** categoria, semana e posto, quando identificado.
- **Visual previsto:** gráfico de cascata (receita → custos → resultado) e barras por categoria.
- **Limitação:** diferenças de gasto com combustível entre semanas podem refletir mudanças no
  estoque do tanque, não apenas no consumo.

### P3. Manhã ou tarde/noite: qual turno tem maior receita por hora?

- **Tipo:** comparativa e diagnóstica.
- **Hipótese original:** acredito fortemente que a **manhã rende mais por hora**, com diferença
  acima de 10%.
  - À noite há mais tarifa dinâmica, então espero **ticket médio maior à tarde/noite**.
  - Porém o trânsito é bem mais pesado, então espero **mais corridas por hora de manhã**, e acho
    que esse efeito é maior que o do dinâmico.
  - Em alguns dias perco mais de 1 hora esperando uma corrida saindo da Barra para a Zona Sul,
    o que derruba o ganho do turno.
- **Decisão apoiada:** qual turno priorizar quando só houver disponibilidade para um deles.
- **Como medir:**
  - Receita por hora.
  - Receita por km em turno.
  - Corridas por hora.
  - Ticket médio.
- **Decomposição** (com base de receita consistente; extras externos apresentados à parte):

  ```text
  Receita das corridas por hora = corridas por hora × receita por corrida
  ```

- **Cortes:** turno calculado pela hora de início (antes de 12h = manhã; a partir de 12h =
  tarde/noite). Sempre que possível, a comparação também será feita apenas entre dias com os
  dois turnos registrados.
- **Critério de relevância prática:** diferença inferior a 10% é tratada como pequena para fins
  de decisão, sem significar equivalência estatística.
- **Visual previsto:** barras comparativas, acompanhadas da quantidade de turnos e de horas observadas.
- **Limitação:** trânsito, tarifa dinâmica e espera podem explicar diferenças, mas não são
  medidos diretamente nesta base. Não será possível atribuir causalidade a esses fatores.

### P4. Como Uber e 99 contribuem para o faturamento?

- **Tipo:** descritiva e comparativa.
- **Hipótese original:** acho que **nenhum dos dois apps domina**. Em alguns dias a 99 parece
  pagar muito mais e, em outros, a Uber. Por isso, acredito que manter os dois ligados é melhor
  do que priorizar só um. Uma participação maior no total não significa, por si só, que uma
  plataforma seja sempre mais vantajosa.
- **Decisão apoiada:** entender a dependência de cada plataforma e identificar padrões que
  mereçam acompanhamento.
- **Como medir:**
  - Participação na receita dos aplicativos.
  - Participação na quantidade de corridas.
  - Ticket médio por plataforma.
  - Contribuição de cada plataforma por hora de turno.
  - Variação da participação entre turnos.
- **Cortes:** plataforma e turno.
- **Visual previsto:** barras empilhadas e tabela comparativa.
- **Limitações:**
  - Os apps operam simultaneamente; não há horas exclusivas de Uber ou de 99.
  - A base registra corridas realizadas, não todas as ofertas recebidas.
  - Não é possível demonstrar quanto eu teria ganhado usando apenas uma plataforma.

### P5. Quantas horas seriam necessárias para atingir diferentes resultados semanais?

- **Tipo:** simulação de cenários para planejamento (prescritiva).
- **Ponto de partida:** trabalho quase todos os dias. Folgo quando estou muito cansado e, nesses
  casos, pulo um turno ou folgo no domingo, que é mais parado. Ainda não tenho uma meta
  definitiva: o objetivo é avaliar resultados semanais de **R$ 400, R$ 600 e R$ 800** (seção 5)
  e verificar sua compatibilidade com a jornada observada.
- **Decisão apoiada:** escolher uma combinação de tempo de trabalho e resultado desejado.
- **Modelo inicial:**

  ```text
  Contribuição por hora  = receita por hora − combustível por hora − demais custos (exceto aluguel) por hora
  Horas para o aluguel   = aluguel semanal ÷ contribuição por hora
  Horas para a meta      = (aluguel semanal + resultado semanal desejado) ÷ contribuição por hora
  ```

  Os demais custos são tratados como uma parcela média por hora. É uma simplificação:
  equipamentos e despesas pontuais não crescem necessariamente com a jornada.
- **Cenários:** desempenho médio observado, semanas de menor desempenho e semanas de maior
  desempenho. Cenários exclusivos de manhã ou de tarde serão exploratórios, pois trabalhar mais
  horas nesses períodos pode não reproduzir a produtividade média atual.
- **Visual previsto:** tabela de cenários e parâmetro interativo de resultado semanal desejado.
- **Limitações:**
  - A projeção não garante disponibilidade de corridas.
  - Mais horas podem significar horários menos rentáveis.
  - A distribuição do custo fixo faz o resultado por hora variar com a jornada.
  - Se a contribuição por hora for zero ou negativa, o modelo não apresenta quantidade viável de horas.
  - A estimativa não será interpretada como recomendação de aumentar a jornada sem considerar
    descanso e disponibilidade pessoal.

---

## 7. Limitações conhecidas

- **Amostra curta:** 21 dias de calendário, com 28 turnos registrados.
- **Somente duas semanas completas:** insuficiente para conclusões estáveis sobre sazonalidade ou previsão.
- **Dados autodeclarados:** sujeitos a erros e omissões (tratados no log de limpeza).
- **Ausência de registro não significa folga:** é preciso distinguir dia não trabalhado de turno não lançado.
- **Horários aproximados:** alguns registros históricos podem conter estimativas.
- **Combustível comprado ≠ combustível consumido:** o estoque inicial e final do tanque é desconhecido.
- **Aluguel pendente:** exige provisão explícita para não superestimar o resultado.
- **KM fora de turno:** conhecidos pela diferença de odômetro, sem identificação precisa do motivo ou do dia.
- **KM com passageiro:** não separados nesta base.
- **Custos por turno rateados:** dependem de convenções de distribuição.
- **Ofertas e recusas não registradas:** impedem avaliar diretamente as decisões de aceitação de corridas.
- **Resultado não é saldo bancário:** transferências, gastos pessoais e dívidas ficam fora da métrica principal.
- **Sem comparação causal:** associações observadas não demonstram que mudar de turno ou de
  plataforma produzirá o mesmo resultado.

## 8. Perguntas para próximas versões (v2+)

- Como a receita por hora varia conforme o clima?
- Quais dias da semana apresentam resultados mais consistentes?
- Como a participação dos apps varia entre dias e turnos?
- A receita por hora está melhorando ou piorando ao longo do tempo?
- Quanto tempo é gasto esperando corridas (ex.: saída da Barra para a Zona Sul), e em qual turno?
- Qual a proporção de quilômetros com e sem passageiro?
- Como medir o consumo real (km/L) com controle adequado do combustível no tanque?
- A regra de recusar corridas abaixo de R$ 1,50/km está sendo cumprida, e ela compensa?
- Como custos pontuais (equipamentos, manutenção) devem ser distribuídos entre períodos?
- Uma meta semanal escolhida pode ser atingida de forma consistente durante várias semanas?

## 9. Critérios de conclusão da v1

A primeira versão será considerada concluída quando:

1. As regras de limpeza e os dados pendentes estiverem documentados.
2. O aluguel estiver coberto por pagamentos confirmados ou provisões identificadas.
3. Os totais de receita e custos estiverem conciliados com as fontes.
4. As cinco perguntas tiverem respostas ou limitações explicitamente apresentadas.
5. Os cenários separarem dados observados de suposições.
6. O README apresentar conclusões proporcionais à amostra, sem promessas de ganhos.
7. Os arquivos publicados estiverem livres de dados pessoais e bancários.
