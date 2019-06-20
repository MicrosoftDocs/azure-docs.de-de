---
title: 'Tutorial: Verwenden eines virtuellen Linux-Computers und einer ASP.NET-Konsolenanwendung zum Speichern von Geheimnissen in Azure Key Vault | Microsoft-Dokumentation'
description: In diesem Tutorial wird beschrieben, wie Sie eine ASP.NET Core-Anwendung zum Lesen eines Geheimnisses aus Azure Key Vault konfigurieren.
services: key-vault
author: msmbaldwin
manager: rajvijan
ms.service: key-vault
ms.topic: tutorial
ms.date: 12/21/2018
ms.author: pryerram
ms.custom: mvc
ms.openlocfilehash: 576ce0abc15a646738fea57dfabf43c889635a4b
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/30/2019
ms.locfileid: "66416931"
---
# <a name="tutorial-use-a-linux-vm-and-a-net-app-to-store-secrets-in-azure-key-vault"></a>Tutorial: Verwenden eines virtuellen Linux-Computers und einer .NET-App zum Speichern von Geheimnissen in Azure Key Vault

Azure Key Vault unterstützt Sie beim Schutz von Geheimnissen wie API-Schlüsseln und Datenbank-Verbindungszeichenfolgen, die für den Zugriff auf Ihre Anwendungen, Dienste und IT-Ressourcen benötigt werden.

In diesem Tutorial richten Sie eine .NET-Konsolenanwendung ein, die unter Verwendung von verwalteten Identitäten für Azure-Ressourcen Informationen aus Azure Key Vault liest. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen eines Schlüsseltresors
> * Speichern eines Geheimnisses in Key Vault
> * Erstellen eines virtuellen Azure Linux-Computers
> * Aktivieren einer [verwalteten Identität](../active-directory/managed-identities-azure-resources/overview.md) für den virtuellen Computer
> * Erteilen der erforderlichen Berechtigungen zum Lesen von Daten aus Key Vault für die Konsolenanwendung
> * Abrufen eines Geheimnisses aus Key Vault

