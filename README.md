# Spring Data JPA & Spring Data JDBC

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
        - [Lecturas sucias](#lecturas-sucias)
        - [Lecturas no repetibles](#lecturas-no-repetibles)
        - [Lecturas fantasma](#lecturas-fantasma)


<a id="fundamentos-JDBC"></a>
# Fudamentos de JDBC

<a id="que-es-jdbc"></a>
## ¿Qué es JDBC?

<a id="La arquitectura de JDBC"></a>
### La arquitectura de JDBC

<a id="jdbc-api"></a>
#### JDBC API

<a id="jdbc-driver"></a>
#### JDBC Driver

<a id="jdbc-driver-manager"></a>
#### JDBC Driver Manager

<a id="datasourcer"></a>
#### DataSource

<a id="tipos-de-drivers-jdbc"></a>
### Tipos de Drivers en JDBC

<a id="tipo-de-driver-1-2-3-4"></a>
#### Tipo 1, 2, 3 y 4

<a id="establecer-conexion"></a>
### Ejemplo para establecer una conexión


<a id="interaccion-con-la-base-de-datos"></a>
## Interacción con la base de datos

<a id="statements"></a>
### Statements 

<a id="statement"></a>
#### Statement

<a id="prepared-statement"></a>
#### PreparedStatement

<a id="callable-statement"></a>
#### CallableStatement

<a id="ejecucion-de-consultas"></a>
### Ejecución de Consultas

<a id="execute-query"></a>
#### El método executeQuery

<a id="execute-update"></a>
#### El método executeUpdate

<a id="result-set"></a>
## Procesamiento de Resultados (ResultSet)

<a id="acceso-a-los-datos"></a>
### Acceso a los datos por nombre de columna o índice

<a id="tipos-en-sql-y-su-correspondencia-con-java"></a>
### Tipos de datos SQL y su correspondencia con tipos de datos Java

<a id="tipos-de-resultset"></a>
### Tipos de ResultSet

<a id="forward-only"></a>
#### forward-only

<a id="scroll-insensitive"></a>
#### scroll-insensitive

<a id="scroll-sensitive"></a>
#### scroll-sensitive

<a id="modificabilidad-de-result-set"></a>
### Modificabilidad de ResultSet

<a id="transacciones"></a>
## Transacciones

<a id="acid"></a>
### Acrónimo ACID

<a id="control-de-transacciones"></a>
### Control de transacciones en JDBC

<a id="metodo-setAutoCommit"></a>
#### Método setAutoCommit

<a id="metodo-commit"></a>
#### Método commit

<a id="metodo-rollback"></a>
#### Método rollback

<a id="niveles-de-aislamiento-en-transacciones"></a>
### Niveles de aislamiento de transacciones

<a id="lecturas-sucias"></a>
#### Lecturas sucias

<a id="lecturas-no-repetibles"></a>
#### Lecturas no repetibles

<a id="lecturas-fantasma"></a>
#### Lecturas fantasma


