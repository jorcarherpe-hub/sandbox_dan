# 1. Introducción a la base de datos Redis:
---
## 1.1. Qué es la base de datos seleccionada:

Redis (acrónimo de Remote Dictionary Server) es un motor de almacenamiento de datos en memoria, de código abierto y extremadamente rápido. Se define como un servidor de estructuras de datos, ya que permite almacenar no solo cadenas de texto, sino también listas, conjuntos y mapas. En 2026, sigue siendo la solución preferida para aplicaciones que requieren latencias de respuesta menores a un milisegundo.

## 1.2. Tipo de base de datos:

Redis es una base de datos NoSQL de tipo Clave-Valor (Key-Value).
Sin embargo, es importante destacar que es "multimodelo" en la práctica, ya que permite comportamientos de:
Documental: Mediante el módulo RedisJSON.
Geospacial: Para índices de coordenadas.
Series temporales: Para métricas de monitoreo.
Distribuida: Gracias a su capacidad nativa de particionamiento (sharding)

## 1.3. Breve contexto de creación:

El problema: En 2009, Salvatore Sanfilippo (su creador) intentaba mejorar la escalabilidad de un analizador de logs en tiempo real llamado LLOOGG. Las bases de datos relacionales tradicionales no podían manejar la carga de escritura constante y las consultas frecuentes sin degradar el rendimiento del disco.
Por qué se desarrolló: Se creó para eliminar el "cuello de botella" del disco duro. Redis nació con la filosofía de mantener todos los datos en la memoria RAM para ofrecer una velocidad de procesamiento que las bases de datos en disco simplemente no podían alcanzar.

## 1.4. Arquitectura general:

Redis utiliza principalmente una arquitectura Cliente-Servidor:
Modelo de proceso único: Tradicionalmente, Redis utiliza un modelo de un solo hilo para ejecutar los comandos, lo que garantiza la atomicidad y evita problemas de concurrencia complejos (aunque en versiones recientes ha introducido hilos auxiliares para tareas de red y limpieza).
Distribuida (Redis Cluster): Permite distribuir los datos entre múltiples nodos de forma transparente, facilitando la alta disponibilidad y el escalado horizontal.
Persistencia: Aunque trabaja en RAM, utiliza archivos RDB (copias de seguridad) y AOF (registros de transacciones) para recuperar datos tras un reinicio.

## 1.5. Lenguaje(s) de consulta y herramientas habituales:

