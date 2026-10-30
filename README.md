# Controlador fuzzy de frenagem em malha fechada

Simulação de um sistema de controle que calcula a intensidade de frenagem de um veículo a partir de três sinais: **distância até o obstáculo**, **velocidade** e **nível de pista molhada**. O controlador lê o estado atual da planta, aplica regras fuzzy e devolve uma frenagem entre 0% e 100%; esse ciclo se repete até o veículo parar ou a simulação terminar.

O projeto é um estudo de lógica fuzzy e controle em malha fechada. Ele não representa um sistema automotivo certificado.

## Modelo do controlador

### Entradas e saída

| Variável | Faixa simulada | Termos linguísticos |
|---|---:|---|
| `distancia` | 0–100 m | `perto`, `medio`, `longe` |
| `velocidade` | 0–120 km/h | `baixa`, `media`, `alta` |
| `molhado` | 0–1 | `seco`, `pouco_molhado`, `molhado` |
| `frenagem` | 0–100% | `nenhuma`, `suave`, `moderada`, `forte` |

As funções de pertinência são triangulares/trapezoidais e foram definidas com `scikit-fuzzy`. O notebook gera seus gráficos antes das simulações para tornar visíveis as regiões de sobreposição entre os termos.

### Regras

O conjunto possui `3 × 3 × 3 = 27` regras. A intensidade base depende da distância e da velocidade:

- quanto menor a distância, maior a frenagem;
- quanto maior a velocidade, maior a frenagem;
- pista pouco molhada aumenta o nível em uma unidade;
- pista molhada aumenta o nível em duas unidades;
- o nível máximo é limitado a `forte`.

Essa penalização progressiva é uma forma simples de representar a perda de aderência da pista.

## Planta veicular simplificada

A classe `PlantaVeicular` não é um simulador físico completo. A cada passo de `0,1 s`, ela:

1. converte a velocidade de km/h para m/s;
2. estima o atrito máximo por `0.8 × (1 − 0.5 × molhado)`;
3. transforma o percentual de frenagem em desaceleração;
4. atualiza velocidade e distância usando integração de Euler;
5. impede que velocidade ou distância fiquem negativas.

O controlador usa a distância, velocidade e condição de pista atuais como sensores. O sinal calculado retorna para a planta, caracterizando a malha fechada.

## Cenários registrados no notebook

| Cenário | Condição inicial | Resultado observado | Leitura correta |
|---|---|---|---|
| 1 — risco alto seco | 100 km/h, 50 m, `molhado=0` | Parada em cerca de 2,2 s, com distância e velocidade zeradas. | A regra reage progressivamente no modelo seco. |
| 2 — risco médio molhado | 60 km/h, 50 m, `molhado=1` | Parada antes do obstáculo; em uma execução registrada restaram 8,32 m. | O atrito menor aumenta o tempo de parada. |
| 3 — risco extremo | 120 km/h, 30 m, `molhado=1` | A distância chega a zero em cerca de 0,9 s. | Falha do controlador: o clamp da planta zera a velocidade quando a distância acaba e mascara uma colisão que ocorreria no mundo real. |
| 4 — risco intermediário | 80 km/h, 50 m, `molhado=0,5` | Chega ao limite de distância no tempo máximo de 4 s. | A parada ocorre sem margem robusta de segurança no modelo atual. |

Os valores podem variar conforme o estado do notebook e a forma de reexecução. O cenário 3 é especialmente importante: ele mostra por que regras fuzzy precisam ser validadas com requisitos de segurança, saturações e modelos físicos adequados.

## Tecnologias

- Python
- Jupyter Notebook
- NumPy
- Matplotlib
- SciPy e NetworkX, usados pelas dependências do `scikit-fuzzy`
- `scikit-fuzzy`

## Como executar

Na raiz do repositório:

```powershell
py -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install numpy matplotlib scipy networkx scikit-fuzzy jupyter
jupyter notebook "sistema fuzzy malha fechada/sistema fuzzy malha fechada/sistema_fuzzy_malha_fechada.ipynb"
```

Execute as células na ordem. O notebook plota as funções de pertinência e, em seguida, as curvas de distância, velocidade e sinal de frenagem para cada cenário.

## Estrutura

```text
sistema fuzzy malha fechada/
├── README.md
└── sistema fuzzy malha fechada/
    └── sistema_fuzzy_malha_fechada.ipynb
```

## Limitações e próximos passos

- O modelo não inclui tempo de reação, massa do veículo, ABS, inclinação da pista, distribuição de peso ou dinâmica real dos pneus.
- O obstáculo é representado apenas por uma distância escalar.
- Quando a distância chega a zero, a planta força a velocidade para zero; isso é conveniente para a animação, mas não é uma modelagem de colisão.
- Uma exceção no cálculo fuzzy atualmente aplica frenagem mínima, comportamento que precisaria de uma estratégia segura de fallback.
- As funções de pertinência e as regras foram definidas manualmente e devem ser calibradas com cenários de teste mais amplos.

Este projeto é exclusivamente acadêmico e não deve controlar veículos reais.
