---
title: 'Schnellstart: Python-Clientbibliothek in Azure Key Vault – Verwalten von Zertifikaten'
description: Erfahren Sie, wie Sie mithilfe der Python-Clientbibliothek Zertifikate in einer Azure Key Vault-Instanz erstellen, daraus abrufen und löschen.
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: d9fdfc89c8c02749fdf677ad119f1eed5a4b1fa2
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185180"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-python"></a>Schnellstart: Azure Key Vault-Zertifikatclientbibliothek für Python

Enthält Informationen zu den ersten Schritten mit der Azure Key Vault-Zertifikatclientbibliothek für Python. Führen Sie die weiter unten beschriebenen Schritte aus, um das Paket zu installieren und den Beispielcode für grundlegende Aufgaben auszuprobieren. Wenn Sie Key Vault zum Speichern von Zertifikaten verwenden, vermeiden Sie das Speichern von Zertifikaten im Code, was die Sicherheit Ihrer App erhöht.

[API-Referenzdokumentation](/python/api/overview/azure/keyvault-certificates-readme) | [Quellcode der Bibliothek](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-certificates) | [Paket (Python-Paketindex)](https://pypi.org/project/azure-keyvault-certificates)

## <a name="prerequisites"></a>Voraussetzungen

- Azure-Abonnement ([kostenloses Abonnement erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F))
- [Python 2.7+ oder 3.5.3+](/azure/developer/python/configure-local-development-environment)
- [Azure-Befehlszeilenschnittstelle](/cli/azure/install-azure-cli)

In diesem Schnellstart wird davon ausgegangen, dass Sie die [Azure CLI](/cli/azure/install-azure-cli) in einem Linux-Terminalfenster ausführen.

## <a name="set-up-your-local-environment"></a>Einrichten Ihrer lokalen Umgebung

In dieser Schnellstartanleitung wird die Azure Identity-Bibliothek mit der Azure CLI verwendet, um den Benutzer bei Azure-Diensten zu authentifizieren. Entwickler können auch Visual Studio oder Visual Studio Code verwenden, um ihre Aufrufe zu authentifizieren. Weitere Informationen finden Sie unter [Authentifizieren des Clients mit der Azure Identity-Clientbibliothek](/java/api/overview/azure/identity-readme).

### <a name="sign-in-to-azure"></a>Anmelden bei Azure

1. Führen Sie den Befehl `login` aus.

    ```azurecli-interactive
    az login
    ```

    Die CLI öffnet Ihren Standardbrowser, sofern sie dazu in der Lage ist, und lädt eine Azure-Anmeldeseite.

    Öffnen Sie andernfalls die Browserseite [https://aka.ms/devicelogin](https://aka.ms/devicelogin), und geben Sie den in Ihrem Terminal angezeigten Autorisierungscode ein.

2. Melden Sie sich im Browser mit Ihren Anmeldeinformationen an.

### <a name="install-the-packages"></a>Installieren der Pakete

1. Erstellen Sie in einem Terminal oder an einer Eingabeaufforderung einen geeigneten Projektordner, und erstellen und aktivieren Sie dann eine virtuelle Python-Umgebung, wie unter [Verwenden von virtuellen Python-Umgebungen](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments) beschrieben.

1. Installieren der Azure Active Directory-Identitätsbibliothek:

    ```terminal
    pip install azure.identity
    ```


1. Installieren Sie die Key Vault-Zertifikatclientbibliothek:

    ```terminal
    pip install azure-keyvault-certificates
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Erstellen einer Ressourcengruppe und eines Schlüsseltresors

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Gewähren des Zugriffs auf Ihren Schlüsseltresor

Erstellen einer Zugriffsrichtlinie für Ihren Schlüsseltresor, mit der Ihrem Benutzerkonto die Zertifikatberechtigung erteilt wird

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --certificate-permissions delete get list create
```

#### <a name="set-environment-variables"></a>Festlegen von Umgebungsvariablen

Diese Anwendung verwendet den Namen des Schlüsseltresors als Umgebungsvariable namens `KEY_VAULT_NAME`.

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME=<your-key-vault-name>
```

macOS oder Linux
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>Erstellen des Beispielcodes

Mit der Azure Key Vault-Zertifikatclientbibliothek für Python können Sie Zertifikate verwalten. Im folgenden Codebeispiel wird veranschaulicht, wie Sie einen Client erstellen und ein Zertifikat festlegen, abrufen und löschen.

Erstellen Sie eine Datei mit dem Namen *kv_certificates.py*, die diesen Code enthält.

```python
import os
from azure.keyvault.certificates import CertificateClient, CertificatePolicy,CertificateContentType, WellKnownIssuerNames 
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)

certificateName = input("Input a name for your certificate > ")

print(f"Creating a certificate in {keyVaultName} called '{certificateName}' ...")

policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()

print(" done.")

print(f"Retrieving your certificate from {keyVaultName}.")

retrieved_certificate = client.get_certificate(certificateName)

print(f"Certificate with name '{retrieved_certificate.name}' was found'.")
print(f"Deleting your certificate from {keyVaultName} ...")

poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>Ausführen des Codes

Stellen Sie sicher, dass sich der Code aus dem vorherigen Abschnitt in einer Datei namens *kv_certificates.py* befindet. Führen Sie den Code dann mithilfe des folgenden Befehls aus:

```terminal
python kv_certificates.py
```

- Wenn Berechtigungsfehler auftreten, stellen Sie sicher, dass Sie den [`az keyvault set-policy`-Befehl](#grant-access-to-your-key-vault) ausgeführt haben.
- Wenn Sie den Code mit dem gleichen Schlüsselnamen erneut ausführen, wird möglicherweise der Fehler „(Konflikt) Zertifikat <name> befindet sich derzeit in einem gelöschten, aber wiederherstellbaren Zustand“ angezeigt. Verwenden Sie einen anderen Schlüsselnamen.

## <a name="code-details"></a>Codedetails

### <a name="authenticate-and-create-a-client"></a>Authentifizieren und Erstellen eines Clients

In dieser Schnellstartanleitung wird der angemeldete Benutzer zum Authentifizieren beim Schlüsseltresor verwendet. Dies ist die bevorzugte Methode für die lokale Entwicklung. Bei Anwendungen, die in Azure bereitgestellt werden, sollte die verwaltete Identität App Service oder einem virtuellen Computer zugewiesen werden. Weitere Informationen finden Sie in der [Übersicht zu verwalteten Identitäten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Im folgenden Beispiel wird der Name Ihres Schlüsseltresors in den Schlüsseltresor-URI mit dem Format „https://\<your-key-vault-name\>.vault.azure.net“ erweitert. In diesem Beispiel wird die Klasse [DefaultAzureCredential()](/python/api/azure-identity/azure.identity.defaultazurecredential) verwendet, mit der derselbe Code in verschiedenen Umgebungen mit verschiedenen Optionen zum Bereitstellen von Identitäten verwendet werden kann. Weitere Informationen finden Sie unter der [DefaultAzureCredential-Authentifizierung](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 

```python
credential = DefaultAzureCredential()
client = CertificateClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-certificate"></a>Speichern eines Zertifikats

Nachdem Sie das Clientobjekt für den Schlüsseltresor abgerufen haben, können Sie mithilfe der [begin_create_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-create-certificate-certificate-name--policy----kwargs-)-Methode ein Zertifikat erstellen: 

```python
policy = CertificatePolicy.get_default()
poller = client.begin_create_certificate(certificate_name=certificateName, policy=policy)
certificate = poller.result()
```

Hier ist für das Zertifikat eine Richtlinie erforderlich, die mit der [CertificatePolicy.get_default](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificatepolicy?#get-default--)-Methode abgerufen wird.

Beim Aufrufen einer `begin_create_certificate`-Methode wird ein asynchroner Aufruf der Azure-REST-API für den Schlüsseltresor generiert. Der asynchrone Aufruf gibt ein Pollerobjekt zurück. Um auf das Ergebnis des Vorgangs zu warten, rufen Sie die `result`-Methode des Pollers auf.

Bei der Verarbeitung der Anforderung authentifiziert Azure die Identität des Aufrufers (Dienstprinzipal) mithilfe des Anmeldeinformationenobjekts, das Sie für den Client bereitgestellt haben.


### <a name="retrieve-a-certificate"></a>Abrufen eines Zertifikats

Um ein Zertifikat aus Key Vault zu lesen, verwenden Sie die [get_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#get-certificate-certificate-name----kwargs-)-Methode:

```python
retrieved_certificate = client.get_certificate(certificateName)
 ```

Mithilfe des Azure CLI-Befehls [az keyvault certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show) können Sie sich vergewissern, dass das Zertifikat festgelegt wurde.

### <a name="delete-a-certificate"></a>Löschen eines Zertifikats

Um ein Zertifikat zu löschen, verwenden Sie die [begin_delete_certificate](/python/api/azure-keyvault-certificates/azure.keyvault.certificates.certificateclient?#begin-delete-certificate-certificate-name----kwargs-)-Methode:

```python
poller = client.begin_delete_certificate(certificateName)
deleted_certificate = poller.result()
```

Die `begin_delete_certificate`-Methode ist asynchron und gibt ein Pollerobjekt zurück. Wenn die `result`-Methode des Pollers aufgerufen wird, wird auf ihren Abschluss gewartet.

Mithilfe des Azure CLI-Befehls [az keyvault certificate show](/cli/azure/keyvault/certificate?#az-keyvault-certificate-show) können Sie sich vergewissern, dass das Zertifikat gelöscht wurde.

Nach dem Löschen verbleibt ein Zertifikat für einen bestimmten Zeitraum in einem gelöschten, aber wiederherstellbaren Zustand. Wenn Sie den Code erneut ausführen, verwenden Sie einen anderen Zertifikatnamen.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie auch mit [Geheimnissen](../secrets/quick-create-python.md) und [Schlüssel](../keys/quick-create-python.md) experimentieren möchten, können Sie den in diesem Artikel erstellten Key Vault wiederverwenden.

Verwenden Sie andernfalls den folgenden Befehl, um die Ressourcengruppe und alle darin enthaltenen Ressourcen zu löschen, wenn Sie mit die in diesem Artikel erstellten Ressourcen nicht mehr benötigen:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über den Azure-Schlüsseltresor](../general/overview.md)
- [Sicherer Zugriff auf einen Schlüsseltresor](../general/secure-your-key-vault.md)
- [Entwicklerhandbuch zu Azure Key Vault](../general/developers-guide.md)
- [Bewährte Methoden zum Verwenden von Key Vault](../general/best-practices.md)
- [Authentifizieren mit Key Vault](../general/authentication.md)