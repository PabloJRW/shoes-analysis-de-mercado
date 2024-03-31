# Documentación de la limpieza y transformación de datos con Python

## Paso 1: Importación de bibliotecas y carga de datos

```python
# Importar bibliotecas necesarias
import os
import pandas as pd
import dateime as dt

# Ruta del arcivo CSV
FILE_PATH = os.path.join('dataset', 'shoes.csv')

# Cargar datos desde un archivo CSV
data = pd.read_csv(FILE_PATH, index_col=False)
```

## Paso 2: Exploración inicial de los datos

```python
# Mostrar 20 filas del Dataframe
data.sample(20)

# Verificar la informacion de las columnas
data.info()
```

## Paso 3: Limpieza de datos

```python
# Corregir tipos de datos incorrectos
data['Order Date'] = pd.datetime(data['Order Date'], format="%m/%d/%y")
data['Release Date'] = pd.datetime(data['Release Date'], format="%m/%d/%y")
data['Sale Price'] = data['Sale Price'].str.replace('$','').str.replace(',', '').astype(dtype='float')
data['Retail Price'] = data['Retail Price'].str.replace('$','').str.replace(',', '').astype(dtype='float')
data['Sneaker Name'] = data['Sneaker Name'].str.lower().str.replace('-', ' ')
```

## Paso 4: Transformación de datos

Extracción de Marca

```python
# Función para extraer la marca de las zapatillas
def extraer_marca(columna, lista_de_marcas):
"""
    Esta función busca una marca específica dentro de un valor de columna y devuelve esa marca si se encuentra en la lista de marcas proporcionada.

    Parámetros:
    - columna: El valor de la columna donde se buscará la marca.
    - lista_de_marcas: Una lista de marcas que se buscarán en la columna.

    Retorna:
    - La marca encontrada en la lista_de_marcas si se encuentra en la columna. Si ninguna marca coincide, devuelve "nike".
    """

	# Itera sobre cada marca en la lista de marcas proporcionada
	for marca in lista_de_marcas:
		# Comprueba si la marca actual está presente en el valor de la columna
		if marca in columna:
			# Devuelve la marca si se encuentra una coincidencia
			return marca
	# Si no se encuentra coincidencia devuelve "nike"
	return "nike'

# Lista de las marcas a buscar
lista_de_marcas = ['nike', 'adidas']
# Se crea una nueva columna llamada 'Marca' en el DataFrame 'data', que contendrá la marca extraída de la columna 'Sneaker Name'.
data['Marca'] = data['Sneaker Name'].str.lower().apply(extraer_marca, lista_de_marcas=lista_de_marcas)
```

Extracción de Modelo

```python
# Función para extraer el modelo de las zapatillas
def extraer_modelo(columna, lista_de_modelos):
	"""
			Esta funcion busca un modelo específico dentro de un valor de columna y devuelve 
			ese modelo si se encuentra en la lista de modelos proporcionada.

			Parámetros:
			- columa: El valor de la columna donde se buscará el modelo.
			- lista_de_modelos: Una lista de modelos que se buscarán en la columna.

			Retorna:
			- El moodelo encontrado en la lista_de_modelos si se encuentra en la columna. Si ningún modelo coincide, devuelve el mismo valor.
	"""

	# Itera sobre cada modelo en la lista de modelos proporcionada
	for modelo in lista_de_modelos:
		# Verifica si en la columna hay algún modelo de la lista proporcionada
		data = data.apply(lambda x: modelo if modelo in x else x)
	
	# Si no se encuentra ningún modelo de la lista proporcionada, devuelve el mismo valor.
	return data

# Lista de los modelos a buscar

# Se crea la columna 'Modelo' con los modelos extraídos con la función extraer_modelos
data['Modelo'] = extraer_modelo(data['Sneaker Name'], lista_de_modelos)
```

Selección y renombrado de variables

```python
# Lista de variables que se mantendrán
mis_variables = ['Release Date', 'Order Date', 'Marca', 'Modelo', 'Shoe Size', 'Retail Price','Sale Price', 'Buyer Region']

# Copia del dataframe con las variables que se mantendrán
data = data[mis_variables].copy()

# Diccionario para renombrar las variables
nombres_de_columnas = {'Release Date':'Fecha de Lanzamiento', 'Order Date':'Fecha de Compra',
'Shoe Size':'Talla', 'Retail Price':'Precio Sugerido', 'Sale Price':'Precio de Venta',
'Buyer Region':'Región'} 

# Renombrando las variables
data.rename(columns=nombres_de_columnas, inplace=True)
```

Creación de nuevas variables

```python
# Diferencia entre el precio de venta y el precio sugerido
data['Diferencial'] = data['Precio de Venta'] - data['Precio Sugerido']

# Diferencia entre el precio de venta y el precio sugerido porcentualmente
data['% Diferencial'] = data['Precio de Venta'] / data['Precio Sugerido']
```

## Paso 5: Guardar los datos limpios y transformados

```python
# Guardar el Dataframe resultante en un nuevo archivo CSV
data.to_csv(os.join.path('datasets','shoes_final.csv'))
```