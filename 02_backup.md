# Herramientas de backup

Además de las herramientas de backup habituales para sistemas de bases de datos (de las cuales en
la documentación de mongo nos indican las consideraciones de uso), incorpora una herramienta nativa que
es mongodump.

## mongodump (No tira el oplog)

Realiza backup a nivel de base de datos o colección

Desde cualquier terminal

mongodump

--host <dirección-servidor> (si no se indica ningún endpoint se usa localhost por defecto)
--uri <uri-servidor>

--port <puerto>

--db <base-datos>

--collection <colección>

--out=<ruta>

Por ejemplo

mongodump --port 27017 --db=maraton --out="backup/bck12112021"

## mongorestore

Restaura bases de datos o colecciones a partir de los backup realizados con mongodump

--host <dirección-servidor> (si no se indica ningún endpoint se usa localhost por defecto)
--uri <uri-servidor>

--port <puerto>

--db <base-datos>

--collection <colección>

<ruta-datos-dump>

Por ejemplo

Nos conectamos con la shell y borramos la colección runners

use maraton

db.runners.drop()

Para restaurar, desde la terminal

mongorestore --port 27017 --db=maraton --collection=runners backup/bck12112021