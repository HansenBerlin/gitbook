# 7 Deployment

### Builds in der Pipeline

Builds für dsn Webservice sowie du REST API erfolgen mit Azure Devops. Im Beispiel ist das Script für den Webservice zu sehen. Die Pipeline wird getriggert, sobald in die Branches Development (Beispiel) oder Release (Zeile 2) gemergt wird. Als Artefakt entsteht ein Dockerimage welches ans Hub mit dem entsprechenden Tag hochgeladen wird (Zeile 26).&#x20;

{% code lineNumbers="true" %}
```yaml
trigger:
- development

resources:
- repo: self

variables:
  tag: '$(Build.BuildId)'

stages:
- stage: Build
  displayName: Build image
  jobs:
  - job: Build
    displayName: Build
    pool:
      name: Default
    steps:
    - task: Docker@2
      displayName: Build an image
      inputs:
        repository: 'hansendockedin/web-app-frontend' 
        containerRegistry: 'Dockerhub registry'
        command: 'buildAndPush'
        dockerfile: '$(Build.SourcesDirectory)/Dockerfile'
        tags: |
          development
```
{% endcode %}

### Deployment der Services

Beide Services stehen nach dem Build als Dockerimage bereit.  Zusätzlich wird das Standardimage der Postgres DB, sowie NGINX als Reverseproxy verwendet. Alles zusammen kann mit einem docker-compose file inklusive der unten gezeigten Netzwerkkonfiguration deployed werden.&#x20;

```mermaid
flowchart LR
	280127(("Nutzer")) --> 100105{"Host"}
	subgraph 897431["Server"]
		100105 --> 885309["NGINX:443"]
		885309 --> 100105
		757994["REST API:80"] --> 812716["Datenbank:5432"]
		759542["Websrvice:80"] --> 757994
		subgraph 857969["D. NW BE"]
			812716
		end
		subgraph 574340["D. NW FE"]
			885309 --> 759542
		end
		subgraph 550531["D. NW FE/BE"]
			757994
		end
	end
```

Zu Demonstrationszwecken werden im sql Skript, welches beim Start des Datenbankcontainers ausgeführt wird, Demodaten erzeugt. Die explizite Konfiguration des Netzwerks ist suboptimal und einem Workaround zur Konfiguration in SQLX (Backend Library) geschuldet. Das abschließende Deployment mit der unten gezeigten Konfiguration ist auf einem Alpine Cloudserver mit 2Gb RAM erfolgt. Weitere notwendige Files (sql Schema, nginx config) befinden sich im Ordner Deployment im Repository.

{% code title="" lineNumbers="true" %}
```docker
version: '3'
services:
  nginx:
    container_name: nginx
    expose:
      - 443   
    volumes:
      - "./nginx/conf/:/etc/nginx/conf.d/:rw"
    networks:
      - frontend
    build:
      context: ./nginx
    depends_on:
      - rest-api
      - webclient
    restart: always
    command: [nginx-debug, '-g', 'daemon off;']

  postgres:
    image: postgres:latest
    container_name: postgres
    expose:
      - 5432
    networks:
      backend:
        ipv4_address: 192.168.176.2
    volumes:
      - postgresDB:/data/postgres
      - ./db/postgres.conf:/etc/postgresql/postgresql.conf
      - ./db/postgres.sql:/docker-entrypoint-initdb.d/postgres.sql
    command: postgres -c config_file=/etc/postgresql/postgresql.conf
    env_file:
      - ./.env

  rest-api:
    image: hansendockedin/web-app-rest-api:latest
    expose:
      - 8000
    networks:
      - backend
      - frontend
    depends_on:
      - postgres
    environment:
      - DATABASE_URL=postgresql://admin:password123@192.168.176.2:5432/rust_sqlx?schema=public

  webclient:
    image: hansendockedin/web-app-frontend:latest
    networks:
      - frontend
    expose:
      - 80
    environment:
      ASPNETCORE_ENVIRONMENT: "Development"
      SERVICE: "http://rest-api:8000/api"
      GOOGLE_OAUTH_CLIENT_ID: "320557105527-q2nr65cv030mrv9jbtt5j874h8bkpjj1.apps.googleusercontent.com"
      MICROSOFT_OAUTH_CLIENT_ID: "d169d24c-3f89-4001-b142-71518363781b"
      GOOGLE_OAUTH_CLIENT_SECRET: "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"
      MICROSOFT_OAUTH_CLIENT_SECRET: "M9v8Q~yrMtSW40eSQnV.Xbx51ZboqW6ue5i6xcj9"
      SERVICE_URL: "http://rest-api/api"

volumes:
  postgresDB:

networks:
  backend:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.176.0/24
          gateway: 192.168.176.1
  frontend:
```
{% endcode %}



