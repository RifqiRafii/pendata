```{code-cell} python
import pandas as pd

# Membaca dataset
df = pd.read_csv("IRIS.csv")

# Menampilkan 10 data pertama
df.head(10)