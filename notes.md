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

>https://insecure_website.com/products?category=Gifts'--

En esta ocasión vamos a ver lo que significa esto : **'--** , el resultado de la consulta seria esta:

```sql
SELECT * FROM products WHERE category = 'Gifts'--' AND released = 1
```

<br>La clave de esto es la secuencia del doble guión: **--** _que es un indicador de comentarios en SQL_. Lo cual esto hace que el resto de la consulta se interprete como un comentario. 





