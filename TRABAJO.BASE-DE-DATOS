1. Introducción a la base de datos Redis
1.1. Qué es la base de datos seleccionada
Redis (acrónimo de Remote Dictionary Server) es un motor de almacenamiento de datos en memoria, de código abierto y extremadamente rápido. Se define como un servidor de estructuras de datos, ya que permite almacenar no solo cadenas de texto, sino también listas, conjuntos y mapas. En 2026, sigue siendo la solución preferida para aplicaciones que requieren latencias de respuesta menores a un milisegundo.
1.2. Tipo de base de datos
Redis es una base de datos NoSQL de tipo Clave-Valor (Key-Value).
Sin embargo, es importante destacar que es "multimodelo" en la práctica, ya que permite comportamientos de:
Documental: Mediante el módulo RedisJSON.
Geospacial: Para índices de coordenadas.
Series temporales: Para métricas de monitoreo.
Distribuida: Gracias a su capacidad nativa de particionamiento (sharding)
1.3. Breve contexto de creación
El problema: En 2009, Salvatore Sanfilippo (su creador) intentaba mejorar la escalabilidad de un analizador de logs en tiempo real llamado LLOOGG. Las bases de datos relacionales tradicionales no podían manejar la carga de escritura constante y las consultas frecuentes sin degradar el rendimiento del disco.
Por qué se desarrolló: Se creó para eliminar el "cuello de botella" del disco duro. Redis nació con la filosofía de mantener todos los datos en la memoria RAM para ofrecer una velocidad de procesamiento que las bases de datos en disco simplemente no podían alcanzar.
1.4. Arquitectura general
Redis utiliza principalmente una arquitectura Cliente-Servidor:
Modelo de proceso único: Tradicionalmente, Redis utiliza un modelo de un solo hilo para ejecutar los comandos, lo que garantiza la atomicidad y evita problemas de concurrencia complejos (aunque en versiones recientes ha introducido hilos auxiliares para tareas de red y limpieza).
Distribuida (Redis Cluster): Permite distribuir los datos entre múltiples nodos de forma transparente, facilitando la alta disponibilidad y el escalado horizontal.
Persistencia: Aunque trabaja en RAM, utiliza archivos RDB (copias de seguridad) y AOF (registros de transacciones) para recuperar datos tras un reinicio.
1.5. Lenguaje(s) de consulta y herramientas habituales
A diferencia de las bases de datos relacionales, Redis no utiliza SQL.
Lenguaje de consulta: Utiliza un conjunto propio de comandos específicos (como SET, GET, HGETALL, ZADD). Estos comandos son atómicos y están optimizados para cada tipo de estructura de datos.
Herramientas habituales:
Redis CLI: Interfaz de línea de comandos estándar.
Redis Insight: La herramienta gráfica (GUI) oficial para visualizar y gestionar datos de manera intuitiva. Puedes descargarla en el sitio oficial de Redis Insight.
Bibliotecas (Clients): Posee conectores oficiales para casi todos los lenguajes modernos (Python, Node.js, Java, Go, C#).
Redis Stack: Un conjunto de extensiones que añaden capacidades de búsqueda avanzada y consulta tipo JSON.