A diferencia de las bases de datos relacionales, Redis no utiliza SQL.
Lenguaje de consulta: Utiliza un conjunto propio de comandos específicos (como SET, GET, HGETALL, ZADD). Estos comandos son atómicos y están optimizados para cada tipo de estructura de datos.
Herramientas habituales:
Redis CLI: Interfaz de línea de comandos estándar.
Redis Insight: La herramienta gráfica (GUI) oficial para visualizar y gestionar datos de manera intuitiva. Puedes descargarla en el sitio oficial de Redis Insight.
Bibliotecas (Clients): Posee conectores oficiales para casi todos los lenguajes modernos (Python, Node.js, Java, Go, C#).
Redis Stack: Un conjunto de extensiones que añaden capacidades de búsqueda avanzada y consulta tipo JSON.

---

# 2. Justificación de uso y casos ideales:

## 2.1 Tipo de aplicaciones donde se utiliza:

Redis es habitual en aplicaciones que requieren:
-Caché de alto rendimiento (respuesta < ms): páginas web, microservicios, API gateways.
-Sesiones y tokens (session store): almacenamiento de sesiones con TTL.
-Colas y sistemas de mensajería ligeros: usando listas (LPUSH/BRPOP) o streams (XADD, XREAD).
-Leaderboard y métricas en tiempo real: sorted sets (ZADD, ZRANGE) para rankings.
-Contadores y rate limiting: incrementos atómicos (INCR, INCRBY) y expiración.
-Pub/Sub en arquitecturas de eventos (notificaciones en tiempo real).
-Persistencia parcial / cache-aside en frontales para bases de datos más lentas.

## 2.2 Tipo de datos que gestiona mejor:

Redis no es solo clave-valor simple. Gestiona eficientemente:
-Strings (JSON serializado, tokens, contadores).
-Hashes (objetos: usuario:{id} -> campos).
-Lists (colas FIFO/LIFO).
-Sets y Sorted Sets (conjuntos sin orden y con orden por score).
-Streams (cola de eventos con id y consumo por grupos).
-Bitmaps, HyperLogLog (cardinalidad), Geospatial (GEORADIUS), Bloom filters (si se instala módulo).
 Ideal para datos de acceso frecuente y de tamaño moderado por clave (aunque soporta valores grandes).

## 2.3 Volumen de datos esperado:

Redis mantiene datos en memoria (RAM). Por tanto el volumen práctico depende de la cantidad de RAM disponible y del tamaño por clave.
Casos típicos:
-Caché de resultados: decenas de GB en clusters para sitios de alto tráfico.
-Leaderboards: cientos de miles a millones de items en sorted set, siempre que haya RAM suficiente.
-Para volúmenes de datos muy grandes (terabytes) se emplea Redis Cluster y/o se almacena en otras DB (persistencia en disco no sustituye la necesidad de RAM).

## 2.4 Número de usuarios o accesos simultáneos:

Redis puede manejar decenas de miles a cientos de miles de operaciones por segundo en hardware modesto; en infraestructuras optimizadas y con clustering puede llegar a millones de ops/s.
Además Redis es single-threaded por instancia para el procesamiento de comandos (aunque I/O y algunas operaciones internas usan threads), por lo que la escalabilidad horizontal (replicas, shards con Cluster) es la estrategia principal para aumentar concurrencia.

## 2.5 Comparación conceptual con otras bases de datos:

**¿Por qué usar Redis y no MySQL/PostgreSQL?**:

-Velocidad: Redis trabaja en memoria y es muchísimo más rápido para lecturas/escrituras simples y operaciones atómicas (contadores, colas, sets).
-Estructuras de datos avanzadas: operaciones nativas en sorted sets, bitmaps, hyperloglog, etc., que serían costosas en SQL.
-Simplicidad para casos transitorios: TTL nativo, ideal para caché y sesiones.

**¿Qué pasaría si se usara una base de datos inadecuada?**:

-Usar MySQL para una leaderboard muy activa provoca latencia (consultas con ORDER BY y actualizaciones frecuentes) y bloqueos.
-Usar una base relacional como caché incrementa coste (I/O disco), reduce throughput, y complica la expiración automática.
-Usar solo Redis para datos transaccionales críticos sin replicación/persistencia configurada puede llevar a pérdida de datos ante caídas si no se toman medidas.

---

# 3. Ventajas y desventajas

## 3.1. Ventajas:

-Rendimiento extremo: Tiene un muy buen rendimiento. Ideal para caché, sesiones, y datos que necesitan baja latencia.
-Fcil de usar: No es complicado de usar, hay herramientas para casi todos los programas y hay clientes para casi cualquier lenguaje.
-Escalabilidad: Se puede ampliar para manejar montones de datos y tráfico.
-Flexibilidad del modelo de datos: No solo guarda datos simples; puedes guardar listas, conjuntos....
-Integración con aplicaciones: Funciona bien con muchos frameworks y lenguajes de programación.

## 3.2. Desventajas:

-Escenarios donde no es recomendable: No es para datos críticos (bancos, transacciones monetarias...). Tampoco para datasets enormes si no tienes mucha RAM.
-Limitaciones técnicas: Todo en RAM significa (coste de memoria). Y si se cae, se pierden datos.
-Curva de aprendizaje: Para sacarle el todo el potencial, toca entender sus estructuras de datos y comandos.
-Problemas de mantenimiento/despliegue: Si se apaga el servidor y no lo configuras bien, se pueden perder cosas.
-Riesgos de mal uso: Usarlo sin saber = problemas.
---

# 4. Supuesto Práctico:

**Sistema de Telemetría y "Live Bidding" para una Plataforma de E-Sports:**

### 1. Descripción del escenario:
-Imagina una plataforma global de torneos de E-Sports (como League of Legends o Valorant) llamada "ProStream Arena". La plataforma necesita gestionar dos funciones críticas en tiempo real para millones de usuarios simultáneos:
-Marcadores en vivo (Leaderboards): Ranking global de jugadores que se actualiza cada segundo según las muertes, asistencias y objetivos cumplidos en la partida.
Subastas de "Drops" en tiempo real: Durante las finales, se liberan objetos digitales limitados (skins, pases) que los espectadores pueden reclamar o subastar en ventanas de tiempo de apenas 30 segundos.

### 2. Contexto técnico:
-Volumen de datos: 5 millones de usuarios activos concurrentes durante un evento "Major".
-Carga de trabajo: 500,000 operaciones de escritura por segundo (actualizaciones de estadísticas de juego) y millones de lecturas para mostrar los marcadores en las apps móviles de los fans.
-Requisito de latencia: Los datos deben reflejarse en la pantalla del usuario en menos de 100 milisegundos desde que ocurre el evento en el servidor de juego.

### 3. Implementación en Redis:
-Para resolver este caso, utilizaríamos las siguientes estructuras de Redis:
-Sorted Sets (ZSETs): Para el ranking de jugadores. Redis ordena automáticamente a los jugadores por su puntuación (score) de forma eficiente (𝑂(log𝑁)).
-Pub/Sub o Streams: Para notificar instantáneamente a todos los clientes cuando se abre una nueva subasta de "Drops".
-Hashes: Para almacenar los perfiles de sesión de los usuarios (ID, inventario rápido, equipo favorito) con acceso instantáneo.
-Keys con Expiración (TTL): Para que las ofertas de las subastas desaparezcan automáticamente cuando termine el tiempo de 30 segundos.

### 7. Justificación: ¿Por qué Redis es la base de datos ideal?
-Redis es la única opción viable para este supuesto por las siguientes tres razones:
-Velocidad de procesamiento en RAM: En un entorno de subastas y juegos en vivo, el acceso a disco (propio de bases de datos como MySQL o MongoDB) generaría "lag" o cuellos de botella. Redis, al operar totalmente en memoria, garantiza latencias de microsegundos.
-Estructuras de datos nativas para rankings: En una base de datos relacional, calcular el "Top 10" de 5 millones de filas constantemente requeriría consultas ORDER BY muy costosas. En Redis, el Sorted Set mantiene la lista ya ordenada en tiempo real; obtener el ranking es una operación casi gratuita para el procesador.
-Manejo de picos de tráfico (Escalabilidad): Durante el clímax de una partida, el tráfico puede triplicarse en segundos. Con Redis Cluster, la plataforma puede distribuir la carga entre varios nodos sin interrumpir el servicio, asegurando que ningún usuario experimente retrasos en sus pujas o en la visualización de los puntos.
---
#5. Análisis de requisitos:

## - Funcionales:
  
-Guardar y buscar datos rápido (tipo "clave-valor"; strings, hashes, listas...).
-Actualizar o eliminar datos existentes.
-Buscar datos por clave (o patrones).
-Usar estructuras como sets, sorted sets para consultas específicas.
-Publicar o suscribirse a canales para notificaciones.
-Información a manejar:
-Datos temporales (ej: sesiones de usuarios, cosas en caché...).

## - No funcionales:

-Velocidad: Responder muy rápido (ideal para cosas en tiempo real).
-Guardar datos: Puede perder datos si se apaga (a menos que se configure bien).
-Disponibilidad: Funcionar siempre (con copias de seguridad + failover (Redis Sentinel/Cluster) por si falla algo).
-Crecimiento: Poder manejar más carga si el sistema crece.
-Seguridad: Controlar quién accede y cifrar datos si es necesario.
---

# 6. Diseño del modelo de datos:

Redis no utiliza tablas ni esquemas fijos, ya que es una base de datos clave-valor en memoria que permite estructurar los datos mediante tipos nativos de estructuras (strings, hashes, listas, conjuntos, etc.).
 En lugar de diseñar tablas, el diseño se basa en la convención de nombres de claves (namespaces) y en la estructura de los valores.
A continuación se presenta el diseño propuesto para el supuesto práctico del juego multijugador online.

## 6.1. Estructuras principales y propósito:

| Entidad                      | Tipo de estructura Redis | Propósito principal                                           |
| ---------------------------- | ------------------------ | ------------------------------------------------------------- |
| Sesiones de usuario          | `Hash` + `TTL`           | Almacenar datos de sesión y expirar automáticamente           |
| Leaderboard global           | `Sorted Set`             | Mantener puntuaciones ordenadas de los jugadores              |
| Cola de tareas               | `List` o `Stream`        | Procesar tareas asíncronas (envíos de correo, notificaciones) |
| Perfil de usuario (opcional) | `Hash`                   | Datos persistentes básicos del jugador                        |
| Contadores globales          | `String`                 | Contadores atómicos para estadísticas o IDs                   |



## 6.2. Convención de nombres (claves):
En Redis, el diseño de la clave es tan importante como la estructura de datos.
Se recomienda una convención jerárquica basada en el uso de dos puntos (:) para separar niveles:
 
~~~
<recurso>:<id>[:atributo]
~~~


### Claves del sistema:

| Clave ejemplo            | Descripción                                           |
| ------------------------ | ----------------------------------------------------- |
| `session:{session_id}`   | Hash que contiene la información de una sesión activa |
| `leaderboard:global`     | Sorted set con puntuaciones de todos los usuarios     |
| `queue:tasks`            | Lista con tareas pendientes                           |
| `user:{user_id}:profile` | Hash con los datos básicos del jugador                |
| `stats:games_played`     | String contador atómico de partidas jugadas           |



## 6.3. Modelado de datos por estructura:

### A. Sesiones de usuario:
-Tipo: Hash
-Clave: session:{session_id}

### Campos principales:

| Campo        | Tipo             | Descripción              |
| ------------ | ---------------- | ------------------------ |
| `user_id`    | String           | ID del usuario asociado  |
| `created_at` | String (ISO8601) | Fecha y hora de creación |
| `ip`         | String           | Dirección IP de conexión |
| `device`     | String           | Dispositivo del usuario  |

-Claves primarias / identificadores: session_id (único por sesión).
-Expiración (TTL): 1800 segundos (30 minutos), para eliminar sesiones inactivas automáticamente.


-Ejemplo (redis-cli):

~~~
HSET session:abc123 user_id "42" created_at "2026-02-01T12:00:00Z" ip "10.0.0.1"
EXPIRE session:abc123 1800
~~~


### B. Leaderboard global (puntuaciones):

-Tipo: Sorted Set
-Clave: leaderboard:global
-Elementos:
-Miembro: user:{user_id}
-Score: puntuación del jugador (float o entero)
-Claves primarias: user_id (único por jugador).
-Orden natural: por score ascendente o descendente.
-Índice implícito: el propio score actúa como índice ordenado.


Ejemplo (redis-cli):
~~~
ZADD leaderboard:global 1520 user:42
ZADD leaderboard:global 1800 user:35
ZRANGE leaderboard:global 0 -1 WITHSCORES
~~~

### C. Cola de tareas:
-Tipo: List (cola FIFO simple) o Stream (si se necesita persistencia y grupos de consumidores).
-Clave: queue:tasks
-Elementos: JSON o texto con el detalle de la tarea.

| Campo     | Descripción                                    |
| --------- | ---------------------------------------------- |
| `task`    | Tipo de tarea (ej. `send_email`, `notify_win`) |
| `user_id` | Usuario afectado                               |
| `data`    | Contenido adicional                            |


Ejemplo con List:

~~~
LPUSH queue:tasks '{"task":"send_email","user_id":42,"data":"welcome"}'
BRPOP queue:tasks 0
~~~

~~~
Ejemplo con Stream (más avanzado):
XADD queue:tasks * task send_email user_id 42 data "welcome"
XREAD COUNT 1 STREAMS queue:tasks 0
~~~

### D. Perfil de usuario (persistente):

-Tipo: Hash
-Clave: user:{user_id}:profile
-Campos principales:

| Campo        | Tipo    | Descripción              |
| ------------ | ------- | ------------------------ |
| `username`   | String  | Nombre de usuario        |
| `email`      | String  | Correo electrónico       |
| `country`    | String  | País                     |
| `level`      | Integer | Nivel actual del jugador |
| `last_login` | String  | Último acceso ISO8601    |


-Ejemplo (redis-cli):

~~~
HSET user:42:profile username "Neo" email "neo@matrix.io" country "Spain" level 12 last_login "2026-02-01T12:00:00Z"
~~~

### E. Contadores globales (estadísticas):
-Tipo: String
-Clave: stats:games_played
-Uso: mantener contadores atómicos con operaciones INCR o INCRBY.

-Ejemplo:
~~~
INCR stats:games_played
~~~

## 6.4. Relaciones entre entidades:

-Redis no maneja relaciones directas (no hay joins), pero se pueden representar referencias lógicas entre claves:
session:{session_id} → user_id → referencia al usuario.

-leaderboard:global contiene miembros user:{user_id} → referencia a perfiles.

-Las colas (queue:tasks) pueden incluir referencias a usuarios mediante user_id.

-Estas relaciones son implícitas y gestionadas por la aplicación, no por Redis.

## 6.5. Índices básicos (si aplica):

Redis no usa índices secundarios automáticos, pero algunos tipos de datos actúan como índices:
| Tipo de estructura | Índice implícito                       |
| ------------------ | -------------------------------------- |
| `Sorted Set`       | orden por `score` (ranking natural)    |
| `Hash`             | búsqueda directa por campo (constante) |
| `List` / `Stream`  | orden secuencial (FIFO)                |
| `Set`              | membership test (`SISMEMBER`) O(1)     |


-En caso de necesitar búsqueda avanzada (por ejemplo, por país o nivel), se pueden crear índices manuales mediante conjuntos auxiliares:
Ejemplo: índice por país:
~~~
SADD index:country:Spain user:42
SADD index:country:Spain user:35
~~~

Esto permite consultas del tipo:
~~~
SMEMBERS index:country:Spain
~~~

## 6.6. Explicación del diseño:

-El diseño está optimizado para:
-Velocidad: todos los accesos son O(1) u O(log n), y los datos residen en memoria.
-Simplicidad: se basa en claves autoexplicativas y estructuras nativas.
-Escalabilidad: cada tipo de dato puede repartirse fácilmente en nodos de un cluster Redis (sharding por prefijo).
-Consistencia funcional: las relaciones entre entidades se gestionan desde la aplicación, evitando joins costosos.
-Persistencia parcial y TTLs automáticos: las sesiones expiran solas y los datos persistentes se guardan con hashes y AOF habilitado.


Este modelo es flexible y extensible, pudiendo añadirse más claves (por ejemplo, user:{id}:inventory, match:{id}:state) sin modificar un esquema predefinido.

---

# 7. Implementación

## Supuesto práctico (resumen breve para contextualizar la implementación)
**Aplicación:** juego multijugador online con:
- (a) store de sesiones (tokens, expiración),
- (b) leaderboard global (puntuaciones),
- (c) cola de eventos para tareas asíncronas.  

Redis será la capa de datos en memoria para estas funcionalidades.  
(Esto justifica el uso de TTL, sorted sets, listas/streams y operaciones atómicas.)


## 7.1 Instalación de Redis

### Opción A — Usar Docker (recomendado para desarrollo y despliegue consistente):

**Ejecutar Redis con persistencia AOF y contraseña (dev):**

~~~~
docker run -d --name redis \
  -p 6379:6379 \
  -v redis-data:/data \
  -e REDIS_PASSWORD=myStrongP@ssw0rd \
  redis:7 redis-server --requirepass myStrongP@ssw0rd --appendonly yes
~~~~

**Notas:**

--appendonly yes activa AOF (append-only file) para mayor durabilidad.

-El volumen redis-data persiste los datos.

### Opción B — Ubuntu / Debian (apt):
~~~~
sudo apt update
sudo apt install redis-server -y

# Habilitar persistencia AOF y exigir contraseña:
# editar /etc/redis/redis.conf
# cambiar:
# appendonly no -> appendonly yes
# requirepass <empty> -> requirepass myStrongP@ssw0rd

sudo systemctl restart redis-server
~~~~

### Opción C — macOS (Homebrew):

~~~~
brew install redis
# editar /usr/local/etc/redis.conf si hace falta
redis-server /usr/local/etc/redis.conf
~~~~

### Opción D — Windows

Usar Docker o la versión Windows Subsystem for Linux (WSL) y seguir la instalación de Linux.
(No se recomienda la versión no oficial).

**-Recomendaciones de seguridad y producción:**
-Usar autenticación (requirepass) y redes privadas (VPC).
-Deshabilitar accesos directos desde Internet (poner detrás de firewall o redis-proxy).
-Usar Redis ACLs (a partir de versiones recientes) para controlar comandos y usuarios.
-Monitoreo: REDIS_METRICS (exporters), logs y alertas.
-Configurar replicación y Sentinel / Cluster para alta disponibilidad.

### 7.2 Creación de la base de datos para el supuesto seleccionado

-Redis no tiene “crear base de datos” como RDBMS; hay 16 bases lógicas por defecto (0..15) seleccionables con SELECT, pero la práctica recomendada es usar la DB 0 y namespaces de claves.
-Convención de keys (ejemplo):

~~~~
session:{session_id}       -> Hash con datos de sesión (TTL)
user:{user_id}:profile     -> Hash con perfil (si se usa)
leaderboard:global         -> Sorted set con score por user_id
queue:tasks                -> List o Stream para colas
~~~~

**Ejemplo de creación (redis-cli):**

~~~~
# Conectar (si hay password)
redis-cli -a myStrongP@ssw0rd

# Crear sesión (hash) con TTL 30 minutos
HSET session:abc123 user_id 42 created_at 1675200000
EXPIRE session:abc123 1800

# Añadir puntuación al leaderboard
ZADD leaderboard:global 1520 user:42

# Push task a la cola
LPUSH queue:tasks '{"task":"send_email","user":42}'
~~~~

### 7.3 CRUD básico: insertar, leer/filtrar, modificar y borrar (por estructura)

A continuación se incluyen ejemplos con redis-cli y también con Python (redis-py).

#### 7.3.1 Operaciones con Strings (clave-valor simple)

**redis-cli:**

~~~~
# Crear / Insertar
SET welcome "Hola, Redis"

# Leer
GET welcome

# Modificar
SET welcome "Bienvenido"

# Borrar
DEL welcome
~~~~

**Python:**
~~~~
import redis
r = redis.Redis(host='localhost', port=6379, password='myStrongP@ssw0rd')

r.set('welcome', 'Hola, Redis')
print(r.get('welcome'))   # b'Hola, Redis'
r.set('welcome', 'Bienvenido')
r.delete('welcome')
~~~~

### 7.3.2 Operaciones con Hashes (modelar objetos) 

Ejemplo: sesión de usuario

**redis-cli:**
~~~~
# Insertar/Crear (HSET admite múltiples campos)
HSET session:abc123 user_id "42" created_at "2026-02-01T12:00:00Z" ip "10.0.0.1"
# TTL
EXPIRE session:abc123 1800

# Leer todos los campos
HGETALL session:abc123

# Leer campo concreto
HGET session:abc123 user_id

# Modificar un campo
HSET session:abc123 ip "10.0.0.2"

# Borrar campo
HDEL session:abc123 ip

# Borrar la sesión (todo)
DEL session:abc123
~~~~
**Python:**
~~~~
r.hset('session:abc123', mapping={'user_id': '42', 'created_at': '2026-02-01T12:00:00Z', 'ip':'10.0.0.1'})
r.expire('session:abc123', 1800)
print(r.hgetall('session:abc123'))
r.hset('session:abc123', 'ip', '10.0.0.2')
r.delete('session:abc123')
~~~~

### 7.3.3 Operaciones con Lists (colas)

**redis-cli (cola de trabajo simple):**
~~~~
# Push una tarea (producer)
LPUSH queue:tasks '{"type":"email","user":42}'

# Consumidor: bloqueo esperando tarea (pop al final)
BRPOP queue:tasks 0   # bloquea hasta que haya un elemento

# Ver elementos recientes
LRANGE queue:tasks 0 9
~~~~

**Python (producer / consumer):**
~~~~

# Producer
r.lpush('queue:tasks', '{"type":"email","user":42}')

# Consumer (bloqueante)
_, task = r.brpop('queue:tasks', timeout=0)
print(task)
~~~~
### 7.3.4 Operaciones con Sets y Sorted Sets

**Sets (sin orden, membership):**

~~~~
SADD online_users 42
SADD online_users 43
SISMEMBER online_users 42
SMEMBERS online_users
SREM online_users 42
~~~~

**Sorted Sets (leaderboard):**

~~~~
# Añadir/actualizar score
ZADD leaderboard:global 1520 user:42
ZADD leaderboard:global 2000 user:43

# Obtener top N
ZRANGE leaderboard:global -10 -1 WITHSCORES

# Obtener rango por score
ZRANGEBYSCORE leaderboard:global 1500 2000 WITHSCORES

# Obtener rank de un usuario (0-index)
ZREVRANK leaderboard:global user:42
ZREVRANGE leaderboard:global 0 9 WITHSCORES

# Incrementar score atómicamente
ZINCRBY leaderboard:global 50 user:42

~~~~

**Python (Sorted Set):**

~~~~
r.zadd('leaderboard:global', {'user:42': 1520})
top10 = r.zrevrange('leaderboard:global', 0, 9, withscores=True)
~~~~

### 7.3.5 Streams (cola avanzada de eventos)
~~~~
# Añadir evento
XADD mystream * user 42 event "login"

# Lectura simple
XRANGE mystream - +

# Consumer group (lectura por grupo de consumidores)
XGROUP CREATE mystream mygroup $ MKSTREAM

# Consumidor lee mensajes pendientes
XREADGROUP GROUP mygroup consumer1 COUNT 10 STREAMS mystream >
~~~~

### 7.3.6 Operaciones atómicas y transacciones
~~~~
MULTI / EXEC: cola de comandos atómicos.
Lua scripting: operaciones complejas atómicas.

Ejemplo: aumentar score solo si nueva puntuación > anterior (Lua):

EVAL "local key=KEYS[1]; local member=ARGV[1]; local new=tonumber(ARGV[2]);
local cur=redis.call('ZSCORE', key, member);
if not cur or new>tonumber(cur) then
  return redis.call('ZADD', key, new, member)
else
  return 0
end" 1 leaderboard:global user:42 1800
~~~~

### 7.3.7 Filtrado / búsqueda de claves

SCAN (no bloqueante) para recorrer el keyspace sin afectar la latencia.

~~~~
SCAN 0 MATCH session:* COUNT 100


Evitar KEYS en producción (bloqueante).
~~~~

### 7.3.8 Backup y persistencia (RDB y AOF)
~~~~
SAVE y BGSAVE: generan snapshots RDB.

AOF (append-only file): registra operaciones; appendfsync configura durabilidad (everysec, always).

Recomendado: combinar AOF con RDB y usar réplicas para alta disponibilidad.
~~~~

**Ejemplo forzar snapshot:**
~~~~
BGSAVE
~~~~

Ejemplo completo: mini-implementación en Python (sesiones + leaderboard + cola)

**Requisitos previos:**
~~~~
pip install redis
~~~~

**Código:**
~~~~
import redis, json, time

r = redis.Redis(host='localhost', port=6379, password='myStrongP@ssw0rd', decode_responses=True)

# 1) Crear sesión
session_id = 'sess:abc123'
r.hset(session_id, mapping={'user_id': '42', 'created_at': time.time()})
r.expire(session_id, 1800)

# 2) Añadir puntuación al leaderboard solo si mejora
user_key = 'user:42'
new_score = 1800
script = """
local key=KEYS[1]; local member=ARGV[1]; local new=tonumber(ARGV[2]);
local cur=redis.call('ZSCORE', key, member);
if not cur or new>tonumber(cur) then
  return redis.call('ZADD', key, new, member)
else
  return 0
end
"""
r.eval(script, 1, 'leaderboard:global', user_key, new_score)

# 3) Push tarea
task = json.dumps({'type':'email','user':42,'body':'Hola'})
r.lpush('queue:tasks', task)

# 4) Consumidor (simplificado)
_, raw = r.brpop('queue:tasks', timeout=1)
if raw:
    payload = json.loads(raw)
    print('procesando', payload)
~~~~

**Operaciones administrativas básicas**
~~~~
# Ver estado general
INFO

# Ver uso de memoria
INFO memory

# Ver clientes conectados
CLIENT LIST

# Reconfigurar en caliente algunos parámetros
CONFIG SET
~~~~

**Ejemplos:**

~~~~
redis-cli -a myStrongP@ssw0rd INFO memory
redis-cli -a myStrongP@ssw0rd CONFIG GET appendonly
~~~~

-Despliegue en producción (puntos clave)

-Clustering (sharding): para escalabilidad horizontal; Redis Cluster fragmenta el keyspace.

-Sentinel: para failover automático (o soluciones gestionadas como Redis Enterprise, AWS ElastiCache Redis).

-Monitoreo: métricas de latencia, throughput y evictions.

-Política de eviction: cuando la memoria se llena (volatile-lru, allkeys-lru, etc.), configurable según necesidad.



