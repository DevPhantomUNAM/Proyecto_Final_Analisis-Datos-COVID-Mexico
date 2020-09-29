![](https://raw.githubusercontent.com/DevPhantomUNAM/Proyecto_Final_Analisis-Datos-COVID-Mexico/master/assets/img/Portada.png)
# Proyecto Final: Analísis Datos COVID México
## Herramientas utilizadas
* Jupyter Notebook
* Pandas
* [Matplotly](https://matplotlib.org/?fbclid=IwAR2_L-pd4Ycnjd4WZWuP8us9L4Z07844QQ9gjTHtHD7GskLTeCh-c-03hro)
* [Plotly](https://plotly.com/python/)

### Instalación

+ Instalaremos Anaconda
  + Nos dirigimos a la [Home de Anaconda](https://www.anaconda.com/) e iremos a la sección de [Download](https://www.anaconda.com/products/individual) (descargas)

+ Abrir Jupyter despues de la instalación
  + En nuestra consola escribiremos
  + `$jupyter notebook`
  
 
Despues de unos segundos te abrira automaticamente tu nootebook donde podras trabajar con Python

##### Comandos  Básicos de Jupyter

| Combinación	| Description                    |
| ------------- | ------------------------------ |
| `Shit + Enter`| Mandas a compilar la linea.	 |
| `Enter`	| Solo haces un salto de linea     |
| `ESC`	| Para salir de modo editor de celda     |


Fuera de editor


| Combinación	| Description                    |
| ------------- | ------------------------------ |
| `A`| Crea una celda arriba	 |
| `B`	| Crea una celda abajo     |
| `X`	| Corta una celda    |
| `M`	| Convertir celda a un Markadown     |
| `Enter`	| Entrar en la celda    |

### Inicio (Limpiando la DATA)

Necesitaremos una libreria para extraer archivois con extención csv ya que nuestra data esta desplegada de esta manera:  
![Captura de Excel con muestra de 20 datos](https://raw.githubusercontent.com/DevPhantomUNAM/Proyecto_Final_Analisis-Datos-COVID-Mexico/master/assets/img/tabla.jpg)

1. Se importa la lireria *csv* para poder manejar nuestros datos guardados en archivos csv
  ```
  import csv
  ```
2. Leemos la base de datos y algunos archivos de la columna empresas, estan divididas por espacios. Como diviremos la cadena en elemenos de una sub-lista, primero los dividiremos por espacios quitando el ;
  ```
  bd = []
with open('bd2.csv') as f:
    reader = csv.reader(f)
    for row in reader:
        x = row[0].replace(" ", "_")
        y = x.replace(";", " ")
        z = y.split()
        bd.append(z)
  ```
La función de replace, me deja remplazar en la cadena, un caracter unico, y en este caso convertimos los espacios que tienen las empresas, a guoines bajos.
La función split, me permite crear varios elementos dependiendo del string, que esten esparados por espacios, asi al meterlos a una lista me aparecen como si fuera:
 ```
  lista = ['elemento1','elemento2','elemento3',...]
```
  
3. Volvemos a separar por espacios, las empresas que contienen guiones bajos
   ```
  new_bd = []
for i,e in enumerate(bd):
    new_bd.append([])
    for j,f in enumerate(e):        
        new_bd[i].append(f.replace("_", " "))
  ```
4. Algunos datos como la columna Edad y la columna de Indicador, estan en formato de cadena, asi que iteraremos para convertir esos datos a numero
```
  bd_v2 = []
for i,e in enumerate(new_bd):
    if i>0:
        nombre= e[0]
        apellido = e[1]
        edad = int(e[2])
        ciudad = e[3]
        empresa = e[4]
        indicador = float(e[5])
    
        bd_v2.append([nombre,apellido,edad,ciudad,empresa,indicador])
  ```
  
5. Instalaremos panda para utilizar nuestros datos en tablas
```
  import pandas as pd
  pd.options.display.max_columns = None 
  pd.options.display.max_rows = None
```

6. Utilizamos ```pd.DataFrame``` y dentro de sus parametros colocamos nuestros datos y asignamos el nombre de cada columna
  
