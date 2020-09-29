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

Necesitaremos una libreria para extraer archivos con extención csv ya que nuestra data esta desplegada de esta manera:  
![Captura de Excel con muestra de 20 datos](https://raw.githubusercontent.com/DevPhantomUNAM/Proyecto_Final_Analisis-Datos-COVID-Mexico/master/assets/img/tabla.jpg)

1. Se importa la lireria *csv* para poder manejar nuestros datos guardados en archivos csv
  ```
  import csv
  ```
2. Leemos la base de datos y vemos que algunos archivos de la columna empresas estan divididas por espacios. 
Como diviremos la cadena en elemenos de una sub-lista, necesitamos que las empresas con espacios, queden en items juntos.

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
La función de replace, me deja remplazar en la cadena, un caracter unico, y en este caso convertimos los espacios que tienen las empresas, a guines bajos.

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

```
df= pd.DataFrame(bd_v2,columns=['Nombre', 'Apellido','Edad','Ciudad','Empresa','Indicador'])
df
```
![Imagen de pandas con las primeras 10 visualizaciones](https://raw.githubusercontent.com/DevPhantomUNAM/Proyecto_Final_Analisis-Datos-COVID-Mexico/master/assets/img/pandas.jpg)

## Personas que tienen Indice > 0.80 == COVID

7. Empezaremos a trabajar con la tabla de personas que tienen COVID. Para detectar quien en nuestra tabla esta contagiado de COVID, tiene un indicador > a 0.8 asi que buscaremos solo las personas que tegan el indice mayor

```
  df[df.Indicador > 0.80]
```
![Imagen donde aparecen las personas con un indicador mayor a 0.80]()

8. Para saber la cantidad de personas que tienen COVID, podemos medir el largo de la tabla con ```len()```
```
print("%d personas tienen un indicador de COVID mayor de 0.8"%(len(df[df.Indicador > 0.80])))
```
#### Para esta muestra, contamos con un total de 21 personas con indicador mayuor a 0.80 lño que indica que tienen COVID

## Rango de edad de las personas que tienen Covid

8. Para sacar e porcentaje de personas que tienen COVID, se hara autoincremental donde dependiendo la cantidad de personas, se sacara automáticamente el porcentaje de personas con COVID

Así que haremos una regla de 3 de la cantidad de personas con COVID multiplicada por el 100 y dividida por la cantidad total de personas en la base de datos

```
porcentajePersonasSinCOVID = ((len(df[df.Indicador < 0.80].index.tolist()))*100)/(len(df))
```

Tambien sacamos el porcentaje de personas que no tienen COVID
```
porcentajePersonasSinCOVID = ((len(df[df.Indicador >= 0.80].index.tolist()))*100)/(len(df))
```

Y utilizaremos la libreria matplotyly para mandar a graficar el resultado del porcentaje de personas que tienen COVID y el % de las que no.

```
labels = ['Personas sin COVID','Personas con COVID'] #int(porcentajePersonasSinCOVID)
sizes = [(int(porcentajePersonasSinCOVID)),(int(porcentajePersonasConCOVID))] 

explode = (0,  0)  

fig1, ax1 = plt.subplots()
ax1.pie(sizes, explode=explode, labels=labels, autopct='%1.1f%%',
        shadow=True, startangle=90)
ax1.axis('equal')  

plt.show()
```

![Gráfica de las personas que tienen COVID](https://raw.githubusercontent.com/DevPhantomUNAM/Proyecto_Final_Analisis-Datos-COVID-Mexico/master/assets/img/personas.jpg)
