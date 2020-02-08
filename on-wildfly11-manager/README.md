# Apiman Manager Docker image
The manager component of the [Apiman](http://www.apiman.io/) - Open Source API Management project. 
This image was build as described in the [official documentation](http://www.apiman.io/latest/production-guide.html).

### Available tags
 - on wildfly11: ```1.5.5```, ```latest```

## What is [Apiman](http://www.apiman.io/)?
From Apiman website:
> The apiman project brings an open source development methodology to API Management, 
> coupling a rich API design & configuration layer with a blazingly fast runtime.
> Features:
>  - **Govern Your APIs** - Flexible, policy-based runtime governance for your APIs. Offer the same API through multiple plans, allowing different levels of service to different API consumers.
>  - **Rich Management Layer** - Use the rich management layer (REST API and separate UI) to manage/configure not only APIs but also the client applications that consume them.
>  - **Easily Embeddable** - Embed the API Management Policy Engine in any application - it has a small footprint and can be completely independent from the management layer.
>  - **Fully Asynchronous** - The runtime engine's API is fully asynchronous and is designed to run equally well in both a standard Java EE environment and newer async runtimes like Vert.x.

The Apiman project is composed of two main components. A **gateway** and 
a **manager** and their dependencies.

In order to run this image you will need the following prerequisites:
 - Elasticsearch ```5.x``` 
 - PostgreSql ```9.x+``` or MySql ```5.x```
 - Keycloak ```6.x``` - To prepare the keycloak server please import the **apiman**
 realm file into your server ([apiman-realm-export.json](apiman-realm-export.json)). 
 After importing the apiman realm, don't forget to regenerate the secret for the
 **apiman** and **apiman-gateway-api** clients. And if you are there, maybe also change the passwords
 for the **admin** and **apimanager** users. The password is *admin123!* for both. Also make sure that the
 proper domains are configured for your clients. For more information on how to configure keycloak, you should check
 [the official documentation](https://www.keycloak.org/documentation.html).

 ## How to use this image
Because there is a lot of stuff to configure in order to run the apiman manager image, I find that the easiest way is via docker-compose. Example:
```yml
version: '3'
services:
  apiman-manager:
    image: "bogdanmic/apiman-manager:latest"
    container_name: apiman-manager
    logging:
      driver: "json-file"
      options:
        max-size: "100m"
        max-file: "5"
    environment:
      APIMAN_ES_PROTOCOL: http
      APIMAN_ES_HOST: localhost
      APIMAN_ES_PORT: 9200
      APIMAN_AUTH_PUBLIC_KEY: "APIMAN_REALM_PUBLIC_KEY"
      APIMAN_AUTH_SERVER_URL: http://keycoak.host:8080/auth
      APIMAN_AUTH_MANAGER_SECRET: keycloak-apiman-secret
      APIMAN_AUTH_UI_SECRET: keycloak-apimanui-secret
      APIMAN_HIBERNATE_DIALECT: apiman-hibernate-dialect
      APIMAN_DB_CONNECTION_URL: db-connection-string
      APIMAN_DB_USER_NAME: db-user-name
      APIMAN_DB_PASSWORD: db-user-password
      APIMAN_DB_VENDOR: db-vendor
    ports:
      - "9990:9990"
      - "9993:9993"
      - "8080:8080"
      - "8443:8443"
```
Don't forget to adjust the **environment variables** according to your setup.

### Environment Variables
In order to run this image you will need **elasticsearch 5.x**, **postgresql 9.x+/mysql 5.x** and **keycloak 6.x**.
You can configure these using the following environment variables:
 - ```APIMAN_ES_PROTOCOL``` - The protocol used to communicate with the elasticsearch service. e.g.```http```
 - ```APIMAN_ES_HOST``` - The host of the elasticsearch service. e.g.```localhost```
 - ```APIMAN_ES_PORT``` - The port used to communiciate with the elasticsearch service. e.g.```9200```
 - ```APIMAN_AUTH_PUBLIC_KEY``` - The public key for the apiman realm from keycloak that will be used for security.
 You can find this out from keycloak from the **apiman** realm.
 - ```APIMAN_AUTH_SERVER_URL``` - The host where the gateway can find the keycloak server. e.g.```http://localhost:8080/auth```
 - ```APIMAN_AUTH_MANAGER_SECRET``` - The  gateway secret used for authentication by the gateway with the keycloak server.
 You can find this out from the **apiman** realm and the **apiman** client.
 - ```APIMAN_AUTH_UI_SECRET``` - The  gateway secret used for authentication by the gateway with the keycloak server.
 You can find this out from the **apiman** realm and the **apimanui** client. Bear in mind that this is not needed
 if your client is public.
 - ```APIMAN_HIBERNATE_DIALECT``` - The hibernate dialect to be used to communicate with the database.
 Possible values: ```io.apiman.manager.api.jpa.ApimanPostgreSQLDialect```, ```io.apiman.manager.api.jpa.ApimanMySQL5Dialect```
 - ```APIMAN_DB_CONNECTION_URL``` - The database connection string that the apiman manager will use to connect to.
 e.g. ```jdbc:postgresql://localhost:5432/apiman_db```
 - ```APIMAN_DB_USER_NAME``` - The username to access the database e.g.```postgres```
 - ```APIMAN_DB_PASSWORD``` - The password to access the database e.g.```postgres```
 - ```APIMAN_DB_VENDOR``` - The driver to use to connect to the database. Possible values: ```postgres```, ```mysql```