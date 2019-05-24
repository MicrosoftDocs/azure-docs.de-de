---
title: 'Tutorial: Verwenden eines virtuellen Linux-Computers und einer Python-Anwendung zum Speichern von Geheimnissen in Azure Key Vault | Microsoft-Dokumentation'
description: In diesem Tutorial wird beschrieben, wie Sie eine Python-Anwendung zum Lesen eines Geheimnisses aus Azure Key Vault konfigurieren.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 09/05/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: e5fbb4e6f6599d8f9560561fc219dbf57ac0cee1
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/06/2019
ms.locfileid: "66147761"
---
# <a name="tutorial-use-a-linux-vm-and-a-python-app-to-store-secrets-in-azure-key-vault"></a>Tutorial: Verwenden eines virtuellen Linux-Computers und einer Python-App zum Speichern von Geheimnissen in Azure Key Vault

Azure Key Vault unterstützt Sie beim Schutz von Geheimnissen wie den API-Schlüsseln und Datenbank-Verbindungszeichenfolgen, die für den Zugriff auf Ihre Anwendungen, Dienste und IT-Ressourcen benötigt werden.

In diesem Tutorial richten Sie eine Azure-Webanwendung ein, die unter Verwendung von verwalteten Identitäten für Azure-Ressourcen Informationen aus Azure Key Vault liest. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen eines Schlüsseltresors
> * Speichern eines Geheimnisses in Ihrem Schlüsseltresor
> * Erstellen einer virtuellen Linux-Maschine
> * Aktivieren einer [verwalteten Identität](../active-directory/managed-identities-azure-resources/overview.md) für den virtuellen Computer
> * Erteilen der erforderlichen Berechtigungen zum Lesen von Daten aus dem Schlüsseltresor für die Konsolenanwendung
> * Abrufen eines Geheimnisses aus Ihrem Schlüsseltresor