Lesen Sie die Informationen zu [grundlegenden Konzepten in Key Vault](key-vault-whatis.md#basic-concepts), bevor Sie fortfahren.

## <a name="prerequisites"></a>Voraussetzungen

* [Git](https://git-scm.com/downloads).
* Ein Azure-Abonnement. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
* [Azure CLI 2.0 oder höher](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) oder Azure Cloud Shell

[!INCLUDE [Azure Cloud Shell](../../includes/cloud-shell-try-it.md)]

## <a name="understand-managed-service-identity"></a>Grundlegendes zur verwalteten Dienstidentität

Azure Key Vault kann Anmeldeinformationen sicher speichern. Dadurch befinden sie sich nicht in Ihrem Code. Zum Abrufen der Anmeldeinformationen ist jedoch eine Authentifizierung bei Azure Key Vault erforderlich. Für die Authentifizierung bei Key Vault benötigen Sie aber Anmeldeinformationen. Es handelt sich um ein klassisches Bootstrap-Problem. Die verwaltete Dienstidentität (Managed Service Identity, MSI) stellt über Azure und Azure Active Directory (Azure AD) eine Bootstrap-Identität bereit, die die ersten Schritte erheblich vereinfacht.

Wenn Sie die MSI für einen Azure-Dienst (z. B. Virtual Machines, App Service oder Functions) aktivieren, erstellt Azure einen Dienstprinzipal für die Instanz des Diensts in Azure Active Directory. Die Anmeldeinformationen für den Dienstprinzipal werden in die Instanz des Diensts eingefügt.

![MSI](media/MSI.png)

Als Nächstes ruft Ihr Code einen lokalen, für die Azure-Ressource verfügbaren Metadatendienst auf, um ein Zugriffstoken zu erhalten.
Ihr Code verwendet das vom lokalen MSI-Endpunkt (MSI_ENDPOINT) erhaltene Zugriffstoken für die Authentifizierung bei einem Azure Key Vault-Dienst.

## <a name="sign-in-to-azure"></a>Anmelden bei Azure

Geben Sie Folgendes ein, um sich mithilfe der Azure-Befehlszeilenschnittstelle bei Azure anzumelden:

```azurecli-interactive
az login
```

## <a name="create-a-resource-group"></a>Erstellen einer Ressourcengruppe

Erstellen Sie mit dem Befehl `az group create` eine Ressourcengruppe. Eine Azure-Ressourcengruppe ist ein logischer Container, in dem Azure-Ressourcen bereitgestellt und verwaltet werden.

Erstellen Sie eine Ressourcengruppe am Standort „USA, Westen“. Wählen Sie einen Namen für Ihre Ressourcengruppe aus, und ersetzen Sie `YourResourceGroupName` im folgenden Beispiel:

```azurecli-interactive
# To list locations: az account list-locations --output table
az group create --name "<YourResourceGroupName>" --location "West US"
```

Diese Ressourcengruppe wird im gesamten Tutorial verwendet.

## <a name="create-a-key-vault"></a>Erstellen eines Schlüsseltresors

Erstellen Sie als Nächstes einen Schlüsseltresor in Ihrer Ressourcengruppe. Geben Sie die folgenden Informationen ein:

* Name des Schlüsseltresors: eine Zeichenfolge mit 3 bis 24 Zeichen, die nur Zahlen, Buchstaben und Bindestriche enthalten darf (0–9, a–z, A–Z und \-).
* Ressourcengruppenname
* Standort: **USA, Westen**

```azurecli-interactive
az keyvault create --name "<YourKeyVaultName>" --resource-group "<YourResourceGroupName>" --location "West US"
```

An diesem Punkt ist nur Ihr Azure-Konto zum Ausführen von Vorgängen für den neuen Tresor autorisiert.

## <a name="add-a-secret-to-the-key-vault"></a>Hinzufügen eines Geheimnisses zum Schlüsseltresor

Fügen Sie nun ein Geheimnis hinzu. In der Praxis speichern Sie ggf. eine SQL-Verbindungszeichenfolge oder beliebige andere Informationen, die sicher aufbewahrt, aber für Ihre Anwendung verfügbar gemacht werden müssen.

Geben Sie in diesem Tutorial die folgenden Befehle ein, um ein Geheimnis im Schlüsseltresor zu erstellen. Das Geheimnis heißt **AppSecret** und hat den Wert **MySecret**.

```azurecli
az keyvault secret set --vault-name "<YourKeyVaultName>" --name "AppSecret" --value "MySecret"
```

## <a name="create-a-linux-virtual-machine"></a>Erstellen einer virtuellen Linux-Maschine

Erstellen Sie mit dem Befehl `az vm create` einen virtuellen Computer.

Im folgenden Beispiel wird eine VM mit dem Namen **myVM** erstellt und ein Benutzerkonto mit dem Namen **azureuser** hinzugefügt. Der Parameter `--generate-ssh-keys` wird genutzt, um automatisch einen SSH-Schlüssel zu generieren und am Speicherort für den Standardschlüssel abzulegen ( **~/.ssh**). Um stattdessen einen bestimmten Satz mit Schlüsseln zu verwenden, können Sie die Option `--ssh-key-value` nutzen.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Das Erstellen des virtuellen Computers und der unterstützenden Ressourcen dauert einige Minuten. In der folgenden Beispielausgabe wird angezeigt, dass die Erstellung des virtuellen Computers erfolgreich war.

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

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Die Ausgabe des Befehls sollte wie folgt aussehen:

```azurecli
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

Notieren Sie sich den Namen in `systemAssignedIdentity`. Sie verwenden ihn im nächsten Schritt.

## <a name="give-the-vm-identity-permission-to-key-vault"></a>Erteilen von Berechtigungen für die VM-Identität für Key Vault

Nun können Sie für die von Ihnen erstellte Identität die Key Vault-Berechtigung erteilen. Führen Sie den folgenden Befehl aus:

```azurecli
az keyvault set-policy --name '<YourKeyVaultName>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Anmelden am virtuellen Computer

Melden Sie sich über ein Terminal am virtuellen Computer an.

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-net-core-on-linux"></a>Installieren von .NET Core unter Linux

Auf Ihrem virtuellen Linux-Computer:

Registrieren Sie mit den folgenden Befehlen den Product Key von Microsoft als vertrauenswürdig:

   ```console
   curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
   sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
   ```

Richten Sie den Hostpaketfeed für die gewünschte Version auf der Grundlage des Betriebssystems ein:

```console
   # Ubuntu 17.10
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-artful-prod artful main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 17.04
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-zesty-prod zesty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 16.04 / Linux Mint 18
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
   
   # Ubuntu 14.04 / Linux Mint 17
   sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-trusty-prod trusty main" > /etc/apt/sources.list.d/dotnetdev.list'
   sudo apt-get update
```

Installieren Sie .NET, und überprüfen Sie die Version:

   ```console
   sudo apt-get install dotnet-sdk-2.1.4
   dotnet --version
   ```

## <a name="create-and-run-a-sample-net-app"></a>Erstellen und Ausführen einer .NET-Beispiel-App

Führen Sie die folgenden Befehle aus: In der Konsole sollte „Hallo Welt“ ausgegeben werden.

```console
dotnet new console -o helloworldapp
cd helloworldapp
dotnet run
```

## <a name="edit-the-console-app-to-fetch-your-secret"></a>Bearbeiten der Konsolen-App zum Abrufen des Geheimnisses

Öffnen Sie die Datei „Program.cs“, und fügen Sie die folgenden Pakete hinzu:

   ```csharp
   using System;
   using System.IO;
   using System.Net;
   using System.Text;
   using Newtonsoft.Json;
   using Newtonsoft.Json.Linq;
   ```

Um die Klassendatei zu ändern und der App so den Zugriff auf das Geheimnis im Schlüsseltresor zu ermöglichen, sind zwei Schritte erforderlich.

1. Rufen Sie ein Token vom lokalen MSI-Endpunkt auf dem virtuellen Computer ab. Daraufhin wird wiederum ein Token aus Azure Active Directory abgerufen.
1. Übergeben Sie das Token an Key Vault, und rufen Sie Ihr Geheimnis ab.

   Bearbeiten Sie die Klassendatei so, dass sie den folgenden Code enthält:

   ```csharp
    class Program
       {
           static void Main(string[] args)
           {
               // Step 1: Get a token from local (URI) Managed Service Identity endpoint which in turn fetches it from Azure Active Directory
               var token = GetToken();

               // Step 2: Fetch the secret value from Key Vault
               System.Console.WriteLine(FetchSecretValueFromKeyVault(token));
           }

           static string GetToken()
           {
               WebRequest request = WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net");
               request.Headers.Add("Metadata", "true");
               WebResponse response = request.GetResponse();
               return ParseWebResponse(response, "access_token");
           }

           static string FetchSecretValueFromKeyVault(string token)
           {
               WebRequest kvRequest = WebRequest.Create("https://prashanthwinvmvault.vault.azure.net/secrets/RandomSecret?api-version=2016-10-01");
               kvRequest.Headers.Add("Authorization", "Bearer "+  token);
               WebResponse kvResponse = kvRequest.GetResponse();
               return ParseWebResponse(kvResponse, "value");
           }

           private static string ParseWebResponse(WebResponse response, string tokenName)
           {
               string token = String.Empty;
               using (Stream stream = response.GetResponseStream())
               {
                   StreamReader reader = new StreamReader(stream, Encoding.UTF8);
                   String responseString = reader.ReadToEnd();

                   JObject joResponse = JObject.Parse(responseString);
                   JValue ojObject = (JValue)joResponse[tokenName];
                   token = ojObject.Value.ToString();
               }
               return token;
           }
       }
   ```

Sie haben gelernt, wie Sie mit Azure Key Vault Vorgänge in einer .NET-Anwendung ausführen, die auf einem virtuellen Azure Linux-Computer ausgeführt wird.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Löschen Sie die Ressourcengruppe, den virtuellen Computer und alle zugehörigen Ressourcen, wenn Sie sie nicht mehr benötigen. Wählen Sie hierzu die Ressourcengruppe für den virtuellen Computer aus, und wählen Sie **Löschen**.

Löschen Sie den Schlüsseltresor mit dem Befehl `az keyvault delete`:

```azurecli-interactive
az keyvault delete --name
                   [--resource group]
                   [--subscription]
```

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Azure Key Vault-REST-API](https://docs.microsoft.com/rest/api/keyvault/)
