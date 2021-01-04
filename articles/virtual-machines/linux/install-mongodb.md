---
title: Installieren von MongoDB auf einem virtuellen Linux-Computer mit der Azure CLI
description: Informationen zum Installieren und Konfigurieren von MongoDB auf einem virtuellen Linux-Computer mithilfe von Azure CLI
author: cynthn
manager: gwallace
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.devlang: azurecli
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/15/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 49a0e48977393aeab7ff93b79e28acc55a87b51a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016181"
---
# <a name="how-to-install-and-configure-mongodb-on-a-linux-vm"></a>Installieren und Konfigurieren von MongoDB auf einem virtuellen Linux-Computer

[MongoDB](https://www.mongodb.org) ist eine beliebte, leistungsfähige Open Source-NoSQL-Datenbank. In diesem Artikel wird das Installieren und Konfigurieren von MongoDB auf einem virtuellen Linux-Computer mithilfe von Azure CLI beschrieben. Hier finden Sie Beispiele mit Informationen zu den folgenden Schritten:

* [Manuelles Installieren und Konfigurieren einer einfachen MongoDB-Instanz](#manually-install-and-configure-mongodb-on-a-vm)
* [Erstellen einer einfachen MongoDB-Instanz mithilfe einer Resource Manager-Vorlage](#create-basic-mongodb-instance-on-centos-using-a-template)
* [Erstellen eines komplexen MongoDB-Shardclusters mit Replikatgruppen mithilfe einer Resource Manager-Vorlage](#create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template)


## <a name="manually-install-and-configure-mongodb-on-a-vm"></a>Manuelles Installieren und Konfigurieren von MongoDB auf einem virtuellen Computer
Für MongoDB stehen [Installationsanweisungen](https://docs.mongodb.com/manual/administration/install-on-linux/) für Linux-Distributionen zur Verfügung, u.a. für Red Hat/CentOS, SUSE, Ubuntu und Debian. Im folgenden Beispiel wird ein virtueller Computer mit dem Namen *CentOS* erstellt. Zum Erstellen dieser Umgebung muss die neueste Version von [Azure CLI](/cli/azure/install-az-cli2) installiert sein, und Sie müssen mithilfe von [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sein.

Erstellen Sie mit [az group create](/cli/azure/group) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli
az group create --name myResourceGroup --location eastus
```

Erstellen Sie mit [az vm create](/cli/azure/vm) einen virtuellen Computer. Im folgenden Beispiel wird der virtuelle Computer *myVM* mit dem Benutzer *azureuser* erstellt, indem die Authentifizierung mit einem öffentlichen SSH-Schlüssel verwendet wird

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image CentOS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Führen Sie die SSH-Authentifizierung beim virtuellen Computer mit Ihrem eigenen Benutzernamen und dem in der Ausgabe aus dem vorherigen Schritt angegebenen `publicIpAddress` aus:

```bash
ssh azureuser@<publicIpAddress>
```

Erstellen Sie zum Hinzufügen der Installationsquellen für MongoDB wie folgt eine **yum**-Repositorydatei:

```bash
sudo touch /etc/yum.repos.d/mongodb-org-3.6.repo
```

Öffnen Sie die MongoDB-Repositorydatei zur Bearbeitung, wie z.B. mit `vi` oder `nano`. Fügen Sie die folgenden Zeilen hinzu:

```sh
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

Installieren Sie MongoDB wie folgt per **yum**:

```bash
sudo yum install -y mongodb-org
```

Standardmäßig wird SELinux für CentOS-Images erzwungen. Dadurch wird Ihr Zugriff auf MongoDB verhindert. Installieren Sie Richtlinienverwaltungstools, und konfigurieren Sie SELinux wie folgt, um MongoDB die Nutzung des TCP-Standardports 27017 zu ermöglichen:

```bash
sudo yum install -y policycoreutils-python
sudo semanage port -a -t mongod_port_t -p tcp 27017
```

Starten Sie den MongoDB-Dienst wie folgt:

```bash
sudo service mongod start
```

Überprüfen Sie die MongoDB-Installation durch Herstellen einer Verbindung mit dem lokalen `mongo`-Client:

```bash
mongo
```

Testen Sie nun die MongoDB-Instanz, indem Sie einige Daten hinzufügen und anschließend eine Suche ausführen:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```

Konfigurieren Sie bei Bedarf den automatischen Start von MongoDB bei einem Systemneustart:

```bash
sudo chkconfig mongod on
```


## <a name="create-basic-mongodb-instance-on-centos-using-a-template"></a>Erstellen einer einfachen MongoDB-Instanz unter CentOS mithilfe einer Vorlage
Sie können mithilfe der folgenden Azure-Schnellstartvorlage von GitHub eine einfache MongoDB-Instanz auf einem einzelnen virtuellen CentOS-Computer erstellen. In dieser Vorlage wird die benutzerdefinierte Skripterweiterung für Linux verwendet, um Ihrem neu erstellten virtuellen CentOS-Computer ein **yum**-Repository hinzuzufügen und MongoDB zu installieren.

* [Einfache MongoDB-Instanz unter CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-on-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json

Zum Erstellen dieser Umgebung muss die neueste Version von [Azure CLI](/cli/azure/install-az-cli2) installiert sein, und Sie müssen mithilfe von [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sein. Erstellen Sie zunächst mit [az group create](/cli/azure/group) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli
az group create --name myResourceGroup --location eastus
```

Stellen Sie als Nächstes mit dem Befehl [az group deployment create](/cli/azure/group/deployment) die MongoDB-Vorlage bereit. Geben Sie bei Aufforderung Ihre eigenen eindeutigen Werte für *newStorageAccountName*, *dnsNameForPublicIP* sowie den Administratorbenutzernamen und das Kennwort an:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-centos/azuredeploy.json
```

Melden Sie sich bei dem virtuellen Computer mit der öffentlichen DNS-Adresse des virtuellen Computers an. Sie können die öffentliche DNS-Adresse mit dem Befehl [az vm show](/cli/azure/vm) anzeigen:

```azurecli
az vm show -g myResourceGroup -n myLinuxVM -d --query [fqdns] -o tsv
```

Stellen Sie eine SSH-Verbindung mit dem virtuellen Computer unter Verwendung Ihres eigenen Benutzernamens und der öffentlichen DNS-Adresse her:

```bash
ssh azureuser@mypublicdns.eastus.cloudapp.azure.com
```

Überprüfen Sie wie folgt die MongoDB-Installation durch Herstellen einer Verbindung mit dem lokalen `mongo`-Client:

```bash
mongo
```

Testen Sie nun die Instanz, indem Sie folgendermaßen einige Daten hinzufügen und anschließend eine Suche ausführen:

```sh
> db
test
> db.foo.insert( { a : 1 } )  
> db.foo.find()  
{ "_id" : ObjectId("57ec477cd639891710b90727"), "a" : 1 }
> exit
```


## <a name="create-a-complex-mongodb-sharded-cluster-on-centos-using-a-template"></a>Erstellen eines komplexen MongoDB-Shardclusters unter CentOS mithilfe einer Vorlage
Sie können mithilfe der folgenden Azure-Schnellstartvorlage von GitHub einen komplexen MongoDB-Shardcluster erstellen. Diese Vorlage basiert auf den [bewährten Methoden für MongoDB-Shardcluster](https://docs.mongodb.com/manual/core/sharded-cluster-components/), um Redundanz und Hochverfügbarkeit zu gewährleisten. Die Vorlage erstellt zwei Shards mit drei Knoten in jeder Replikatgruppe. Darüber hinaus werden eine Konfigurationsserver-Replikatgruppe mit drei Knoten sowie zwei **mongos**-Routerserver erstellt, um Konsistenz für Anwendungen auf allen Shards zu ermöglichen.

* [MongoDB-Shardcluster unter CentOS](https://github.com/Azure/azure-quickstart-templates/tree/master/mongodb-sharding-centos) - https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json

> [!WARNING]
> Für die Bereitstellung dieses komplexen MongoDB-Shardclusters sind mehr als 20 Kerne erforderlich. Dies ist in der Regel die Standardanzahl von Kernen pro Region für ein Abonnement. Stellen Sie eine Azure-Supportanfrage zum Erhöhen der Kernanzahl.

Zum Erstellen dieser Umgebung muss die neueste Version von [Azure CLI](/cli/azure/install-az-cli2) installiert sein, und Sie müssen mithilfe von [az login](/cli/azure/reference-index) bei einem Azure-Konto angemeldet sein. Erstellen Sie zunächst mit [az group create](/cli/azure/group) eine Ressourcengruppe. Im folgenden Beispiel wird eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus* erstellt:

```azurecli
az group create --name myResourceGroup --location eastus
```

Stellen Sie als Nächstes mit dem Befehl [az group deployment create](/cli/azure/group/deployment) die MongoDB-Vorlage bereit. Definieren Sie bei Bedarf Ihre eigenen Ressourcennamen und -größen, z.B. für *mongoAdminUsername*, *sizeOfDataDiskInGB* und *configNodeVmSize*:

```azurecli
az group deployment create --resource-group myResourceGroup \
  --parameters '{"adminUsername": {"value": "azureuser"},
    "adminPassword": {"value": "P@ssw0rd!"},
    "mongoAdminUsername": {"value": "mongoadmin"},
    "mongoAdminPassword": {"value": "P@ssw0rd!"},
    "dnsNamePrefix": {"value": "mypublicdns"},
    "environment": {"value": "AzureCloud"},
    "numDataDisks": {"value": "4"},
    "sizeOfDataDiskInGB": {"value": 20},
    "centOsVersion": {"value": "7.0"},
    "routerNodeVmSize": {"value": "Standard_DS3_v2"},
    "configNodeVmSize": {"value": "Standard_DS3_v2"},
    "replicaNodeVmSize": {"value": "Standard_DS3_v2"},
    "zabbixServerIPAddress": {"value": "Null"}}' \
  --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-sharding-centos/azuredeploy.json \
  --name myMongoDBCluster \
  --no-wait
```

Es kann über eine Stunde dauern, bis die Bereitstellung und die Konfiguration aller VM-Instanzen abgeschlossen ist. Das Flag `--no-wait` wird am Ende des vorherigen Befehls verwendet, sodass die Steuerung an die Eingabeaufforderung zurückgegeben wird, nachdem die Vorlagenbereitstellung auf der Azure-Plattform akzeptiert wurde. Sie können dann mit dem Befehl [az group deployment show](/cli/azure/group/deployment) den Bereitstellungsstatus anzeigen. Im folgenden Beispiel wird der Status für die Bereitstellung *myMongoDBCluster* in der Ressourcengruppe *myResourceGroup* angezeigt:

```azurecli
az group deployment show \
    --resource-group myResourceGroup \
    --name myMongoDBCluster \
    --query [properties.provisioningState] \
    --output tsv
```

## <a name="next-steps"></a>Nächste Schritte
In diesen Beispielen wird vom virtuellen Computer eine lokale Verbindung mit der MongoDB-Instanz hergestellt. Wenn Sie von einem anderen virtuellen Computer oder Netzwerk eine Verbindung mit der MongoDB-Instanz herstellen möchten, stellen Sie sicher, dass die entsprechenden [Netzwerksicherheitsgruppen-Regeln](nsg-quickstart.md) erstellt werden.

In diesen Beispielen wird die MongoDB-Kernumgebung für Entwicklungszwecke bereitgestellt. Wenden Sie die erforderlichen Optionen der Sicherheitskonfiguration für Ihre Umgebung an. Weitere Informationen finden Sie in den [Dokumenten zur MongoDB-Sicherheit](https://docs.mongodb.com/manual/security/).

Weitere Informationen zur Erstellung mithilfe von Vorlagen finden Sie in der [Übersicht über den Azure Resource Manager](../../azure-resource-manager/management/overview.md).

Die Azure Resource Manager-Vorlagen verwenden die benutzerdefinierte Skripterweiterung zum Herunterladen und Ausführen von Skripts auf Ihren virtuellen Computern. Weitere Informationen finden Sie unter [Verwenden der benutzerdefinierten Skripterweiterung von Azure mit virtuellen Linux-Computern](../extensions/custom-script-linux.md).
