---
title: 'Tutorial: Anpassen eines virtuellen Linux-Computers mit cloud-init in Azure'
description: In diesem Tutorial erfahren Sie, wie Sie cloud-init und Key Vault zum Anpassen virtueller Linux-Computer beim ersten Start in Azure verwenden.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2019
ms.author: cynthn
ms.custom: mvc, devx-track-js, devx-track-azurecli
ms.openlocfilehash: 456c42dc0b25e168744ce283cddbd63b877813ab
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/27/2020
ms.locfileid: "92747146"
---
# <a name="tutorial---how-to-use-cloud-init-to-customize-a-linux-virtual-machine-in-azure-on-first-boot"></a>Tutorial: Verwenden von cloud-init zum Anpassen eines virtuellen Linux-Computers in Azure beim ersten Start

In einem früheren Tutorial haben Sie erfahren, wie eine SSH-Verbindung mit einem virtuellen Computer hergestellt und NGINX manuell installiert wird. Um VMs auf schnelle und einheitliche Weise zu erstellen, ist meist eine Form der Automatisierung erwünscht. Eine gängige Methode zum Anpassen virtueller Computer beim ersten Start ist die Verwendung von [cloud-Init](https://cloudinit.readthedocs.io). In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Erstellen einer cloud-init-Konfigurationsdatei
> * Erstellen eines virtuellen Computers, der eine cloud-init-Datei verwendet
> * Anzeigen einer laufenden Node.js-App nach dem Erstellen der VM
> * Verwenden von Key Vault zum sicheren Speichern der Zertifikate
> * Automatisieren der sicheren Bereitstellung von NGINX mithilfe von cloud-init

Wenn Sie die CLI lokal installieren und verwenden möchten, müssen Sie für dieses Tutorial die Azure CLI-Version 2.0.30 oder höher ausführen. Führen Sie `az --version` aus, um die Version zu finden. Informationen zum Durchführen einer Installation oder eines Upgrades finden Sei bei Bedarf unter [Installieren der Azure CLI]( /cli/azure/install-azure-cli).

## <a name="cloud-init-overview"></a>Übersicht zu cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) ist ein weit verbreiteter Ansatz zum Anpassen einer Linux-VM beim ersten Start. Sie können mit cloud-init Pakete installieren und Dateien schreiben oder Benutzer und Sicherheit konfigurieren. Da cloud-init während des ersten Startvorgangs ausgeführt wird, müssen Sie keine zusätzlichen Schritte oder erforderlichen Agents auf Ihre Konfiguration anwenden.

Cloud-init funktioniert auch Distributionen übergreifend. Verwenden Sie z.B. nicht **apt-get install** oder **yum install** , um ein Paket zu installieren. Stattdessen können Sie eine Liste mit zu installierenden Paketen definieren. „cloud-init“ verwendet automatisch das native Paketverwaltungstool für die ausgewählte Distribution.

Wir arbeiten mit unseren Partnern zusammen, damit cloud-init einbezogen wird und in den Images arbeitet, die sie in Azure bereitstellen. In der folgenden Tabelle ist die aktuelle cloud-init-Verfügbarkeit für Azure-Plattformimages aufgeführt:

| Herausgeber | Angebot | SKU | Version | cloud-init-fähig |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |latest |ja | 
|Canonical |UbuntuServer |16.04-LTS |latest |ja | 
|Canonical |UbuntuServer |14.04.5-LTS |latest |ja |
|CoreOS |CoreOS |Stable |latest |ja |
|OpenLogic 7.6 |CentOS |7-CI |latest |preview |
|RedHat 7.6 |RHEL |7-RAW-CI |7.6.2019072418 |ja |
|RedHat 7.7 |RHEL |7-RAW-CI |7.7.2019081601 |preview |


## <a name="create-cloud-init-config-file"></a>Erstellen der Konfigurationsdatei „cloud-init.txt“
Um cloud-init in Aktion zu sehen, erstellen Sie einen virtuellen Computer, der NGINX installiert und eine einfache „Hello World“-Node.js-App ausführt. Die folgende cloud-init-Konfiguration installiert die erforderlichen Pakete, erstellt eine Node.js-App und initialisiert und startet dann die Anwendung.

Erstellen Sie an der Bash-Eingabeaufforderung oder in der Cloud Shell eine Datei namens *cloud-init.txt* , und fügen Sie die folgende Konfiguration ein. Geben Sie beispielsweise `sensible-editor cloud-init.txt` ein, um die Datei zu erstellen und eine Liste mit verfügbaren Editoren anzuzeigen. Stellen Sie sicher, dass die gesamte Datei „cloud-init“ ordnungsgemäß kopiert wird, insbesondere die erste Zeile:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

Weitere Informationen zu den cloud-init-Konfigurationsoptionen finden Sie in den [cloud-init-Konfigurationsbeispielen](https://cloudinit.readthedocs.io/en/latest/topics/examples.html).

## <a name="create-virtual-machine"></a>Erstellen eines virtuellen Computers
Vor der Erstellung eines virtuellen Computers müssen Sie zunächst mit [az group create](/cli/azure/group#az-group-create) eine Ressourcengruppe erstellen. Das folgende Beispiel erstellt am Standort *eastus* eine Ressourcengruppe mit dem Namen *myResourceGroupAutomate* :

```azurecli-interactive
az group create --name myResourceGroupAutomate --location eastus
```

Jetzt können Sie mit [az vm create](/cli/azure/vm#az-vm-create) einen virtuellen Computer erstellen. Verwenden Sie den `--custom-data`-Parameter, um Ihre cloud-init-Konfigurationsdatei zu übergeben. Geben Sie den vollständigen Pfad zu der Konfigurationsdatei *cloud-init.txt* an, wenn Sie die Datei außerhalb Ihres vorhandenen Arbeitsverzeichnisses gespeichert haben. Im folgenden Beispiel wird ein virtueller Computer namens *myVM* erstellt:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myAutomatedVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init.txt
```

Es dauert einige Minuten, den virtuellen Computer zu erstellen, die Pakete zu installieren und die App zu starten. Es gibt Hintergrundaufgaben, die weiterhin ausgeführt werden, wenn Ihnen von der Azure CLI wieder eine Eingabeaufforderung angezeigt wird. Unter Umständen dauert es noch einige Minuten, bis Sie auf die App zugreifen können. Sobald der virtuelle Computer erstellt ist, notieren Sie die `publicIpAddress`, die von der Azure-CLI angezeigt wird. Diese Adresse wird verwendet, um über einen Webbrowser auf die Node.js-App zuzugreifen.

Damit Webdatenverkehr Ihren virtuellen Computer erreicht, öffnen Sie Port 80 über das Internet mit [az vm open-port](/cli/azure/vm#az-vm-open-port):

```azurecli-interactive
az vm open-port --port 80 --resource-group myResourceGroupAutomate --name myAutomatedVM
```

## <a name="test-web-app"></a>Testen der Web-App
Jetzt können Sie einen Webbrowser öffnen und *http:\/\/\<publicIpAddress>* in die Adressleiste eingeben. Geben Sie Ihre eigene öffentliche IP-Adresse aus dem Erstellungsprozess des virtuellen Computers an. Ihre Node.js-App wird wie im folgenden Beispiel angezeigt:

![Anzeigen der ausgeführten NGINX-Website](./media/tutorial-automate-vm-deployment/nginx.png)


## <a name="inject-certificates-from-key-vault"></a>Einfügen von Zertifikaten aus Key Vault
Dieser optionale Abschnitt zeigt, wie Sie Zertifikate sicher in Azure Key Vault speichern und während der Bereitstellung des virtuellen Computers einfügen können. Anstatt ein benutzerdefiniertes Image zu verwenden, in dem die Zertifikate integriert sind, stellt dieser Prozess sicher, dass die meisten aktuellen Zertifikate einem virtuellen Computer beim ersten Start eingefügt werden. Während des Prozesses verlässt das Zertifikat nie die Azure-Plattform, noch wird es in einem Skript, Befehlszeilenverlauf oder einer Vorlage verfügbar gemacht.

Azure Key Vault schützt Kryptografieschlüssel und Geheimnisse, wie z.B. Zertifikate oder Kennwörter. Key Vault optimiert die Schlüsselverwaltung und ermöglicht Ihnen, die Kontrolle über Schlüssel zu behalten, die für den Datenzugriff und die Verschlüsselung Ihrer Daten verwendet werden. Dieses Szenario stellt einige Key Vault-Konzepte zum Erstellen und Verwenden eines Zertifikats vor, ist jedoch keine vollständige Übersicht über die Verwendung von Key Vault.

Die folgenden Schritte zeigen Ihnen, wie Sie Folgendes durchführen können:

- Erstellen einer Azure Key Vault-Instanz
- Generieren oder Hochladen eines Zertifikats in Key Vault
- Erstellen eines geheimen Schlüssels aus dem Zertifikat zum Einfügen in einen virtuellen Computer
- Erstellen eines virtuellen Computers und Einfügen des Zertifikats

### <a name="create-an-azure-key-vault"></a>Erstellen einer Azure Key Vault-Instanz
Erstellen Sie zunächst eine Key Vault-Instanz mit [az keyvault create](/cli/azure/keyvault#az-keyvault-create), und aktivieren Sie sie für die Verwendung, wenn Sie einen virtuellen Computer bereitstellen. Jede Key Vault-Instanz benötigt einen eindeutigen Namen, der nur aus Kleinbuchstaben besteht. Ersetzen Sie *mykeyvault* im folgenden Beispiel durch Ihren eigenen eindeutigen Key Vault-Namen:

```azurecli-interactive
keyvault_name=mykeyvault
az keyvault create \
    --resource-group myResourceGroupAutomate \
    --name $keyvault_name \
    --enabled-for-deployment
```

### <a name="generate-certificate-and-store-in-key-vault"></a>Generieren eines Zertifikats und Speichern in Key Vault
Für die Produktion sollten Sie ein gültiges, von einem vertrauenswürdigen Anbieter signiertes Zertifikat mit [az keyvault certificate import](/cli/azure/keyvault/certificate#az-keyvault-certificate-import) importieren. Für dieses Tutorial zeigt das folgende Beispiel, wie Sie ein selbstsigniertes Zertifikat mit [az keyvault certificate create](/cli/azure/keyvault/certificate#az-keyvault-certificate-create) generieren können, das die Standardzertifikatrichtlinie verwendet:

```azurecli-interactive
az keyvault certificate create \
    --vault-name $keyvault_name \
    --name mycert \
    --policy "$(az keyvault certificate get-default-policy --output json)"
```


### <a name="prepare-certificate-for-use-with-vm"></a>Vorbereiten des Zertifikats für die Verwendung mit der VM
Um das Zertifikat während der Erstellung des virtuellen Computers zu verwenden, rufen Sie die ID des Zertifikats mit [az keyvault secret list-versions](/cli/azure/keyvault/secret#az-keyvault-secret-list-versions) ab. Das Zertifikat muss in einem bestimmten Format vorliegen, um es beim Systemstart verwenden zu können. Wandeln Sie es daher mit [az vm secret format](/cli/azure/vm) um. Im folgenden Beispiel wird die Ausgabe dieser Befehle Variablen zugewiesen, um die Verwendung in den nächsten Schritten zu vereinfachen:

```azurecli-interactive
secret=$(az keyvault secret list-versions \
          --vault-name $keyvault_name \
          --name mycert \
          --query "[?attributes.enabled].id" --output tsv)
vm_secret=$(az vm secret format --secret "$secret" --output json)
```


### <a name="create-cloud-init-config-to-secure-nginx"></a>Erstellen der cloud-init-Konfiguration zum Sichern von NGINX
Wenn Sie einen virtuellen Computer erstellen, werden Zertifikate und Schlüssel im geschützten Verzeichnis */var/lib/waagent/* gespeichert. Um das Hinzufügen des Zertifikats zum virtuellen Computer und Konfigurieren von NGINX zu automatisieren, können Sie eine aktualisierte cloud-init-Konfiguration aus dem vorherigen Beispiel verwenden.

Erstellen Sie eine Datei namens *cloud-init-secured.txt* , und fügen Sie die folgende Konfiguration ein. Erstellen Sie bei Verwendung der Cloud Shell die cloud-init-Konfigurationsdatei nicht auf Ihrem lokalen Computer, sondern über die Cloud Shell. Geben Sie beispielsweise `sensible-editor cloud-init-secured.txt` ein, um die Datei zu erstellen und eine Liste mit verfügbaren Editoren anzuzeigen. Stellen Sie sicher, dass die gesamte Datei „cloud-init“ ordnungsgemäß kopiert wird, insbesondere die erste Zeile:

```yaml
#cloud-config
package_upgrade: true
packages:
  - nginx
  - nodejs
  - npm
write_files:
  - owner: www-data:www-data
    path: /etc/nginx/sites-available/default
    content: |
      server {
        listen 80;
        listen 443 ssl;
        ssl_certificate /etc/nginx/ssl/mycert.cert;
        ssl_certificate_key /etc/nginx/ssl/mycert.prv;
        location / {
          proxy_pass http://localhost:3000;
          proxy_http_version 1.1;
          proxy_set_header Upgrade $http_upgrade;
          proxy_set_header Connection keep-alive;
          proxy_set_header Host $host;
          proxy_cache_bypass $http_upgrade;
        }
      }
  - owner: azureuser:azureuser
    path: /home/azureuser/myapp/index.js
    content: |
      var express = require('express')
      var app = express()
      var os = require('os');
      app.get('/', function (req, res) {
        res.send('Hello World from host ' + os.hostname() + '!')
      })
      app.listen(3000, function () {
        console.log('Hello world app listening on port 3000!')
      })
runcmd:
  - secretsname=$(find /var/lib/waagent/ -name "*.prv" | cut -c -57)
  - mkdir /etc/nginx/ssl
  - cp $secretsname.crt /etc/nginx/ssl/mycert.cert
  - cp $secretsname.prv /etc/nginx/ssl/mycert.prv
  - service nginx restart
  - cd "/home/azureuser/myapp"
  - npm init
  - npm install express -y
  - nodejs index.js
```

### <a name="create-secure-vm"></a>Erstellen einer sicheren VM
Jetzt können Sie mit [az vm create](/cli/azure/vm#az-vm-create) einen virtuellen Computer erstellen. Die Zertifikatsdaten werden mit dem `--secrets`-Parameter aus Key Vault eingefügt. Wie im vorherigen Beispiel übergeben Sie auch hier die cloud-init-Konfigurationsdatei mit dem `--custom-data`-Parameter:

```azurecli-interactive
az vm create \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --custom-data cloud-init-secured.txt \
    --secrets "$vm_secret"
```

Es dauert einige Minuten, den virtuellen Computer zu erstellen, die Pakete zu installieren und die App zu starten. Es gibt Hintergrundaufgaben, die weiterhin ausgeführt werden, wenn Ihnen von der Azure CLI wieder eine Eingabeaufforderung angezeigt wird. Unter Umständen dauert es noch einige Minuten, bis Sie auf die App zugreifen können. Sobald der virtuelle Computer erstellt ist, notieren Sie die `publicIpAddress`, die von der Azure-CLI angezeigt wird. Diese Adresse wird verwendet, um über einen Webbrowser auf die Node.js-App zuzugreifen.

Damit sicherer Webdatenverkehr Ihren virtuellen Computer erreicht, öffnen Sie Port 443 über das Internet mit [az vm open-port](/cli/azure/vm#az-vm-open-port):

```azurecli-interactive
az vm open-port \
    --resource-group myResourceGroupAutomate \
    --name myVMWithCerts \
    --port 443
```

### <a name="test-secure-web-app"></a>Testen der sicheren Web-App
Jetzt können Sie einen Webbrowser öffnen und *https:\/\/\<publicIpAddress>* in die Adressleiste eingeben. Geben Sie Ihre eigene öffentliche IP-Adresse wie in der Ausgabe des vorherigen VM-Erstellungsprozesses gezeigt an. Akzeptieren Sie die Sicherheitswarnung, wenn Sie ein selbstsigniertes Zertifikat verwendet haben:

![Akzeptieren der Webbrowser-Sicherheitswarnung](./media/tutorial-automate-vm-deployment/browser-warning.png)

Gesicherte NGINX-Website und Node.js-App werden dann wie im folgenden Beispiel angezeigt:

![Anzeigen der ausgeführten sicheren NGINX-Website](./media/tutorial-automate-vm-deployment/secured-nginx.png)


## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie virtuelle Computer beim ersten Start mit cloud-init konfiguriert. Sie haben Folgendes gelernt:

> [!div class="checklist"]
> * Erstellen einer cloud-init-Konfigurationsdatei
> * Erstellen eines virtuellen Computers, der eine cloud-init-Datei verwendet
> * Anzeigen einer laufenden Node.js-App nach dem Erstellen der VM
> * Verwenden von Key Vault zum sicheren Speichern der Zertifikate
> * Automatisieren der sicheren Bereitstellung von NGINX mithilfe von cloud-init

Im nächsten Tutorial erfahren Sie, wie Sie benutzerdefinierte VM-Images erstellen.

> [!div class="nextstepaction"]
> [Erstellen von benutzerdefinierten VM-Images](./tutorial-custom-images.md)
