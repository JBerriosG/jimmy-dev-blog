---
title: 'Introducción a Springboot con Spring Data JPA'
subTitle: 'Simplifica el acceso a los datos en tus aplicaciones'
description: 'Lorem ipsum dolor sit amet'
pubDate: 'Sep 21 2024'
heroImage: '/blogs/spring-data-jpa/spring-data-jpa.webp'
category: 'dev'
---

En el mundo del desarrollo de aplicaciones Java, la interacción con bases de datos puede llegar a ser un proceso tedioso y propenso a errores. Crear conexiones, escribir consultas SQL manualmente, y gestionar transacciones son tareas que requieren mucho código repetitivo. Aquí es donde entran en juego Spring Boot y Spring Data JPA, dos poderosas herramientas que no solo simplifican este proceso, sino que también permiten a los desarrolladores enfocarse en la lógica de negocio en lugar de preocuparse por detalles técnicos complejos.

Spring Boot es un framework que facilita la creación de aplicaciones basadas en Spring de manera rápida, eliminando la necesidad de configuraciones extensas y complicadas. Con su enfoque en la "configuración por convención", Spring Boot proporciona un conjunto de herramientas que permiten a los desarrolladores arrancar proyectos con menos fricción. Todo lo que necesitas hacer es definir las dependencias correctas, y Spring Boot se encargará del resto, desde la configuración del servidor hasta la inyección de dependencias.

Por otro lado, Spring Data JPA es un módulo especializado de Spring que se integra con Java Persistence API (JPA) para manejar la interacción con bases de datos relacionales de manera sencilla. A través de una interfaz común, como JpaRepository, Spring Data JPA abstrae las operaciones básicas de base de datos (como crear, leer, actualizar y borrar registros) sin necesidad de escribir código SQL manualmente. Además, permite ejecutar consultas complejas de manera declarativa mediante nombres de método, reduciendo significativamente el esfuerzo de desarrollo.

Juntos, Spring Boot y Spring Data JPA proporcionan una solución integral para crear aplicaciones escalables y mantenibles que interactúan con bases de datos. Ya no es necesario escribir largos bloques de código repetitivo ni preocuparse por la configuración manual de entidades y conexiones; ambos frameworks lo manejan de manera automática y eficiente.

En este artículo, aprenderás cómo aprovechar la potencia de estos dos frameworks para crear una aplicación que interactúe con una base de datos utilizando entidades JPA, repositorios y controladores REST. Al final, tendrás una comprensión clara de cómo funcionan y cómo pueden agilizar el desarrollo de aplicaciones en tu día a día.

### Configuración del proyecto

