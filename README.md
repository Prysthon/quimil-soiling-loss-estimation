# Quimil — preparação de dados para ML

Este projeto prepara as medições do inversor Quimil e da estação LABCHECAP para estimar
potência de referência após limpezas. A entrega é uma tabela de **7.786 linhas e 18
colunas, sem nulos**. A equipe de ML fica responsável pela modelagem.

Todo o código de preparação está nos dois notebooks, com evidências, gráficos, motivos
dos tratamentos e conferências. Leia também o [resumo dos tratamentos](docs/tratamentos_dados.md).

## Executar

Use Python 3.12 e uv:

```bash
uv sync --locked
uv run jupyter lab
```

Selecione o Python de `.venv` como kernel. Os arquivos originais devem estar em `data/raw/`:

- `Quimil-inversor-2025.csv`
- `Quimil-inversor-2026.csv`
- `LABCHECAP-2025.xlsx`
- `LABCHECAP-2026.xlsx`

Execute as células, na ordem, primeiro no
[notebook 01](notebooks/01-data-profiling-eda.ipynb) e depois no
[notebook 02](notebooks/02-gold-abt.ipynb).

O **01** limpa as colunas, documenta a correção do vento e grava as duas fontes separadas
em `data/silver/`. O **02** pareia os horários, aplica o corte operacional, seleciona
blocos completos de 30 minutos com GHI ≥ 200 W/m² e acrescenta as janelas pós-limpeza.

## Tabela final

Disponível em [CSV](data/gold/abt_30min.csv) e [Parquet](data/gold/abt_30min.parquet).
Os preditores são **`ghi` e `temp_amb`**; o alvo é **`total_active_power`**, em W.
As demais colunas são controles. As médias de potência não representam energia, e as
janelas pós-limpeza indicam períodos candidatos a limpos.

Os arquivos recebidos são preservados. Não há preenchimento de lacunas ou conversão de fuso.
