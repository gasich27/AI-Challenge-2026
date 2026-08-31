# AI Challenge 2026 (Qualification) — Retention Prediction

Классификация пользователей по признаку retention на основе агрегированной статистики сессий и покупок.

## Стек

![Python](https://img.shields.io/badge/Python-3.11-blue)
![pandas](https://img.shields.io/badge/pandas-data-150458)
![scikit--learn](https://img.shields.io/badge/scikit--learn-ML-orange)
![CatBoost](https://img.shields.io/badge/CatBoost-model-green)

---
---

## Задача

Для каждого пользователя нужно предсказать вероятность целевого класса `retention`. Основная метрика — ROC-AUC.

---
---

## Данные

Используются признаки количества и длительности сессий, давности последней активности, покупок, средней стоимости покупки, числа активных дней, разброса длительности сессий и активности в выходные.

Исходные `train.csv`, `test.csv` и `sample_submission.csv` находятся локально в `data/` и не добавляются в Git.

---
---

## Валидация

Основная схема — Stratified 5-fold CV. Для проверки устойчивости финального решения использован repeated CV на нескольких `random_seed`. Метрика во всех сравнениях — ROC-AUC.

---
---

## Эксперименты

Проверялись Logistic Regression, Decision Tree, Gradient Boosting, Random Forest, CatBoost, XGBoost, ExtraTrees, преобразования признаков и blending. Логарифмирование, ratios и дополнительные производные признаки не дали устойчивого прироста и не вошли в финальный набор.

---
---

## Финальное решение

Используется ensemble из трёх моделей:

- CatBoost — 50%;
- Random Forest — 20%;
- ExtraTrees — 30%.

CatBoost: `iterations=500`, `depth=4`, `learning_rate=0.03`, `l2_leaf_reg=3`, `random_strength=1`. Для четырёх дискретных признаков добавляются категориальные копии: `sessions_count`, `purchases_count`, `active_days`, `is_weekend_user`.

ExtraTrees: `n_estimators=800`, `max_depth=11`, `min_samples_leaf=5`, `max_features=1.0`.

Для Random Forest используется дополнительный признак `is_one_session = sessions_count == 1`.

---
---

## Результат

Repeated OOF ROC-AUC: `0.676410 ± 0.001102` (min `0.674597`).

Итоговый результат на test: `0.6771335516287832`.

---
---

## Структура проекта

```text
ai-challenge-2026/
├── notebooks/
│   ├── ai_challenge.ipynb
│   └── archive/
├── data/                  # локальные данные, не коммитятся
├── results/               # локальные submissions, не коммитятся
├── README.md
├── requirements.txt
└── .gitignore
```

---
---

## Запуск

```bash
pip install -r requirements.txt
jupyter notebook
```

Открыть `notebooks/ai_challenge.ipynb` и выполнить ячейки последовательно.

---
---
---

![alt text](<Frame 765.png>)
