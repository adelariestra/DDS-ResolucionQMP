# QMP Iteración 7
---
## Notas
GET no lleva body, por su semántica no tiene sentido.

Recurso del que puede haber varias instancias siempre va en plural

Códigos de respuesta se utilizan los default de HTTP.

Bodys de respuestas los modelamos nosotros

## Como usuarie de QueMePongo quiero ver todas las prendas que tengo en mi guardarropas desde el navegador para poder administrarlas

Ruta: GET 'https://macowins-server.herokuapp.com/prendas'

Body:
```json
```

Código respuesta:
- 200 : Prendas encontradas
- 404 : ruta no encontrada
- 500 : Server Error

Body respuesta:
```Json
[
    {
    "id_prenda": 155,
    "tipo": "pantalon",
    "material": "algodón",
    "color": "255,255,255"
  },
    {
    "id_prenda": 156,
    "tipo": "pantalon",
    "material": "algodón",
    "color": "255,255,255"
  },
    {
    "id_prenda": 157,
    "tipo": "pantalon",
    "material": "algodón",
    "color": "255,255,255"
  },
  ...
]
```

### Notas y Correcciones
#### Ruta
GET /prendas
Mal, **no se a que guardarropas apunto**, me entrega todas las prendas de todos los guardarropas.

GET /usuario/:idUsuario/guardarropas/:idGuardarropas/
Mal, guardarropas es un contenedor de prendas, no una lista de prendas. Devuelve nombre, id, prendas, etc. 

GET /usuario/:idUsuario/guardarropas/:idGuardarropas/prendas
Bien si quiero permitir hacer consultas de otros usuarios que no sean el logueado  

GET guardarropas/:idGuardarropas/prendas
Bien si solo quiero ver el del usuario loggueado, usuario puede omitirse de la URL, se utiliza el de la sesión.

GET /usuario/me/guardarropas/:idGuardarropas/prendas
Otra opción para usar el current

nota: hay que preguntar si se usa del current o se permite otros. Sino indicar qué se asume.

#### Body Request
Bien

#### Código
200 - Ok
404 - 
401 - No hay usr logueado
403 - User no tiene permisos

#### Body response
Bien

## Como usuario de QueMePongo, quiero crear una prenda desde el navegador
Ruta: POST 'https://macowins-server.herokuapp.com/prendas'

Body:
```json
{
	"id_prenda": 155,
	"tipo": "pantalon",
	"material": "algodón",
	"color": "255,255,255"
}
```

Código respuesta:
- 200 : OK
- 404 : ruta no encontrada
- 500 : Server Error

Body respuesta:
```json 
```

### Notas y Correcciones
#### Rutas
POST /prendas
Tiene sentido cuando las prendas son independientes de los guardarropas y pueden no estar en ninguno.

POST /guardarropas/:idGuardarropas/prendas
Tiene sentido especificar el guardarropas porque las prendas siempre pertenecen a uno.

Cuál uso depende de la naturaleza de la relación.
**Composición vs Agregación**
eg: tiene sentido prenda sin guardarropas? Siempre pertenece a uno solo? Sus ciclos de vida estan conectados?

En este caso, la correcta es `POST /guardarropas/:idGuardarropas/prendas`

#### Body Request
ID no va en el post, lo asigna el modelo

#### Código
200 - Creado
404 - 
400 - Algun dato esta mal
401 - No hay usr logueado
403 - User no tiene permisos
...

#### Body response
201
{
  id: < id asignado >
  ... demás datos que se enviaron
}


## Como usuarie de QueMePongo quiero ver una prenda en particular que tengo en mi guardarropas para poder editarla
Ruta: GET 'https://macowins-server.herokuapp.com/prendas?id_prenda=< id_prenda >'

Body:
```json
```

Código respuesta:
- 200 : OK, prenda encontrada
- 404 : Prenda no encontrada

Body respuesta:
```json 
{
    "id_prenda": 155,
    "tipo": "pantalon",
    "material": "algodón",
    "color": "255,255,255"
}

```

### Notas y Correcciones
#### Rutas
GET /prendas/:id
Opción mas simple y concisa. Asume que la prenda es una entidad y hay un repositorio donde puedo buscarlas por id.

GET /guardarropas/:idGuardarropas/prendas/:idPrendas
Mantiene las URIs más consistenetes a como venimos trabajando.

nota: ambas son razonables.


## Como usuarie de QueMePongo, quiero poder eliminar una prenda de mi guardarropas
Ruta: DELETE 'https://macowins-server.herokuapp.com/prendas?id_prenda=< id_prenda >'

Body:
```json
```

Código respuesta:
- 200 : Prenda encontrada y eliminada
- 404 : Prenda no encontrada
- 500: Server error

Body respuesta:
```json 
```
### Notas y Correcciones
#### Rutas
DELETE /prendas/:idPrendas
DELETE /guardarropas/:idGuardarropas/prendas/:idPrendas

Misma situación que el req anterior.


## Como usuarie de QueMePongo, quiero poder ver mis eventos para administrarlos
Ruta: GET 'https://macowins-server.herokuapp.com/eventos'

Body:
```json
```

Código respuesta:
- 200 : OK, Eventos encontradas
- 404 : ruta no encontrada
- 500 : Server Error

Body respuesta:
```json
[{
  "id_evento": 301550,
    ... info del evento
},
...
]
```
### Notas y Correcciones
#### Rutas
GET /eventos
Asume que los eventos pertenecen a un usuario y siempre usamos el del usuario

GET /usuario/:id/eventos
Me deja ver los eventos de cualquier usuario. Eg: vista de administrador

## Como usuarie de QueMePongo, quiero poder abrir las sugerencias de prendas para un evento  en mi navegador
Ruta: GET 'https://macowins-server.herokuapp.com/sugerencias?id_evento=< id_evento >'

Body:
```json
```

Código respuesta:
- 200 : OK, sugerencias encontradas
- 404 : ruta no encontrada
- 500 : Server Error

Body respuesta:
```json
[
    {
    "id_prenda": 155,
    "tipo": "pantalon",
    "material": "algodón",
    "color": "255,255,255"
  },
    {
    "id_prenda": 156,
    "tipo": "pantalon",
    "material": "algodón",
    "color": "255,255,255"
  },
    {
    "id_prenda": 157,
    "tipo": "pantalon",
    "material": "algodón",
    "color": "255,255,255"
  },
  ...
]
```
### Notas y Correcciones
#### Rutas
GET /eventos/:id/sugerencias
Asume que son del usuario logueado
No tiene sentido una sugerencia sin un evento

#### Body Request

#### Código

#### Body response


---
## TODO
---
- [X] Armar rutas REST (URI + Método)
- [X] Esbozar qué información se enviaría en el body (por ej, en un JSON)
- [X] Pensar códigos de respuesta posibles para cada operación
- [X] Esbozar bodies de respuesta posibles

---