---
title: Tutorial zur Rotation von Ressourcen mit zwei Sätzen mit Anmeldeinformationen
description: In diesem Tutorial erfahren Sie, wie Sie die Rotation eines Geheimnisses für Ressourcen automatisieren, bei denen zwei Sätze mit Anmeldeinformationen für die Authentifizierung verwendet werden.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: rotation
ms.service: key-vault
ms.subservice: secrets
ms.topic: tutorial
ms.date: 06/22/2020
ms.author: jalichwa
ms.openlocfilehash: 72541b8d8f8d8865c680c36f7f84cd91a4ce8ba2
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96903325"
---
# <a name="automate-the-rotation-of-a-secret-for-resources-that-have-two-sets-of-authentication-credentials"></a>Automatisieren der Geheimnisrotation für Ressourcen, die über zwei Sätze mit Anmeldeinformationen für die Authentifizierung verfügen

Die beste Möglichkeit zur Authentifizierung bei Azure-Diensten ist die Verwendung einer [verwalteten Identität](../general/authentication.md). Es gibt jedoch einige Szenarien, in denen dies nicht möglich ist. In diesen Fällen werden Zugriffsschlüssel oder Kennwörter verwendet. Sie sollten Zugriffsschlüssel und Kennwörter häufig rotieren.

In diesem Tutorial wird gezeigt, wie Sie die regelmäßige Rotation von Geheimnissen für Datenbanken und Dienste automatisieren, bei denen zwei Sätze mit Anmeldeinformationen für die Authentifizierung verwendet werden. In diesem Tutorial wird veranschaulicht, wie Sie Azure Storage-Kontoschlüssel rotieren, die in Azure Key Vault als Geheimnisse gespeichert sind. Sie verwenden eine Funktion, die über eine Azure Event Grid-Benachrichtigung ausgelöst wird. 

> [!NOTE]
> Speicherkontoschlüssel können in Key Vault automatisch verwaltet werden, wenn Sie Shared Access Signature-Token für den delegierten Zugriff auf das Speicherkonto bereitstellen. Es gibt Dienste, für die Speicherkonto-Verbindungsschlüssel mit Zugriffsschlüsseln erforderlich sind. Für dieses Szenario empfehlen wir diese Lösung.

Hier ist die Rotationslösung dargestellt, die in diesem Tutorial beschrieben wird: 

![Diagramm: Rotationslösung](../media/secrets/rotation-dual/rotation-diagram.png)

Bei dieser Lösung werden von Azure Key Vault individuelle Speicherkonto-Zugriffsschlüssel als Versionen desselben Geheimnisses gespeichert, und für nachfolgende Versionen wird zwischen dem Primär- und dem Sekundärschlüssel gewechselt. Wenn ein Zugriffsschlüssel in der aktuellen Version des Geheimnisses gespeichert wird, wird der Alternativschlüssel erneut generiert und für Key Vault als die neue und aktuelle Version des Geheimnisses hinzugefügt. Die Lösung ermöglicht den gesamten Rotationszyklus für die Anwendung, um die Aktualisierung auf den aktuellsten erneut generierten Schlüssel durchzuführen. 

1. 30 Tage vor dem Ablaufdatum eines Geheimnisses veröffentlicht Key Vault das Ereignis für den baldigen Ablauf in Event Grid.
1. Event Grid überprüft die Ereignisabonnements und ruft per HTTP POST den Funktions-App-Endpunkt auf, der dieses Ereignis abonniert hat.
1. Die Funktions-App identifiziert den Alternativschlüssel (nicht den aktuellen) und ruft das Speicherkonto für die erneute Generierung auf.
1. Die Funktions-App fügt den erneut generierten Schlüssel Azure Key Vault als neue Version des Geheimnisses hinzu.

