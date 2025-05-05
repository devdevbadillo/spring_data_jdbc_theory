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
            - [La anotación @Param](#anotacion-param)
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
            - [Conversión de datos con Spring Data JDBC](#conversion-de-datos-spring-data-jdbc)
        - [Repositorios en Spring Data JDBC](#repositorios-en-spring-data-jdbc)
            - [Creación de Interfaces de Repositorio](#creacion-de-interfaces)
            - [Query Methods](#query-methods-spring-datajdbc)
            - [La anotación @Query](#la-anotacion-query)
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

<a id="spring-data-commons"></a>
### Spring Data Commons

<a id="repositories"></a>
#### Repositories

<a id="crud-repository"></a>
#### CrudRepository

<a id="paging-and-sorting-repository"></a>
#### PagingAndSortingRepository

<a id="query-method"></a>
#### Query Methods

<a id="anotacion-param"></a>
#### La anotación @Param

<a id="proyecciones"></a>
#### Proyecciones (Projections)

<a id="que-es-spring-data-jdbc"></a>
### ¿Qué es Spring Data JDBC?

<a id="configuracion-de-spring-data-jdbc"></a>
#### Configuración de Spring Data JDBC

<a id="configuracion-y-dependencias"></a>
##### Dependencias y configuración de la base de datos

<a id="#entidades-y-mapeo"></a>
### Entidades y Mapeo

<a id="anotaciones-de-mapeo"></a>
#### Anotaciones de Mapeo

<a id="la-anotacion-table"></a>
##### La anotación @Table

<a id="la-anotacion-column"></a>
##### La anotación @Column

<a id="la-anotacion-transient"></a>
##### La anotación @Transient

<a id="la-anotacion-mapped-collection"></a>
##### La anotación @MappedCollection

<a id="generacion-de-claves"></a>
#### Generación de Claves

<a id="la-anotacion-id-y-generated-value"></a>
##### Las anotaciones @Id y @GeneratedValue

<a id="relaciones"></a>
#### Relaciones

<a id="uno-a-uno"></a>
##### Uno-a-Uno

<a id="uno-a-muchos"></a>
##### Uno-a-Muchos

<a id="muchos-a-muchos"></a>
##### Muchos-a-Muchos

<a id="conversion-de-datos-spring-data-jdbc"></a>
#### Conversión de datos con Spring Data JDBC

<a id="repositorios-en-spring-data-jdbc"></a>
### Repositorios en Spring Data JDBC

<a id="creacion-de-interfaces"></a>
#### Creación de Interfaces de Repositorio

<a id="query-methods-spring-datajdbc"></a>
#### Query Methods

<a id="la-anotacion-query"></a>
#### La anotación @Query

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