Stellen Sie vor dem Fortfahren sicher, dass Sie mit den [Grundlagen von Key Vault](key-vault-whatis.md#basic-concepts) vertraut sind.

## <a name="prerequisites"></a>Voraussetzungen

* [Git](https://git-scm.com/downloads).
* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
* [Azure CLI Version 2.0.4 oder höher](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) oder Azure Cloud Shell.

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Grundlegendes zur verwalteten Dienstidentität

Azure Key Vault dient zum sicheren Speichern von Anmeldeinformationen, damit sich diese nicht in Ihrem Code befinden. Um diese abzurufen, müssen Sie sich bei Azure Key Vault authentifizieren. Für die Authentifizierung bei Key Vault benötigen Sie aber Anmeldeinformationen. Es handelt sich um ein klassisches Bootstrap-Problem. Die verwaltete Dienstidentität (Managed Service Identity, MSI) stellt über Azure und Azure Active Directory (Azure AD) eine Bootstrap-Identität bereit, die die ersten Schritte erheblich vereinfacht.

Wenn Sie MSI für einen Azure-Dienst aktivieren (z. B. Virtual Machines, App Service oder Functions), erstellt Azure einen Dienstprinzipal für die Instanz des Diensts in Azure AD. Die Anmeldeinformationen für den Dienstprinzipal werden in die Instanz des Diensts eingefügt.

![MSI](media/MSI.png)

Als Nächstes ruft Ihr Code einen lokalen, für die Azure-Ressource verfügbaren Metadatendienst auf, um ein Zugriffstoken zu erhalten. Ihr Code verwendet das vom lokalen MSI-Endpunkt erhaltene Zugriffstoken für die Authentifizierung bei einem Azure Key Vault-Dienst.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Geben Sie Folgendes ein, um sich mithilfe der Azure-Befehlszeilenschnittstelle bei Azure anzumelden:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie mit dem Befehl `az group create` eine Ressourcengruppe am Standort „USA, Westen“, indem Sie den folgenden Code verwenden. Ersetzen Sie `YourResourceGroupName` durch einen Namen Ihrer Wahl.

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Diese Ressourcengruppe wird im gesamten Tutorial verwendet.

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

Als Nächstes erstellen Sie einen Schlüsseltresor in der Ressourcengruppe aus dem vorherigen Schritt. Geben Sie die folgenden Informationen ein:

* Name des Schlüsseltresors: Der Name muss eine Zeichenfolge aus 3 bis 24 Zeichen sein und darf nur die Zeichen 0–9, a–z, A–Z und Bindestrich (-) enthalten.
* Ressourcengruppenname
* Standort: **USA, Westen**.

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

An diesem Punkt ist nur Ihr Azure-Konto zum Ausführen von Vorgängen für den neuen Tresor autorisiert.

## <a name="add-a-secret-to-the-key-vault"></a>Hinzufügen eines Geheimnisses zum Schlüsseltresor

Wir fügen ein Geheimnis hinzu, um die Vorgehensweise zu veranschaulichen. Es kann sein, dass Sie eine SQL-Verbindungszeichenfolge oder andere Informationen speichern möchten, die sowohl sicher als auch für Ihre Anwendung zugänglich sein müssen.

Geben Sie die folgenden Befehle ein, um im Schlüsseltresor ein Geheimnis namens *AppSecret* zu erstellen. Dieses Geheimnis speichert den Wert **MySecret**.

```azurecli-interactive
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Erstellen einer virtuellen Linux-Maschine

Erstellen Sie mit dem Befehl `az vm create` einen virtuellen Computer.

Im folgenden Beispiel wird eine VM mit dem Namen **myVM** erstellt und ein Benutzerkonto mit dem Namen **azureuser** hinzugefügt. Der Parameter `--generate-ssh-keys` generiert automatisch einen SSH-Schlüssel und legt ihn am Speicherort für den Standardschlüssel ab (**~/.ssh**). Um stattdessen einen bestimmten Satz mit Schlüsseln zu erstellen, können Sie die Option `--ssh-key-value` nutzen.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Das Erstellen des virtuellen Computers und der unterstützenden Ressourcen dauert einige Minuten. In der folgenden Beispielausgabe wird angezeigt, dass die Erstellung des virtuellen Computers erfolgreich war:

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-00-00-00-00-00",
  "powerState": "VM running",
  "privateIpAddress": "XX.XX.XX.XX",
  "publicIpAddress": "XX.XX.XXX.XXX",
  "resourceGroup": "myResourceGroup"
}
```

Notieren Sie sich in der Ausgabe Ihres virtuellen Computers die Adressangabe unter `publicIpAddress`. Sie verwenden diese Adresse in späteren Schritten, um auf den virtuellen Computer zuzugreifen.

## <a name="assign-an-identity-to-the-vm"></a>Zuweisen einer Identität zum virtuellen Computer

Erstellen Sie eine vom System zugewiesene Identität für den virtuellen Computer, indem Sie den folgenden Befehl ausführen:

```azurecli-interactive
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Die Ausgabe des Befehls lautet:

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Notieren Sie sich den Namen in `systemAssignedIdentity`. Sie benötigen ihn im nächsten Schritt.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Erteilen von Berechtigungen für die VM-Identität für Key Vault

Nun können Sie für die von Ihnen erstellte Identität die Key Vault-Berechtigung erteilen. Führen Sie den folgenden Befehl aus:

```azurecli-interactive
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Anmelden am virtuellen Computer

Melden Sie sich über ein Terminal am virtuellen Computer an.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-library-on-the-vm"></a>Installieren der Python-Bibliothek auf dem virtuellen Computer

Laden Sie die Python-Bibliothek [requests](https://pypi.org/project/requests/2.7.0/) herunter, und installieren Sie sie, um HTTP GET-Aufrufe durchzuführen.

## <a name="create-edit-and-run-the-sample-python-app"></a>Erstellen, Bearbeiten und Ausführen der Python-Beispiel-App

Erstellen Sie eine Python-Datei mit dem Namen **Sample.py**.

Öffnen Sie die Datei „Sample.py“, und bearbeiten Sie sie, damit sie den folgenden Code enthält:

```python
# importing the requests library
  import requests
  
# Step 1: Fetch an access token from an MSI-enabled Azure resource      
  # Note that the resource here is https://vault.azure.net for the public cloud, and api-version is 2018-02-01
  MSI_ENDPOINT = "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net"
  r = requests.get(MSI_ENDPOINT, headers = {"Metadata" : "true"})

# Extracting data in JSON format 
  # This request gets an access token from Azure Active Directory by using the local MSI endpoint
  data = r.json()

# Step 2: Pass the access token received from the previous HTTP GET call to the key vault
  KeyVaultURL = "https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01"
  kvSecret = requests.get(url = KeyVaultURL, headers = {"Authorization": "Bearer " + data["access_token"]})

print(kvSecret.json()["value"])
```

Mit dem obigen Code wird ein zweistufiger Prozess durchgeführt:

   1. Ein Token wird vom lokalen MSI-Endpunkt auf dem virtuellen Computer abgerufen. Der Endpunkt ruft dann ein Token aus Azure Active Directory ab.
   1. Das Token wird an den Schlüsseltresor übergeben, und Ihr Geheimnis wird abgerufen.

Führen Sie den folgenden Befehl aus: Der Wert des Geheimnisses sollte angezeigt werden.

```console
python Sample.py
```

In diesem Tutorial haben Sie gelernt, wie Sie Azure Key Vault mit einer Python-App verwenden, die auf einem virtuellen Linux-Computer ausgeführt wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Wählen Sie hierzu die Ressourcengruppe für den virtuellen Computer aus, und wählen Sie **Löschen**.

Löschen Sie den Schlüsseltresor mit dem Befehl `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource-group]
                   [--subscription]
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Azure Key Vault-REST-API](https://docs.microsoft.com/rest/api/keyvault/)
