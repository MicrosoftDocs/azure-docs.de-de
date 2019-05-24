---
title: Tutorial – Erstellen eines MEAN-Stacks auf einem virtuellen Linux-Computer in Azure | Microsoft-Dokumentation
description: In diesem Tutorial erfahren Sie, wie Sie einen MEAN-Stack (MongoDB, Express, AngularJS und Node.js) auf einem virtuellen Linux-Computer in Azure erstellen.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/08/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: eac10cd735ae16cf1c7c6d6987a3b53060caa1cd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66155316"
---
# <a name="tutorial-create-a-mongodb-express-angularjs-and-nodejs-mean-stack-on-a-linux-virtual-machine-in-azure"></a>Tutorial: Erstellen eines MEAN-Stacks (MongoDB, Express, AngularJS und Node.js) auf einem virtuellen Linux-Computer in Azure

In diesem Tutorial wird veranschaulicht, wie Sie einen MEAN-Stack (MongoDB, Express, AngularJS und Node.js) auf einem virtuellen Linux-Computer in Azure implementieren. Mit dem erstellten MEAN-Stack können Bücher in einer Datenbank hinzugefügt, gelöscht und aufgelistet werden. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen eines virtuellen Linux-Computers
> * Installieren von Node.js
> * Installieren von MongoDB und Einrichten des Servers
> * Installieren von Express und Einrichten von Routen zum Server
> * Zugreifen auf die Routen mit AngularJS
> * Ausführen der Anwendung

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.30 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).


## <a name="create-a-linux-vm"></a>Erstellen eines virtuellen Linux-Computers

