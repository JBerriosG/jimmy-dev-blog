---
title: 'Introducción a Springboot con Spring Data JPA'
subTitle: 'Simplifica el acceso a los datos en tus aplicaciones'
description: 'Lorem ipsum dolor sit amet'
pubDate: 'Sep 21 2024'
heroImage: '/spring-data-jpa.png'
category: 'dev'
---

En el mundo del desarrollo de aplicaciones Java, la interacción con bases de datos puede llegar a ser un proceso tedioso y propenso a errores. Crear conexiones, escribir consultas SQL manualmente, y gestionar transacciones son tareas que requieren mucho código repetitivo. Aquí es donde entran en juego Spring Boot y Spring Data JPA, dos poderosas herramientas que no solo simplifican este proceso, sino que también permiten a los desarrolladores enfocarse en la lógica de negocio en lugar de preocuparse por detalles técnicos complejos.

Spring Boot es un framework que facilita la creación de aplicaciones basadas en Spring de manera rápida, eliminando la necesidad de configuraciones extensas y complicadas. Con su enfoque en la "configuración por convención", Spring Boot proporciona un conjunto de herramientas que permiten a los desarrolladores arrancar proyectos con menos fricción. Todo lo que necesitas hacer es definir las dependencias correctas, y Spring Boot se encargará del resto, desde la configuración del servidor hasta la inyección de dependencias.

Por otro lado, Spring Data JPA es un módulo especializado de Spring que se integra con Java Persistence API (JPA) para manejar la interacción con bases de datos relacionales de manera sencilla. A través de una interfaz común, como JpaRepository, Spring Data JPA abstrae las operaciones básicas de base de datos (como crear, leer, actualizar y borrar registros) sin necesidad de escribir código SQL manualmente. Además, permite ejecutar consultas complejas de manera declarativa mediante nombres de método, reduciendo significativamente el esfuerzo de desarrollo.

Juntos, Spring Boot y Spring Data JPA proporcionan una solución integral para crear aplicaciones escalables y mantenibles que interactúan con bases de datos. Ya no es necesario escribir largos bloques de código repetitivo ni preocuparse por la configuración manual de entidades y conexiones; ambos frameworks lo manejan de manera automática y eficiente.

En este artículo, aprenderás cómo aprovechar la potencia de estos dos frameworks para crear una aplicación que interactúe con una base de datos utilizando entidades JPA, repositorios y controladores REST. Al final, tendrás una comprensión clara de cómo funcionan y cómo pueden agilizar el desarrollo de aplicaciones en tu día a día.

### Configuración del proyecto

Comenzaremos accediendo a la web de [Spring Initializr](https://start.spring.io/) para preparar nuestro proyecto inicial. Pueden seguir el ejemplo que puse en la imagen si así lo desean.

![Spring Initializr](/spring-1.png)

Ahora pasaremos a lo más relevante para que el proyecto funcione: las dependencias. Agregaremos las siguientes dependencias:

- Spring Web: Proporciona el soporte necesario para 
    crear APIs RESTful y manejar solicitudes HTTP.
- Spring Data JPA: Abstrae la capa de persistencia al 
    simplificar las operaciones CRUD mediante JPA.
- Lombok: Genera automáticamente código repetitivo como 
    getters, setters y constructores, mediante anotaciones 
    como @Getter y @Setter.
- MySQL Driver: Contiene el conector JDBC para 
    interactuar con bases de datos MySQL desde la 
    aplicación.
- PostgreSQL Driver: Incluye el controlador JDBC para 
    conectarse a bases de datos PostgreSQL.
- H2 Database: Proporciona una base de datos ligera en 
    memoria, ideal para pruebas y desarrollo rápido.

Al terminar con la configuración inicial del proyecto en Spring Initializr le damos al botón Generate y se nos descargara un archivo .zip.

### Iniciando el proyecto

Comenzamos por abrir y descomprimir el zip que se nos descargo en el paso anterior. Abrimos nuestro IDE de confianza, y arrastramos la carpeta sobre este, en mi caso el que suelo usar y mas les recomiendo para trabajar con java es IntelliJ IDEA, pero ud pueden usar el que mas les acomode. Se les verá mas o menos asi:

![IntelliJ IDEA](/spring-2.png)

Aquí debajo les dejare la configuración inicial de su archivo .properties que se encuentra en la carpeta resources.

![Archivo properties](/spring-3.png)

Siguiendo las configuraciones de la imagen tendran su aplicacion de springboot conectada a una base de datos en memoria, al momento echar a correr la aplicacion de springboot podremos acceder a la interfaz grafica de H2 a traves del siguiente link [http://localhost:8080/h2-console](http://localhost:8080/h2-console), gracias a esto podremos ver las tablas y los datos de manera mas comoda.