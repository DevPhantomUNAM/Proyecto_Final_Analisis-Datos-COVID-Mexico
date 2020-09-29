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

## Grafica de Pastel para representar los rangos de edad de las personas con COVID

8. Volveremos a utilizar la tabla de personas con COVID y agregaremos un filtro de edad, para saber cuantas personas tienen COVID con menos de 20 años

```
rango1 = df[(df.Indicador > 0.80) & (df.Edad < 20)]
rango1
```
![Imagen donde aparecen las personas con un indicador mayor a 0.80 y un rango menor de 20 anios]()

Para consegir personas de un rango de 20 años años a 40 utilizaremos la condicional &
```
rango2 = df[(df.Indicador > 0.80) & (df.Edad >= 20) & (df.Edad <40)]
rango2
```
![Imagen donde aparecen las personas de 20 años hasta 40]()

Sacaremos la cantidad total por cada tabla utilizando la funcion ```len()```

```
datosEdades = [[(len(rango1)),(len(rango2)),(len(rango3)),(len(rango4))]]
```

##### Gráficamos
![Gráfica del rango de personas por edades con indicador de COVID](https://raw.githubusercontent.com/DevPhantomUNAM/Proyecto_Final_Analisis-Datos-COVID-Mexico/master/assets/img/rangopie.jpg)

Tambien podemos utilizar una gráfica de barras

![Gráfica de barras para rango de edades](https://raw.githubusercontent.com/DevPhantomUNAM/Proyecto_Final_Analisis-Datos-COVID-Mexico/master/assets/img/rangobarra.jpg)

## Edad promedio

9. Para sacar el promedio de la edad, primero obtendremos la edad de las personas con COVID, que sera de la taba COVID

```
edades = df[df.Indicador > 0.80].Edad.tolist()
print(edades)
```

Hacemos una teración en esa lista para sumar cada elemento
```
promedio = 0
for i,e in enumerate(edades):
    promedio = promedio + e
    
promedio = promedio / (len(df[df.Indicador > 0.80]))

print(" El promedio de edad de las personas que tienen covid es de %.f años" %(promedio))
```
#### Para esta muestra, el promedio de edad de las personas que tienen covid es de 69 años

## Ciudad donde hay más contagiados
10. De nuevo utilizaremos la tabla con personas con COVID y esta vez contaremos las ciudades 

```
tabla2 = df[df.Indicador > 0.80].Ciudad.value_counts()
tabla3 = pd.DataFrame(tabla2)
tabla3
```

Obtendremos los indices de las ciudades y la cantidad de personas que hay en cada ciudad

```
ciudades = list(tabla3.index)
personasCiudad = tabla3['Ciudad'].tolist()

print(ciudades)
print(personasCiudad)
```
##### Gráficamos el porcentaje de persoans con un indicador de COVID por ciudad

```
labels = ciudades
sizes = personasCiudad

fig1, ax1 = plt.subplots()
ax1.pie(sizes, labels=labels, autopct='%1.1f%%',
        shadow=True, startangle=90)
ax1.axis('equal')  

plt.show()
```
![Gráfica de ciudades con porcentaje de las personas con indicador de COVID](https://raw.githubusercontent.com/DevPhantomUNAM/Proyecto_Final_Analisis-Datos-COVID-Mexico/master/assets/img/ciudades.jpg)

## Semáforo

11. Para el semáforo utilizaremos otra libreria de gráficas llamada **plotly** (Requiere instalación, vease su [documentación](https://plotly.com/python/))

```
import plotly.graph_objects as go

fig = go.Figure(go.Indicator(
    mode = "gauge+number+delta",
    value = (len(df[df.Indicador > 0.80])),
    domain = {'x': [0, 1], 'y': [0, 1]},
    title = {'text': "Semáforo COVID", 'font': {'size': 24}},
    
    gauge = {
        'axis': {'range': [None, (len(df))], 'tickwidth': 1, 'tickcolor': "darkblue"},
        'bar': {'color': "#E91E63"},
        'bgcolor': "white",
        'borderwidth': 2,
        'bordercolor': "gray",
        'steps': [
            {'range': [0,1], 'color': '#4CAF50'},
            {'range': [1,30], 'color': '#FDD835'},
            {'range': [30,70], 'color': '#FB8C00'},
            {'range': [70, 100], 'color': '#E53935'}],
        'threshold': {
            'line': {'color': "red", 'width': 4},
            'thickness': 0.75,
            'value': 150}}))

fig.update_layout(paper_bgcolor = "lavender", font = {'color': "darkblue", 'family': "Arial"})

fig.show()
```

En steps van los colores que se indicaron para el proyecto segun los rangos

![Gr´pafica de speed proporcionada por ploty](https://raw.githubusercontent.com/DevPhantomUNAM/Proyecto_Final_Analisis-Datos-COVID-Mexico/master/assets/img/semaforo.jpg)

### Insights recavados para esta muestra

| Información	| Description                    | Insights |
| ------------- | ------------------------------ |  ------- |
| Personas que tienen Indice > 0.80 == COVID| 21	 | De la muestra obtenida, aun contamos un un indice bajo de personas con indicador de COVID, pero personas que puedan estar en un rango de .60 a .80 pueden ser posibles persoans futuras con indicador de COVID |
| Rango de edad de las personas que tienen Covid	| **Menos de 20:** 3  <br>**20 a 40:** 2 <br>**40 a 60:** 6   <br>**Más de 60:** 13| Vemos que en nuestra población, las personas mayores de 60, tienen a tener más indicadores de COVID, despues el rango de menos de 20 y los dos de 40 a 60, y por ultimo estan los de 20 a 40 y se deba a que en esta edad, el cuerpo tenga mejores defensas. |
| Edad promedio	| 69 Años    | Al igual que insight de arriba, nuestro indice mayor es arriba de los 60 años. | 
| Ciudad donde hay más contagiados	|   Waarloos, Copertino, Kollam, Dro  | Dentro de estas ciudades se tiene un porcentaje de 9.7 % de infectarse al 4.8% que existen en las demas ciudades. | 
| Semáforo	| Amarillo  | Nuestra muestra determina que estamos en semaforo amarillo, aun que dado los insight recabados, aumente el numero de casos. |
