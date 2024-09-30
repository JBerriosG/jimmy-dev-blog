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

Comenzaremos por crear nuestro primer archivo, y este será la entidad o entity como se le conoce en ingles. La REST API que crearemos para este ejemplo es una API de mounstros, en la cual podremos crear los mounstros que queramos siguiendo la estructura que definamos para nuestra tabla en la base de datos. Cuando acabemos con el proyecto podremos crea un mounstro, obtener una lista de todos los mounstros guardados en la base de datos, tambien podremos obtener una lista filtrada por tipo de mounstro y una lista filtrada por el lugar de origen de ese mounstro. Por lo cual no solo aprenderan las funciones basicas de un REST API, si no tambien una manera de crear nuestras propias funciones para traer datos desde la base de datos.

#### 1.- Entity

![Spring Data Entity](/blogs/spring-data-jpa/spring-5.png)

Como puede ver en la imagen una entidad no es mas que una clase de java comun, solo que utiliza algunos decoradores especiales que explicaremos a continuación:

````yml
- Entity: Se utiliza para marcar una clase de java
 como una entidad, osea que sera mapeada a una tabla
 en la base de datos.
- Getter: Genera automaticamente los getters de la clase.
- Setter: Genera automaticamente los setters de la clase.
- Table: Sirve para indicar un nombre para la tabla que
sera creada al momento de ejecutar nuestrar API.
- Id: Le indica a JPA cual es el campo que usaremos como
 ID.
- GeneratedValue: Le indica a JPA cual sera la estrategia
que usaremos para generar los ID automaticamente.
````

Gracias a estos decoradores springboot y spring data jpa pueden generar la tabla en la base de datos con el nombre asignado en el decorador Table y la estructura de columnas indicada en los campos de la clase.

Algo que no explique antes es que, para efectos practicos y en realidad buenas practicas al momento de programar en java es escencial que cada clase lleve en su nombre el rol que cumple en la API, te puedes guiar por el decorador principal que tengan o tambien por la interfase de la que extiendan, pero ya daremos mas ejemplos por el momento tenemos MonsterEntity que al ser una entidad lleva el sufijo Entity al final.

#### 2.- Repository
![Spring Data Repository](/blogs/spring-data-jpa/spring-6.png)

Empecemos por explicar un poco los que es un repository, para que puedan entender un repository es una clase que extiende desde JpaRepository por lo que adquiere todas los metodos y funcionalidades que ofrece JpaRepository, esta clase especial de JPA es la que nos permite persistir los datos en la base de datos, osea es la que posee los metodos save( para guardar y actualizar registros), findAll( para obtener todos los registros de la tabla), y delete ( para borrar registros de la tabla).

Ahora que ya sabemos en consiste un repository, tenemos que indicar como es que se configura de manera correcta y en que deberían fijarse si algo no les funciona luego de un refactoring al codigo, es muy normal. Si pueden notar en la imagen luego de la palabra extends colocamos el nombre de la extension JpaRepository y <MonsterEntity, Long>, esto ultimo significa que JPA usará MonsterEntity como la entidad que define la estructura de los monsters y que el ID es de tipo Long.

Lo mas importante a recalcar aqui son los 2 metodos que aparecen allí, ya que si se dan cuenta no aparece ninguno de los que he mencianado antes, son 2 metodos totalmente nuevos. Para crear metodos asi, que no aparecen en muchas documentaciones con ejemplo hemos de seguir una serie de recomendaciones para que todo salga bien.

````yml
1.- La nomenclatura es obligatoria, los metodos en JPA 
respository hacen uso de camelCase, tal como findAll(),
metodo que trae todos los registros de la tabla.

2.- En la imagen podemos ver los metodos findByType()
y findByOrigin(), ambos metodos traen todos los registros
pero filtrados por el campo que se indica despues de la
palabra find.

3.- Metodos como los mencionados en el punto 2 se les 
llama metodos personalizados, estos debemos crearlos 
solo en caso de ser necesarios, si no, nuestra clase 
repository solo debe extender de JpaRepository y no 
contener nada dentro.

4.- Los metodos mencionados en el primer punto ya vienen 
incluidos en la clase JpaRepository por lo que no hay 
que escribirlos en nuestro repository, solo se escriben 
los metodos personalizados.
````

#### 3.- Services
![spring-data-services](/blogs/spring-data-jpa/spring-7.png)

