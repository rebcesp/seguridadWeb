# _Mis Apuntes (WebSecurityAcademy)_

## _Que es una Inyeccion SQL_

Es una vulnerabilidad de seguridad web que permite a un atacante interferir con las consultas que una aplicación reaqliza en su base de datos.

En algunas situaciones, un atcante puede escalar un ataque de inyeccion de SQL para comprometer el servidor subyacente u otra infraestruc

```sql
SELECT * FROM products WHERE category = 'Gifts' OR 1=1--' AND released = 1
````
