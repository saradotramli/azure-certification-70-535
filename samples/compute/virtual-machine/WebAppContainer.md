# Django starter app for Web App on Linux

```git
git clone https://github.com/Azure-Samples/docker-django-webapp-linux.git --config core.autocrlf=input
cd docker-django-webapp-linux
```

## Custom Docker image for Web App

```docker
docker build --tag selvendiranj/docker-django-webapp-linux:v1.0.0 .
docker run -p 2222:8000 selvendiranj/docker-django-webapp-linux:v1.0.0
docker login --username selvendiranj
docker push selvendiranj/docker-django-webapp-linux:v1.0.0
```

## Deploy app to Azure

```
az group create --name dockerDjangoWebappResourceGroup --location "East US"
az appservice plan create --name dockerDjangoServicePlan --resource-group dockerDjangoWebappResourceGroup --sku S1 --is-linux
az webapp create --resource-group dockerDjangoWebappResourceGroup --plan dockerDjangoServicePlan --name djangoAas --deployment-container-image-name selvendiranj/docker-django-webapp-linux:v1.0.0
az webapp config appsettings set --resource-group dockerDjangoWebappResourceGroup --name djangoAas --settings WEBSITES_PORT=8000
```

## Change web app and redeploy

```azure
az webapp stop--name djangoAas --resource-group dockerDjangoWebappResourceGroup
az webapp update --name djangoAas --resource-group dockerDjangoWebappResourceGroup
az webapp start --name djangoAas --resource-group dockerDjangoWebappResourceGroup
```

## Connect to Web App for Containers using SSH

```
https://<app_name>.scm.azurewebsites.net/webssh/host
The top command exposes all running processes in a container.
```

## Azure Container Registry

```azure
az acr create --name <azure-container-registry-name> --resource-group myResourceGroup --sku Basic --admin-enabled true
az acr credential show --name <azure-container-registry-name>
docker login <azure-container-registry-name>.azurecr.io --username <registry-username>
docker tag <azure-container-registry-name>.azurecr.io/mydockerimage
docker push <azure-container-registry-name>.azurecr.io/mydockerimage:v1.0.0
az acr repository list -n <azure-container-registry-name>
az acr credential show --name <azure-container-registry-name>
az webapp config container set --name <app_name> --resource-group myResourceGroup --docker-custom-image-name <azure-container-registry-name>.azurecr.io/mydockerimage --docker-registry-server-url https://<azure-container-registry-name>.azurecr.io --docker-registry-server-user <registry-username> --docker-registry-server-password <password>
az group delete --name myResourceGroup
```
