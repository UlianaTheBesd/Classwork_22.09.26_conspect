# 22.09.26 Семинар

НЭ и КР будут с прокторингом + запрещено использование ИИ.
Поэтому важно понимать код и уметь его делать самостоятельно.

#### Условие самостоятельного задания:

" " "
*Датасет insurance.csv содержит информацию о величине страховых выплат ( в долларах).* 
*Описание столбцов:*
*age - возраст застрахованного ( в годах)*
*sex -  пол (мужской/женский)* 
*bmi (body mass index) - индекс массы тела*
*children -  количество детей у застрахованного*
*smoker - курит застрахованный или нет*
*region - регион проживания*
*charges - целевая переменная (величина страховых выплат, в долларах).*

*Задания по датасету:*
*Перед вами датасет insurance_1.csv. Описание датасета представлено выше.*
*В этом задании вам предлагается изучить данные о сотрудниках некоторой компании.* 
1) *Считайте данные в pandas dataframe (используйте функцию pd.read_csv())*
2) *Проверьте есть ли в данных пропуски. В скольких столбцах есть пропуски и сколько? (используйте функцию isnull().sum())*
3) *Заполните пропуски средним значением по столбцу. Для нахождения среднего используйте функцию mean(). Значение округлите до сотых.*
4) *Исправьте опечатки в столбце sex. Сколько опечаток вы исправили?* 
5) *Переведите столбцы sex и smoker в бинарные значения (male: 0, female: 1 и yes: 1, no: 0). Используйте функцию replace().*
6) *Выведите долю курящих сотрудников с индексом массы тела большим 35 и возрастом большим 40 лет (среди всех сотрудников). Сколько строк получилось?*
" " "

Note. В insurance_1.csv (был дан exel) иногда данные bmi читались как даты, а не как float (например, 27.9 читалось как "27 сентября"). Чтобы не было такой ошибки, я применила код:

```
# Исправить колонки с "data" -> "float".
df = pd.read_excel('insurance_1.xlsx')
df['bmi'] = pd.to_numeric(df['bmi'], errors='coerce')
df.to_excel('insurance_1_fixed.xlsx', index=False)
```
*(то есть, командой `to_numeric` мы переводим столбец в числовое число + errors='coerce'[^1]).*

#### 1. Первое задание.

"*1) Считайте данные в pandas dataframe (используйте функцию pd.read_csv())*".

```
import pandas as pd

df = pd.read_excel('insurance_1_fixed.xlsx')
```

#### 2. Второе задание.

"*2) Проверьте есть ли в данных пропуски. В скольких столбцах есть пропуски и сколько? (используйте функцию isnull().sum())*"

```
import pandas as pd

df = pd.read_excel('insurance_1_fixed.xlsx')

print(df.isnull().sum())
```

Вывод:

```
PS D:\yadro-learning-2026\Codes> & "C:\Program Files\Python313\python.exe" d:/yadro-learning-2026/Codes/filedo.py
age           0
sex           0
bmi         233
children      0
smoker        0
region        0
charges       0
dtype: int64
```

#### 3. Третье задание.

"*3) Заполните пропуски средним значением по столбцу. Для нахождения среднего используйте функцию mean(). Значение округлите до сотых.*"

```
import pandas as pd

df = pd.read_excel('insurance_1_fixed.xlsx')

mean_bmi = df["bmi"].mean()
df["bmi"] = df["bmi"].fillna(mean_bmi)

print("mean_bmi:", mean_bmi)
print(df)
```
*(то есть, основная команда - `fillna()`).*

Вывод:
```
PS D:\yadro-learning-2026\Codes> & "C:\Program Files\Python313\python.exe" d:/yadro-learning-2026/Codes/filedo.py
mean_bmi: 31.525570135746605
      age     sex       bmi  children smoker     region      charges
0      19  female  31.52557         0    yes  southwest    16884.924
1      18     mal  33.77000         1     no  southeast    1725.5523
2      28    male  31.52557         3     no  southeast     4449.462
3      33    male  22.70500         0     no  northwest  21984.47061
4      32    male  28.88000         0     no  northwest    3866.8552
...   ...     ...       ...       ...    ...        ...          ...
1333   50    male  30.97000         3     no  northwest   10600.5483
1334   18  female  31.92000         0     no  northeast    2205.9808
1335   18  female  36.85000         0     no  southeast    1629.8335
1336   21  female  31.52557         0     no  southwest     2007.945
1337   61  female  31.52557         0    yes  northwest   29141.3603

[1338 rows x 7 columns]
```

#### 4. Четвёртое задание.

"*4) Исправьте опечатки в столбце sex. Сколько опечаток вы исправили?* ".

