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

### Execute o Quarkus no modo JVM

Quando terminar de iterar no modo de desenvolvedor, você pode executar o aplicativo como um
arquivo jar convencional.

Primeiro compile-o:

> ./mvnw package

Em seguida, precisamos garantir que você tenha uma instância do PostgreSQL em execução (o Quarkus inicia automaticamente uma para o modo de desenvolvimento e teste). Para configurar um banco de dados PostgreSQL com o Docker:

> docker run -it --rm=true --name quarkus_test -e POSTGRES_USER=quarkus_test -e POSTGRES_PASSWORD=quarkus_test -e POSTGRES_DB=quarkus_test -p 5432:5432 postgres:13.3

As propriedades de conexão para a fonte de dados Agroal são definidas no arquivo de configuração padrão do Quarkus,
`src/main/resources/application.properties`.

Em seguida, execute-o:

> java -jar ./target/quarkus-app/quarkus-run.jar

Dê uma olhada em quão rápido ele inicializa.
Ou meça o consumo total de memória nativa...

### Execute o Quarkus como um aplicativo nativo

Você também pode criar um executável nativo a partir deste aplicativo sem fazer nenhum
alterações no código-fonte. Um executável nativo remove a dependência da JVM:
tudo o que é necessário para executar o aplicativo na plataforma de destino está incluído no
o executável, permitindo que o aplicativo seja executado com sobrecarga mínima de recursos.

A compilação de um executável nativo demora um pouco mais, pois o GraalVM executa
etapas para remover codepaths desnecessários. Use o perfil `native` para compilar um
executável nativo:

> ./mvnw package -Dnative

Depois de tomar uma xícara de café, você poderá executar este binário diretamente:

> ./target/hibernate-reactive-quickstart-1.0.0-SNAPSHOT-runner

Por favor, prepare-se: não engasgue com aquela xícara de café fresco que você acabou de tomar.

Agora observe o tempo que levou para inicializar e lembre-se: esse tempo foi gasto principalmente para gerar as tabelas em seu banco de dados e importar os dados iniciais.
    
Em seguida, talvez você esteja pronto para medir quanta memória esse serviço está consumindo.

N.B. Isso implica que todas as dependências foram compiladas para nativas;
isso é um monte de coisas: desde os aprimoramentos de bytecode que o Hibernate ORM
aplica-se às suas entidades, aos componentes essenciais de nível inferior, como o driver PostgreSQL JDBC, o servidor web Undertow.

## Veja a demonstração em seu navegador

Navegar para:

<http://localhost:8080/index.html>

Divirta-se e junte-se à equipe de colaboradores!

## Executando a demonstração no Kubernetes

Esta seção fornece informações extras para executar o banco de dados e a demonstração no Kubernetes.
Além de executar o banco de dados no Kubernetes, um serviço precisa ser exposto para que a demonstração se conecte ao banco de dados.

Em seguida, reconstrua a imagem do docker de demonstração com uma propriedade do sistema que aponte para o banco de dados.

```bash
-Dquarkus.datasource.reactive.url=jdbc:postgresql://<DB_SERVICE_NAME>/quarkus_test
```
