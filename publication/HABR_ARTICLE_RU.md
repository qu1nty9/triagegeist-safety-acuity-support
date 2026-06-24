# Как оценивать AI для emergency triage: не только accuracy, а риск опасной under-triage

## Коротко

Мы сделали reproducible ML-проект для emergency department triage на синтетических данных Triagegeist. Модель предсказывает acuity level 1-5, но главный фокус проекта не в том, чтобы получить красивую accuracy. Главный фокус: не пропускать пациентов с высокой срочностью, то есть true acuity 1-2.

Идея проекта:

> AI в triage должен быть не “автоматической заменой медсестры”, а safety layer, который подсвечивает пациентов, требующих немедленного review.

Поэтому мы отдельно считаем severe under-triage: случаи, когда true acuity 1-2 предсказаны как 3-5.

## Почему это важно

В emergency department triage решение принимается быстро, под нагрузкой и с неполной информацией. Ошибки здесь асимметричны.

Если система переоценила срочность, это неприятно: больше нагрузки, больше review, больше resource use.

Если система недооценила срочность, это опаснее: пациент, которому нужна срочная помощь, может ждать слишком долго.

Поэтому обычные ML-метрики вроде accuracy, macro-F1 и ROC-AUC полезны, но недостаточны. Для triage нужно отдельно смотреть safety-critical failure mode.

## Данные

Мы использовали синтетический Triagegeist dataset:

- 80,000 train records;
- 20,000 test records;
- vitals;
- demographics;
- structured intake fields;
- chief complaint text;
- comorbidity history flags;
- target: `triage_acuity` от 1 до 5.

Важно: raw dataset не публикуется в GitHub, потому что Kaggle terms запрещают redistributing outside Kaggle. В репозитории лежат notebook, агрегированные метрики, графики и writeup.

## Что было важно сделать правильно

### 1. Убрать leakage

В train есть `disposition` и `ed_los_hours`. Это post-triage outcomes: они появляются после оказания помощи, а не в момент triage.

Если использовать их как признаки, можно получить красивые метрики, но это будет неправильная модель. В реальном workflow этих признаков нет на входе.

Поэтому мы явно исключили:

- `disposition`;
- `ed_los_hours`.

### 2. Считать safety metric

Мы ввели метрику:

```text
severe under-triage = true acuity 1-2 predicted as 3-5
```

Именно это наиболее опасный класс ошибок.

### 3. Сделать high-risk alert

Вместо того чтобы говорить “модель сама ставит triage score”, мы сделали более реалистичный workflow:

```text
score = P(acuity <= 2)
if score >= threshold:
    flag patient for immediate clinician review
```

Так AI работает как safety net, а финальное решение остается за clinician.

## Модель

Мы использовали простой и воспроизводимый pipeline:

- numeric vitals: median imputation + missingness indicators;
- `pain_score == -1`: treat as missing;
- categorical fields: one-hot encoding;
- chief complaint text: TF-IDF unigrams/bigrams;
- clinical flags: hypotension, hypoxia, tachycardia, fever, altered mental status;
- classifier: `SGDClassifier(loss="log_loss", class_weight="balanced")`.

Почему не сложная нейросеть? Потому что для этого проекта важнее прозрачность, reproducibility и safety evaluation, а не архитектурная сложность.

## Результаты

Notebook полностью отработал в Kaggle за 3 минуты 27 секунд.

На fixed validation split full valid-feature model получил:

- accuracy: 0.9946;
- macro-F1: 0.9840;
- quadratic weighted kappa: 0.9975;
- severe under-triage: 0.0000.

5-fold CV:

- mean accuracy: 0.9813;
- mean macro-F1: 0.9564;
- mean QWK: 0.9918;
- mean severe under-triage: 0.00024.

Один fold был слабее остальных. Мы не стали это скрывать: наоборот, это важная часть честной оценки. Single split может переоценивать стабильность.

## High-risk alert

Самая важная практическая часть:

При threshold 0.50 модель:

- flag-нула 20.8% validation patients;
- поймала 100% true acuity 1-2;
- пропустила 0 high-acuity cases;
- дала 1 false alert на 16,000 validation records.

Это звучит как реалистичный workflow: не заменить triage staff, а поднять руку и сказать “этого пациента нужно посмотреть сейчас”.

## Что модель выучила

Интерпретируемые признаки выглядели клинически правдоподобно:

- unresponsive / altered mental status;
- high pain;
- respiratory abnormalities;
- fever;
- shock-related complaint terms;
- overdose;
- meningitis-like terms;
- anaphylaxis-like presentations;
- collapse.

Chief complaint text оказался самым сильным сигналом. Это полезный вывод, но и ограничение: synthetic chief complaints могут быть слишком “чистыми” по сравнению с реальными triage notes.

## Ограничения

Это не medical device.

Это не доказательство клинической эффективности.

Это не модель, которую можно сразу поставить в больницу.

Главные ограничения:

- dataset synthetic;
- нет external validation;
- нет prospective workflow study;
- нет real clinician interaction;
- возможен shortcut learning по текстовым шаблонам;
- нет анализа alert fatigue в реальной среде.

Для движения к реальной клинической проверке нужны:

- external hospital datasets;
- calibration analysis;
- subgroup safety monitoring;
- prospective silent-mode validation;
- clinician-in-the-loop workflow testing;
- governance and escalation policy.

## Что получилось в итоге

На мой взгляд, ценность проекта не в том, что “модель хорошо классифицирует”.

Ценность в framing:

> Для clinical AI нужно заранее определить dangerous failure mode и строить evaluation вокруг него.

В emergency triage таким failure mode является dangerous under-triage.

Проект можно использовать как шаблон для safety-first evaluation:

- leakage controls;
- ablations;
- CV variance;
- error review;
- subgroup safety;
- threshold analysis;
- high-risk alert workflow;
- honest limitations.

GitHub repository:

https://github.com/qu1nty9/triagegeist-safety-acuity-support