Erstellen Sie mit dem Befehl [az group create](https://docs.microsoft.com/cli/azure/group) eine Ressourcengruppe und mit dem Befehl [az vm create](https://docs.microsoft.com/cli/azure/vm) einen virtuellen Linux-Computer. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Im folgenden Beispiel wird über die Azure-Befehlszeilenschnittstelle die Ressourcengruppe *myResourceGroupMEAN* am Standort *eastus* erstellt. Der virtuelle Computer *myVM* wird mit SSH-Schlüsseln erstellt, falls diese nicht bereits an einem Standardschlüsselspeicherort vorhanden sind. Um einen bestimmten Satz von Schlüsseln zu verwenden, verwenden Sie die Option „--ssh-key-value“.

```azurecli-interactive
az group create --name myResourceGroupMEAN --location eastus
az vm create \
    --resource-group myResourceGroupMEAN \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --admin-password 'Azure12345678!' \
    --generate-ssh-keys
az vm open-port --port 3300 --resource-group myResourceGroupMEAN --name myVM
```

Nach dem Erstellen des virtuellen Computers werden in der Azure-Befehlszeilenschnittstelle ähnliche Informationen wie im folgenden Beispiel angezeigt: 

```azurecli-interactive
{
  "fqdns": "",
  "id": "/subscriptions/{subscription-id}/resourceGroups/myResourceGroupMEAN/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.72.77.9",
  "resourceGroup": "myResourceGroupMEAN"
}
```
Notieren Sie sich den Wert von `publicIpAddress`. Diese Adresse wird verwendet, um auf den virtuellen Computer zuzugreifen.

Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung mit dem virtuellen Computer. Achten Sie darauf, dass Sie die richtige öffentliche IP-Adresse verwenden. Im Beispiel oben lautete die IP-Adresse „13.72.77.9“.

```bash
ssh azureuser@13.72.77.9
```

## <a name="install-nodejs"></a>Installieren von Node.js

[Node.js](https://nodejs.org/en/) ist eine JavaScript-Runtime, die auf der JavaScript Engine V8 von Chrome aufbaut. Node.js wird in diesem Tutorial zum Einrichten der Express-Routen und AngularJS-Controller verwendet.

Installieren Sie Node.js auf dem virtuellen Computer mithilfe der mit SSH geöffneten Bash-Shell.

```bash
sudo apt-get install -y nodejs
```

## <a name="install-mongodb-and-set-up-the-server"></a>Installieren von MongoDB und Einrichten des Servers
In [MongoDB](https://www.mongodb.com) werden Daten in flexiblen, JSON-ähnlichen Dokumenten gespeichert. Felder in einer Datenbank können zwischen unterschiedlichen Dokumenten variieren, und die Datenstruktur kann sich mit der Zeit ändern. In der Beispielanwendung fügen wir in MongoDB Buchdatensätze hinzu, die den Buchnamen, die ISBN-Nummer, den Autor und die Anzahl der Seiten enthalten. 

1. Legen Sie auf dem virtuellen Computer mithilfe der mit SSH geöffneten Bash-Shell den MongoDB-Schlüssel fest.

    ```bash
    sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 0C49F3730359A14518585931BC711F9BA15703C6
    echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu trusty/mongodb-org/3.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.4.list
    ```

2. Aktualisieren Sie den Paket-Manager mit dem Schlüssel.
  
    ```bash
    sudo apt-get update
    ```

3. Installieren Sie MongoDB.

    ```bash
    sudo apt-get install -y mongodb
    ```

4. Starten Sie den Server.

    ```bash
    sudo service mongodb start
    ```

5. Wir müssen auch das Paket [body-parser](https://www.npmjs.com/package/body-parser-json) installieren, um die an den Server übergebenen JSON-Anfragen verarbeiten zu können.

    Installieren Sie den npm-Paket-Manager.

    ```bash
    sudo apt-get install npm
    ```

    Installieren Sie das Paket „body-parser“.
    
    ```bash
    sudo npm install body-parser
    ```

6. Erstellen Sie den Ordner *Books*, und fügen Sie ihm die Datei *server.js* hinzu, die die Konfiguration für den Webserver enthält.

    ```javascript
    var express = require('express');
    var bodyParser = require('body-parser');
    var app = express();
    app.use(express.static(__dirname + '/public'));
    app.use(bodyParser.json());
    require('./apps/routes')(app);
    app.set('port', 3300);
    app.listen(app.get('port'), function() {
        console.log('Server up: http://localhost:' + app.get('port'));
    });
    ```

## <a name="install-express-and-set-up-routes-to-the-server"></a>Installieren von Express und Einrichten von Routen zum Server

[Express](https://expressjs.com) ist ein einfaches und flexibles Node.js-Webanwendungsframework, das Features für Web- und Mobilanwendungen umfasst. Mit Express werden in diesem Tutorial Bücherinformationen in die und aus der MongoDB-Datenbank übergeben. [Mongoose](https://mongoosejs.com) ist eine unkomplizierte, schemabasierte Lösung zum Modellieren Ihrer Anwendungsdaten. Mit Mongoose wird in diesem Tutorial ein Buchschema für die Datenbank bereitgestellt.

1. Installieren Sie Express und Mongoose.

    ```bash
    sudo npm install express mongoose
    ```

2. Erstellen Sie im Ordner *Books* den Ordner *apps*, und fügen Sie die Datei *routes.js* mit den definierten Express-Routen hinzu.

    ```javascript
    var Book = require('./models/book');
    module.exports = function(app) {
      app.get('/book', function(req, res) {
        Book.find({}, function(err, result) {
          if ( err ) throw err;
          res.json(result);
        });
      }); 
      app.post('/book', function(req, res) {
        var book = new Book( {
          name:req.body.name,
          isbn:req.body.isbn,
          author:req.body.author,
          pages:req.body.pages
        });
        book.save(function(err, result) {
          if ( err ) throw err;
          res.json( {
            message:"Successfully added book",
            book:result
          });
        });
      });
      app.delete("/book/:isbn", function(req, res) {
        Book.findOneAndRemove(req.query, function(err, result) {
          if ( err ) throw err;
          res.json( {
            message: "Successfully deleted the book",
            book: result
          });
        });
      });
      var path = require('path');
      app.get('*', function(req, res) {
        res.sendfile(path.join(__dirname + '/public', 'index.html'));
      });
    };
    ```

3. Erstellen Sie im Ordner *apps* den Ordner *models*, und fügen Sie die Datei *book.js* mit der definierten Konfiguration für das Buchmodell hinzu.  

    ```javascript
    var mongoose = require('mongoose');
    var dbHost = 'mongodb://localhost:27017/test';
    mongoose.connect(dbHost);
    mongoose.connection;
    mongoose.set('debug', true);
    var bookSchema = mongoose.Schema( {
      name: String,
      isbn: {type: String, index: true},
      author: String,
      pages: Number
    });
    var Book = mongoose.model('Book', bookSchema);
    module.exports = mongoose.model('Book', bookSchema); 
    ```

## <a name="access-the-routes-with-angularjs"></a>Zugreifen auf die Routen mit AngularJS

[AngularJS](https://angularjs.org) bietet ein Webframework zum Erstellen dynamischer Ansichten in Ihren Webanwendungen. In diesem Tutorial wird AngularJS zum Verbinden der Webseite mit Express und zum Ausführen von Aktionen in der Buchdatenbank verwendet.

1. Wechseln Sie wieder zum Verzeichnis *Books* (`cd ../..`), erstellen Sie dann den Ordner *public*, und fügen Sie die Datei *script.js* mit der definierten Controllerkonfiguration hinzu.

    ```javascript
    var app = angular.module('myApp', []);
    app.controller('myCtrl', function($scope, $http) {
      $http( {
        method: 'GET',
        url: '/book'
      }).then(function successCallback(response) {
        $scope.books = response.data;
      }, function errorCallback(response) {
        console.log('Error: ' + response);
      });
      $scope.del_book = function(book) {
        $http( {
          method: 'DELETE',
          url: '/book/:isbn',
          params: {'isbn': book.isbn}
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
      $scope.add_book = function() {
        var body = '{ "name": "' + $scope.Name + 
        '", "isbn": "' + $scope.Isbn +
        '", "author": "' + $scope.Author + 
        '", "pages": "' + $scope.Pages + '" }';
        $http({
          method: 'POST',
          url: '/book',
          data: body
        }).then(function successCallback(response) {
          console.log(response);
        }, function errorCallback(response) {
          console.log('Error: ' + response);
        });
      };
    });
    ```
    
2. Erstellen Sie im Ordner *public* die Datei *index.html* mit der definierten Webseite.

    ```html
    <!doctype html>
    <html ng-app="myApp" ng-controller="myCtrl">
      <head>
        <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.6.4/angular.min.js"></script>
        <script src="script.js"></script>
      </head>
      <body>
        <div>
          <table>
            <tr>
              <td>Name:</td> 
              <td><input type="text" ng-model="Name"></td>
            </tr>
            <tr>
              <td>Isbn:</td>
              <td><input type="text" ng-model="Isbn"></td>
            </tr>
            <tr>
              <td>Author:</td> 
              <td><input type="text" ng-model="Author"></td>
            </tr>
            <tr>
              <td>Pages:</td>
              <td><input type="number" ng-model="Pages"></td>
            </tr>
          </table>
          <button ng-click="add_book()">Add</button>
        </div>
        <hr>
        <div>
          <table>
            <tr>
              <th>Name</th>
              <th>Isbn</th>
              <th>Author</th>
              <th>Pages</th>
            </tr>
            <tr ng-repeat="book in books">
              <td><input type="button" value="Delete" data-ng-click="del_book(book)"></td>
              <td>{{book.name}}</td>
              <td>{{book.isbn}}</td>
              <td>{{book.author}}</td>
              <td>{{book.pages}}</td>
            </tr>
          </table>
        </div>
      </body>
    </html>
    ```

##  <a name="run-the-application"></a>Ausführen der Anwendung

1. Wechseln Sie wieder zum Verzeichnis *Books* (`cd ..`), und starten Sie den Server durch Ausführen des folgenden Befehls:

    ```bash
    nodejs server.js
    ```

2. Öffnen Sie in einem Webbrowser die Adresse, die Sie für den virtuellen Computer aufgezeichnet haben. Beispiel: *http:\//13.72.77.9:3300*. Eine Seite ähnlich der folgenden sollte angezeigt werden:

    ![Buchdatensatz](media/tutorial-mean/meanstack-init.png)

3. Geben Sie Daten in den Textfeldern ein, und klicken Sie auf **Hinzufügen**. Beispiel: 

    ![Hinzufügen des Buchdatensatzes](media/tutorial-mean/meanstack-add.png)

4. Nach der Aktualisierung der Seite sollte eine Seite ähnlich der folgenden angezeigt werden:

    ![Auflisten von Buchdatensätzen](media/tutorial-mean/meanstack-list.png)

5. Sie können auf **Löschen** klicken und den Buchdatensatz aus der Datenbank entfernen.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie eine Webanwendung erstellt, in der Buchdatensätze mithilfe eines MEAN-Stacks auf einem virtuellen Linux-Computer nachverfolgt werden. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Erstellen eines virtuellen Linux-Computers
> * Installieren von Node.js
> * Installieren von MongoDB und Einrichten des Servers
> * Installieren von Express und Einrichten von Routen zum Server
> * Zugreifen auf die Routen mit AngularJS
> * Ausführen der Anwendung

Im nächsten Tutorial erfahren Sie, wie Sie Webserver mit SSL-Zertifikaten sichern.

> [!div class="nextstepaction"]
> [Sichern von Webservern mit SSL](tutorial-secure-web-server.md)