## <a name="prerequisites"></a>Voraussetzungen
* Ein Azure-Abonnement. [Erstellen Sie ein kostenloses Abonnement.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* Azure Key Vault:
* Zwei Azure-Speicherkonten.

Sie können den folgenden Bereitstellungslink verwenden, falls Sie nicht über einen vorhandenen Schlüsseltresor und vorhandene Speicherkonten verfügen:

[![Link für die Bereitstellung in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FInitial-Setup%2Fazuredeploy.json)

1. Wählen Sie unter **Ressourcengruppe** die Option **Neu erstellen** aus. Geben Sie der Gruppe den Namen **akvrotation**, und wählen Sie dann **OK** aus.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

    ![Screenshot: Erstellung einer Ressourcengruppe](../media/secrets/rotation-dual/dual-rotation-1.png)

Sie verfügen jetzt über einen Schlüsseltresor und zwei Speicherkonten. Sie können dieses Setup in der Azure-Befehlszeilenschnittstelle überprüfen, indem Sie diesen Befehl ausführen:

```azurecli
az resource list -o table -g akvrotation
```

Das Ergebnis sieht in etwa wie diese Ausgabe aus:

```console
Name                     ResourceGroup         Location    Type                               Status
-----------------------  --------------------  ----------  ---------------------------------  --------
akvrotation-kv         akvrotation      eastus      Microsoft.KeyVault/vaults
akvrotationstorage     akvrotation      eastus      Microsoft.Storage/storageAccounts
akvrotationstorage2    akvrotation      eastus      Microsoft.Storage/storageAccounts
```

## <a name="create-and-deploy-the-key-rotation-function"></a>Erstellen und Bereitstellen der Funktion für die Schlüsselrotation

Als Nächstes erstellen Sie zusätzlich zu anderen erforderlichen Komponenten eine Funktions-App mit einer systemseitig verwalteten Identität. Außerdem stellen Sie die Rotationsfunktion für die Speicherkontoschlüssel bereit.

Für die Rotationsfunktion der Funktions-App werden die folgenden Komponenten und Konfigurationseinstellungen benötigt:
- Einen Azure App Service-Plan
- Ein Speicherkonto zum Verwalten der Trigger der Funktions-App
- Eine Zugriffsrichtlinie zum Zugreifen auf Geheimnisse in Key Vault
- Eine Zuweisung der Dienstrolle „Speicherkonto-Schlüsseloperator“ zur Funktions-App, um den Zugriff auf die Zugriffsschlüssel des Speicherkontos zu ermöglichen
- Eine Funktion für die Schlüsselrotation mit einem Ereignisauslöser und einem HTTP-Trigger (bedarfsgesteuerte Rotation)
- Ein Event Grid-Ereignisabonnement für das Ereignis **SecretNearExpiry**

1. Wählen Sie den Link zur Bereitstellung der Vorlage in Azure aus: 

   [![Link zur Bereitstellung der Vorlage in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FFunction%2Fazuredeploy.json)

1. Wählen Sie in der Liste **Ressourcengruppe** die Option **akvrotation** aus.
1. Geben Sie im Feld **Storage Account RG** (RG des Speicherkontos) den Namen der Ressourcengruppe ein, in der Ihr Speicherkonto enthalten ist. Übernehmen Sie den Standardwert **[resourceGroup().name]** , falls sich Ihr Speicherkonto bereits in der Ressourcengruppe befindet, in der Sie die Funktion für die Schlüsselrotation bereitstellen möchten.
1. Geben Sie im Feld **Name des Speicherkontos** den Namen des Speicherkontos ein, das die zu rotierenden Zugriffsschlüssel enthält.
1. Geben Sie im Feld **Key Vault RG** (RG des Schlüsseltresors) den Namen der Ressourcengruppe ein, in der sich Ihr Schlüsseltresor befindet. Übernehmen Sie den Standardwert **[resourceGroup().name]** , falls sich Ihr Schlüsseltresor bereits in der Ressourcengruppe befindet, in der Sie die Funktion für die Schlüsselrotation bereitstellen möchten.
1. Geben Sie im Feld **Name des Schlüsseltresors** den Namen des Schlüsseltresors ein.
1. Geben Sie im Feld **Name der Funktions-App** den Namen der Funktions-App ein.
1. Geben Sie im Feld **Name des Geheimnisses** den Namen des Geheimnisses ein, unter dem Sie die Zugriffsschlüssel speichern möchten.
1. Geben Sie im Feld **Repository-URL** den GitHub-Speicherort des Funktionscodes ein: **https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell.git** .
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

   ![Screenshot: Erstellen des ersten Speicherkontos](../media/secrets/rotation-dual/dual-rotation-2.png)

Nachdem Sie die obigen Schritte ausgeführt haben, verfügen Sie über ein Speicherkonto, eine Serverfarm, eine Funktions-App und Application Insights. Nach Abschluss der Bereitstellung wird die folgende Seite angezeigt: ![Screenshot: Seite „Ihre Bereitstellung wurde abgeschlossen“](../media/secrets/rotation-dual/dual-rotation-3.png)
> [!NOTE]
> Wenn ein Fehler auftritt, können Sie die Option **Erneut bereitstellen** auswählen, um die Bereitstellung der Komponenten abzuschließen.


Bereitstellungsvorlagen und Code für die Rotationsfunktion finden Sie auf [GitHub](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell).

## <a name="add-the-storage-account-access-keys-to-key-vault"></a>Hinzufügen der Speicherkonto-Zugriffsschlüssel zu Key Vault

Legen Sie zuerst Ihre Zugriffsrichtlinie so fest, dass für Benutzer Berechtigungen zum **Verwalten von Geheimnissen** gewährt werden:

```azurecli
az keyvault set-policy --upn <email-address-of-user> --name akvrotation-kv --secret-permissions set delete get list
```

Sie können jetzt ein neues Geheimnis mit einem Speicherkonto-Zugriffsschlüssel als Wert erstellen. Darüber hinaus benötigen Sie die Ressourcen-ID des Speicherkontos, die Gültigkeitsdauer des Geheimnisses und die Schlüssel-ID zum Hinzufügen zum Geheimnis, damit der Schlüssel von der Rotationsfunktion im Speicherkonto erneut generiert werden kann.

Ermitteln Sie die Ressourcen-ID des Speicherkontos. Sie finden diesen Wert in der `id`-Eigenschaft.
```azurecli
az storage account show -n akvrotationstorage
```

Listen Sie die Speicherkonto-Zugriffsschlüssel auf, damit Sie die Schlüsselwerte abrufen können:

```azurecli
az storage account keys list -n akvrotationstorage 
```

Führen Sie diesen Befehl aus, indem Sie Ihre abgerufenen Werte für `key1Value` und `storageAccountResourceId` verwenden:

```azurecli
$tomorrowDate = (get-date).AddDays(+1).ToString("yyy-MM-ddThh:mm:ssZ")
az keyvault secret set --name storageKey --vault-name akvrotation-kv --value <key1Value> --tags "CredentialId=key1" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Wenn Sie ein Geheimnis mit einem Ablaufdatum in naher Zukunft erstellen, wird innerhalb weniger Minuten ein `SecretNearExpiry`-Ereignis veröffentlicht. Von diesem Ereignis wird wiederum die Funktion zum Rotieren des Geheimnisses ausgelöst.

Sie können überprüfen, ob Zugriffsschlüssel erneut generiert wurden, indem Sie den Speicherkontoschlüssel und das Key Vault-Geheimnis abrufen und vergleichen.

Verwenden Sie diesen Befehl, um die Informationen zum Geheimnis abzurufen:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey
```
Beachten Sie, dass `CredentialId` auf einen anderen `keyName` aktualisiert und `value` erneut generiert wird. ![Screenshot: Ausgabe des Befehls „az keyvault secret show“ für das erste Speicherkonto](../media/secrets/rotation-dual/dual-rotation-4.png)

Rufen Sie die Zugriffsschlüssel ab, um die Werte zu vergleichen:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Screenshot: Ausgabe des Befehls „az storage account keys list“ für das erste Speicherkonto](../media/secrets/rotation-dual/dual-rotation-5.png)

## <a name="add-storage-accounts-for-rotation"></a>Hinzufügen von Speicherkonten für die Rotation

Sie können dieselbe Funktions-App wiederverwenden, um Schlüssel für mehrere Speicherkonten zu rotieren. 

Zum Hinzufügen von Speicherkontoschlüsseln zu einer vorhandenen Funktion für die Rotation benötigen Sie Folgendes:
- Eine Zuweisung der Dienstrolle „Speicherkonto-Schlüsseloperator“ zur Funktions-App, um den Zugriff auf die Zugriffsschlüssel des Speicherkontos zu ermöglichen
- Ein Event Grid-Ereignisabonnement für das Ereignis **SecretNearExpiry**

1. Wählen Sie den Link zur Bereitstellung der Vorlage in Azure aus: 

   [![Link zur Bereitstellung der Vorlage in Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2Fjlichwa%2FKeyVault-Rotation-StorageAccountKey-PowerShell%2Fmaster%2Farm-templates%2FAdd-Event-Subscriptions%2Fazuredeploy.json)

1. Wählen Sie in der Liste **Ressourcengruppe** die Option **akvrotation** aus.
1. Geben Sie im Feld **Name des Speicherkontos** den Namen des Speicherkontos ein, das die zu rotierenden Zugriffsschlüssel enthält.
1. Geben Sie im Feld **Name des Schlüsseltresors** den Namen des Schlüsseltresors ein.
1. Geben Sie im Feld **Name der Funktions-App** den Namen der Funktions-App ein.
1. Geben Sie im Feld **Name des Geheimnisses** den Namen des Geheimnisses ein, unter dem Sie die Zugriffsschlüssel speichern möchten.
1. Klicken Sie auf **Überprüfen + erstellen**.
1. Klicken Sie auf **Erstellen**.

   ![Screenshot: Erstellen eines weiteren Speicherkontos](../media/secrets/rotation-dual/dual-rotation-7.png)

### <a name="add-another-storage-account-access-key-to-key-vault"></a>Hinzufügen eines weiteren Speicherkonto-Zugriffsschlüssels zu Key Vault

Ermitteln Sie die Ressourcen-ID des Speicherkontos. Sie finden diesen Wert in der `id`-Eigenschaft.
```azurecli
az storage account show -n akvrotationstorage2
```

Listen Sie die Speicherkonto-Zugriffsschlüssel auf, damit Sie den Wert für „key2“ abrufen können:

```azurecli
az storage account keys list -n akvrotationstorage2 
```

Führen Sie diesen Befehl aus, indem Sie Ihre abgerufenen Werte für `key2Value` und `storageAccountResourceId` verwenden:

```azurecli
tomorrowDate=`date -d tomorrow -Iseconds -u | awk -F'+' '{print $1"Z"}'`
az keyvault secret set --name storageKey2 --vault-name akvrotation-kv --value <key2Value> --tags "CredentialId=key2" "ProviderAddress=<storageAccountResourceId>" "ValidityPeriodDays=60" --expires $tomorrowDate
```

Verwenden Sie diesen Befehl, um die Informationen zum Geheimnis abzurufen:
```azurecli
az keyvault secret show --vault-name akvrotation-kv --name storageKey2
```
Beachten Sie, dass `CredentialId` auf einen anderen `keyName` aktualisiert und `value` erneut generiert wird. ![Screenshot: Ausgabe des Befehls „az keyvault secret show“ für das zweite Speicherkonto](../media/secrets/rotation-dual/dual-rotation-8.png)

Rufen Sie die Zugriffsschlüssel ab, um die Werte zu vergleichen:
```azurecli
az storage account keys list -n akvrotationstorage 
```
![Screenshot: Ausgabe des Befehls „az storage account keys list“ für das zweite Speicherkonto](../media/secrets/rotation-dual/dual-rotation-9.png)

## <a name="key-vault-dual-credential-rotation-functions"></a>Key Vault-Funktionen für die Rotation von dualen Anmeldeinformationen

- [Speicherkonto](https://github.com/jlichwa/KeyVault-Rotation-StorageAccountKey-PowerShell)
- [Redis Cache](https://github.com/jlichwa/KeyVault-Rotation-RedisCacheKey-PowerShell)

## <a name="next-steps"></a>Nächste Schritte
- Übersicht: [Überwachen von Key Vault mit Azure Event Grid](../general/event-grid-overview.md)
- Gewusst wie: [Erstellen Ihrer ersten Funktion im Azure-Portal](../../azure-functions/functions-create-first-azure-function.md)
- Vorgehensweise: [Verwenden von Logic Apps zum Empfangen einer E-Mail bei Statusänderungen von Key Vault-Geheimnissen](../general/event-grid-logicapps.md)
- Referenz: [Azure Event Grid-Ereignisschema für Azure Key Vault](../../event-grid/event-schema-key-vault.md)
