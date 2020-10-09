# QMP Iteración 7
---
## Notas
protocolo://dominio:puerto/ruta#fragmento?parametro1=valor1&parametro2=valor2

## Resolución
### Como usuarie de QueMePongo quiero ver todas las prendas que tengo en mi guardarropas desde el navegador para poder administrarlas

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

### Como usuario de QueMePongo, quiero crear una prenda desde el navegador
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

### Como usuarie de QueMePongo quiero ver una prenda en particular que tengo en mi guardarropas para poder editarla
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

### Como usuarie de QueMePongo, quiero poder eliminar una prenda de mi guardarropas
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

### Como usuarie de QueMePongo, quiero poder ver mis eventos para administrarlos
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

### Como usuarie de QueMePongo, quiero poder abrir las sugerencias de prendas para un evento  en mi navegador
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

## Correcciones Resolución

---
## TODO
---
- [X] Armar rutas REST (URI + Método)
- [X] Esbozar qué información se enviaría en el body (por ej, en un JSON)
- [X] Pensar códigos de respuesta posibles para cada operación
- [X] Esbozar bodies de respuesta posibles

---