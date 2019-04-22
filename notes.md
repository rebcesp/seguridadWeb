# _Mis Apuntes (WebSecurityAcademy)_

## _Que es una Inyeccion SQL_

Es una vulnerabilidad de seguridad web que permite a un atacante interferir con las consultas que una aplicación reaqliza en su base de datos.

En algunas situaciones, un atcante puede escalar un ataque de inyeccion de SQL para comprometer el servidor subyacente u otra infraestructura de back-end, o realizar un ataque de denegación de servicio.

### _Ejemplos Breves_ 


```sql
UNION SELECT username, password FROM users--
```

```sql
SELECT name, description, FROM products WHERE category = 'Gifts' UNION SELECT username, passsowrd FROM users--
```
## _1.Recuperando datos ocultos_

Si consideramos que tenemos una aplicacion de shooping que muestra productos en diferentes categorias. 

En esta sección no explicare la anatomia de una URL, esto lo puedes encontrar en Google por ejemplo aqui: [_Anatomia de una URL_](https://developer.mozilla.org/es/docs/Learn/Common_questions/Qu%C3%A9_es_una_URL)

---


Pongamos un ejemplo supongamos que tenemos la categoria de Gifts(Regalos) , la URL es esta:

>_https://insecure-website.com/products?category=Gifts_

Esto hace que la aplicación realice una consulta SQL para recuperar detalles de los productos relevantes de la base de datos

La URL anterior definidida estaria interpretandose en SQL de esta manera:

```sql
SELECT * FROM products WHERE category = 'Gifts' AND released = 1
```
**Esta consulta preguntará en la base de datos y esta retornara lo siguiente:**

* Todos los detalles(*)
* De la tabla Productos
* Donde la categoría es Gifts
* Lanzara los productos que son iguales a 1

>La restricción liberada = 1 se usa para ocultar productos que no se lanzan.

>Para productos no lanzados, presublimente liberados es = 0

<br>En este caso la aplicación no implementa ninguna defensa contra los ataques de inyección SQL, porque un atacante podria consutir un ataque como este:

>_https://insecure_website.com/products?category=Gifts'--_

En esta ocasión vamos a ver lo que significa esto : **'--** , el resultado de la consulta seria esta:

```sql
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1
```

<br>La clave de esto es la secuencia del doble guión: **--** _que es un indicador de comentarios en SQL_. Lo cual esto hace que el resto de la consulta se interprete como un comentario. Esto elimina de manera efectiva el resto de la consulta, porque ya no incluye ``` AND released = 1``` Esto significa que muestran todos los productos no publicados.

<br>
También yendo mas lejos un atacante puede hacer que la aplicación muestre todos los productos de cualquier categoría, ahora lo veremos como: 

>_https://insecure-website.com/products?category=Gifts'+OR+1=1--_

El resultado de esta consulta SQL sería esta manera: 

```sql
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1
```
## _Explicación de OR + 1 = 1_

Empezemos entendiendo que en enunciado siempre verdadero esto se usa entre otras cosas para ```ANULAR la condición WHERE y obtener todos los resultados de una consulta.```

Es un enunciado siempre verdadero que se usa para , entre otras cosas, anular una condición Where y obtener todos los resultados de una consulta.

Supongamos que tenemos una tabla usuario , y un campo nombre_usuario

```sql
SELECT * FROM usuarios WHERE nombre_usuario = ’Smith’
```
Esta consulta devuelve los registros donde nombre_usuario sea Smith.

Suponiendo que la tabla hubiese unicidad por este campo, devolvería 1 solo registro.

Si ahora hacemos:
```sql
SELECT * FROM usuarios WHERE nombre_usuario = ’Smith’ OR 1=1
```
>Como 1=1 es SIEMPRE verdadero, nombre_usuario=’Smith’ OR 1=1 siempre es verdadero de acuerdo a la tabla de verdad de OR.

Como resultado el ```SELECT``` alterado devuelve TODOS los registros.

Esta es una técnica básica de inyección SQL para que una consulta devuelva información adicional por alteración ‘aditiva’ (es decir, la consulta original permanece inalterada, y se añade texto que altera su resultado).

## _Taulogía - Explicación._

Es una condicion que siempre es verdadera, ES DECIR ES UNA FORMULA BIEN FORMULADA que resulta siempre verdadera para cualquier interpretación.

## _**Diagrama Tabla de verdad**_

<img src="https://upload.wikimedia.org/wikipedia/commons/3/3e/Logical_connectives_Hasse_diagram.svg" alt="kodama" style="width:25%; margin:left; display:block;">

El primer ejercicio creo que se ha explicado a detalle, pasemos al segundo.









