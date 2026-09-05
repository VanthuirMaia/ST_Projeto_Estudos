# Séries Temporais — Setup de Experimento

Estudos práticos da disciplina **Previsão de Séries Temporais**, focados em reproduzir o pipeline experimental apresentado em aula: manipulação de dados temporais com pandas, implementação de métricas de avaliação e comparação de estratégias de previsão com ARIMA.

## Contexto

Baseado no material `3-setup_de_experimento.pptx` do Prof. Domingos Santos (UPE), este projeto implementa passo a passo:

1. Carregamento e análise exploratória de série temporal
2. Diferenciação para tornar a série estacionária
3. Criação de janelas temporais (lags) para transformar série em problema supervisionado
4. Implementação de 7 métricas de desempenho (MSE, RMSE, MAE, MAPE, POCID, ARV, U-Theil)
5. Comparação entre estratégias de previsão multi-step com ARIMA

**Dataset:** `AirPassengers` (144 observações mensais, 1949–1960)

## Estrutura

```
pandaseerros/
├── .venv/                 # ambiente virtual Python
├── .gitignore
├── aula04.ipynb           # notebook principal — pipeline completo
├── datas.ipynb            # exercícios de manipulação temporal com pandas
├── README.md
└── requirements.txt
```

## Setup

```bash
# criar e ativar ambiente virtual
python -m venv .venv
source .venv/bin/activate    # Linux/Mac
.venv\Scripts\activate       # Windows

# instalar dependências
pip install -r requirements.txt
```

**Dependências principais:**

- `pandas`, `numpy`, `matplotlib` — manipulação e visualização
- `scikit-learn` — normalização e métricas base
- `statsmodels` — dataset AirPassengers e ARIMA
- `pmdarima` — auto-ARIMA (seleção automática de ordem)

## O que foi implementado

### Bloco 1 — Análise exploratória

- Carregamento do dataset via `statsmodels`
- Conversão de índice temporal
- Diagnóstico visual: tendência crescente + sazonalidade multiplicativa

### Bloco 2 — Métricas

Implementação manual das 7 métricas do slide 16, incluindo as específicas de séries temporais:

- **POCID** — acerto de direção do movimento
- **ARV** — comparação com previsão pela média
- **U-Theil** — comparação com previsão naive

Validação empírica com 3 cenários sintéticos (previsão perfeita, média, naive).

### Bloco 3 — Preparação para ML

- Função `create_windowing()` para criar lags automaticamente
- Split holdout respeitando ordem temporal
- Normalização com `MinMaxScaler` (fit apenas no treino)

### Bloco 4 — ARIMA

Comparação entre duas estratégias de previsão multi-step:

| Estratégia   | MSE     | MAE   | MAPE   | POCID | ARV   | U-Theil |
| ------------ | ------- | ----- | ------ | ----- | ----- | ------- |
| ARIMA direct | 6700.95 | 60.71 | 13.11% | 59.26 | 1.086 | 1.618   |
| ARIMA 1-step | 2060.45 | 37.92 | 8.72%  | 62.96 | 0.334 | 0.866   |

**Descobertas principais:**

- Ordem selecionada por `auto_arima`: **(4, 1, 2)**
- ARIMA direct converge para a média após ~5 passos, perdendo toda sazonalidade
- ARIMA direct tem **ARV > 1 e U-Theil > 1** — é pior que prever a média/naive
- ARIMA 1-step ganha da naive por margem pequena (~13%), evidenciando limitação de ARIMA sem componente sazonal em séries sazonais

## Próximos passos

- [ ] Implementar MLP e SVR sobre série diferenciada (slide 36)
- [ ] Comparar 4 modelos ML × 2 ARIMAs em tabela consolidada (slide 37)
- [ ] Explorar transformação log para lidar com sazonalidade multiplicativa

## Referências

- Slide da aula: `3-setup_de_experimento.pptx` (Prof. Domingos Santos, UPE)
- SILVA, David A. et al. _Measurement of fitness function efficiency using data envelopment analysis_. Expert Systems with Applications, v. 41, n. 16, p. 7147-7160, 2014.
