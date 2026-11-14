# Tratamento dos dados — Quimil

Preparamos os dados para estimar potência de referência após limpezas e compará-la com
a observada nas mesmas condições ambientais. Entregamos **7.786 linhas, 18 colunas e
nenhum nulo** para ML.

Cada tratamento segue **evidência → motivo → aplicação → conferência** nos notebooks.
Os arquivos recebidos permanecem originais em `data/raw/`.

## O que fizemos e por quê

| Tema | O que observamos e fizemos |
|---|---|
| Datas | Concatenamos os anos, convertemos datas e ordenamos registros: **zero datas inválidas e zero timestamps duplicados**. O fuso não foi informado; preservamos horários sem conversão. |
| Colunas vazias | Removemos **colunas 100% nulas**, textos e identificadores, incluindo `ps_id`, sem descartar linhas. Depois, as duas fontes ficaram sem nulos. |
| Vento | Corrigimos **20.513 negativos para zero**: 20.459 em −0,018 e 54 em −0,036 m/s. Velocidade escalar não deve ser negativa; a correção é operacional, sem confirmação da causa no sensor. |
| Zeros de vento | Passaram de **39,12% para 50,38%**. Zero é medição, não ausência de dado. O vento permanece na silver, fora dos preditores de ML. |
| Pareamento | Combinamos potência e ambiente nos **81.859 horários exatamente iguais**, antes das médias. Ignorar segundos não acrescentou pares. As 945 leituras do inversor sem par permanecem somente na silver. |
| Horários com segundos | Há **32 registros meteorológicos fora da grade de cinco minutos**, todos sem par. Preservamos os horários originais, sem arredondar. |
| Corte operacional | Retiramos **3.112 pares a partir de 12/08/2026 às 11:35**, conforme acordado. A causa operacional é desconhecida; o histórico permanece na silver. |
| Lacunas | Exigimos **seis pares completos de cinco minutos** por bloco datado de 30 minutos. Excluímos **935 blocos incompletos**, sem interpolar ou preencher lacunas. |
| Irradiância | A GHI mínima é **0,74666 W/m²**, sem zero exato; valores positivos aparecem à noite. Mantivemos **GHI ≥ 200 W/m² em cada um dos seis pontos**, conforme aprovado, retirando 4.838 blocos completos de baixa irradiância. |
| Potência e agregação | Preservamos zeros e extremos positivos. Calculamos a **média, não a soma**, de potência, GHI e temperatura nos mesmos seis instantes. Potência continua em W; não calculamos energia. |
| Limpezas | Marcamos janelas candidatas de **7, 15 e 30 dias**, do dia seguinte até antes da próxima limpeza, sem compensar dias ausentes. |

## Entrega para ML

A silver conserva **82.804 × 15** do inversor e **182.129 × 5** da meteorologia.
Na tabela final, os preditores são **`ghi` e `temp_amb`**; o alvo é **`total_active_power`**.
As outras 15 colunas são controles. As janelas de 7, 15 e 30 dias sinalizam,
respectivamente, **520, 1.113 e 2.052 blocos candidatos**.

Pós-limpeza é uma hipótese; fora da janela não significa “sujo”. A seleção não
representa a geração diária completa.

## Para um slide

- Removemos colunas vazias e identificadores; corrigimos os pequenos negativos conhecidos do vento.
- Pareamos os mesmos horários e aplicamos o corte operacional, preservando zeros de geração.
- Selecionamos blocos completos de 30 minutos com GHI ≥ 200, sem preencher lacunas.
- Entregamos **7.786 blocos sem nulos**, com ambiente, potência e indicadores pós-limpeza.
