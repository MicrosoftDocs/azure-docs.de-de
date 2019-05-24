---
title: Bereitstellen von ElasticSearch auf einem virtuellen Entwicklungscomputer in Azure
description: 'Tutorial: Installieren des Elastic Stack auf einem virtuellen Linux-Entwicklungscomputer in Azure'
services: virtual-machines-linux
documentationcenter: virtual-machines
author: rloutlaw
manager: justhe
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 10/11/2017
ms.author: routlaw
ms.openlocfilehash: 4d6dce952eca3d528a310685106a017dd7e3b80f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 04/23/2019
ms.locfileid: "66166037"
---
# <a name="install-the-elastic-stack-on-an-azure-vm"></a>Installieren des Elastic Stack auf einem virtuellen Azure-Computer

In diesem Artikel erfahren Sie, wie Sie [Elasticsearch](https://www.elastic.co/products/elasticsearch), [Logstash](https://www.elastic.co/products/logstash) und [Kibana](https://www.elastic.co/products/kibana) auf einem virtuellen Ubuntu-Computer in Azure bereitstellen. Wenn Sie den Elastic Stack in Aktion erleben möchten, können Sie optional eine Verbindung mit Kibana herstellen und mit einigen Beispielprotokolldaten arbeiten. 

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen eines virtuellen Ubuntu-Computers in einer Azure-Ressourcengruppe
> * Installieren von Elasticsearch, Logstash und Kibana auf dem virtuellen Computer
> * Senden von Beispieldaten an Elasticsearch mithilfe von Logstash 
> * Öffnen von Ports und Arbeiten mit Daten in der Kibana-Konsole


 Diese Bereitstellung eignet sich für einfache Entwicklungsszenarien mit dem Elastic Stack. Weitere Informationen zum Elastic Stack (einschließlich Empfehlungen für eine Produktionsumgebung) finden Sie in der [Elastic-Dokumentation](https://www.elastic.co/guide/index.html) sowie im [Azure-Architekturcenter](/azure/architecture/elasticsearch/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.4 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl [az group create](/cli/azure/group) eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden. 

Das folgende Beispiel erstellt eine Ressourcengruppe mit dem Namen *myResourceGroup* am Standort *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-machine"></a>Erstellen eines virtuellen Computers

Erstellen Sie mit dem Befehl [az vm create](/cli/azure/vm) einen virtuellen Computer. 

Das folgende Beispiel erstellt einen virtuellen Computer mit dem Namen *myVM* und SSH-Schlüssel, falls sie nicht bereits an einem Standardschlüsselspeicherort vorhanden sind. Um einen bestimmten Satz von Schlüsseln zu verwenden, nutzen Sie die Option `--ssh-key-value`.  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys
```

Nach dem Erstellen der VM zeigt die Azure CLI ähnliche Informationen wie im folgenden Beispiel an. Notieren Sie sich den Wert von `publicIpAddress`. Diese Adresse wird verwendet, um auf den virtuellen Computer zuzugreifen.

```azurecli-interactive 
{
  "fqdns": "",
  "id": "/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="ssh-into-your-vm"></a>Herstellen einer SSH-Verbindung mit Ihrem virtuellen Computer

Wenn Sie die öffentliche IP-Adresse des virtuellen Computers nicht bereits kennen, führen Sie den Befehl [az network public-ip list](/cli/azure/network/public-ip) aus:

```azurecli-interactive
az network public-ip list --resource-group myResourceGroup --query [].ipAddress
```

Erstellen Sie mit dem folgenden Befehl eine SSH-Sitzung mit dem virtuellen Computer. Ersetzen Sie die korrekte öffentliche IP-Adresse Ihres virtuellen Computers. In diesem Beispiel lautet die IP-Adresse *40.68.254.142*.

```bash
ssh azureuser@40.68.254.142
```

## <a name="install-the-elastic-stack"></a>Einrichten des Elastic Stack

Importieren Sie den Elasticsearch-Signaturschlüssel, und aktualisieren Sie Ihre APT-Quellenliste, sodass sie das Elastic-Paketrepository enthält:

```bash
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/5.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-5.x.list
```

Installieren Sie Java Virtual Machine auf dem virtuellen Computer, und konfigurieren Sie die JAVA_HOME-Variable. Dies ist für die Ausführung der Elastic Stack-Komponenten erforderlich.

```bash
sudo apt update && sudo apt install openjdk-8-jre-headless
export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
```

Führen Sie den folgenden Befehl aus, um die Ubuntu-Paketquellen zu aktualisieren und Elasticsearch, Kibana und Logstash zu installieren:

```bash
sudo apt update && sudo apt install elasticsearch kibana logstash   
```

> [!NOTE]
> Eine ausführliche Installationsanleitung mit Verzeichnislayouts und Erstkonfiguration finden Sie in der [Elastic-Dokumentation](https://www.elastic.co/guide/en/elastic-stack/current/installing-elastic-stack.html).

## <a name="start-elasticsearch"></a>Starten von Elasticsearch 

Führen Sie den folgenden Befehl aus, um Elasticsearch auf Ihrem virtuellen Computer zu starten:

```bash
sudo systemctl start elasticsearch.service
```

Dieser Befehl erzeugt keine Ausgabe. Führen Sie daher den folgenden `curl`-Befehl aus, um sich zu vergewissern, dass Elasticsearch auf dem virtuellen Computer ausgeführt wird:

```bash
sudo curl -XGET 'localhost:9200/'
```

Wenn Elasticsearch ausgeführt wird, erhalten Sie eine Ausgabe wie die folgende:

```json
{
  "name" : "w6Z4NwR",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "SDzCajBoSK2EkXmHvJVaDQ",
  "version" : {
    "number" : "5.6.3",
    "build_hash" : "1a2f265",
    "build_date" : "2017-10-06T20:33:39.012Z",
    "build_snapshot" : false,
    "lucene_version" : "6.6.1"
  },
  "tagline" : "You Know, for Search"
}
```

## <a name="start-logstash-and-add-data-to-elasticsearch"></a>Starten von Logstash und Hinzufügen von Daten zu Elasticsearch

Führen Sie den folgenden Befehl aus, um Logstash zu starten:

```bash 
sudo systemctl start logstash.service
```

Testen Sie Logstash im interaktiven Modus, um sicherzustellen, dass es ordnungsgemäß funktioniert:

```bash
sudo /usr/share/logstash/bin/logstash -e 'input { stdin { } } output { stdout {} }'
```

Hierbei handelt es sich um eine einfache [Logstash-Pipeline](https://www.elastic.co/guide/en/logstash/5.6/pipeline.html), die Standardeingaben als Standardausgaben zurückgibt. 

```output
The stdin plugin is now waiting for input:
hello azure
2017-10-11T20:01:08.904Z myVM hello azure
```

Richten Sie Logstash so ein, dass Kernelnachrichten von diesem virtuellen Computer an Elasticsearch weitergeleitet werden. Erstellen Sie in einem leeren Verzeichnis eine neue Datei namens `vm-syslog-logstash.conf`, und fügen Sie die folgende Logstash-Konfiguration ein:

```Logstash
input {
    stdin {
        type => "stdin-type"
    }

    file {
        type => "syslog"
        path => [ "/var/log/*.log", "/var/log/*/*.log", "/var/log/messages", "/var/log/syslog" ]
        start_position => "beginning"
    }
}

output {

    stdout {
        codec => rubydebug
    }
    elasticsearch {
        hosts  => "localhost:9200"
    }
}
```

Testen Sie diese Konfiguration, und senden Sie die Syslog-Daten an Elasticsearch:

```bash
sudo /usr/share/logstash/bin/logstash -f vm-syslog-logstash.conf
```

Die Syslog-Einträge werden während der Übermittlung an Elasticsearch in Ihrem Terminal angezeigt. Verwenden Sie `CTRL+C`, um Logstash zu verlassen, nachdem Sie einige Daten gesendet haben.

## <a name="start-kibana-and-visualize-the-data-in-elasticsearch"></a>Starten von Kibana und Visualisieren der Daten in Elasticsearch

Bearbeiten Sie `/etc/kibana/kibana.yml`, und ändern Sie die IP-Adresse, an der Kibana lauscht, damit Sie darauf über Ihren Webbrowser zugreifen können.

```bash
server.host:"0.0.0.0"
```

Führen Sie den folgenden Befehl aus, um Kibana zu starten:

```bash
sudo systemctl start kibana.service
```

Öffnen Sie über die Azure-Befehlszeilenschnittstelle den Port 5601, um den Remotezugriff auf die Kibana-Konsole zu ermöglichen:

```azurecli-interactive
az vm open-port --port 5601 --resource-group myResourceGroup --name myVM
```

Öffnen Sie die Kibana-Konsole, und klicken Sie auf **Erstellen**, um auf der Grundlage der zuvor an Elasticsearch gesendeten Syslog-Daten einen Standardindex zu generieren. 

![Durchsuchen von Syslog-Ereignissen in Kibana](media/elasticsearch-install/kibana-index.png)

Klicken Sie in der Kibana-Konsole auf **Discover** (Entdecken), um die Syslog-Ereignisse zu durchsuchen und zu filtern.

![Durchsuchen von Syslog-Ereignissen in Kibana](media/elasticsearch-install/kibana-search-filter.png)

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie den Elastic Stack für einen virtuellen Entwicklungscomputer in Azure bereitgestellt. Es wurde Folgendes vermittelt:

> [!div class="checklist"]
> * Erstellen eines virtuellen Ubuntu-Computers in einer Azure-Ressourcengruppe
> * Installieren von Elasticsearch, Logstash und Kibana auf dem virtuellen Computer
> * Senden von Beispieldaten aus Logstash an Elasticsearch 
> * Öffnen von Ports und Arbeiten mit Daten in der Kibana-Konsole
