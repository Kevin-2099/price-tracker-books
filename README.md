# price-tracker-books
Este proyecto es un mini MVP en Python para rastrear el precio diario de un libro en la web [Books to Scrape](https://books.toscrape.com/), guardar el historial en un archivo CSV y descargarlo automáticamente.

El script está pensado para ejecutarse en Google Colab, facilitando la extracción y el guardado de datos sin configuración adicional.

---

## Funcionalidades

- Extrae el nombre y precio actual de un libro específico.
- Guarda la fecha, nombre y precio en un archivo CSV `price_history.csv`.
- Si el archivo ya existe, añade una nueva fila con los datos actuales.
- Descarga automáticamente el CSV al finalizar la ejecución (en Google Colab).

---

## Uso

1. Abre el notebook en Google Colab o clona el repositorio.
2. Ejecuta la celda que contiene el script.
3. Al finalizar, se descargará el archivo `price_history.csv` con el historial actualizado.

---

## Requisitos

- Python 3.x
- Librerías: `requests`, `beautifulsoup4`, `pandas`
- Entorno recomendado: Google Colab (para descarga automática)

Puedes instalar las librerías con:

pip install requests beautifulsoup4 pandas

## Código principal
import requests 
from bs4 import BeautifulSoup
import pandas as pd
from datetime import datetime
import os
from google.colab import files

URL = "https://books.toscrape.com/catalogue/a-light-in-the-attic_1000/index.html"
HEADERS = {"User-Agent": "Mozilla/5.0"}

response = requests.get(URL, headers=HEADERS)
soup = BeautifulSoup(response.content, "html.parser")

product_name = soup.find("div", class_="product_main").h1.text.strip()
price_raw = soup.find("p", class_="price_color").text.strip()
price = float(price_raw[1:])

today = datetime.today().strftime("%Y-%m-%d")

data = {"date": today, "product": product_name, "price_gbp": price}

filename = "price_history.csv"

if os.path.exists(filename):
    df_old = pd.read_csv(filename)
    df_new = pd.DataFrame([data])
    df_all = pd.concat([df_old, df_new], ignore_index=True)
    df_all.to_csv(filename, index=False)
else:
    pd.DataFrame([data]).to_csv(filename, index=False)

print(f"{today} | {product_name} | £{price}")
print("✅ Registro guardado en 'price_history.csv'")

files.download(filename)
## Licencia
Este proyecto está bajo licencia MIT — consulta el archivo LICENSE para más detalles.

