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

Comenzaremos accediendo a la web de [Spring Initializr](https://start.spring.io/). Aquí, tenemos muchas consideraciones que tomar dependiendo del tipo de proyecto que vayamos a crear.

Empecemos por lo básico: debemos elegir si nuestro proyecto será hecho con Maven o Gradle. Esto es importante, ya que mucha de la documentación o tutoriales sobre la implementación de algunas librerías en Spring Boot están basados en Maven, que es lo más común en la industria. Sin embargo, esto no impide que podamos usar Gradle alguna vez. Les pongo mi ejemplo: desde que comencé a usar Java y Spring Boot en mi día a día laboral, siempre he utilizado Gradle. Lo bueno es que todo lo que se puede hacer con Maven, se puede convertir fácilmente a Gradle.

Dejando ese punto de lado, debemos asegurarnos de escoger Java como lenguaje base, seleccionar la versión de Spring Boot que utilizaremos, y configurar los nombres de nuestros paquetes y artefactos. Pueden seguir los ejemplos que puse en la imagen si así lo desean.

![Spring Initializr](/spring-1.png)

Ahora pasaremos a lo más relevante para que el proyecto funcione: las dependencias. Agregaremos las siguientes dependencias:
```markdown
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
```

Es importante agregar solo el driver de la base de datos que vayamos a utilizar, no los tres mencionados. H2 Database se incluye únicamente para quienes deseen realizar pruebas sin configurar un entorno completo de base de datos en su máquina. Esta base de datos es útil solo en entornos de desarrollo, ya que facilita las pruebas iniciales cuando aún no se ha decidido qué base de datos utilizar en el proyecto (algo que ocurre con frecuencia en la práctica). Lo mejor es que, como veremos más adelante, cambiar de base de datos es muy sencillo y no afectará a los demás componentes que desarrollemos.
