# 📊 End-to-End A/B Testing & Product Analytics Dashboard

![Tableau](https://img.shields.io/badge/Tableau-Public-BS0052?style=for-the-badge&logo=tableau&logoColor=white)
![Python](https://img.shields.io/badge/Python-3.10+-3776AB?style=for-the-badge&logo=python&logoColor=white)
![SciPy](https://img.shields.io/badge/SciPy-Statistics-8CAAE6?style=for-the-badge&logo=scipy&logoColor=white)
![Status](https://img.shields.io/badge/Project-Production--Ready-brightgreen?style=for-the-badge)

[🔗 Переглянути інтерактивний дашборд у Tableau Public](https://public.tableau.com/views/ab_test_stats/ExecutiveSummary?:language=en-US&publish=yes&:sid=&:redirect=auth&:display_count=n&:origin=viz_share_link)

---

## 📌 Про проєкт & Контекст

Даний проєкт є **навчально-демонстраційним кейсом (Portfolio Case)**, створеним для демонстрації повного циклу аналітичної роботи — від формулювання гіпотези та симуляції даних до статистичного тестування та інтерактивної візуалізації.

### ❓ Бізнес-питання
> *«Чи впливає оновлення елемента інтерфейсу (зміна кнопки закліку до дії) на конверсію в покупку та загальний виторг на користувача?»*

Для проведення аналізу було **згенеровано синтетичний датасет**, який реалістично імітує поведінку **10 000 користувачів** електронної комерції протягом місячного експерименту (жовтень 2026). Користувачі були випадково та рівномірно розподілені на дві групи:
* **Група A (Control)** — старий варіант інтерфейсу.
* **Група B (Treatment)** — новий варіант інтерфейсу.

---

## 🚀 Стек технологій & Інструменти

* **Python (Pandas, NumPy, SciPy, Statsmodels)** — генерація синтетичних даних, статистичні тести (Z-test, Welch's t-test, Chi-square), оцінка довірильних інтервалів.
* **Tableau Public** — побудова багатосторінкового інтерактивного дашборду для стейкхолдерів.
* **A/B Testing Methodology** — перевірка SRM (Sample Ratio Mismatch), аналіз накопиченої конверсії (Cumulative CR), оцінка guardrail-метрика.

---

## 📊 Ключові екрани дашборду (Dashboard Architecture)

### 1. Executive Summary (Високорівневі KPI)
Загальний огляд ключових показників експерименту та щоденна динаміка основних метрик у розрізі тестових груп.

![Executive Summary](./Executive%20summary.png)

### 2. Funnel & Segment Analysis (Воронка конверсії та сегментація)
Аналіз поведінкової воронки за допомогою дзеркального графіку (Tornado Funnel) та оцінка сегментів за пристроями, країнами й джерелами трафіку.

![Funnel & Segment Analysis](./Funnel_segment%20analysis.png)

### 3. Time Dynamics & Guardrail Metrics (Контроль якості тесту)
Перевірка стабілізації накопиченого Conversion Rate у часі та оцінка рівномірності сплітування трафіку (SRM Check).

![Time Dynamics & Guardrail Metrics](./Time%20dynamics_guardrail%20metrics.png)

### 4. Detail Stat Table (Статистична деталізація)
Повне статистичне зведення результатів із p-values, абсолютним/відносним ліфтом та 95% довірчими інтервалами.

![Stat Summary](./Stat%20summary.png)

---

## 📈 Результати аналізу та статистичні висновки

За результатами симуляції та розрахованих статистичних критеріїв:

| Метрика | Group A (Control) | Group B (Treatment) | Absolute Lift | Relative Lift (%) | p-value | Stat. Significant |
| :--- | :---: | :---: | :---: | :---: | :---: | :---: |
| **Conversion Rate (CR)** | 9.8% | 12.8% | +3.0% | **+30.6%** | `< 0.001` | **True** |
| **Revenue per User (RPU)** | $7.18 | $10.13 | +$2.94 | **+40.96%** | `< 0.001` | **True** |
| **Average Order Value (AOV)** | $73.22 | $79.02 | +$5.80 | **+7.92%** | `0.025` | **True** |
| **Add-to-Cart Rate** | 24.7% | 28.9% | +4.2% | **+16.99%** | `< 0.001` | **True** |

### 🔍 Головні висновки:
1. **Зростання конверсії:** Варіант B показав статистично значуще зростання Conversion Rate з $9.8\%$ до $12.8\%$ ($p < 0.001$).
2. **Перевірка SRM:** Значення $Chi\text{-}Square = 2.3$ підтверджує відсутність Sample Ratio Mismatch (фактичний спліт $50.8\%$ vs $49.2\%$). Експеримент проведено коректно.
3. **Стабільність у часі:** Графік **Cumulative CR** демонструє, що після 5-го дня тесту криві стабілізувалися, і перевага варіанту B є стійкою, а не наслідком тимчасового сплеску.

---

## 🐍 Python Code (Data Generation & Statistical Analysis)

Нижче наведено фрагмент коду, використаного для проведення статистичних тестів та підготовки даних для Tableau:

```python
import numpy as np
import pandas as pd
from scipy import stats
from statsmodels.stats.proportion import proportions_ztest

# 1. Завантаження або генерація даних (Raw Data Sample)
# df = pd.read_csv('ab_test_raw_data.csv')

# 2. Розрахунок Z-test для Conversion Rate
count = np.array([df[df['group']=='A']['purchased'].sum(), df[df['group']=='B']['purchased'].sum()])
nobs = np.array([df[df['group']=='A']['user_id'].nunique(), df[df['group']=='B']['user_id'].nunique()])

z_stat, p_val_cr = proportions_ztest(count, nobs)

# 3. Розрахунок Welch's t-test для Revenue per User (RPU)
rpu_a = df[df['group']=='A']['revenue']
rpu_b = df[df['group']=='B']['revenue']

t_stat, p_val_rpu = stats.ttest_ind(rpu_b, rpu_a, equal_val=False)

print(f"CR Z-test p-value: {p_val_cr:.4f}")
print(f"RPU Welch's t-test p-value: {p_val_rpu:.4f}")
```

---

## 💡 Практична цінність та обмеження

* **Контекст бізнесу:** Незважаючи на позитивні статистичні показники тесту, у реальних умовах перед впровадженням рішення обов'язково оцінюється витратна частина на розробку, ефекти новізни (Novelty Effect) та потенційні довгі ризики для LTV.
* **Призначення:** Проєкт відображає підхід до розробки продуктових дашбордів для C-level та аналітичних команд.

---
*Автор: Іван Раделицький*  
*[Tableau Public Profile](https://public.tableau.com/app/profile/ivananalyst/vizzes)*
