import requests
from bs4 import BeautifulSoup
import pandas as pd

# Usamos un sitio simple con HTML estático
url = "https://www.x-rates.com/table/?from=USD&amount=1"

# Hacemos la solicitud HTTP
response = requests.get(url)

# Parseamos el HTML
soup = BeautifulSoup(response.text, 'html.parser')

# Buscamos la tabla principal
table = soup.find('table', class_='tablesorter ratesTable')

# Extraemos filas
rows = table.find_all('tr')[1:]  # ignoramos encabezado

# Guardamos los datos
data = []
for row in rows:
    cols = row.find_all('td')
    if len(cols) >= 2:
        currency = cols[0].text.strip()
        rate = cols[1].text.strip()
        data.append({'Currency': currency, 'Rate': rate})

# Creamos el DataFrame
df = pd.DataFrame(data)

# Limpiamos datos (convertimos tasas a números)
df['Rate'] = df['Rate'].str.replace(',', '').astype(float)

# Guardamos en CSV
df.to_csv('exchange_rates.csv', index=False)

print(df.head())

# scraper.py
