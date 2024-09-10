# Solifi Realtime Reporting Kafka Consumer


<!-- TOC -->
* [Solifi Realtime Reporting Kafka Consumer](#solifi-realtime-reporting-kafka-consumer)
  * [Change Log](#change-log)
    * [Release 1.1.3](#release-113)
    * [Release 1.1.2](#release-112)
    * [Release 1.1.1](#release-111)
    * [Release 1.0.12](#release-1012)
    * [Release 1.0.11](#release-1011)
    * [Release 1.0.10](#release-1010)
        * [Schema Changes](#schema-changes)
    * [Release 1.0.9](#release-109)
        * [Schema Changes](#schema-changes-1)
    * [Release 1.0.8](#release-108)
        * [Schema Changes](#schema-changes-2)
    * [Release 1.0.7](#release-107)
        * [Schema Changes](#schema-changes-3)
    * [Release 1.0.6](#release-106)
        * [Schema Changes](#schema-changes-4)
    * [Release 1.0.5](#release-105)
        * [Schema Changes](#schema-changes-5)
    * [Release 1.0.4](#release-104)
        * [Schema Changes](#schema-changes-6)
    * [Release 1.0.3](#release-103)
  * [Supported Deployment Methods](#supported-deployment-methods)
  * [Supported Backend Databases](#supported-backend-databases)
  * [Access To LimePoint Support](#access-to-limepoint-support)
  * [Java Version Support](#java-version-support)
  * [Anatomy Of The Consumer](#anatomy-of-the-consumer)
    * [The Consumer Application](#the-consumer-application)
    * [Understanding Application.yaml File](#understanding-applicationyaml-file)
  * [Deployment Methods](#deployment-methods)
    * [Deploying Standalone](#deploying-standalone)
    * [Deploying As Docker Container](#deploying-as-docker-container)
    * [Deploying on Azure Kubernetes (AKS)](#deploying-on-azure-kubernetes-aks)
        * [Create ConfigMaps](#create-configmaps)
        * [Create Deployment](#create-deployment)
        * [Validate Container Logs](#validate-container-logs)
        * [References](#references)
    * [Deploying on ECS](#deploying-on-ecs)
    * [Deploying on K8s](#deploying-on-k8s)
  * [Advanced Deployment Methods](#advanced-deployment-methods)
    * [Running Multiple Consumers](#running-multiple-consumers)
    * [Running Consumers in Different environments](#running-consumers-in-different-environments)
  * [Handling Date & Time](#handling-date--time)
  * [Scaling Consumer Application](#scaling-consumer-application)
  * [Error Handling](#error-handling)
  * [Auditing](#auditing)
    * [How Auditing Works](#how-auditing-works)
    * [Points to note](#points-to-note-)
<!-- TOC -->

## Change Log

### Release 1.1.3

Updated schemas for ILS 1.24.17.

### Release 1.1.2

This release adds support for all schemas supported by Asset Based Lending - ABL (1.24.15) application.  

### Release 1.1.1

This release adds support for all schemas supported by ILS (1.24.15), Originations FMO (1.24.15) and PM-CMS (1.24.12) applications.  
Also added support to automatically map all topics ending with `_v2` in ILS to their respective backend schemas.

### Release 1.0.12

##### Schema Changes
|   | Topic Name                  | Fields Added | Fields Deleted | Other Updates | Status    |
|---|-----------------------------|--------------|----------------|---------------|-----------|
| 1 | as_fed_depr_nf              |              |                |               | New Topic |
| 2 | as_oper_depr_nf             |              |                |               | New Topic |
| 3 | de_addl_pymt_dap_bi_key_ph  |              |                |               | New Topic |
| 4 | de_addl_pymt_detail_nf      |              |                |               | New Topic |
| 5 | de_pymt_detail_nf           |              |                |               | New Topic |
| 6 | ls_add_ausr_additional_data |              |                |               | New Topic |
| 7 | ls_chng_log_nf              |              |                |               | New Topic |
| 8 | re_master_nf                |              |                |               | New Topic |
|   |                             |              |                |               |           |


### Release 1.0.11

- The `insert_date` column in the audit tables now honour the property `data.timezone` unlike previous versions where this property was only used for non-audit tables and defaulted to `UTC`. Fixes MINTSD-1344.

### Release 1.0.10

##### Schema Changes

New columns will be added into existing tables.

|   | Topic Name                | Fields Added         | Fields Deleted | Other Updates | Status         |
|---|---------------------------|----------------------|----------------|---------------|----------------|
| 1 | ls_gl_history_nf          | gh_unique_trans_id   |                |               | Existing Topic |
| 2 | ls_ctd_pymthist_nf        | h_direct_debit_file  |                |               | Existing Topic |
|   |                           | h_unique_trans_id    |                |               |                |
| 3 | cs_defaults_nf            | cd_primary_currency  |                |               | Existing Topic |
| 4 | il_gl_transfer_journal_nf | tj_gl_fund_exch_rate |                |               | Existing Topic |
|   |                           | tj_gl_fund_currency  |                |               |                |
|   |                           | tj_gl_currency       |                |               |                |
|   |                           |                      |                |               |                |


### Release 1.0.9

**Changes**
1. The application will now halt instead of logging a warning if it encounters errors stemming from invalid configurations, such as incorrect schema registry values, during the process of data deserialization.

##### Schema Changes

This release will delete the below column, which could result in a potential data loss.

|   | Topic Name         | Fields Added | Fields Deleted | Other Updates | Status         |
|---|--------------------|--------------|----------------|---------------|----------------|
| 1 | ls_check_history_n |              | ch_currency    |               | Existing Topic |
|   |                    |              |                |               |                |


### Release 1.0.8

**Changes**
1. Any data filed mismatches between what's sent and what is in the database will throw an exception and add into error_log table.

##### Schema Changes
|   | Topic Name                  | Fields Added | Fields Deleted | Other Updates | Status    |
|---|-----------------------------|--------------|----------------|---------------|-----------|
| 1 | ls_variabl_variable_date_ph |              |                |               | New Topic |
|   |                             |              |                |               |           |

### Release 1.0.7

**Changes**
1. License start date is validated. Application will not be started before the start date in the license.
2. Executing ddl scripts can be disabled using spring.liquibase.enabled configuration if client desire to maintain the database on their own. 

**Bug fixes**
1. Fixed a class loading issue thrown when running the application through the jar.

##### Schema Changes
No new schema changes

### Release 1.0.6

**Changes**
1. Introduced auditing functionality. If Audits are enabled, audit tables are created and data is inserted for auditing. Refer the section [Auditing](#auditing) later in this document.

##### Schema Changes

|   | Topic Name                  | Fields Added | Fields Deleted | Other Updates | Status    |
|---|-----------------------------|--------------|----------------|---------------|-----------|
| 1 | as_add_ausr_additional_data |              |                |               | New Topic |
| 2 | as_fed_depr_nf              |              |                |               | New Topic |
| 3 | as_oper_depr_nf             |              |                |               | New Topic |
| 4 | data_streaming              |              |                |               | New Topic |
| 5 | ls_add_ausr_additional_data |              |                |               | New Topic |
| 6 | ls_pymt_schedule_nf         |              |                |               | New Topic |
| 7 | re_master_nf                |              |                |               | New Topic |
|   |                             |              |                |               |           |

### Release 1.0.5

**Changes**  
1. Introduced error_log table to log any data related errors when consuming from upstream brokers. This table will store any events that the Consumer could not process. Refer the section [Error Handling](#error-handling) later in this document.
2. The consumer will now add default values for fields as outlined in the **Schema Changes** table below. 

**Bug fixes**  
1. Switched `TIMESTAMP` to `DATETIME` to accept negative timestamp values for MySQL and MariaDb database to overcome the date limitation.
2. Downsized the size of `VARCHAR` columns from `VARCHAR(4000)` to `VARCHAR(100)` to avoid exceeding row size limitations for MariaDB and MySQL.
3. Fields with logical type `timestamp-micros` will be considered as a timestamp equivalent column, similar to logical type `timestamp-millis`.

##### Schema Changes

|   | Topic Name     | Fields Added | Fields Deleted | Other Updates                                                               | Status         |
|---|----------------|--------------|----------------|-----------------------------------------------------------------------------|----------------|
| 1 | addl_lessor_nf |              |                | - **al_tax_engine**: Field default value is changed from **null** to **I**. | Existing Topic |
|   |                |              |                |                                                                             |                |


### Release 1.0.4

This release has some minor bug fixes.

1. Fixed a date conversion issue for mandatory date fields.
2. Internal enhancements and code cleanups (Removed internal code structure for producer and set default values for optional fields).
3. Disabled automatic topic creations by consumer.
4. Enhanced logging capability for more clarity on the logs.

##### Schema Changes

|   | Topic Name      | Fields Added | Fields Deleted | Other Updates                                                                                | Status         |
|---|-----------------|--------------|----------------|----------------------------------------------------------------------------------------------|----------------|
| 1 | as_open_item_nf |              |                |                                                                                              | New Topic      |
| 2 | as_user3_nf     |              |                |                                                                                              | New Topic      |
| 3 | ds_connector_2  |              |                | - **last_update_ts**: logical type has changed from _timestamp-millis_ to _timestamp-micros_ | Existing Topic |
|   |                 |              |                |                                                                                              |                |

### Release 1.0.3

This release introduces breaking changes for customers that are on version **1.0.2** or lower. Please contact LimePoint support if you are upgrading to **1.0.3**. For fresh installations please be aware of the new configurations this release introduces.

1. Enhanced license validation introduced and exposed via config **solifi.license.path** and **solifi.license.signature.path**. Please get in touch with LimePoint support to grab the new licenses. Property **solifi.license.enc** has been deprecated and removed.
2. Added config **solifi.data.timezone** to allow custom timezones when persisting data in the target databases. Default would be system timezone where the consumer application runs. Values should be as a [TZ Identifier](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones). eg: Australia/Adelaide. Updating this config will **change** existing records. To update the tables with a timezone value, simply change the config in application.yml and restart the consumer application with a new groupID or reset the offset of the existing topics for the existing groupID.
3. Enhanced how the consumer app handles table metadata and change management. This change will now allow customers running multiple copies of the consumer application across platforms and point to the same database. e.g One instance can run on docker and another instance can run as a standalone and both instances can write to the same database.

---

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

1. Postgres SQL
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
2. A license files (license.license and signature file)
3. A sample application.yaml file

### The Consumer Application
The consumer application has inbuilt functionality to manage the backend database schemas. When the consumer starts for the first time, it checks if the database has the required schemas. If the schemas are missing, it will create all the schemas that it is aware of. Note that the consumer **will** create all schemas it knows of, irrespective of the schemas you may want. However, it will only persist messages in the schemas that you specify in the `application.yaml` file as discussed later in this document.

When Solifi publishes new schemas or makes changes to existing schemas, LimePoint will publish a new Consumer Jar/docker image. Once the new app is started, it will detect what changes are required to the backend database and make appropriate changes. The changes are idempotent in nature, i.e. the consumer won't make any changes to the database schemas if they already are up-to date.

### Understanding Application.yaml File
The behavior of the consumer is controlled via an application.yaml file. This file directs the consumer on which brokers to read data from, what backend database to persist data to, how many threads to run etc. You can strip out configurations that you don't need in your application.yaml. The sample below shows all possible configurations that are currently supported. 

```yaml
spring:
  kafka:
    consumer:
      group-id: # e.g. myabc-consumer-1; Enter  your consumer group ID here. You can get this information from your Solifi representative. We encourage to use a new group ID to support correct E2E testing. Using an existing group ID could cause no data, because data offsets could be already read and committed from another application.
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
      #partition.assignment.strategy: # This is optional. Will default to RangeAssignor if not provided. eg: org.apache.kafka.clients.consumer.RoundRobinAssignor, org.apache.kafka.clients.consumer.StickyAssignor, org.apache.kafka.clients.consumer.RangeAssignor
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
    #enabled: true # by default this is true. This should be set to 'false' if ddl scripts shouldn't be executed through solifi-consumer application. In such case, client needs to take responsibility in running their own scripts to generate the tables and make sure the tables are ready with correct columns before starting the application. Any future changes usually received and handled through updating solifi-consumer, will also need to be handled by the client.
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
  topics: # List of topics client wishes to consume from Solifi brokers. Note that the topic names must not have any prefix.
  #- addl_lessor_nf
  #- addl_parame_euro_dd_lessors
  #- address_nf
  concurrency: 5 # for better performance this should be equal to the topic partition count
  prefix: # Client prefix for Solifi brokers. e.g. uat.myabc.ILS.canonical
  license:
    path: # Path of the license file provided by LimePoint.
    signature:
      path: # Path of the signature file provided by LimePoint.
  data.timezone: # TZ identifier in TZDB. Refer: https://en.wikipedia.org/wiki/List_of_tz_database_time_zones. If not present, reads the default system timezone and saves timestamp values in system timezone.
  audit-tables: # Topics which needs to be audited. Depending on this, audit tables will be created and data will be inserted to audit tables. Empty config will not create any audit tables.
  #- all
  #- addl_lessor_nf
  #- addl_parame_euro_dd_lessors
  #- address_nf
```

## Deployment Methods

You must have your license file ready and the application.yaml file ready before you start the deployment.

### Deploying Standalone

To deploy as a standalone application:

* Download the latest solifi-consumer-<version>.jar. Reach out to LimePoint Support on how to get access.
* Place the application.yaml in the same folder as the jar.
* Place the license.enc file in a desired folder and update the path in the application.yaml.
* Make sure you are in the same folder as the jar file and application.yml file and run the jar file using the below command. This will start the application and create the database tables and start consuming from the kafka topics specified under _solifi.topics_ config.

````bash
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
      - /<path_of_license_file_on_your_system>:/license.license
      - /<path_of_signature_file_on_your_system>:/sign256.sign
    environment:
      - spring.config.additional-location=/application.yml # path of the application.yaml file
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
kubectl create configmap consumer-config --from-file=application.yaml --from-file=license.license --from-file=sign256.sign 
kubectl describe configmaps consumer-config
kubectl edit configmap -n <namespace> <configMapName> -o yaml

# apply update to ConfigMap using dry-run and replace
kubectl create configmap consumer-config  --from-file=license.license --from-file=sign256.sign --from-file=application.yml -o yaml --dry-run=client | kubectl replace -f -
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
          #imagePullPolicy: Always #This is optional. Default would be 'IfNotPresent'. Change to 'Always' if need to pull the image whenever pod restarts.
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
            - key: "license.license"
              path: "license.license"
            - key: "sign256.sign"
              path: "sign256.sign"   
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

## Handling Date & Time
1. You would notice a warning log, **_[WARNING] Ignoring invalid logical type for name: date_** during solifi-consumer execution. The reason is due to a mismatch of a data type sent from Solifi.  Usually "logicalType" represented in avro schema for _date_ fields are expected to be of "type" _int_, but Solifi sends date fields as _long_ values. Therefore, the fields are generated as Long fields, which is warned through this warning log. For more details check on the [avro spec](https://avro.apache.org/docs/1.10.2/spec.html#:~:text=with%20RFC%2D4122-,Date,-The%20date%20logical). 
2. The timestamp-millis logical type represents an instant on the global timeline, independent of a particular time zone or calendar, with a precision of one millisecond. Please note that time zone information gets lost in this process. Upon reading a value back, we can only reconstruct the instant, but not the original representation. In practice, such timestamps are typically displayed to users in their local time zones, therefore they may be displayed differently depending on the execution environment. For more information read on the [timestamp-millis in avro spec](https://avro.apache.org/docs/1.10.2/spec.html#:~:text=00%3A00%3A00.000000.-,Timestamp,-(millisecond%20precision)). To avoid such misrepresentations we have introduced the **solifi.data.timezone** configuration where the desired timezone to be saved in the database could be decided. This config value needs to be in TZ identifier format (If not provided will default to system timezone, which is usually UTC in docker environments).
3. Date fields will follow the same conditions as timestamp values (mentioned in above point 2) where the timezone will be controlled by **solifi.data.timezone** configuration. If not specified, system default timezone will be used (If not provided will default to system timezone, which is usually UTC in docker environments).

## Scaling Consumer Application
Scaling is highly dependent on the number of partitions in a kafka topic. As per kafka concepts, consumers could be defined within a consumer group to listen to a topic in parallel. 
eg: if a topic has 5 partitions, then starting 5 consumers under the same consumer group ID will enable parallel processing by the different consumers.

Rather than starting separate consumers individually, Solifi-consumer application is capable of supporting such scenario simply by setting the number of concurrent threads/listeners count of the application by increasing the solifi.concurrency configuration in application.yml. 
eg: solifi.concurrency = 5, is equal to having 5 consumers listening to the topics. 

Additionally, if there is are topics with higher TPS, then clients could start up a separate consumer with that particular topic(s) only and set the thread count(solifi.concurrency config) to the number of partitions of the topic. 

## Error Handling
The consumer application captures any data related errors (e.g. invalid data) and loads them in the **error_log** table. This table gets created and managed by the Consumer during startup. Customers should keep track of the this table to lookup failed messages. The messages in this table are not cleaned up by the Consumer and it is expected that the clients cleanup as they see fit.

## Auditing
Auditing can be enabled by specifying the topic names as a list in 'solifi.audit-tables' config in application.yml. If this is enabled, an additional audit table as <topicname>_audit will be created in the database. Any data changes in the main tables will be recorded in audit tables. Specify 'all' in the config if all tables need to be audited or leave empty if auditing should be disabled.
Eg 1: Creates _audit tables for _all_ topics and inserts data whenever there is any change in the records of main table.
````yaml
solifi.audit-tables:
  - all
````

Eg 2: Creates _audit tables only for addl_lessor_nf topic and inserts data only when addl_lessor_nf has any change in the records.
````yaml
solifi.audit-tables:
  - addl_lessor_nf
````

### How Auditing Works
**Scenario 1:** Any data 'insert' into main tables
A similar record will be inserted into the audit table with all the metadata information, inserted user, entered time, revision and action (action=INSERT)

Eg: Insert record

_Main Table: ls_master_

| id | contract_number | invoice_date            |
|----|-----------------|-------------------------|
| 1  | ABC             | 2023-10-23 03:35:02.275 |

_Audit Table: ls_master_audit_

| id | revision | contract_number | invoice_date            | performed_action | inserted_user   | inserted_date           |
|----|----------|-----------------|-------------------------|------------------|-----------------|-------------------------|
| 1  | 1        | ABC             | 2023-10-23 03:35:02.275 | INSERT           | solifi-consumer | 2023-11-13 03:35:02.275 |

**Scenario 2:** Any data 'update' into main table records
Received updated record will be inserted into the audit table as a new record with all the metadata information, user, entered time, revision and action (action=UPDATE)

Eg: Update record

_Main Table: ls_master_

| id | contract_number | invoice_date            |
|----|-----------------|-------------------------|
| 1  | ABCDEF          | 2023-10-23 03:35:02.275 |

_Audit Table: ls_master_audit_

| id | revision | contract_number | invoice_date            | performed_action | inserted_user   | inserted_date           |
|----|----------|-----------------|-------------------------|------------------|-----------------|-------------------------|
| 1  | 1        | ABC             | 2023-10-23 03:35:02.275 | INSERT           | solifi-consumer | 2023-11-13 03:35:02.275 |
| 1  | 2        | ABCDEF          | 2023-10-23 03:35:02.275 | UPDATE           | solifi-consumer | 2023-11-14 06:33:23.234 |

**Scenario 3: Any data 'delete'**
A record with null values will be inserted in to the audit table with all the metadata information, user, entered time, revision and action (action=DELETE)

Eg: Delete record

_Main Table: ls_master_

| id | contract_number | invoice_date |
|----|-----------------|--------------|
|    | <No Records>    |              |


_Audit Table: ls_master_audit_

| id | revision | contract_number | invoice_date            | performed_action | inserted_user   | inserted_date           |
|----|----------|-----------------|-------------------------|------------------|-----------------|-------------------------|
| 1  | 1        | ABC             | 2023-10-23 03:35:02.275 | INSERT           | solifi-consumer | 2023-11-13 03:35:02.275 |
| 1  | 2        | ABCDEF          | 2023-10-23 03:35:02.275 | UPDATE           | solifi-consumer | 2023-11-14 06:33:23.234 |
| 1  | 3        | null            | null                    | DELETE           | null            | null                    |

### Points to note 

* Data in audit tables aren't cleaned up. It needs to be handled manually according to data retention policies.
* Since auditing stores a copy of the data, additional storage is required. So you must plan the storage accordingly. Usually, if you enable auditing globally (via the ALL flag), you need approximately more than twice the storage.
* Any configuration changes do not come into effect without a restart of the consumer.
