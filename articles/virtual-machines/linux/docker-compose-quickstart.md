---
title: Verwenden von Docker Compose für einen virtuellen Linux-Computer in Azure | Microsoft Docs
description: Installieren und Verwenden von Docker und Compose für virtuelle Linux-Computer mit der Azure-Befehlszeilenschnittstelle
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 02ab8cf9-318d-4a28-9d0c-4a31dccc2a84
ms.service: virtual-machines-linux
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/14/2019
ms.author: cynthn
ms.openlocfilehash: 03501ea774cf58a4be88ed9155e5cfdfb99f0379
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61474068"
---
# <a name="get-started-with-docker-and-compose-to-define-and-run-a-multi-container-application-in-azure"></a>Erste Schritte mit Docker und Compose zum Definieren und Ausführen einer Anwendung mit mehreren Containern in Azure
Bei [Compose](https://github.com/docker/compose) verwenden Sie eine einfache Textdatei zum Definieren einer Anwendung, die aus mehreren Docker-Containern besteht. Sie starten Ihre Anwendung dann mit einem einzelnen Befehl, mit dem alle Schritte zur Bereitstellung Ihrer definierten Umgebung ausgeführt werden. In diesem Artikel wird beispielsweise veranschaulicht, wie Sie schnell einen WordPress-Blog mit einer MariaDB SQL-Back-End-Datenbank auf einem virtuellen Ubuntu-Computer einrichten. Sie können aber auch Compose verwenden, um komplexere Anwendungen einzurichten.

Dieser Artikel wurde zuletzt am 14.02.2019 unter Verwendung von [Azure Cloud Shell](https://shell.azure.com/bash) und Version 2.0.58 der [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) geprüft.

## <a name="create-docker-host-with-azure-cli"></a>Erstellen eines Docker-Hosts mit der Azure CLI
Installieren Sie die neueste Version der [Azure CLI](/cli/azure/install-az-cli2), und melden Sie sich mit [az login](/cli/azure/reference-index) bei einem Azure-Konto an.

Erstellen Sie zuerst mit [az group create](/cli/azure/group) eine Ressourcengruppe für Ihre Docker-Umgebung. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli-interactive
az group create --name myDockerGroup --location eastus
```

Erstellen Sie eine Datei namens *cloud-init.txt*, und fügen Sie die folgende Konfiguration ein. Geben Sie `sensible-editor cloud-init.txt` ein, um die Datei zu erstellen und eine Liste der verfügbaren Editoren anzuzeigen. 

```yaml
#include https://get.docker.com
```

Jetzt können Sie mit [az vm create](/cli/azure/vm#az-vm-create) einen virtuellen Computer erstellen. Verwenden Sie den `--custom-data`-Parameter, um Ihre cloud-init-Konfigurationsdatei zu übergeben. Geben Sie den vollständigen Pfad zu der Konfigurationsdatei *cloud-init.txt* an, wenn Sie die Datei außerhalb Ihres vorhandenen Arbeitsverzeichnisses gespeichert haben. Mit dem folgenden Beispiel werden ein virtueller Computer namens *myDockerVM* erstellt und Port 80 für Webdatenverkehr geöffnet.

```azurecli-interactive
az vm create \
    --resource-group myDockerGroup \
    --name myDockerVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
az vm open-port --port 80 \
    --resource-group myDockerGroup \
    --name myDockerVM
```

Es dauert einige Minuten, den virtuellen Computer zu erstellen, die Pakete zu installieren und die App zu starten. Es gibt Hintergrundaufgaben, die weiterhin ausgeführt werden, wenn Ihnen von der Azure CLI wieder eine Eingabeaufforderung angezeigt wird. Sobald der virtuelle Computer erstellt ist, notieren Sie die `publicIpAddress`, die von der Azure-CLI angezeigt wird. 

                 

## <a name="install-compose"></a>Installieren von Compose


SSH zu Ihrem neuen virtuellen Docker-Hostcomputer. Geben Sie Ihre eigenen IP-Adresse an.

```bash
ssh azureuser@10.10.111.11
```

Installieren Sie Compose auf dem virtuellen Computer.

```bash
sudo apt install docker-compose
```


## <a name="create-a-docker-composeyml-configuration-file"></a>Erstellen der Konfigurationsdatei „docker-compose.yml“
Erstellen Sie eine `docker-compose.yml`-Konfigurationsdatei, um die Docker-Container zu definieren, die auf dem virtuellen Computer ausgeführt werden sollen. Die Datei enthält Angaben zum Image, das für jeden Container ausgeführt werden soll, erforderliche Umgebungsvariablen und Abhängigkeiten, Ports und Links zwischen Containern. Details zur Syntax der YML-Datei finden Sie in der [Compose-Dateireferenz](https://docs.docker.com/compose/compose-file/).

Erstellen Sie die Datei *docker-compose.yml*. Verwenden Sie Ihren bevorzugten Text-Editor, um der Datei einige Daten hinzuzufügen. Das folgende Beispiel erstellt die Datei mit einer Eingabeaufforderung für `sensible-editor`, um einen zu verwendenden Editor auszuwählen.

```bash
sensible-editor docker-compose.yml
```

Fügen Sie das folgende Beispiel in Ihre Docker Compose-Datei ein. Diese Konfiguration nutzt Images aus der [Docker-Hub-Registrierung](https://registry.hub.docker.com/_/wordpress/) , um WordPress (Open-Source-Blogging- und Content Management-System) und die verknüpfte MariaDB SQL-Back-End-Datenbank zu installieren. Geben Sie Ihr eigenes Kennwort (*MYSQL_ROOT_PASSWORD*) ein.

```yml
wordpress:
  image: wordpress
  links:
    - db:mysql
  ports:
    - 80:80

db:
  image: mariadb
  environment:
    MYSQL_ROOT_PASSWORD: <your password>
```

## <a name="start-the-containers-with-compose"></a>Starten des Containers mit Compose
Führen Sie in demselben Verzeichnis, in dem sich die Datei *docker-compose.yml* befindet, den folgenden Befehl aus (je nach Umgebung kann es erforderlich sein, `docker-compose` mit `sudo` auszuführen):

```bash
sudo docker-compose up -d
```

Mit diesem Befehl werden die in *docker-compose.yml* angegebenen Docker-Container gestartet. Es dauert ein bis zwei Minuten, bis dieser Schritt abgeschlossen ist. Daraufhin wird eine Ausgabe angezeigt, die in etwa wie folgt aussieht:

```
Creating wordpress_db_1...
Creating wordpress_wordpress_1...
...
```


Um zu prüfen, ob die Container ausgeführt werden, geben Sie `sudo docker-compose ps`ein. Die Ausgabe sollte folgendermaßen aussehen:

```
        Name                       Command               State         Ports
-----------------------------------------------------------------------------------
azureuser_db_1          docker-entrypoint.sh mysqld      Up      3306/tcp
azureuser_wordpress_1   docker-entrypoint.sh apach ...   Up      0.0.0.0:80->80/tcp
```

Sie können nun auf dem virtuellen Computer über Port 80 eine direkte Verbindung mit WordPress herstellen. Öffnen Sie einen Webbrowser, und geben Sie den IP-Adressnamen Ihres virtuellen Computers ein. Daraufhin sollte der Startbildschirm von WordPress angezeigt werden, auf dem Sie die Installation abschließen und erste Schritte mit der Anwendung ausführen können.

![WordPress-Startbildschirm](./media/docker-compose-quickstart/wordpressstart.png)

## <a name="next-steps"></a>Nächste Schritte
* Weitere Beispiele zum Erstellen und Bereitstellen von Apps mit mehreren Containern finden Sie in der [Composer-Befehlszeilenreferenz](https://docs.docker.com/compose/reference/) und im [Benutzerhandbuch](https://docs.docker.com/compose/).
* Verwenden Sie eine von Ihnen oder der [Community](https://azure.microsoft.com/documentation/templates/)erstellte Vorlage des Azure-Ressourcen-Managers, um eine Azure-VM mit Docker und eine mit Compose eingerichtete Anwendung bereitzustellen. Die Vorlage [WordPress-Blog mit Docker bereitstellen](https://github.com/Azure/azure-quickstart-templates/tree/master/docker-wordpress-mysql) verwendet Docker und Compose, um WordPress schnell mit einem MySQL-Back-End auf einem virtuellen Ubuntu-Computer bereitzustellen.
* Sie können Docker Compose auch in ein Docker Swarm-Cluster integrieren. Entsprechende Szenarien finden Sie unter [Using Compose with Swarm](https://docs.docker.com/compose/swarm/) (Verwenden von Compose mit Swarm).

