# Sistema fuzzy de frenagem em malha fechada

Simulação de um controlador fuzzy para definir a intensidade de frenagem de um veículo a partir da distância até o obstáculo, da velocidade e da condição de pista molhada.

## Objetivo

Aplicar lógica fuzzy a um problema de controle em que as decisões não precisam ser apenas “frear” ou “não frear”. O controlador combina variáveis linguísticas e regras para produzir uma força de frenagem gradual.

## Como funciona

- **Entradas:** distância, velocidade e nível de pista molhada.
- **Saída:** intensidade de frenagem em porcentagem.
- **Controlador:** regras fuzzy implementadas com `scikit-fuzzy`.
- **Planta:** modelo simplificado de um veículo em malha fechada.
- **Avaliação:** simulação e gráficos de diferentes cenários de risco.

O notebook também registra uma limitação importante: em um cenário extremo, o conjunto de regras demora a reagir. Essa análise ajuda a identificar onde o controlador precisa ser ajustado antes de qualquer uso real.

## Tecnologias

- Python
- Jupyter Notebook
- NumPy
- Matplotlib
- scikit-fuzzy

## Como executar

```bash
pip install numpy matplotlib scikit-fuzzy jupyter
cd "sistema fuzzy malha fechada/sistema fuzzy malha fechada"
jupyter notebook sistema_fuzzy_malha_fechada.ipynb
```

## Estrutura

```text
sistema fuzzy malha fechada/
└── sistema fuzzy malha fechada/
    └── sistema_fuzzy_malha_fechada.ipynb
```

## Observação

Este projeto é uma simulação acadêmica. O modelo não deve ser usado para controlar veículos reais sem validação, testes de segurança e uma modelagem física adequada.
