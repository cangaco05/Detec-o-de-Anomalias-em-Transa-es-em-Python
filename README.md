# 🔍 Detecção de Anomalias em Transações Financeiras

> Projeto desenvolvido como parte do **Bootcamp Afya – Automação de Dados com IA** na [DIO](https://dio.me)  
> Detector de transações suspeitas inspirado nos sistemas antifraude usados por fintechs como o Nubank.

![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white)
![scikit-learn](https://img.shields.io/badge/scikit--learn-1.5.1-F7931E?style=for-the-badge&logo=scikitlearn&logoColor=white)
![Pandas](https://img.shields.io/badge/Pandas-2.2.2-150458?style=for-the-badge&logo=pandas&logoColor=white)
![pytest](https://img.shields.io/badge/pytest-8.2.2-0A9EDC?style=for-the-badge&logo=pytest&logoColor=white)
![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)

---

## 📌 Objetivo

Identificar **padrões anômalos em transações financeiras** usando Machine Learning não-supervisionado, sem a necessidade de dados rotulados previamente. O modelo aprende o comportamento normal das transações e sinaliza desvios que podem indicar fraude.

---

## 🧠 Como funciona

O projeto utiliza o algoritmo **Isolation Forest**, que parte de um princípio simples:

> *Pontos anômalos são mais fáceis de isolar do que pontos normais.*

O algoritmo cria múltiplas árvores de decisão com cortes aleatórios. Transações que precisam de **poucos cortes** para ficarem isoladas recebem um score alto de anomalia — pois estão distantes do padrão esperado.

```
Transação normal  → precisa de muitos cortes → score baixo  ✅
Transação suspeita → isolada rapidamente      → score alto   ⚠️
```

**Parâmetros utilizados:**

| Parâmetro | Valor | Descrição |
|-----------|-------|-----------|
| `n_estimators` | 300 | Número de árvores |
| `contamination` | 0.02 | Taxa esperada de anomalias (2%) |
| `random_state` | 42 | Reprodutibilidade |

---

## 🗂️ Estrutura do Projeto

```
deteccao-anomalias-transacoes/
├── data/
│   ├── raw/
│   │   └── transacoes.csv          # Dados de entrada
│   └── processed/
│       └── transacoes_com_score.csv # Saída com scores
├── models/
│   └── isolation_forest.pkl        # Modelo serializado
├── notebooks/
│   └── 01_eda.ipynb                # Análise exploratória
├── src/
│   ├── __init__.py
│   ├── data_loader.py              # Carregamento e validação
│   ├── features.py                 # Engenharia de features
│   ├── train.py                    # Treinamento do modelo
│   └── predict.py                  # Inferência em novos dados
├── tests/
│   └── test_features.py            # Testes unitários
├── .gitignore
├── main.py                         # Ponto de entrada
├── requirements.txt
└── README.md
```

---

## 🚀 Como Executar

### 1. Clone o repositório

```bash
git clone https://github.com/SEU_USUARIO/deteccao-anomalias-transacoes.git
cd deteccao-anomalias-transacoes
```

### 2. Crie o ambiente virtual

```bash
python -m venv .venv

# Linux / macOS
source .venv/bin/activate

# Windows
.venv\Scripts\activate
```

### 3. Instale as dependências

```bash
pip install -r requirements.txt
```

### 4. Prepare os dados

Coloque seu arquivo CSV em `data/raw/transacoes.csv`. O arquivo deve conter as colunas:

| Coluna | Tipo | Descrição |
|--------|------|-----------|
| `valor` | numérico | Valor da transação |
| `hora` | inteiro (0–23) | Hora do dia |
| `pais` | string | País de origem |
| `canal` | string | Canal (app, web, etc.) |
| `cliente_id` | string/int | Identificador do cliente |
| `rotulo` *(opcional)* | 0 ou 1 | 0 = normal, 1 = fraude |

### 5. Execute o pipeline

```bash
python main.py
```

---

## 📊 Saídas Geradas

**Modelo treinado:**
```
models/isolation_forest.pkl
```

**CSV com scores:**
```
data/processed/transacoes_com_score.csv
```

Exemplo de saída:

| valor | hora | pais | canal | anomalia | score_anomalia |
|-------|------|------|-------|----------|----------------|
| 85.50 | 14 | BR | app | 0 | 0.082 |
| 9800.00 | 3 | US | web | 1 | -0.312 |
| 120.00 | 10 | BR | app | 0 | 0.091 |

> `anomalia = 1` indica transação suspeita. Quanto mais negativo o `score_anomalia`, maior a suspeita.

**Relatório de classificação** *(quando a coluna `rotulo` estiver presente):*

```
              precision    recall  f1-score   support

           0     0.9823    0.9701    0.9762      4900
           1     0.6140    0.7200    0.6629       100

    accuracy                         0.9624      5000
```

---

## ⚙️ Features Utilizadas

| Feature | Origem | Descrição |
|---------|--------|-----------|
| `valor_log` | Engenharia | Log do valor — reduz assimetria |
| `hora_noturna` | Engenharia | 1 se hora entre 0h e 5h |
| `pais_*` | One-hot | Codificação do país de origem |
| `canal_*` | One-hot | Codificação do canal de acesso |

---

## 🧪 Testes

```bash
pytest tests/ -v
```

```
tests/test_features.py::test_build_features_basic PASSED   ✅
```

---

## 🛠️ Stack

| Biblioteca | Versão | Uso |
|------------|--------|-----|
| `pandas` | 2.2.2 | Manipulação de dados |
| `numpy` | 1.26.4 | Operações numéricas |
| `scikit-learn` | 1.5.1 | Isolation Forest |
| `joblib` | 1.4.2 | Serialização do modelo |
| `matplotlib` | 3.9.0 | Visualizações |
| `seaborn` | 0.13.2 | Visualizações estatísticas |
| `pytest` | 8.2.2 | Testes unitários |

---

## 📈 Próximos Passos

- [ ] Adicionar Z-Score e IQR como camadas complementares de detecção
- [ ] Criar API REST com **FastAPI** para inferência em tempo real
- [ ] Dashboard interativo com **Streamlit**
- [ ] Testar com dataset público: [Credit Card Fraud Detection – Kaggle](https://www.kaggle.com/datasets/mlg-ulb/creditcardfraud)
- [ ] Adicionar explicabilidade com **SHAP values**
- [ ] Containerizar com **Docker**

---

## 📄 Licença

Distribuído sob a licença MIT. Veja `LICENSE` para mais informações.

---

## 👤 Autor

**Amaury Prates**  
[![LinkedIn](https://img.shields.io/badge/LinkedIn-0077B5?style=flat&logo=linkedin&logoColor=white)](https://www.linkedin.com/in/amaury-prates/)
[![GitHub](https://img.shields.io/badge/GitHub-181717?style=flat&logo=github&logoColor=white)](https://github.com/cangaco05)

---

> ⭐ Se este projeto te ajudou, deixe uma estrela no repositório!
