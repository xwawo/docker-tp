# tp-docker

# 1-1 Dockerfile :  

FROM postgres:14.1-alpine

ENV POSTGRES_DB=db \
   POSTGRES_USER=usr \
   POSTGRES_PASSWORD=pwd

COPY CreateScheme.sql /docker-entrypoint-initdb.d
COPY InsertData.sql /docker-entrypoint-initdb.d

## Commandes : 

docker build -t <username>/postgredb .
docker network create app-network
docker run -p "5432:5432" --network app-network --name database -d postgredb
docker run -p "8090:8080" --net=app-network --name=adminer -d adminer

# 1-2
Cela permet de build et run l'application d'une traite. Si on buildait à part, cela couterait une perte de temps inutile et peut être source d'erreur.

# Explications dockerfile :

## Build
FROM maven:3.8.6-amazoncorretto-17 AS myapp-build   -> image docker maven avec le nom "myapp-build"
ENV MYAPP_HOME /opt/myapp                           -> Variable d'environnement
WORKDIR $MYAPP_HOME                                 -> Repertoire de l'application
COPY pom.xml .                                      -> ajout du pom.xml pour les dépendances maven
COPY src ./src                                      -> ajout des fichiers sources
RUN mvn package -DskipTests                         -> build le projet avec maven

## Run
FROM amazoncorretto:17                              -> image docker avec le kit java
ENV MYAPP_HOME /opt/myapp                           -> Variable env
WORKDIR $MYAPP_HOME                                 -> Repertoire de l'application
COPY --from=myapp-build $MYAPP_HOME/target/*.jar $MYAPP_HOME/myapp.jar  
                                                    -> Récupère les .jar pour faire l'executable

ENTRYPOINT java -jar myapp.jar                      -> lance le .jar

# 1-3 Document docker-compose commands

## "build"

Cette commande va faire un build de tous les services qui composent notre application

## "up"

Cette commande va demarrer tous les services, si ils pas dosponible il va faire un build d'abord.

## "images"

Cette commande permet de lister les images qui ont été construite par notre docker-compose

## "ps"

Cette commande permet de lister les conteneurs de notre application

## "start/stop 'service-name'"
