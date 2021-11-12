# Herramientas de exportación/importación de documentos en MongoDB

Exportar datos

## ¿Donde conseguir las herramientas?

Disponibles en https://www.mongodb.com/try/download/database-tools

Y la mejor ubicación es en la misma carpeta de binarios de los ejecutables mongo
para así aprovechar la path. En Window:

C:\Program Files\MongoDB\Server\5.0\bin

## mongoexport

Exporta los docs en formato json (por defecto) ó csv

Se ejecuta con el comando del mismo nombre de la herramienta seguido de las siguientes opciones:

mongoexport

--host <dirección-servidor> (si no se indica ningún endpoint se usa localhost por defecto)
--uri <uri-servidor>

--port <puerto>

--db <base-datos>

--collection <colección>

--fields=<campo1,campo2,campo3...>

--type=<json | csv> (si no se indica se usa json por defecto)

--noHeaderLine (para la exportación en csv)

--out=<ruta/nombre-archivo.json | csv>

Desde cualquier terminal (si tenemos el ejecutable mongoexport en la carpeta con path)

```
mongoexport --port 27017 --db=maraton --collection=runners --fields=name,surname1,age --out=runners.json

mongoexport --port 27017 --db=maraton --collection=runners --fields=name,surname1,age --type=csv --out=runners.csv

```

## Mongoimport

Importar documentos desde archivos json, csv ó tsv

mongoimport

--host <dirección-servidor> (si no se indica ningún endpoint se usa localhost por defecto)
--uri <uri-servidor>

--port <puerto>

--db <base-datos>

--collection <colección>

--type=<json | csv> (si no se indica se usa json por defecto)

--file=<ruta>/<nombre-archivo>.json | .csv | .tsv

--mode=<insert | upsert | merge> (si no se indica se usa insert por defecto) 

--upsertFields=<campo1,campo2,...>

Por ejemplo

Nos creamos un archivo clientes.json con unos registros

{"_id": 1, "nombre": "Juan"}
{"_id": 2, "nombre": "Sara"}
{"_id": 3, "nombre": "Raquel"}
{"_id": 4, "nombre": "Laura"}
{"_id": 5, "nombre": "Jorge"}

Y ahora importamos:

mongoimport --port 27017 --db="tienda" --collection="clientes" --file=clientes/clientes.json --type=json

Comprobamos

mongo

use tienda

db.clientes.find()
{ "_id" : 2, "nombre" : "Sara" }
{ "_id" : 3, "nombre" : "Raquel" }
{ "_id" : 4, "nombre" : "Laura" }
{ "_id" : 5, "nombre" : "Jorge" }
{ "_id" : 1, "nombre" : "Juan" }

Si modificamos el archivo clientes.json

{"_id": 1, "nombre": "Juan"}
{"_id": 2, "nombre": "Sara"}
{"_id": 3, "nombre": "Raquel"}
{"_id": 4, "nombre": "Laura"}
{"_id": 5, "nombre": "Jorge"}
{"_id": 6, "nombre": "Carlos"}

Si volvemos a lanzar el comando

mongoimport --port 27017 --db="tienda" --collection="clientes" --file=clientes/clientes.json --type=json

Produce 5 errores porque los documentos con _id de 1 a 5 ya existian, el 6 si lo importa

Pero si añadimos la opción --mode upsert

mongoimport --port 27017 --db="tienda" --collection="clientes" --file=clientes/clientes.json --type=json --mode=upsert

Entonces ya no devuelve errores, si encuentra el _id lo modifica y si no lo encuentra lo crea. Si cambiamos de nuevo.

{"_id": 1, "nombre": "Juan"}
{"_id": 2, "nombre": "Sara"}
{"_id": 3, "nombre": "Gonzalo"} 
{"_id": 4, "nombre": "Lucía"} // Este se actualizaría porque ha cambiado los valores
{"_id": 5, "nombre": "Jorge"}
{"_id": 6, "nombre": "Carlos"}
{"_id": 7, "nombre": "David"} // Este se inserta porque con el _id no había ningún otro documento

Y repetimos una vez más

mongoimport --port 27017 --db="tienda" --collection="clientes" --file=clientes/clientes.json --type=json --mode=upsert

 2 document(s) imported successfully. 0 document(s) failed to import.

Además de _id, se puede controlar el modo upsert con la opción --upsertFields