Comenzaremos accediendo a la web de [Spring Initializr](https://start.spring.io/) para preparar nuestro proyecto inicial. Pueden seguir el ejemplo que puse en la imagen si así lo desean.

![Spring Initializr](/blogs/spring-data-jpa/spring-1.png)

Ahora pasaremos a lo más relevante para que el proyecto funcione: las dependencias. Agregaremos las siguientes dependencias:
````yml
- Spring Web: Proporciona el soporte necesario para 
crear APIs RESTful y manejar solicitudes HTTP.
- Spring Data JPA: Abstrae la capa de persistencia al 
simplificar las operaciones CRUD mediante JPA.
- Lombok: Genera automáticamente código repetitivo 
como getters, setters y constructores, mediante anotaciones
 como @Getter y @Setter.
- MySQL Driver: Contiene el conector JDBC para interactuar 
con bases de datos MySQL desde la aplicación.
- PostgreSQL Driver: Incluye el controlador JDBC para 
conectarse a bases de datos PostgreSQL.
- H2 Database: Proporciona una base de datos ligera en 
memoria, ideal para pruebas y desarrollo rápido.
````

Al terminar con la configuración inicial del proyecto en Spring Initializr le damos al botón Generate y se nos descargara un archivo .zip.

### Iniciando el proyecto

Comenzamos por abrir y descomprimir el zip que se nos descargo en el paso anterior. Abrimos nuestro IDE de confianza, y arrastramos la carpeta sobre este, en mi caso el que suelo usar y mas les recomiendo para trabajar con java es IntelliJ IDEA, pero ud pueden usar el que mas les acomode. Se les verá mas o menos asi:

![IntelliJ IDEA](/blogs/spring-data-jpa/spring-2.png)

Aquí debajo les dejare la configuración inicial de su archivo .properties que se encuentra en la carpeta resources.

![Archivo properties](/blogs/spring-data-jpa/spring-3.png)

Siguiendo las configuraciones de la imagen tendran su aplicacion de springboot conectada a una base de datos en memoria, al momento echar a correr la aplicacion de springboot podremos acceder a la interfaz grafica de H2 a traves del siguiente link [http://localhost:8080/h2-console](http://localhost:8080/h2-console), gracias a esto podremos ver las tablas y los datos de manera mas comoda.

Dentro de nuestro paquete principal, en mi caso seria com.jpa, crearemos 4 paquetes o carpetas nuevas:

````yml
- Entities
- Repositories
- Services
- Controllers
````
Así como se ve en la siguiente imagen:

![springboot estructura de carpetas](/blogs/spring-data-jpa/spring-4.png)

### Creando la funcionalidad del proyecto

Comenzaremos por crear nuestro primer archivo, que será la entidad o entity como se conoce en inglés. La REST API que desarrollaremos para este ejemplo será una API de monstruos, en la que podremos crear monstruos siguiendo la estructura que definamos para nuestra tabla en la base de datos. Al finalizar el proyecto, seremos capaces de crear un monstruo, obtener una lista de todos los monstruos almacenados en la base de datos, así como obtener listas filtradas por tipo de monstruo y por su lugar de origen. De este modo, no solo aprenderán las funciones básicas de una REST API, sino también cómo crear métodos personalizados para extraer datos específicos desde la base de datos.

#### 1.- Entity

Una Entity en Spring Data JPA es una clase que representa una tabla en la base de datos. Cada instancia de la clase se corresponde con una fila de esa tabla. Las entities contienen los atributos de los objetos que queremos almacenar, donde cada campo de la clase representa una columna en la tabla. Spring Data JPA se encarga de mapear automáticamente las relaciones entre la entidad y la base de datos, facilitando las operaciones de persistencia, como guardar, actualizar y eliminar datos.

En resumen, una Entity es la representación del modelo de datos en el código que se asocia directamente con una tabla en la base de datos. A continuación, se muestra cómo crear una entidad simple llamada MonsterEntity, que será mapeada a una tabla con columnas correspondientes a sus atributos.

![Spring Data Entity](/blogs/spring-data-jpa/spring-5.png)

##### Puntos a considerar del codigo de entity.

1.- Anotación @Entity: Esta anotación marca la clase MonsterEntity como una entidad de JPA, lo que significa que se mapeará a una tabla en la base de datos. En este caso, la clase representa la tabla monster.

2.- Anotación @Table(name = "monster"): Especifica el nombre de la tabla en la base de datos que esta entidad representa. Si no se define, JPA utilizaría el nombre de la clase por defecto.

3.- Anotación @Id: Define el campo Id como la clave primaria de la tabla. Es el identificador único para cada registro (monstruo).

4.- Anotación @GeneratedValue(strategy = GenerationType.IDENTITY): Indica que el valor del campo Id se generará automáticamente en la base de datos usando una estrategia de auto-incremento (en este caso, IDENTITY).

5.- Anotaciones @Getter y @Setter: Proporcionan automáticamente los métodos getter y setter para cada campo de la entidad, evitando la necesidad de escribir manualmente estos métodos. Esto es gracias a la librería Lombok.

6.- Campos de la entidad: Los atributos name, origin, type y weakness representan las columnas de la tabla monster. Cada instancia de MonsterEntity se corresponde con una fila en la tabla, y estos campos almacenan los detalles del monstruo, como su nombre, origen, tipo y debilidad.
<br>

#### 2.- Repository
![Spring Data Repository](/blogs/spring-data-jpa/spring-6.png)

Un Repository en Spring Data JPA es una interfaz que actúa como puente entre la aplicación y la base de datos, encargándose de realizar las operaciones de acceso a los datos (como consultar, guardar, actualizar o eliminar). Al extender una de las interfaces proporcionadas por Spring, como JpaRepository, no es necesario escribir manualmente el código SQL, ya que Spring Data JPA genera automáticamente las consultas.

En resumen, un Repository es una capa de abstracción que facilita la interacción con la base de datos, permitiendo realizar operaciones CRUD de manera simple y eficiente sin preocuparse por los detalles de la implementación SQL.

##### Puntos a considerar del codigo de repository.

1.- Extensión de JpaRepository: La interfaz MonsterRepository extiende de JpaRepository<MonsterEntity, Long>, lo que significa que hereda un conjunto de métodos predeterminados para realizar operaciones CRUD (Create, Read, Update, Delete) y consultas sobre la entidad MonsterEntity. El tipo Long indica que el identificador de la entidad es de este tipo.

2.- Método findByType(String type): Este método personalizado permite filtrar los monstruos en la base de datos por su tipo. Spring Data JPA genera automáticamente la implementación de este método, basándose en el nombre de la propiedad type de la entidad MonsterEntity.

3.- Método findByOrigin(String origin): Similar al anterior, este método filtra los monstruos según su lugar de origen. Spring Data JPA también genera la implementación de este método en función del nombre del campo origin de la entidad.

<br>

#### 3.- Services
![spring-data-services](/blogs/spring-data-jpa/spring-7.png)

En Spring Boot, un Service es una clase que contiene la lógica de negocio de la aplicación. Se encarga de procesar datos, coordinar operaciones entre diferentes partes de la aplicación y manejar las transacciones, separando estas responsabilidades del controlador y del acceso a los datos.

Cuando trabajamos con Spring Data JPA, el Service actúa como intermediario entre el Repository (que maneja la persistencia de los datos en la base de datos) y el Controller (que maneja las solicitudes HTTP). En otras palabras, el Service encapsula la lógica que define cómo interactuar con los datos, proporcionando un nivel adicional de abstracción para que los controladores no se encarguen directamente de las operaciones de acceso a la base de datos.

En resumen, un Service en Spring Boot:

1.- Separa la lógica de negocio del acceso a los datos.

2.- Facilita la reutilización y mantenimiento del código.

3.- Hace que el código sea más modular y testeable.
<br>

##### Puntos a considerar del codigo de service:
1.- Anotación @Service: Marca la clase como un Service en Spring, lo que significa que se trata de una capa de lógica de negocio. Spring administra esta clase y la inyecta donde sea necesario.

2.- Inyección de dependencias con @Autowired: Aquí inyectamos el MonsterRepository, lo que permite que el servicio interactúe con la base de datos. Spring se encarga de crear y gestionar la instancia del Repository.

3.- Método getAllMonsters(): Este método recupera todos los monstruos de la base de datos utilizando el método findAll() del Repository. Es una operación de lectura sencilla que devuelve una lista con todos los registros.

4.- Método saveMonster(MonsterEntity monster): Se encarga de guardar una nueva entidad de monstruo o actualizar una existente en la base de datos. Utiliza el método save() del Repository.

5.- Métodos de búsqueda personalizada (getAllMonstersByType y getAllMonsterByOrigin): Estos métodos aplican filtros sobre los datos almacenados, devolviendo solo los monstruos que coincidan con el tipo o el lugar de origen, respectivamente. Internamente, utilizan los métodos personalizados findByType() y findByOrigin() del Repository.

#### 4.- Controllers
![spring-data-controllers](/blogs/spring-data-jpa/spring-8.png)
En Spring Boot, un Controller es una clase que maneja las solicitudes HTTP entrantes (GET, POST, PUT, DELETE, etc.) y devuelve respuestas adecuadas, actuando como la capa de presentación de la aplicación. Utiliza anotaciones como @RestController o @Controller para indicar que la clase debe gestionar las peticiones web.

Cuando trabajamos con Spring Data JPA, el Controller se comunica con un Service, el cual contiene la lógica de negocio, y este a su vez interactúa con un Repository para realizar las operaciones de acceso a la base de datos. El Controller delega la lógica de negocio al Service, asegurando una separación de responsabilidades y manteniendo el código modular y fácil de mantener.

En resumen, un Controller en Spring Boot:

1.- Gestiona las solicitudes HTTP y devuelve respuestas.

2.- Actúa como puente entre la interfaz de usuario o API y la lógica de negocio.

3.- Facilita la comunicación entre el usuario y el acceso a los datos a través del Service y Repository.

##### Puntos a considerar del código de controller.

1.- Anotación @RestController: Esta anotación indica que la clase MonsterController es un controlador que maneja solicitudes web y devuelve respuestas en formato JSON. Combina las funcionalidades de @Controller y @ResponseBody, simplificando el manejo de respuestas.

2.- Anotación @RequestMapping("/api"): Define la ruta base para todas las solicitudes que este controlador manejará. En este caso, todas las rutas comenzarán con /api.

3.- Inyección de dependencias con @Autowired: Aquí se inyecta el MonsterService, que permite al controlador acceder a la lógica de negocio y realizar operaciones sobre los monstruos.

4.- Método getAllMonsters(): Este método maneja las solicitudes GET a la ruta /api/monsters. Llama al servicio para recuperar todos los monstruos y devuelve la lista como respuesta.

5.- Método saveMonster(@RequestBody MonsterEntity monster): Maneja las solicitudes POST a la ruta /api/monster. Utiliza la anotación @RequestBody para deserializar el objeto JSON recibido en el cuerpo de la solicitud a una instancia de MonsterEntity, que luego se guarda mediante el servicio.

6.- Método getAllMonstersByType(@PathVariable String type): Este método maneja las solicitudes GET a la ruta /api/monsters/type/{type}. Utiliza @PathVariable para extraer el valor del parámetro type de la URL y pasarle a la función del servicio para obtener los monstruos filtrados.

7.- Método getAllMonsterByOrigin(@PathVariable String origin): Similar al método anterior, maneja las solicitudes GET a la ruta /api/monsters/origin/{origin}. Extrae el valor de origin de la URL para obtener los monstruos por su lugar de origen.

### Probando nuestra REST API

Si llegaron hasta aqui felicitaciones. Es momento de echar a andar nuestra REST API y probar sus endpoints con algun software como Postman o Insomnia. Primero les dejo las imagenes para los que no saben como echar a andar la aplicación.

![Iniciando la aplicacion](/blogs/spring-data-jpa/spring-9.png)

Luego debería verse de la siguiente manera:
![Consola activa de la aplicacion](/blogs/spring-data-jpa/spring-10.png)

Ahora si, luego de que hayan iniciado la aplicacion de java springboot y hayan descargado su software de preferencia es momento de hacer unas pruebas. Yo utilizo Postman por lo que las imagenes serán de este software, pero todo lo que haga tambien funciona para insomnia y otros softwares similares.

Para efectos practicos ya tengo algunos datos creados, pero probaremos crear un registro mas.

#### Save Monster:
![save monster](/blogs/spring-data-jpa/spring-11.png)

Si se fijan en el apartado de Body de tipo JSON en postman estoy enviando un JSON con todos los campos de la entidad MonsterEntity, y debajo aparece el resultado que respondio la REST API luego de crear el recurso en la base de datos, en este caso si aparece el ID autogenerado que nos devuelve luego de crearlo.

#### Get All Monsters:
![Get All Monsters](/blogs/spring-data-jpa/spring-12.png)

A diferencia de save en Get all monsters no debemos enviar nada por body, solo vasta con apuntar al endpoint que configuramos en el controller de nuestra REST API. Y si se fijan en este endpoint, nos devuelve un arreglo de JSONs con los campos de la entidad MonsterEntity.

#### Get All Monsters By Type:
![Get All Monsters By Type](/blogs/spring-data-jpa/spring-13.png)
Cuando queremos filtar por algun campo, en este caso por el tipo de monster debemos enviar ese campo por parametro de ruta como se aprecia en la imagen. Este Get nos devolvera todos los monsters que cumplan con el parametro enviado. Les dejare una imagen mas de este Get para que aprecien bien que funciona filtrando por nuestro monster recien creado.
![Get All Monsters By Type](/blogs/spring-data-jpa/spring-16.png)

#### Get All Monsters By Origin:
![Get All Monsters By Origin](/blogs/spring-data-jpa/spring-14.png)
Al igual que el endpoint anterior en este puedes filtrar por un campo en especifico, en nuestro caso estamos filtrando por el lugar de origen de los monsters, y nos devolvera una arreglo de JSONs con todos los monsters que cumplan con el parametro enviado. Les dejare otra imagen para que al igual que By Type aprecien que este endpoint funciona muy bien.
![Get All Monsters By Origin](/blogs/spring-data-jpa/spring-15.png)


Sin mas que decir me despido y espero poder seguir aportando a la comunidad de programadores de habla hispana y el mundo. Nos vemos en un proximo articulo.