Сколько опечаток:
```
import pandas as pd

df = pd.read_excel('insurance_1_fixed.xlsx')

print(len(df[df["sex"]=="emale"]["sex"]) + len(df[df["sex"]=="mal"]["sex"])) 
```
*1 (команда `df[условия][столбец]` чтобы вывести объект Series (один столбец), который содержит индексы тех строк с 'emale', а также сами ами значения строк)*
*2 (можно использовать с Series `len()`).*
3 *(то есть, `df[условия]` выводит все столбцы - со строками, где условия совпали, а `df[условия][столбец]` выводит только категорию этого столбца).*

Вывод:
```
PS D:\yadro-learning-2026\Codes> & "C:\Program Files\Python313\python.exe" d:/yadro-learning-2026/Codes/filedo.py
3
```

Чтобы менять что-то, используем метод `.replace()`.
Синтаксис:
Есть словарь: `{'что_меняем': 'на_что_меняем'}`.
То есть: `df['sex'] = df['sex'].replace({'emale': 'female'})`:
```
import pandas as pd

df = pd.read_excel('insurance_1_fixed.xlsx')

df["sex"] = df['sex'].replace({"emale": "female"}).replace({"mal": "male"})

print(df)
print("len + len:", len(df[df["sex"]=="emale"]["sex"]) + len(df[df["sex"]=="mal"]["sex"]))
```

Вывод:
```
PS D:\yadro-learning-2026\Codes> & "C:\Program Files\Python313\python.exe" d:/yadro-learning-2026/Codes/filedo.py
      age     sex     bmi  children smoker     region      charges
0      19  female     NaN         0    yes  southwest    16884.924
1      18    male  33.770         1     no  southeast    1725.5523
2      28    male     NaN         3     no  southeast     4449.462
3      33    male  22.705         0     no  northwest  21984.47061
4      32    male  28.880         0     no  northwest    3866.8552
...   ...     ...     ...       ...    ...        ...          ...
1333   50    male  30.970         3     no  northwest   10600.5483
1334   18  female  31.920         0     no  northeast    2205.9808
1335   18  female  36.850         0     no  southeast    1629.8335
1336   21  female     NaN         0     no  southwest     2007.945
1337   61  female     NaN         0    yes  northwest   29141.3603

[1338 rows x 7 columns]
len + len: 0
```

#### 5. Пятое задание.

"*5) Переведите столбцы sex и smoker в бинарные значения (male: 0, female: 1 и yes: 1, no: 0). Используйте функцию replace().*"

```
import pandas as pd

df = pd.read_excel('insurance_1_fixed.xlsx')

df["sex"] = df["sex"].replace({"female" : "1"}).replace({"male" : "0"})
df["smoker"] = df["smoker"].replace({"yes" : "1"}).replace({"no" : "0"})

print(df)
```

Вывод:
```
PS D:\yadro-learning-2026\Codes> & "C:\Program Files\Python313\python.exe" d:/yadro-learning-2026/Codes/filedo.py
      age  sex     bmi  children smoker     region      charges
0      19    1     NaN         0      1  southwest    16884.924
1      18  mal  33.770         1      0  southeast    1725.5523
2      28    0     NaN         3      0  southeast     4449.462
3      33    0  22.705         0      0  northwest  21984.47061
4      32    0  28.880         0      0  northwest    3866.8552
...   ...  ...     ...       ...    ...        ...          ...
1333   50    0  30.970         3      0  northwest   10600.5483
1334   18    1  31.920         0      0  northeast    2205.9808
1335   18    1  36.850         0      0  southeast    1629.8335
1336   21    1     NaN         0      0  southwest     2007.945
1337   61    1     NaN         0      1  northwest   29141.3603

[1338 rows x 7 columns]
```
*(Да, когда я изменяю значения таблицы в коде - это не меняет исходных таблиц; видимо, чтобы что-то изменилось, нужно вывести его в файл, как перед первым заданием: `df.to_excel('insurance_1_fixed.xlsx', index=False)`).*

![[Pasted image 20260922141439.png|628]]
*(способ учителя записать 5 задание).*
#### 6. Шестое задание.

"*6) Выведите долю курящих сотрудников с индексом массы тела большим 35 и возрастом большим 40 лет (среди всех сотрудников). Сколько строк получилось?*"

```
import pandas as pd

df = pd.read_excel('insurance_1_fixed.xlsx')

# во избежание NaN, которые иногда снова появляются...
mean_bmi = df["bmi"].mean()
df["bmi"] = df["bmi"].fillna(mean_bmi)

print(len(df[(df["smoker"] == "yes") & (df["bmi"] > 35) & (df["age"] > 40)]))
```

Вывод:
```
PS D:\yadro-learning-2026\Codes> & "C:\Program Files\Python313\python.exe" d:/yadro-learning-2026/Codes/filedo.py
36
```

[^1]: Параметр `errors` отвечает на вопрос *"что именно делать, если в столбце встретится значение, которое невозможно превратить в число"* и имеет три установки: (1) 'coerce' (принудительно - если видит странное значение, заменяет это поле на NaN), (2) 'raise' (при наличии даже одного странного значения вылетает ValueError), (3) 'ignore' (оставляет значение как есть).
