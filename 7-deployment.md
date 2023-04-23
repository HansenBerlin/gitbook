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



Automatisierung Azure, Linode, Beispiel

