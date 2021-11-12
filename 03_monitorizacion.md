# Monitorización

## Herramientas

mongostat

mongotop <segundos>

Disponibles en https://www.mongodb.com/try/download/database-tools

## Métodos

use admin

db.serverStatus() // Información general del servidor

A nivel de base de datos, disponemos de un resumen de estadísticas

use maraton

db.stats()

Operaciones en curso

db.currentOp()

db.runners.stats() // idem a serverStatus() a nivel de colección

Para replica set

rs.status()

## Profiler ¡Ojo certificacion!

El database profiler recolectar las información de las operaciones para ayudar a detectar las operaciones
y consultas ineficientes.

Se establece y configura a nivel de base de datos, pero también se puede establecer a nivel de instancia.

Dispone de los siguientes niveles:

- level 0. Desactivado (por defecto).
- level 1. Recolecta la información de las operaciones que superen el valor de slowms (100ms)
- level 2. Recolecta la información de todas las operaciones.

A nivel de servidor, como flag de mongod:

mongod --profile <nivel>

en el archivo de conf:

operationProfiling:
   mode: <off | slowOp | all>

Mas habitual a nivel de base de datos.

use maraton

db.getProfilingStatus() / Devuelve la configuración del profiler

db.setProfilingLevel(2) / Recibe el nuevo level

Una vez establecido el nivel 1 o 2, mongo comenzará a guardar en el profiler la información de 
las operaciones según el criterio del nivel. Guarda los datos en una colección system.profile de 
tipo capped.

Realizamos unas operaciones para comprobar.

db.runners.find({surname1: 'Novo'})

db.runners.find({age: {$gte: 18}})

Para comprobar los documentos del profiler

db.system.profile.findOne()

Se pueden crear consultas o agregaciones para obtener información de las operaciones
lentas:

db.system.profile.find( {op: "query"}, {millis: 1, ns: 1, "command.filter": 1, planSummary: 1})
                 .sort({millis: -1})
                 .pretty()

Si por ejemplo necesitamos saber el nº de consultas lentas para una determinada forma de consulta (por
ejemplo consultas con el campo surname1) podemos:

db.system.profile.find( {op: "query", "command.filter.surname1": /./}).count()