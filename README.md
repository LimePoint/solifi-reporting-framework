# Solifi Realtime Reporting Kafka Consumer

Solifi Realtime Reporting application is a Kafka Consumer that allows customers to read messages from Solifi brokers and persist it in the database of choice. The consumer is generic and can be used by any customer using Solifi services and allows modifications via configuration files.

The consumer provides the following functionalities:

1. No code approach to consume Solifi generated messages.
2. Support for deploying as a standalone application or on a container platform of choice.
3. Running multiple consumers to scale up message consumption.
4. Support for multiple backend databases.
5. Access to LimePoint Support Portal.
6. Free consumer upgrades when changes occur in upstream Solifi.

## Supported Deployment Methods
The consumer can be deployed as a standalone Java application or as a container.

## Supported Backend Databases
The consumer can persist messages to the following databases:

1. Postgre SQL
2. Microsoft SQL
3. MySQL
4. MariaDB
5. Oracle Database

You must provide a user that can create/update database schemas in the database. The consumer comes pre-built with schema information and manages it automatically.

## Access To LimePoint Support
To get access to any issues related to the workings of the consumer, customers should raise a ticket at https://support.limepoint.com. Speak to your account manager on how to get access or drop an email at support@limepoint.com

## Java Version Support
The consumer is supported with the following Java versions:

1. OpenJDK 11

## Anatomy Of The Consumer
The consumer artefact consists for three important files which are all required for it to function properly, viz.

1. The Java archive (solifi-consumer-<version>.jar)
2. A license file
3. A sample application.yaml file

### The Consumer Application
The consumer application has inbuilt functionality to manage the backend database schemas. When the consumer starts for the first time, it checks if the database has the required schemas. If the schemas are missing, it will create all the schemas that it is aware of. Note that the consumer **will** create all schemas it knows of, irrespective of the schemas you may want. However, it will only persist messages in the schemas that you specify in the `application.yaml` file as discussed later in this document.

When Solifi publishes new schemas or makes changes to existing schemas, LimePoint will publish a new Consumer Jar/docker image. Once the new app is started, it will detect what changes are required to the backend database and make appropriate changes. The changes are idempotent in nature, i.e. the consumer won't make any changes to the database schemas if they already are up-to date.

### Understanding Application.yaml File
The behavior of the consumer is controlled via a application.yaml file. This file directs the consumer on which brokers to read data from, what backend database to persist data to, how many threads to run etc. You can strip out configurations that you don't need in your application.yaml. The sample below shows all possible configurations that are currently supported. 

```yaml
spring:
  kafka:
    consumer:
      group-id: # e.g. myabc-consumer-1; Enter  your consumer group ID here. You can get this information from your Solifi representative. 
    bootstrap-servers: # e.g. pkc-89hc.ap-southeast-2.aws.confluent.cloud:9092; This is the address of your Solifi brokers. You can get this information from your Solifi representative. 
    properties:
      schema:
        registry:
          url: # e.g. https://myabc-123.ap-southeast-2.aws.confluent.cloud; This is the address of your schema registry. You can get this information from your Solifi representative. 
          basic.auth.user.info: API-KEY:API-SECRET # e.g. AOI8hCTKKA:51m2VSl0yn3515ixYLJN7S0ykawEMo5qtm2E2; Credentials to connect to the schema registry. This is the address of your schema registry. You can get this information from your Solifi representative.
      ssl.endpoint.identification.algorithm: https
      sasl.mechanism: PLAIN
      security.protocol: SASL_SSL
      retry.backoff.ms: 500
      basic.auth.credentials.source: USER_INFO
      sasl.jaas.config: org.apache.kafka.common.security.plain.PlainLoginModule required username="API-KEY" password="API-SECRET"; # e.g You must enter your username and password for Kakfa here. You can get this information from your Solifi representative.
  # Datasources control where the messages are stored in the backend.
  datasource:
    ## Microsoft SQL
    #url: <sql server names>
    #driver-class-name: com.microsoft.sqlserver.jdbc.SQLServerDriver
    #username: <sql username>
    #password: <sql password>
    ## H2
    #url: jdbc:h2:mem:test
    #driver-class-name: org.h2.Driver
    #username: sa
    #password:     
    ## PostgreSQL
    #url: jdbc:postgresql://192.156.31.82:5432/solifidb
    #driver-class-name: org.postgresql.Driver
    #username: postgres
    #password: postgres
    ## MySql
    #url: jdbc:mysql://192.168.29.82:3306/solifidb
    #driver-class-name: com.mysql.cj.jdbc.Driver
    #username: root
    #password: mysql    
    ## Oracle
    #url: jdbc:oracle:thin:@192.156.31.82:1521/XE
    #driver-class-name: oracle.jdbc.OracleDriver
    #username: sys as sysdba
    #password: Oracle123$    
    ## Maria DB
    #url: jdbc:mariadb://192.156.31.82:3304/solifidb
    #driver-class-name: org.mariadb.jdbc.Driver
    #username: root
    #password: mariadb123
  # The consumer uses Liquibase to manage and maintain the schemas in the target database. We use two properties to direct Liquibase on the type of database and prefix to be used.
  # liquibase.database.type # database type client uses.
  # liquibase.client.prefix # client prefix used in the schema registry. Same as the value of _solifi.prefix_ in application.yaml
  liquibase:
    parameters:
      liquibase:
        client:
          prefix: # Client prefix for Solifi brokers. e.g. uat.myabc.ILS.canonical. Same as solifi.prefix
        database:
          type: sqlserver # database type used by the client. supported types: mariadb, mysql, oracle, postgres, sqlserver
    
logging:
  level:
    com.limepoint.solifi: INFO

solifi:
  topics: # List of topics the client wishes to consume from Solifi brokers. Note that the topic names must not have any prefix.
  #- addl_lessor_nf
  #- addl_parame_euro_dd_lessors
  #- address_nf
  concurrency: 5
  prefix: # Client prefix for Solifi brokers. e.g. uat.myabc.ILS.canonical
  license:
    enc:
      path: # Path of the license file provided by LimePoint.

```

