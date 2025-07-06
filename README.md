# df-anonymizer

A lightweight Python library designed to apply privacy-preserving transformations on datasets in `pandas.DataFrame` format.  
It is ideal for preparing data for research, analysis, reporting or machine learning while protecting sensitive personal information.

## ✨ Key Features

- **Masking**: Mask email addresses and identification numbers
- **Pseudonymization**: Generate unique pseudonyms with key mapping table
- **Data perturbation**: Add privacy noise to age, weight, height, etc.
- **Data generalization**: Bucket or reduce granularity for numeric and date values
- **Suppression**: Remove sensitive columns or filter out specific records
- **Shuffling**: Randomly reorder rows
- **Evaluation**: Compute the k-anonymity score for your dataset

> All functions are optimized to work with `pandas.DataFrame` structures.

## 📦 Installation

```bash
pip install df-anonymizer
```


## 👉 Example

### 📌 Pseudonymization

```python
import pandas as pd
from df_anonymizer import pseudonymization

df = pd.DataFrame({'NRIC': ['S1234567A', 'S2345678B', 'S3456789C']})
anon_df = pseudonymization(df, 'NRIC')
print(anon_df)

# Example output:
#        NRIC
# 0  xcvbn192835467
# 1  poiuy726354091
# 2  fgdte012582538
```

### 📌 Masking

```python
from df_anonymizer import maskID, maskEmail

df_mask = pd.DataFrame({
    'ID': ['123456789', '987654321'],
    'Email': ['alice@example.com', 'bob@example.com']
})
df_mask = maskID(df_mask, 'ID')
print(df_mask)

# Output:
#          ID              Email
# 0     ******789     alice@example.com
# 1     ******321     bob@example.com


df_mask = maskEmail(df_mask, 'Email')
print(df_mask)

# Output:
#          ID              Email
# 0     123456789     a****@example.com
# 1     987654321     b**@example.com
```

### 📌 Data Perturbation

```python
from df_anonymizer import (
    agePerturbation, weightPerturbation, heightPerturbation,
    dataPerturbation, datePerturbation
)

df_perturb = pd.DataFrame({
    'Age': [25, 34, 57],
    'Weight': [58.4, 72.1, 80.5],
    'Height': [163.2, 177.5, 170.0],
    'Score': [81, 92, 87],
    'Date': pd.to_datetime(['2023-01-01', '2023-01-15', '2023-02-01'])
})

df_perturb = agePerturbation(df_perturb, 'Age')
print(df_perturb)

# Output:
#    Age  Weight  Height  Score       Date
# 0   24    58.4   163.2     81 2023-01-01
# 1   33    72.1   177.5     92 2023-01-15
# 2   57    80.5   170.0     87 2023-02-01

df_perturb = weightPerturbation(df_perturb, 'Weight')
print(df_perturb)

# Output:
#    Age  Weight  Height  Score       Date
# 0   25    57.0   163.2     81 2023-01-01
# 1   34    72.0   177.5     92 2023-01-15
# 2   57    78.0   170.0     87 2023-02-01

df_perturb = heightPerturbation(df_perturb, 'Height')
print(df_perturb)

# Output:
#    Age  Weight  Height  Score       Date
# 0   25    58.4    160     81  2023-01-01
# 1   34    72.1    175     92  2023-01-15
# 2   57    80.5    170     87  2023-02-01

df_perturb = dataPerturbation(df_perturb, 'Score', base_number=10)
print(df_perturb)

# Output:
#    Age  Weight  Height  Score       Date
# 0   25    58.4   163.2     80  2023-01-01
# 1   34    72.1   177.5     90  2023-01-15
# 2   57    80.5   170.0     80  2023-02-01

df_perturb = datePerturbation(df_perturb, 'Date', max_days=7)
print(df_perturb)

# Output:
#    Age  Weight  Height  Score       Date
# 0   25    58.4   163.2     81  2022-12-30
# 1   34    72.1   177.5     92  2023-01-17
# 2   57    80.5   170.0     87  2023-01-25
```

### 📌 Data Generalization

```python
from df_anonymizer import dateGeneralization, meanGeneralization, dataBucketing

df_gen = pd.DataFrame({
    'DOB': pd.to_datetime(['1990-01-01', '1995-05-15', '1995-05-25']),
    'Income': [2000, 4500, 7800],
    'Age': [22, 35, 47]
})

df_gen = dateGeneralization(df_gen, 'DOB', verbose=False)
print(df_gen)

# Output:
#     DOB  Income   Age
# 0  1990    2000    22
# 1  1995    4500    35
# 2  1995    7800    47

df_gen = meanGeneralization(df_gen, 'Income', bins=3)
print(df_gen)

# Output:
#     DOB         Income  Age
# 0  1990-01-01    1750    22
# 1  1995-05-15    4750    35
# 2  1995-05-25    7750    47

df_gen = dataBucketing(df_gen, 'Age', bins=[0, 30, 60], labels=['Young', 'Adult'])
print(df_gen)

# Output:
#     DOB         Income    Age
# 0  1990-01-01    1750    Young
# 1  1995-05-15    4750    Adult
# 2  1995-05-25    7750    Adult
```

### 📌 Suppression

```python
from df_anonymizer import attributeSuppression, recordSuppression

df_sup = pd.DataFrame({
    'Name': ['Alice', 'Bob'],
    'Age': [25, 40],
    'City': ['SG', 'NY']
})

df_sup = attributeSuppression(df_sup, ['Name'])
print(df_sup)

# Output:
#    Age City
# 0   25   SG
# 1   40   NY

df_sup = recordSuppression(df_sup, ['City'], [['NY']])
print(df_sup)

# Output:
#    Name   Age City
# 0  Alice  25   SG
```
### 📌 Shuffling

```python
from df_anonymizer import dataShuffling

df_shuffle = pd.DataFrame({'Name': ['A', 'B', 'C'], 'Age': [20, 30, 40]})
df_shuffle = dataShuffling(df_shuffle)
print(df_shuffle)

# Output:
#   Name  Age
# 0    B   30
# 1    A   20
# 2    C   40
```

### 📌 k-Anonymity Score

```python
from df_anonymizer import calculateKAnonymity

df_kanon = pd.DataFrame({
    'Age': [25, 25, 30, 30],
    'Zip': ['12345', '12345', '67890', '67890']
})

k_score = calculateKAnonymity(df_kanon, ['Age', 'Zip'])
print(f"k-anonymity score: {k_score}")

# Output:
# k-anonymity score: 2
```

## 📑 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.


## 📘 Reference

1. [Guide To Basic Anonymization](https://www.pdpc.gov.sg/help-and-resources/2018/01/basic-anonymisation) 