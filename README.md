# angular2 no container

  Embora não seja realmente difícil, levei bastante tempo para usar angular-cli no meu Mac em um container docker.

  Este projeto contém meus pensamentos iniciais para criar, desenvolver e executar facilmente um aplicativo Angular 2 com container docker.

# Ideia geral

  Este projeto fornece 2 Dockerfiles. O primeiro Dockerfile está contido na raiz e responsável pela configuração de uma imagem contendo uma versão específica (atualmente é v1.0.0-beta.32.3) de angular-cli. Ele também prepara um usuário não-root. No entanto, isso não deve ser usado até que o projeto inicial é criado com ng novo. As permissões de raiz são necessárias para criar o projeto.

  Uma vez que o projeto inicial é criado com base Dockerfile, ambos os arquivos de docker-app-seed podem ser copiados para o recém-criado Angular 2 App. Esses arquivos permitem instalar todas as dependências dentro de um contêiner e executar comandos específicos do projeto oferecidos por angular-cli (por exemplo, ng servir).

## Creating a new Angular 2 App

You simply copy the [Dockerfile](Dockerfile) and call `docker build -t angular-cli:1.0.0-beta.32.3 .` in the same folder. This will create an image containing the appropriate angular-cli version.

This new image can now be used to create the basis for your new Angular 2 App. Simply call 

```
docker run -it --rm --name create-angular-project -v "$PWD":/home/app angular-cli:1.0.0-beta.32.3 ng new -sn APP_NAME
```

Obviously you should replace `APP_NAME` with the name of the app you like to build.

This finally creates your new app in a subfolder. With the `-sn` option we skip the installation of the complete tooling. This is important, because we don't want to have all the node modules installed in the bind-mounted host folder.

Instead we create another image, which stores the node modules on a separate volume. See [this article](http://jdlm.info/articles/2016/03/06/lessons-building-node-app-docker.html) for an excellent discussion. The next section shows you how to actually build the new image.

## Building a new image for development

Once the app is created with `ng new` we can switch into the folder and build an image with all dependencies. First you need to copy the `Dockerfile` as well as the `docker-compose.yml` file from the `docker-app-seed` folder into your newly created application folder. Once you copied the files you might want to adapt the `APP_NAME` in the `Dockerfile` and the service name in the `docker-compose.yml`.

Finally you can build the image, which compiles and serves your app with:

```
docker-compose build
```

## Serving your new App

Finally you can call `docker-compose up` to serve your new app. It will be accessible on `http://localhost:4200/`. You can see that everything works as expected, if a website with "app works" is shown in your browser.