## Deployment Methods

You must have your license file ready and the application.yaml file ready before you start the deployment.

### Deploying Standalone

To deploy as a standalone application:

* Download the latest solifi-consumer-<version>.jar. Reach out to LimePoint Support on how to get access.
* Place the application.yaml in the same folder as the jar.
* Place the license.enc file in a desired folder and update the path in the application.yaml.
* Make sure you are in the same folder as the jar file and application.yml file and run the jar file using the below command. This will start the application and create the database tables and start consuming from the kafka topics specified under _solifi.topics_ config.

````
java -jar solifi-consumer-<version>.jar
````

### Deploying As Docker Container

To deploy the consumer as a container, you must have access to LimePoint's docker hub. Please reach out to LimePoint Support to get your access. Once you have access to the repositories, login and pull the image.
```bash
docker login -u <username_provided_by_limepoint_support> # Use the password provided by support when prompted
docker image pull limepoint/solifi-reporting:<version>
```

To run in a docker container, a docker compose file needs to be created. Make sure the application configs are placed correctly where the application could read and the paths in the configs are updated in the correct path formats. Below is a sample docker file. 
You should also make sure the environmental variable, spring.config.additional-location is passed in correctly pointing to the application.yml path.
````
services:
  solifi-consumer:
    image: limepoint/solifi-reporting:<version>
    volumes:
      - /<path_of_application.yaml_on_your_system>:/application.yaml
      - /<path_of_license_file_on_your_system>:/license.enc
    environment:
      - spring.config.additional-location=/application.yml # path of the external application.yaml file
````

Start-up the docker container using, 
````
docker compose up
````

### Deploying on Azure Kubernetes (AKS)

To deploy to AKS, you must first create the required infrastructure. For example, you must create the AKS cluster. If you are planning to use MS SQL, you must also create the database services. 

Since the consumer requires `application.yaml` and `license` files to function correctly, you must make provisions for the Consumer to read those configurations. One way to do that is to make use of `configMaps`. 

#### Create ConfigMaps
Use the following commands to create configMaps to store the contents of the license file and the application.yaml file.
````
kubectl create configmap consumer-config --from-file=application.yaml --from-file=license.enc
kubectl describe configmaps consumer-config
kubectl edit configmap -n <namespace> <configMapName> -o yaml
````

#### Create Deployment
You must now create the `deployment.yaml` to allow AKS to deploy your configurations.
````
apiVersion : apps/v1
kind: Deployment
metadata:
  name: solificonsumer # name of the deployment 
