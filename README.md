# Spring Data JDBC

## Tabla de Contenido

- [Fundamentos de JDBC](#fundamentos-JDBC)
  - [¿Qué es JDBC?](#que-es-jdbc)
    - [La arquitectura de JDBC](#la-arquitectura-de-jdbc)
        - [JDBC API](#jdbc-api)
        - [JDBC Driver](#jdbc-driver)
        - [JDBC Driver Manager](#jdbc-driver-manager)
        - [DataSource](#datasource)
    - [Tipos de Drivers en JDBC](#tipos-de-drivers-jdbc)
      - [Tipo 1, 2, 3 y 4](#tipo-de-driver-1-2-3-4)
    - [Ejemplo para establecer una conexión](#establecer-conexion)
  - [Interacción con la base de datos](#interaccion-con-la-base-de-datos)
      - [Statements](#statements)
          - [Statement](#statement)
          - [PreparedStatement](#prepared-statement)
          - [CallableStatement](#callable-statement)
      - [Ejecución de Consultas](#ejecucion-de-consultas)
          - [El método executeQuery](#execute-query)
          - [El método executeUpdate](#execute-update)
      - [Procesamiento de Resultados (ResultSet)](#result-set)
          - [Acceso a los datos por nombre de columna o índice](#acceso-a-los-datos)
          - [Tipos de datos SQL y su correspondencia con tipos de datos Java](#tipos-en-sql-y-su-correspondencia-con-java)
          - [Tipos de ResultSet](#tipos-de-resultset)
              - [forward-only](#forward-only)
              - [scroll-insensitive](#scroll-insensitive)
              - [scroll-sensitive](#scroll-sensitive)
          - [Modificabilidad de ResultSet](#modificabilidad-de-result-set)
  - [Transacciones](#transacciones)
    - [Acrónimo ACID](#acid)
    - [Control de transacciones en JDBC](#control-de-transacciones)
        - [Método setAutoCommit](#metodo-setAutoCommit)
        - [Método commit](#metodo-commit)
        - [Método rollback](#metodo-rollback)
    - [Niveles de aislamiento de transacciones](#niveles-de-aislamiento-en-transacciones)
 - [Fundamentos Spring Data JDBC](#fundamentos-de-spring-data-jdbc)
     - [¿Qués es Spring Data?](#que-es-spring-data)
        - [Spring Data Commons](#spring-data-commons)
            - [Repositories](#repositories)
            - [CrudRepository](#crud-repository)
            - [PagingAndSortingRepository](#paging-and-sorting-repository)
            - [Query Methods](#query-method)
            - [Proyecciones (Projections)](#proyecciones)
    - [¿Qué es Spring Data JDBC?](#que-es-spring-data-jdbc)
        - [Configuración de Spring Data JDBC](#configuracion-de-spring-data-jdbc)
            - [Dependencias y configuración de la base de datos](#configuracion-y-dependencias)
        - [Entidades y Mapeo](#entidades-y-mapeo)
            - [Anotaciones de Mapeo](#anotaciones-de-mapeo)
                - [La anotación @Table](#la-anotacion-table)
                - [La anotación @Column](#la-anotacion-column)
                - [La anotación @Transient](#la-anotacion-transient)
                - [La anotación @MappedCollection](#la-anotacion-mapped-collection)
            - [Generación de Claves](#generacion-de-claves)
                - [Las anotaciones @Id y @GeneratedValue](#la-anotacion-id-y-generated-value)
            - [Relaciones](#relaciones)
                - [Uno-a-Uno](#uno-a-uno)
                - [Uno-a-Muchos](#uno-a-muchos)
                - [Muchos-a-Muchos](#muchos-a-muchos)
        - [Repositorios en Spring Data JDBC](#repositorios-en-spring-data-jdbc)
            - [Creación de Interfaces de Repositorio](#creacion-de-interfaces)
            - [La anotación @Query](#la-anotacion-query)
            - [La anotación @Param](#la-anotacion-param)
            - [Consultas Nativas](#consultas-nativas)
            - [Implementaciones Personalizadas de Repositorio](#personalizando-el-repositorio)
        - [Manejo de Transacciones](#manejo-de-transacciones)
            - [La anotación @Transactional](#anotacion-transactional)
            - [Propagación de Transacciones](#propagacion-de-transacciones)
        - [Auditoría](#auditoria)
            - [La anotación @CreatedBy](#la-anotacion-createdby)
            - [La anotación @LastModifiedBy](#la-anotacion-lastmodifiedby)
            - [La anotación @CreatedDate](#la-anotacion-created-date)
            - [La anotación @LastModifiedDate](#la-anotacion-last-modfied-date)
        - [Eventos de Dominio](#eventos-de-dominio)

<a id="fundamentos-JDBC"></a>
# Fudamentos de JDBC

<a id="que-es-jdbc"></a>
## ¿Qué es JDBC?

JDBC es una API (Interfaz de Programación de Aplicaciones) proporcionada por Java que nos permite interactuar con diferentes tipos de **bases de datos relacionales**. Podemos pensar a JDBC como un traductor entre el código Java y el lenguaje específico que habla cada sistema de gestión de bases de datos (SGBD) como MySQL, PostgreSQL, Oracle, SQL Server, etc.

Así mismo, abstrae las particularidades de cada base de datos a través de un **conjunto de interfaces y clases estándar**, a través de ellas permite que el código de Java sea más portable (con cambios mínimos, se puede trabajar con diferentes bases de datos) y más sencillo de desarrollar.

![image](https://github.com/user-attachments/assets/f1ab9309-ec86-4d67-be9a-5976a7f815d5)

<a id="La arquitectura de JDBC"></a>
### La arquitectura de JDBC
La arquitectura de JDBC se compone de varios elementos que trabajan en conjunto para facilitar la comunicación con las bases de datos.

![image](https://github.com/user-attachments/assets/60066b77-76c9-4f12-9367-89c675e28a50)

<a id="jdbc-api"></a>
#### JDBC API
Consiste en un conjunto de **interfaces y clases empaquetadas** principalmente en el paquete java.sql (y extensiones en javax.sql). Estas interfaces y clases definen los contratos y las funcionalidades necesarias para realizar operaciones con la base de datos.

<a id="jdbc-driver"></a>
#### JDBC Driver
Un JDBC Driver es una **implementación específica** de las interfaces JDBC para un sistema de gestión de **bases de datos en particular**  (por ejemplo, el driver JDBC de MySQL, el driver JDBC de PostgreSQL, etc). Este componente es el que realmente establece la **comunicación entre la API JDBC y la base de datos**.

¿Cómo logra el establecer esta comunicación?

El driver lo que hace es traducir las llamadas a las interfaces de JDBC realizadas por la aplicación de Java **al protocolo de comunicación específico** que entiende la base de datos. A demás de ello el driver se encarga de traducir los datos que la base de datos devuelve al formato que Java puede entender.

> [!NOTE]
> Es necesario incluir el archivo JAR del driver específico de la base de datos que se va a utilizar en el classpath del proyecto de Java para que el DriverManager pueda encontrarlo y utilizarlo.

<a id="jdbc-driver-manager"></a>
#### JDBC Driver Manager
El DriverManager actúa como una especie de **central de administración de drivers JDBC**. Su principal responsabilidad es cargar los drivers apropiados para la base de datos a la que la aplicación de Java está intentando interactuar.

También, el manejador de drivers se encarga de mantener una lista de los drivers registrados y de proporcionar **métodos para establecer conexiones** a través de la URL de la base de datos, el usuario y la contraseña.

<a id="datasourcer"></a>
#### DataSource
Representa una fuente de conexiones a una base de datos. Debido a esta interfaz, la configuración de la conexión se realiza en un solo lugar (a través de la configuración del DataSource), lo que facilita la administración y los cambios en caso de migrarse a otro manejador de base de datos.

Uno de los mayores beneficios de utilizar esta interfaz es el **Pooling de conexiones**. Lo que permite es mantener un grupo de conexiones ya establecidas y listas para ser utilizadas. Cuando la aplicación necesita una conexión, la obtiene del pool y la devuelve cuando termina, permitiendo que otras partes de la aplicación la reutilicen.

<a id="tipos-de-drivers-jdbc"></a>
### Tipos de Drivers en JDBC
Existen diferentes tipos de drivers dentrod e JDBC. Actualmente el de uso más común es el **Tipo 4: Native-Protocol all-Java Driver ("Thin Driver")**.

¿Por qué es el más utilizado? Debido que, este tipo de driver está completamente escrito en Java, lo cual genera una mayor portabilidad debibo que no tiene dependencias externas. A demás, este tipo de driver se comunica directamente con el servidor de la base de datos utilizando el protocolo de red nativo de la base de datos y gracias a ello es el driver con un mayor rendimiento.


<a id="tipo-de-driver-1-2-3-4"></a>
#### Tipo 1, 2, 3 y 4

|Tipo de Driver	| Descripción	| Portabilidad |	Rendimiento |	Dependencia |	Uso Actual |
|-----------------|------------|-------------|---------------|-------------|--------------- |
| Tipo 1	| JDBC-ODBC Bridge	  | Baja |	Bajo |	Driver ODBC específico de la base de datos|	En desuso |
| Tipo 2	| Native-API partly Java	| Media	| Medio	| Bibliotecas nativas específicas de la base de datos	| Menos común |
| Tipo 3	| Network-Protocol all-Java	| Alta |	Medio	| Servidor de middleware |Menos común |
| Tipo 4	| Native-Protocol all-Java ("Thin Driver")	 |Muy alta	| Alto	| Archivo JAR del driver específico de la base de datos |	Muy común |

<a id="establecer-conexion"></a>
### Ejemplo para establecer una conexión

Dentro del siguiente código, el paso importante es el método estático **getConnection()** de la clase DriverManager. Esté método intenta establecer una conexión con la base de datos utilizando la URL, el usuario y la contraseña proporcionados. Si la conexión se establece correctamente, devuelve un objeto Connection la cual representa una **sesión de conexión** con la base de datos

```
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;

public class EjemploConexion {

    public static void main(String[] args) {

        String url = "jdbc:mysql://localhost:3306/nombredelabasededatos";
        String usuario = "usuario_mysql";
        String contraseña = "contraseña_mysql";

        try (Connection conexion = DriverManager.getConnection(url, usuario, contraseña)) {

            System.out.println("Intentando conectar a la base de datos...");
            System.out.println("¡Conexión establecida exitosamente!");

            // Operaciones, consultas, etc...

        } catch (SQLException e) {
            System.err.println("Error al conectar a la base de datos: " + e.getMessage());
            e.printStackTrace();
        }

        // La conexión se cierra automáticamente...
    }
}
```

<a id="interaccion-con-la-base-de-datos"></a>
## Interacción con la base de datos

Una vez que se ha podido establecer una conexión con la base de datos, el siguiente paso es interactuar con ella y para realizar operaciones como consultar, insertar, actualizar o eliminar datos. JDBC proporciona tres interfaces principales para enviar sentencias SQL a la base de datos: Statement, PreparedStatement y CallableStatement.

<a id="statements"></a>
### Statements 
Las interfaces Statement, PreparedStatement y CallableStatement extienden la interfaz **java.sql.Statement**. Cada una está diseñada para diferentes tipos de interacciones con la base de datos.

<a id="statement"></a>
#### Statement
La interfaz Statement es la interfaz básica para ejecutar **sentencias SQL estáticas** (es decir, sentencias SQL que no tienen parámetros variables). Se utiliza para consultas simples donde la estructura del SQL **es conocida en tiempo de compilación y no cambia**.

> [!IMPORTANT]
> Debido a que las cadenas SQL se construyen concatenando valores directamente en la sentencia, es susceptible a ataques de inyección SQL si los valores provienen de fuentes no confiables (como la entrada del usuario).

> Ejemplo
```
try (Statement statement = conexion.createStatement()) {
    String nombre = "Ejemplo";
    String sql = "SELECT * FROM mi_tabla WHERE nombre = '" + nombre + "'";
    ResultSet resultSet = statement.executeQuery(sql);
    // ...
} catch (SQLException e) {
    e.printStackTrace();
}
```

<a id="prepared-statement"></a>
#### PreparedStatement
La interfaz PreparedStatement **extiende** Statement y representa una sentencia SQL **precompilada**. Esto significa que la estructura de la sentencia SQL se envía a la base de datos para su análisis y compilación antes de que se proporcionen los valores de los parámetros. 

> Ejemplo

```
String sql = "SELECT * FROM mi_tabla WHERE nombre = ?";
try (PreparedStatement preparedStatement = conexion.prepareStatement(sql)) {
    preparedStatement.setString(1, "Ejemplo");
    ResultSet resultSet = preparedStatement.executeQuery();
    // ...
} catch (SQLException e) {
    e.printStackTrace();
}
```

En este ejemplo, el ? es un marcador de posición para el valor del nombre. El método setString(1, "Ejemplo") establece el valor del primer parámetro (índice 1) como "Ejemplo". 

La base de datos tratará "Ejemplo" como un valor de dato, no como parte de la estructura SQL y gracias a ello se tiene una seguridad contra la inyección SQL

<a id="callable-statement"></a>
#### CallableStatement

La interfaz CallableStatement **extiende** PreparedStatement y se utiliza para **ejecutar procedimientos almacenados** que residen en la base de datos.
¿Qué es un procedimiento almacena o store procedure? Los procedimientos almacenados son **bloques de código** SQL que se pueden invocar desde la aplicación. Pueden tener parámetros de entrada, parámetros de salida y devolver un resultado.

> Ejemplo
```
String sql = "{call obtener_datos_usuario(?, ?)}";
try (CallableStatement callableStatement = conexion.prepareCall(sql)) {
    callableStatement.setInt(1, 123); // Establece el valor del parámetro de entrada (índice 1)
    callableStatement.registerOutParameter(2, java.sql.Types.VARCHAR); // Registra el parámetro de salida (índice 2) como VARCHAR
    callableStatement.execute();
    String nombreUsuario = callableStatement.getString(2); // Obtiene el valor del parámetro de salida
    System.out.println("Nombre del usuario: " + nombreUsuario);
} catch (SQLException e) {
    e.printStackTrace();
}
```
En este ejemplo, se invoca un procedimiento almacenado llamado obtener_datos_usuario que toma un entero como entrada y devuelve un VARCHAR como salida.

<a id="ejecucion-de-consultas"></a>
### Ejecución de Consultas
Una vez que se ha creado un objeto Statement (o sus subtipos), es necesario ejecutar la sentencia SQL utilizando uno de los siguientes métodos: executeQuery, executeUpdate.

<a id="execute-query"></a>
#### El método executeQuery
El método executeQuery() se utiliza para ejecutar sentencias SQL que devuelven un **conjunto de resultados**, principalmente las sentencias SELECT. Devuelve un objeto java.sql.ResultSet que contiene los datos devueltos por la consulta.

> Ejemplo

```
try (Statement statement = conexion.createStatement()) {
    String sql = "SELECT id, nombre FROM mi_tabla";
    ResultSet resultSet = statement.executeQuery(sql);
    // ... procesar el ResultSet ...
} catch (SQLException e) {
    e.printStackTrace();
}
```

<a id="execute-update"></a>
#### El método executeUpdate
El método executeUpdate() se utiliza para ejecutar sentencias SQL que **modifican los datos en la base de datos**, como INSERT, UPDATE, DELETE y también para sentencias DDL (Data Definition Language) como CREATE TABLE o ALTER TABLE.

Devuelve un entero que indica el **número de filas afectadas** por la operación (para INSERT, UPDATE, DELETE) o **0 para sentencias DDL**.

> Ejemplo

```
String insertSql = "INSERT INTO mi_tabla (nombre, edad) VALUES (?, ?)";
try (PreparedStatement preparedStatement = conexion.prepareStatement(insertSql)) {
    preparedStatement.setString(1, "Nuevo Registro");
    preparedStatement.setInt(2, 30);
    int filasAfectadas = preparedStatement.executeUpdate();
    System.out.println("Filas insertadas: " + filasAfectadas);
} catch (SQLException e) {
    e.printStackTrace();
}
```

<a id="result-set"></a>
### Procesamiento de Resultados (ResultSet)
Un ResultSet representa un **conjunto de filas de datos** que cumplen con los criterios de tu consulta. Es posible navegar a través de estas filas y acceder a los valores de las columnas.

<a id="acceso-a-los-datos"></a>
#### Acceso a los datos por nombre de columna o índice
Es posible acceder a los valores de las columnas en la fila actual del ResultSet de dos maneras:

1. Por nombre de columna
```
while (resultSet.next()) { // Mueve el cursor a la siguiente fila
    int id = resultSet.getInt("id");
    String nombre = resultSet.getString("nombre");
    java.sql.Date fechaCreacion = resultSet.getDate("fecha_creacion");
    // ...
}
```

2. Por índice de columna (basado en el orden en que aparecen en la consulta SQL, empezando desde 1):
```
while (resultSet.next()) {
    int id = resultSet.getInt(1);
    String nombre = resultSet.getString(2);
    java.sql.Date fechaCreacion = resultSet.getDate(3);
    // ... hacer algo con los datos ...
}
```

> [!NOTE]
> Es preferible acceder a los datos por **nombre de columna**, ya que hace que el código sea más legible y menos dependiente del orden de las columnas en la consulta.

<a id="tipos-en-sql-y-su-correspondencia-con-java"></a>
#### Tipos de datos SQL y su correspondencia con tipos de datos Java
JDBC proporciona métodos en la interfaz **ResultSet** para obtener los datos de las columnas en el tipo de datos Java apropiado. 

> Correspondencia entre tipos de datos SQL y tipo de datos de Java

Tipo de Datos SQL |	Método ResultSet en Java	| Tipo de Datos Java
------------------|---------------------------|--------------------
INTEGER, INT	    | getInt(columnName)	      | int
BIGINT	          | getLong(columnName)	      | long
FLOAT	            | getFloat(columnName)	    | float
DOUBLE, REAL	    | getDouble(columnName)	    | double
BOOLEAN	          | getBoolean(columnName)	  | boolean
VARCHAR, CHAR, TEXT	| getString(columnName)	  | String
DATE	            |   getDate(columnName)	    | java.sql.Date
TIME	            | getTime(columnName)	      | java.sql.Time
TIMESTAMP	        | getTimestamp(columnName)	| java.sql.Timestamp
BLOB              | getBlob(columnName)	      | java.sql.Blob
CLOB	            | getClob(columnName)	      | java.sql.Clob

<a id="tipos-de-resultset"></a>
### Tipos de ResultSet
La interfaz ResultSet ofrece diferentes tipos de cursores y funcionalidades, que se especifican al crear el **Statement o PreparedStatement** utilizando métodos como createStatement(int resultSetType, int resultSetConcurrency) o prepareStatement(String sql, int resultSetType, int resultSetConcurrency)

<a id="forward-only"></a>
#### forward-only
Este tipo de ResultSet es utilizado por defecto. Dentro de este ResultSet solo puede moverse hacia adelante, desde la primera fila hasta la última. **No es posible moverse hacia atrás ni a una fila específica** utilizando métodos como previous(), absolute(), o relative().

Generalmente ofrece el mejor rendimiento, ya que no necesita mantener información para movimientos hacia atrás o actualizaciones y gracias a ello es el tipo de ResultSet con un mejor rendimiento.

<a id="scroll-insensitive"></a>
#### scroll-insensitive
El cursor se puede **mover en cualquier dirección** (adelante, atrás, a una posición específica). Los cambios realizados en la base de datos por otras transacciones después de que se abrió el ResultSet no se reflejan en este objeto. Es una "fotografía" de los datos en el momento de la consulta.

Este tipo de ResultSet es útil cuando se necesita navegar por los resultados varias veces o en un orden diferente, y **no es crítico ver los cambios más recientes** realizados por otros.

<a id="scroll-sensitive"></a>
#### scroll-sensitive
Similar a TYPE_SCROLL_INSENSITIVE en cuanto a la capacidad de desplazamiento del cursor. La diferencia es que este tipo de ResultSet **refleja los cambios realizados en la base de datos por otras transacciones mientras el ResultSet está abierto** (la capacidad de ver los cambios depende de la implementación del driver y la base de datos).

> [!NOTE]
> Puede tener el rendimiento más bajo, ya que necesita monitorear los cambios en la base de datos. Su uso es en escenarios donde es crucial ver los datos más actualizados en tiempo real mientras se navega por los resultados.

<a id="modificabilidad-de-result-set"></a>
### Modificabilidad de ResultSet
Además del tipo de desplazamiento, también se puede especificar la concurrencia del ResultSet, que determina si se puede **actualizar el ResultSet y, por lo tanto, la base de datos a través de este objeto**

> Tipos de modificabilidad: 
> 1. ResultSet.CONCUR_READ_ONLY
>   - Es el valor por defecto si no se especifica otro. El ResultSet es de solo lectura. No se pueden realizar actualizaciones a través de este objeto.
> 2. ResultSet.CONCUR_UPDATABLE
>   -  El ResultSet se puede utilizar para actualizar los datos en la base de datos. Esto implica mover el cursor a la fila que se desea actualizar, insertar o eliminar.

> [!IMPORTANT]
> No todos los drivers y bases de datos admiten CONCUR_UPDATABLE. La consulta SQL que genera el ResultSet también debe ser actualizable.
 

<a id="transacciones"></a>
## Transacciones
Una transacción es una secuencia de una o más operaciones en la base de datos que se tratan como **una unidad lógica de trabajo**. Esto significa que todas las operaciones dentro de una transacción deben completarse con éxito para que los cambios se guarden permanentemente en la base de datos. Si alguna operación dentro de la transacción falla, todos los cambios realizados hasta ese punto deben deshacerse para **restaurar la base de datos a su estado original**.

<a id="acid"></a>
### Acrónimo ACID
El término ACID es un acrónimo que define las cuatro propiedades clave de una transacción de base de datos confiable.

> 1. Atomicidad (Atomicity)
Una transacción debe tratarse como una unidad indivisible. Todas las operaciones dentro de la transacción deben tener éxito, o ninguna de ellas debe aplicarse. Si alguna parte de la transacción falla, toda la transacción se revierte, y la base de datos permanece en el estado en el que estaba antes de que comenzara la transacción. **Es como una operación "todo o nada"**.

> 2. Consistencia (Consistency)
**Una transacción debe mover la base de datos de un estado consistente a otro estado consistente**. Un estado consistente significa que todos los datos cumplen con todas las reglas definidas para la base de datos, incluyendo restricciones de integridad (como claves primarias, claves foráneas, restricciones NOT NULL, etc)

> 3. Aislamiento (Isolation)
Las transacciones concurrentes (múltiples transacciones que se ejecutan al mismo tiempo) deben ejecutarse de tal manera que parezca que **cada transacción se ejecuta de forma completamente aislada de las demás**. Los resultados de una transacción **no deben ser visibles para otras transacciones**. El aislamiento ayuda a prevenir problemas como la lectura de datos inconsistentes o la corrupción de datos debido a la interferencia entre transacciones.

> 4. Durabilidad (Durability)
Una vez que una transacción se ha confirmado, los cambios realizados en la base de datos deben ser permanentes y deben sobrevivir a cualquier falla del sistema (como cortes de energía, fallas del disco duro, etc)

<a id="control-de-transacciones"></a>
### Control de transacciones en JDBC
JDBC proporciona mecanismos para controlar las transacciones dentro de tus aplicaciones Java a través de la interfaz java.sql.Connection

<a id="metodo-setAutoCommit"></a>
#### Método setAutoCommit
Por defecto, cuando se establece una nueva conexión JDBC, el modo de **auto-commit está habilitado**. Esto significa que cada sentencia SQL que ejecutas se trata como una transacción individual y se confirma automáticamente al completarse con éxito.

> [!NOTE]
> En caso de que el auto-commit esté deshabilitado, entonces, las transacciones deben confirmarse o revertirse explícitamente.

```
try ( Connection conexion = DriverManager.getConnection(url, usuario, contraseña) ) {
    conexion.setAutoCommit(false); // Deshabilitar el auto-commit

    // Realizar una serie de operaciones que forman una transacción
    PreparedStatement pstmt1 = conexion.prepareStatement("UPDATE cuenta SET saldo = saldo - ? WHERE id = ?");
    pstmt1.setDouble(1, 100.00);
    pstmt1.setInt(2, 1);
    pstmt1.executeUpdate();
    pstmt1.close();

    // Si todas las operaciones fueron exitosas, confirmamos la transacción
    conexion.commit();
    System.out.println("Transacción confirmada.");

} catch (SQLException e) {
    // Si alguna operación falla, revertimos la transacción
    if (conexion != null) {
        try {
            conexion.rollback();
            System.err.println("Transacción revertida.");
        } catch (SQLException ex) {
            System.err.println("Error al hacer rollback: " + ex.getMessage());
        }
    }
    e.printStackTrace();
}
```

<a id="metodo-commit"></a>
#### Método commit
El método commit() de la interfaz Connection se utiliza para confirmar todos los cambios realizados dentro de la transacción actual. Una vez que se llama a commit(), los cambios se hacen permanentes en la base de datos y son visibles para otras transacciones (sujeto al nivel de aislamiento). El commit() marca el final exitoso de una transacción.

> [!NOTE]
> Este método solo tiene efecto si el modo de auto-commit está deshabilitado (setAutoCommit(false))

<a id="metodo-rollback"></a>
#### Método rollback
El método rollback() de la interfaz Connection se utiliza para deshacer todos los cambios realizados dentro de la transacción actual desde el último punto de confirmación (o desde el inicio de la transacción si no ha habido confirmaciones previas).

> [!NOTE]
> Al igual que con commit(), rollback() solo tiene efecto si el modo de auto-commit está deshabilitado.

<a id="niveles-de-aislamiento-en-transacciones"></a>
### Niveles de aislamiento de transacciones
El nivel de aislamiento de una transacción define el grado en que una transacción está aislada de las modificaciones realizadas por otras transacciones concurrentes. 
JDBC permite configurar el nivel de aislamiento de una conexión utilizando el método **setTransactionIsolation(int level)** de la interfaz Connection

> Connection.TRANSACTION_READ_UNCOMMITTED (Nivel 0)

El nivel de aislamiento más bajo. Permite que una transacción **vea los cambios no confirmados realizados por otras transacciones** (conocido como lectura sucia). Este nivel ofrece la máxima concurrencia pero la menor consistencia, ya que los datos leídos podrían ser revertidos posteriormente.

> Connection.TRANSACTION_READ_COMMITTED (Nivel 1)

Garantiza que una transacción solo puede **leer datos que han sido confirmados por otras transacciones**. Evita las lecturas sucias. Sin embargo, una transacción puede leer la misma fila varias veces y **obtener valores diferentes si otra transacción confirma una actualización de esa fila** entre las lecturas (conocido como lectura no repetible).

> Connection.TRANSACTION_REPEATABLE_READ (Nivel 2)

Evita las lecturas sucias y las lecturas no repetibles. Sin embargo, todavía puede ocurrir el problema de las lecturas fantasma, donde una transacción puede ejecutar una consulta que devuelve un conjunto de filas, y si otra transacción inserta nuevas filas que cumplen con los mismos criterios de búsqueda y las confirma, la primera transacción podría ver estas nuevas filas en una lectura posterior.

> Connection.TRANSACTION_SERIALIZABLE (Nivel 3)

Garantiza que las transacciones concurrentes se ejecuten de forma completamente aislada, como si se ejecutaran en serie (una después de la otra). Evita las lecturas sucias, las lecturas no repetibles y las lecturas fantasma

<a id="fundamentos-de-spring-data-jdbc"></a>
## Fundamentos Spring Data JDBC

<a id="que-es-spring-data"></a>
### ¿Qués es Spring Data?
Spring Data no es una única librería, sino una **colección de módulos**, donde cada módulo se enfoca en una tecnología de persistencia específica. Proporciona una capa de abstracción sobre estas diferentes tecnologías, permitiendo a los desarrolladores interactuar con los almacenes de datos.

> Beneficios
> 1. Minimiza la necesidad de escribir código repetitivo para tareas comunes de acceso a datos
> 2. Ofrece una API más uniforme para interactuar con diferentes tipos de almacenes de datos
> 3. Permite a los desarrolladores centrarse más en la lógica de negocio de la aplicación en lugar de los detalles de bajo nivel de la persistencia de datos.

<a id="spring-data-commons"></a>
### Spring Data Commons
Spring Data Commons **es el núcleo** sobre el cual se construyen todos los módulos específicos de Spring Data. Proporciona un conjunto de abstracciones y conceptos fundamentales que son **compartidos por todos los módulos**, independientemente de la tecnología de persistencia subyacente. 

<a id="repositories"></a>
#### Repositories
La interfaz Repository es la **interfaz central de la abstracción de persistencia de datos** en Spring Data. Actúa como un marcador para indicar que una interfaz **es un componente de acceso a datos** (un "Data Access Object" o DAO en patrones tradicionales).

> [!NOTE]
> En sí misma, la interfaz Repository **no define ningún método específico** para las operaciones de datos. Su principal función es la de ser una interfaz base que otras interfaces más especializadas extienden para proporcionar funcionalidades concretas.

En el siguiente código, UsuarioRepository es un repositorio que gestiona entidades de tipo Usuario y utiliza Long para **identificar las instancias de Usuario**.
```
import org.springframework.data.repository.Repository;

// Interfaz de repositorio para la entidad 'Usuario'
public interface UsuarioRepository extends Repository<Usuario, Long> {
    // 'Usuario' es el tipo de entidad que este repositorio gestiona
    // 'Long' es el tipo del ID de la entidad 'Usuario'
}
```

<a id="crud-repository"></a>
#### CrudRepository
La interfaz CrudRepository es una extensión de Repository que proporciona métodos estándar para las operaciones CRUD (Create, Read, Update, Delete) en una entidad.

```
import org.springframework.data.repository.CrudRepository;
import java.util.List;

public interface UsuarioRepository extends CrudRepository<Usuario, Long> {
    // Hereda métodos como:
    // - <S extends T> S save(S entity); // Guardar o actualizar una entidad
    // - Optional<T> findById(ID id);    // Buscar una entidad por su ID
    // - boolean existsById(ID id);     // Verificar si una entidad con el ID existe
    // - Iterable<T> findAll();         // Obtener todas las entidades
    // - long count();                 // Contar el número de entidades
    // - void deleteById(ID id);        // Eliminar una entidad por su ID
    // - void delete(T entity);         // Eliminar una entidad
    // - void deleteAll(Iterable<? extends T> entities); // Eliminar múltiples entidades
    // - void deleteAll();              // Eliminar todas las entidades
}
```

Al extender CrudRepository, tu UsuarioRepository automáticamente tendrá disponibles todos estos métodos básicos para interactuar con la tabla usuario en la base de datos, **sin necesidad de escribir ninguna implementación**.

<a id="paging-and-sorting-repository"></a>
#### PagingAndSortingRepository
La interfaz PagingAndSortingRepository es otra extensión de CrudRepository que añade métodos para realizar la **paginación y el ordenamiento** de los resultados de las consultas.

Se muestra el siguiente método, se puede observar lo fácil que es el implementar las funcionalidades de paginación en las APIs gracias a Spring Data.
```
import org.springframework.data.repository.PagingAndSortingRepository;
import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;

public interface UsuarioRepository extends PagingAndSortingRepository<Usuario, Long> {
    // Hereda todos los métodos de CrudRepository
    // Además, añade métodos como:
    // - Iterable<T> findAll(Sort sort); // Obtener todas las entidades ordenadas
    // - Page<T> findAll(Pageable pageable); // Obtener una página de entidades (con ordenamiento implícito en Pageable)
}
```

- Sort: Un objeto que define los criterios de ordenamiento (por qué campo y en qué dirección).
- Pageable: Un objeto que encapsula la información sobre la paginación (número de página, tamaño de la página, y opcionalmente, el ordenamiento).
- Page: Un objeto que representa una página de resultados, incluyendo el contenido, el número total de elementos, el número total de páginas, etc.

<a id="query-method"></a>
#### Query Methods
Una de las características más poderosas de Spring Data Commons es la capacidad de derivar consultas automáticamente a partir de los nombres de los métodos que se definen en las interfaces de repositorio. Spring Data analiza el nombre del método siguiendo ciertas convenciones y genera la consulta de base de datos correspondiente.

> Sintaxis básica de los Query Methods:

Los nombres de los métodos generalmente comienzan con un prefijo (como find...By, getBy..., count...By, exists...By, delete...By) seguido del nombre de uno o más atributos de la entidad. Es posible usar operadores para especificar las condiciones de búsqueda (como Equals, LessThan, GreaterThan, Like, Between, In, IsNull, IsNotNull, OrderBy).

> Ejemplo
```
public interface UsuarioRepository extends CrudRepository<Usuario, Long> {
    // Buscar usuarios por su nombre (atributo 'nombre' de la entidad Usuario)
    List<Usuario> findByNombre(String nombre);

    // Buscar usuarios por su email exacto
    Usuario getByEmail(String email);

    // Buscar usuarios cuya edad sea mayor que un valor dado
    List<Usuario> findByEdadGreaterThan(int edad);

    // Buscar usuarios cuyo nombre contenga una cierta cadena (usando 'like' en SQL)
    List<Usuario> findByNombreContaining(String parteDelNombre);

    // Buscar usuarios activos y ordenados por fecha de creación descendente
    List<Usuario> findByActivoTrueOrderByFechaCreacionDesc();

    // Contar el número de usuarios con un cierto estado
    long countByEstado(String estado);

    // Verificar si existe algún usuario con un email específico
    boolean existsByEmail(String email);

    // Eliminar usuarios con un cierto estado
    void deleteByEstado(String estado);
}
```

<a id="proyecciones"></a>
#### Proyecciones (Projections)
Las proyecciones en Spring Data Commons te permiten recuperar solo un subconjunto de los atributos de una entidad cuando se realiza una consulta. 

> Proyecciones basadas en interfaces (Interface-based Projections)
Se define una interfaz cuyos **métodos corresponden a los atributos que deseas recuperar**. Spring Data se encarga de crear una implementación de esta interfaz en tiempo de ejecución, utilizando los datos de la entidad.

> Ejemplo 
```
public interface UsuarioNombreEmail {
    String getNombre();
    String getEmail();
}

public interface UsuarioRepository extends CrudRepository<Usuario, Long> {
    List<UsuarioNombreEmail> findByActivoTrue();
}
```

En este caso, la consulta findByActivoTrue() devolverá una lista de objetos que implementan la interfaz UsuarioNombreEmail, conteniendo solo el nombre y el email de los usuarios activos

> Proyecciones basadas en clases cerradas (Class-based Projections - DTOs)
Es posible utilizar una clase simple de transferencia de datos (DTO) como tipo de retorno para los métodos de consulta. Spring Data intentará mapear los resultados de la consulta a las propiedades del DTO.

> [!IMPORTANT]
> Los nombres de los atributos del DTO deben coincidir con los nombres de las columnas o alias en la consulta

<a id="que-es-spring-data-jdbc"></a>
### ¿Qué es Spring Data JDBC?
Se ha mencionado con anterioridad que Spring Data es un conjunto de módulos. Spring Data JDBC es uno de estos módulos, diseñado específicamente para simplificar el acceso a **bases de datos relacionales** utilizando JDBC como la capa de acceso a datos subyacente.

<a id="configuracion-de-spring-data-jdbc"></a>
#### Configuración de Spring Data JDBC
Para comenzar a utilizar Spring Data JDBC dentro de un proyecto de Spring, primero es necesario incluir las dependencias necesarias. Es recomendable hacer uso de Spring Boot, debido que simplifica enormemente gracias a los "Starters". El starter específico para Spring Data JDBC es **spring-boot-starter-data-jdbc**.

> Ejemplo de dependencia en Maven (pom.xml)
```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jdbc</artifactId>
</dependency>

<dependency>
    <groupId>com.mysql</groupId>
    <artifactId>mysql-connector-j</artifactId>
    <scope>runtime</scope>
</dependency>
```

Donde:
1. Al incluir el spring-boot-starter-data-jdbc, Spring Boot automáticamente gestionará las dependencias transitivas necesarias para Spring Data JDBC y el core de JDBC.
2. El artefacto con el id **mysql-connector-j** es el **Driver** para poder establecer conexión con la base de datos.

<a id="configuracion-y-dependencias"></a>
##### Dependencias y configuración de la base de datos
Una vez que se tienen las dependencias, el siguiente paso es configurar la conexión a tu base de datos. Spring Boot utiliza el **DataSource de Spring Framework** para esto. Es posible configurar los detalles de la conexión (URL, usuario, contraseña, driver) a través de las **propiedades de Spring Boot** en el archivo application.properties o application.yml.

> Ejemplo de una configuración con el application.yml

```
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/tu_base_de_datos
    username: tu_usuario
    password: tu_contraseña
    driver-class-name: com.mysql.cj.jdbc.Driver
```
Spring Boot intentará automáticamente configurar un DataSource basado en estas propiedades. Debido a esto, no es necesario escribir explícitamente el código para obtener y liberar conexiones como se tendría que realizar con JDBC puro.

> [!NOTE]
> Si es necesaria una configuración más avanzada, es posible crear un propio DataSource cómo **Bean** y Spring Boot lo utilizará

<a id="#entidades-y-mapeo"></a>
### Entidades y Mapeo
En Spring Data JDBC, las entidades **son las clases Java que representan las tablas** dentro de una base de datos. El mapeo es el proceso de definir cómo los atributos de tus clases de entidad se corresponden con las columnas de las tablas.

Spring Data JDBC utiliza anotaciones para facilitar este mapeo.

<a id="anotaciones-de-mapeo"></a>
#### Anotaciones de Mapeo
Spring Data JDBC proporciona varias anotaciones en el paquete org.springframework.data.relational.core.mapping (y algunas relacionadas en otros paquetes de Spring Data) para configurar el mapeo entre las entidades y la base de datos.

<a id="la-anotacion-table"></a>
##### La anotación @Table
La anotación @Table se utiliza a **nivel de clase** para especificar el nombre de la tabla en la base de datos a la que se mapea la entidad.

> [!IMPORTANT]
> Si no se utiliza esta anotación, Spring Data JDBC intentará inferir el nombre de la tabla a partir del nombre de la clase (convirtiendo de CamelCase a snake_case).

> Ejemplo
```
import org.springframework.data.relational.core.mapping.Table;

@Table("usuarios") // La entidad Usuario se mapea a la tabla 'usuarios'
public class Usuario {
    // ... atributos ...
}
```

<a id="la-anotacion-column"></a>
##### La anotación @Column
La anotación @Column se utiliza a **nivel de atributo** (campo) para especificar el nombre de la columna en la base de datos a la que se mapea el atributo. También permite configurar otras propiedades de la columna, como la longitud, si permite valores nulos, etc.

> [!IMPORTANT]
> Si no se utiliza esta anotación, Spring Data JDBC intentará inferir el nombre de la columna a partir del nombre del atributo (convirtiendo de CamelCase a snake_case)

> Ejemplo
```
import org.springframework.data.relational.core.mapping.Column;

public class Usuario {
    @Id
    private Long id;

    @Column("nombre_completo") // El atributo 'nombre' se mapea a la columna 'nombre_completo'
    private String nombre;

    @Column(value = "correo_electronico", nullable = false, length = 100)
    private String email;

    private int edad; // Se mapea a la columna 'edad' por defecto

    // ... getters y setters ...
}
```

<a id="la-anotacion-transient"></a>
##### La anotación @Transient
La anotación @Transient se utiliza a nivel de atributo para indicar que **un atributo de la entidad no debe ser persistido en la base de datos**. Es decir, este atributo **existirá en la clase Java pero no tendrá una columna correspondiente en la tabla**.

> Ejemplo
```
import org.springframework.data.annotation.Transient;

public class Usuario {
    // ... atributos persistentes ...

    @Transient
    private String atributoTemporal; // Este atributo no se mapeará a una columna

    // ... getters y setters para atributoTemporal ...
}
```

<a id="la-anotacion-mapped-collection"></a>
##### La anotación @MappedCollection
La anotación @MappedCollection sirve para indicar que un atributo de la entidad es una colección de otras entidades relacionadas.

> Para relaciones uno-a-muchos

Si una entidad **tiene una colección** de otras entidades relacionadas, y la relación se gestiona mediante una clave foránea en la tabla **de la entidad referenciada que apunta a la tabla de la entidad propietaria**, @MappedCollection se utiliza en el lado "uno" de la relación.

> Ejemplo
```
@Table("clientes")
public class Cliente {
    @Id
    private Long id;
    private String nombre;

    @MappedCollection(idColumn = "cliente_id") // 'cliente_id' es la clave foránea en la tabla 'ordenes'
    private Set<Orden> ordenes;

    // ...
}

@Table("ordenes")
public class Orden {
    @Id
    private Long id;
    private String numeroOrden;
    @Column("cliente_id")
    private Long clienteId; // Clave foránea que referencia a la tabla 'clientes'
    // ...
}
```

> Para relaciones muchos-a-muchos

En una relación muchos-a-muchos, se suele utilizar una tabla de unión (junction table) para relacionar las dos entidades. Con @MappedCollection, se puede especificar cómo se debe realizar la consulta para obtener las entidades relacionadas a través de esta tabla de unión.

> Ejemplo
```
@Table("productos")
public class Producto {
    @Id
    private Long id;
    private String nombre;

    @MappedCollection(keyColumn = "producto_id", valueColumn = "categoria_id")
    private Set<Long> categorias; // IDs de las categorías relacionadas

    // ...
}

// No necesitas una clase explícita para la tabla de unión si solo contiene las claves foráneas.
// Spring Data JDBC manejará la consulta basándose en la configuración de @MappedCollection.
// Asumimos una tabla 'producto_categoria' con columnas 'producto_id' y 'categoria_id'.
```
En este caso, @MappedCollection indica que la colección categorias contiene los IDs de las categorías relacionadas. Spring Data JDBC utilizará la tabla de unión producto_categoria para obtener estos IDs.

<a id="generacion-de-claves"></a>
#### Generación de Claves
Spring Data JDBC facilita la configuración de cómo se generan las claves primarias para las entidades creadas dentro del proyecto.

<a id="la-anotacion-id-y-generated-value"></a>
##### Las anotaciones @Id y @GeneratedValue

- La anotación @Id se utiliza a **nivel de atributo** para marcar el atributo como la clave primaria de la entidad
- La anotación @GeneratedValue se utiliza junto con @Id para especificar la **estrategia de generación de la clave primaria**. Spring Data JDBC soporta diferentes estrategias, aunque la más común para bases de datos relacionales es la **generación automática por la base de datos**.

```
import org.springframework.data.annotation.Id;
import org.springframework.data.annotation.GeneratedValue;
import org.springframework.data.relational.core.mapping.Table;
import org.springframework.data.relational.core.mapping.Column;

@Table("productos")
public class Producto {
    @Id
    @GeneratedValue // Indica que la base de datos generará el valor del ID
    private Long id;

    private String nombre;
    private Double precio;

    // ...
}
```

<a id="relaciones"></a>
#### Relaciones
El manejo de relaciones es un aspecto importante del mapeo objeto-relacional. Spring Data JDBC proporciona mecanismos para mapear las relaciones más comunes entre entidades.

<a id="uno-a-uno"></a>
##### Uno-a-Uno
En una relación uno-a-uno, cada instancia de una entidad **está relacionada con exactamente una instancia de otra entidad**. En la base de datos, esto se suele implementar con una clave foránea en una de las tablas que referencia la clave primaria de la otra tabla.

La forma de mapear una relación uno-a-uno depende de la propiedad de la relación (qué tabla contiene la clave foránea).

1. Si la entidad actual tiene la clave foránea, entonces, se utiliza un atributo de la entidad relacionada y se mapea a la columna de la clave foránea.

> [!IMPORTANT]
>  No hay una anotación @OneToOne explícita como en JPA. La relación se infiere por la existencia del atributo de la otra entidad y la clave foránea.

2. Si la otra entidad tiene la clave foránea, entonces, se utiliza @MappedCollection en la entidad propietaria para indicar la relación.

> Ejemplo

```
@Table("personas")
public class Persona {
    @Id
    private Long id;
    private String nombre;

    @Column("direccion_id") // Clave foránea a la tabla 'direcciones'
    private Long direccionId;

    // ...
}

@Table("direcciones")
public class Direccion {
    @Id
    private Long id;
    private String calle;
    private String ciudad;

    // ...
}
```

En este caso, la tabla personas tiene una clave foránea direccion_id que referencia la tabla direcciones. En la clase Persona, el atributo direccionId representa esta relación. Para acceder a la entidad Direccion, es necesario realizar una consulta separada utilizando el direccionId.

<a id="uno-a-muchos"></a>
##### Uno-a-Muchos
En una relación uno-a-muchos, una instancia de una entidad está relacionada con múltiples instancias de otra entidad. Esto se implementa con una clave foránea en la tabla de la entidad "muchos" que referencia la clave primaria de la entidad "uno".

Como vimos con @MappedCollection, esta anotación se utiliza en el lado "uno" de la relación para indicar la colección de entidades "muchos"

```
@Table("clientes")
public class Cliente {
    @Id
    private Long id;
    private String nombre;

    @MappedCollection(idColumn = "cliente_id") // 'cliente_id' es la clave foránea en la tabla 'ordenes'
    private Set<Orden> ordenes;

    // ...
}

@Table("ordenes")
public class Orden {
    @Id
    private Long id;
    private String numeroOrden;
    @Column("cliente_id")
    private Long clienteId; // Clave foránea que referencia a la tabla 'clientes'
    // ...
}
```

<a id="muchos-a-muchos"></a>
##### Muchos-a-Muchos
En una relación muchos-a-muchos, múltiples instancias de una entidad pueden estar relacionadas con múltiples instancias de otra entidad. Esto se implementa utilizando una tabla de unión (junction table) que contiene las claves foráneas de ambas tablas relacionadas.

```
@Table("productos")
public class Producto {
    @Id
    @GeneratedValue
    private Long id;
    private String nombre;

    @MappedCollection(keyColumn = "producto_id", valueColumn = "categoria_id")
    private Set<Long> categorias; // IDs de las categorías relacionadas

    // ...
}
```

<a id="repositorios-en-spring-data-jdbc"></a>
### Repositorios en Spring Data JDBC
Como se ha mencionado anteriormente, los repositorios en Spring Data son una abstracción para el acceso a datos. Spring Data JDBC sigue este paradigma, proporcionando interfaces que comunmente son extendidas para interactuar con las tablas de la base de datos.

<a id="creacion-de-interfaces"></a>
#### Creación de Interfaces de Repositorio

<a id="query-methods-spring-datajdbc"></a>
#### Query Methods

<a id="la-anotacion-query"></a>
#### La anotación @Query

<a id="la-anotacion-param"></a>
#### La anotación @Param

<a id="consultas-nativas"></a>
#### Consultas Nativas

<a id="personalizando-el-repositorio"></a>
#### Implementaciones Personalizadas de Repositorio

<a id="manejo-de-transacciones"></a>
### Manejo de Transacciones

<a id="anotacion-transactional"></a>
#### La anotación @Transactional

<a id="propagacion-de-transacciones"></a>
#### Propagación de Transacciones

<a id="auditoria"></a>
### Auditoría

<a id="la-anotacion-createdby"></a>
#### La anotación @CreatedBy

<a id="la-anotacion-lastmodifiedby"></a>
#### La anotación @LastModifiedBy

<a id="la-anotacion-created-date"></a>
#### La anotación @CreatedDate

<a id="la-anotacion-last-modfied-date"></a>
#### La anotación @LastModifiedDate

<a id="eventos-de-dominio"></a>
### Eventos de Dominio
