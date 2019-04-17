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