spec:
  replicas: 1
  selector:
    matchLabels: # Match all pods with the defined labels
      app: solificonsumer
  template: # deployemnt wraps a pod. Defines the pod information
    metadata: # metadata of the pod
      labels:
        app: solificonsumer # pods will not have given names, this is to identify and group pods 
    spec:
      containers: # A pod wraps one or more containers. Here we define all containers
        - name: solificonsumer-container 
          image: limepoint/solifi-reporting:<version>
          env: 
          - name: spring.config.additional-location
            value: "/config/application.yaml"
          volumeMounts: # mount the volume in to the container
          - name: config-volume
            mountPath: /config
            readOnly: true
      imagePullSecrets: # credentials needed to pull the image from the registry
        - name: regcred
      volumes: # creates the volumes
        - name: config-volume # volume to hold spring configs
          configMap: 
            name: consumer-config  
            items:
            - key: "application.yaml"
              path: "application.yaml" 
            - key: "license.enc"
              path: "license.enc"      
````

Apply the deployment.yaml. This will create a new deployment under 'Azure Workloads' and start-up the 'solificonsumer' container pods.
````
kubectl apply -f ./deployment.yaml
````

#### Validate Container Logs

You can view the logs from the pods to verify if container started successfully.
```
kubectl get pods
kubectl logs --follow <pod name> --all-containers
```

#### References

Refer https://kubernetes.io/docs/concepts/overview/working-with-objects/ and https://kubernetes.io/docs/concepts/configuration/configmap/#using-configmaps-as-files-from-a-pod for more information on how to work with AKS deployments and configMaps.

### Deploying on ECS
TBD

### Deploying on K8s
TBD

## Advanced Deployment Methods

### Running Multiple Consumers
Solifi-consumer application is able to run multiple consumers in parallel, writing to the same database. Parallel running will be helpful when there are topics which needs more capacity to process data like transactional data or where there are multiple numbers of topics to read from. This way we could allocate different topics to different consumers and reduce the processing time, resources per consumer. 

**Eg1:** **Multiple consumers with different topics**

Start an instance of solifi-consumer.jar as Consumer1 with TopicA(update the application.yml with the topic name).

````
...
solifi:
    topics:
        - TopicA
...
````
Run,
` java -jar solifi-consumer-<version>.jar`

Start another instance of solifi-consumer.jar as Consumer2 with TopicB by copying the jar and application.yml to a new folder. Update the application.yml with the topic names.

````
...
solifi:
    topics:
        - TopicB
...
````
Run,
` java -jar solifi-consumer-<version>.jar`

* Tables will be created only once.
* Consumer1 will connect and read all data from TopicA.
* Consumer2 will connect and read all data from TopicB.
* Both will process data separately without any interference and will insert/update data into the tables in parallel.


**Eg2:** **Multiple consumers with overlapping topics**

Start an instance of solifi-consumer.jar as Consumer1 with TopicA & TopicB.
````
...
solifi:
    topics:
        - TopicA
        - TopicB
...
````
Run,
` java -jar solifi-consumer-<version>.jar`

Start a different instance of solifi-consumer.jar as Consumer2 with TopicB & TopicC by copying the jar and application.yml to a new folder. Update the application.yml with the topic names.
````
...
solifi:
    topics:
        - TopicB
        - TopicC
...
````
Run,
` java -jar solifi-consumer-<version>.jar`

Start another instance of solifi-consumer.jar as Consumer3 with TopicD by copying the jar and application.yml to another new folder. Update the application.yml with the topic name.
````
...
solifi:
    topics:
        - TopicD
...
````
Run,
` java -jar solifi-consumer-<version>.jar`

* Tables will be created only once. 
* Consumer1 will connect and read all data from TopicA and TopicB.
* Consumer2 will connect and read all data from TopicC. If TopicB has multiple partitioning, then Consumer2 will also receive data from TopicB to process. This will depend on the kafka brokers. Stopping Consumer2 will re-allocate all data from TopicB into Consumer1.
* If TopicB only has one partitioning, then Consumer2 will not receive data from TopicB.
* Consumer3 will connect and read all data from TopicD.
* All consumers will process data separately without any interference and will insert/update data into the tables in parallel.

### Running Consumers in Different environments
You need to update the application.yml file according to the environment. 
Additionally, if it is a kubernetes deployment and configMaps are used, then configMaps of that namespace should also be updated.
