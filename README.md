# micro_services_application

### Objectif :

Mise en oeuvre d'une application distribuée basée sur deux micro-services en utilisant les bonnes pratiques  :
  - Couches DA0, Service, Web, DTO
  - Utilisation de MapStruct pour le mapping entre les objet Entities et DTO
  - Génération des API-DOCS en utilisant SWAGGER3 (Open API)
  - Communication entre micro-services en utilisant OpenFeign
  - Spring Cloud Gateway
  - Eureka Discovery Service
  - Utilisation de Docker pour exécuter les applications 
 
### Application « Customer »

![image](https://user-images.githubusercontent.com/62363101/198904818-ffb9611a-ce9e-4227-b844-51b62c633aa3.png)

### Application « Billing »

![image](https://user-images.githubusercontent.com/62363101/198904841-9f6fc4dc-a074-4d03-9efd-0dbcb0cd7f1a.png)

### Application « Eureka service »

![image](https://user-images.githubusercontent.com/62363101/198904866-fc6a01f0-6bf7-4bc1-bb18-01df849ff232.png)

### Application « gateway »

![image](https://user-images.githubusercontent.com/62363101/198904875-99a5dda7-6670-4cd1-8ad3-3a84c356b023.png)

## Partie Docker
En utilisant Docker-compose, nous pouvons créer les quatre images en même temps et exécuter également les conteneurs.

### Création des packages (fichiers Jar)
Nous devons ensuite créer les fichiers jar pour les quatre applications à l'aide de la commande ci-dessous :
-mvn clean install

### Création les fichiers Docker
Création les fichiers Docker pour les quatre applications. Le contenu des fichiers docker serait presque le même que celui indiqué ci-dessous,il faut just modifier le port et le nom de fichier jar:

![image](https://user-images.githubusercontent.com/62363101/198904981-9d56a0e3-8ca3-438f-9fd8-560faa852bec.png)

### Création de docker-compose.yml
Nous devons créer le fichier docker-compose.yml dans toutes les applications rootDocker.
et nous devons créer 4 répertoires qui contiendraient le fichier docker et le fichier jar pour chaque application. Ceci est montré ci-dessous
##### docker-compose.yml

version: '3'
services:
  eureka-service:
    build: EurikaService/
    hostname: eureka-service
    ports:
      - "8761:8761"
    networks:
      - default-network

  openlab-customer-service:
    build: CustomerService/
    hostname: openlab-customer-service
    ports:
      - "8081:8081"
    depends_on:
      - eureka-service
    environment:
      - eureka.client.service-url.defaultZone=http://eureka-service:8761/eureka
    networks:
      - default-network

  enset-billing-service:
    build: BillingService/
    hostname: enset-billing-service
    ports:
      - "8083:8083"
    restart: on-failure
    depends_on:
      - openlab-customer-service
      - eureka-service
    environment:
      - eureka.client.service-url.defaultZone=http://eureka-service:8761/eureka
    networks:
      - default-network

  gateway:
    build: GatewayService/
    hostname: gateway
    ports:
      - "9999:9999"
    depends_on:
      - openlab-customer-service
      - enset-billing-service
      - eureka-service
    environment:
      - eureka.client.service-url.defaultZone=http://eureka-service:8761/eureka
    networks:
      - default-network

networks:
  default-network:
    driver: bridge

### Démarrer les containers
Enfin, nous devons allumer les conteneurs. Pour ce faire, dans le terminal, et exécutez la commande ci-dessous:
- docker-compose up --build

### Structure de nouveau Projet

![image](https://user-images.githubusercontent.com/62363101/198905133-31a5baf5-56df-4d19-aa50-7f21df87d597.png)

### Les images de docker

![image](https://user-images.githubusercontent.com/62363101/198905164-97622d63-5d5b-493b-87d1-532a8b9aa029.png)

![image](https://user-images.githubusercontent.com/62363101/198905172-910a5f9c-564f-4069-b8f9-ef30e6780731.png)

### Les containers de docker

![image](https://user-images.githubusercontent.com/62363101/198905196-dc6d9081-93d5-4e87-8cbc-28d93c360de1.png)

### Execution avec Docker
#### Eureka-servie

![image](https://user-images.githubusercontent.com/62363101/198905254-7722bc1f-4a79-425b-b34b-6ae4c80a0724.png)

#### Customer-service

![image](https://user-images.githubusercontent.com/62363101/198905294-99cddd44-1c9e-4139-960f-12ddd402049e.png)

#### Billing-service

![image](https://user-images.githubusercontent.com/62363101/198905327-c302731e-35a9-43dc-b251-9baf2dc7b12a.png)






