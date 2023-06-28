6# Demonstração do Quarkus: Hibernate Reactive e RESTEasy Reactive

Este é um serviço CRUD mínimo que expõe alguns endpoints sobre REST, 
com um front-end baseado em Angular para que você possa brincar com ele em seu navegador.

Embora o código seja surpreendentemente simples, sob o capô isso está usando:
 - RESTEasy Reactive para expor os endpoints REST
  - Hibernate Reactive para executar as operações CRUD no banco de dados
  - Um banco de dados PostgreSQL; veja abaixo para executar um via Docker
  - ArC, a ferramenta de injeção de dependência inspirada em CDI com sobrecarga zero

## Requisitos

Para compilar e executar esta demonstração, você precisará:

- JDK 11+
- GraalVM

Além disso, você precisará de um banco de dados PostgreSQL ou do Docker para executá-lo.

### Configurando GraalVM e JDK 11+

Certifique-se de que as variáveis de ambiente `GRAALVM_HOME` e `JAVA_HOME` tenham
definido e que um comando JDK 11+ `java` está no caminho.

Consulte o [Guia de criação de um executável nativo](https://quarkus.io/guides/building-native-image)
para obter ajuda na configuração do seu ambiente.

## Construindo a demonstração

Inicie a compilação do Maven nas fontes verificadas desta demonstração:

> ./mvnw package

## Executando a demonstração

### Codificação ao vivo com Quarkus

O plugin Maven Quarkus fornece um modo de desenvolvimento que suporta
codificação ao vivo. Para experimentar:

> ./mvnw quarkus:dev

Neste modo você pode fazer alterações no código e ter as alterações aplicadas imediatamente, apenas atualizando seu navegador.

O hot reload funciona mesmo ao modificar suas entidades JPA.
Tente! Até mesmo o esquema do banco de dados será atualizado em tempo real.

### Run Quarkus in JVM mode

When you're done iterating in developer mode, you can run the application as a
conventional jar file.

First compile it:

> ./mvnw package

Next we need to make sure you have a PostgreSQL instance running (Quarkus automatically starts one for dev and test mode). To set up a PostgreSQL database with Docker:

> docker run -it --rm=true --name quarkus_test -e POSTGRES_USER=quarkus_test -e POSTGRES_PASSWORD=quarkus_test -e POSTGRES_DB=quarkus_test -p 5432:5432 postgres:13.3

Connection properties for the Agroal datasource are defined in the standard Quarkus configuration file,
`src/main/resources/application.properties`.

Then run it:

> java -jar ./target/quarkus-app/quarkus-run.jar

Have a look at how fast it boots.
Or measure total native memory consumption...

### Run Quarkus as a native application

You can also create a native executable from this application without making any
source code changes. A native executable removes the dependency on the JVM:
everything needed to run the application on the target platform is included in
the executable, allowing the application to run with minimal resource overhead.

Compiling a native executable takes a bit longer, as GraalVM performs additional
steps to remove unnecessary codepaths. Use the  `native` profile to compile a
native executable:

> ./mvnw package -Dnative

After getting a cup of coffee, you'll be able to run this binary directly:

> ./target/hibernate-reactive-quickstart-1.0.0-SNAPSHOT-runner

Please brace yourself: don't choke on that fresh cup of coffee you just got.
    
Now observe the time it took to boot, and remember: that time was mostly spent to generate the tables in your database and import the initial data.
    
Next, maybe you're ready to measure how much memory this service is consuming.

N.B. This implies all dependencies have been compiled to native;
that's a whole lot of stuff: from the bytecode enhancements that Hibernate ORM
applies to your entities, to the lower level essential components such as the PostgreSQL JDBC driver, the Undertow webserver.

## See the demo in your browser

Navigate to:

<http://localhost:8080/index.html>

Have fun, and join the team of contributors!

## Running the demo in Kubernetes

This section provides extra information for running both the database and the demo on Kubernetes.
As well as running the DB on Kubernetes, a service needs to be exposed for the demo to connect to the DB.

Then, rebuild demo docker image with a system property that points to the DB. 

```bash
-Dquarkus.datasource.reactive.url=jdbc:postgresql://<DB_SERVICE_NAME>/quarkus_test
```
