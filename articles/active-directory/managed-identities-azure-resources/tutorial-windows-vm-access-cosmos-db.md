---
title: 'Tutorial: Verwenden einer verwalteten Identität für den Zugriff auf Azure Cosmos DB – Windows – Azure AD'
description: In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität für einen virtuellen Windows-Computer verwenden, um auf Azure Cosmos DB zuzugreifen.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/29/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cd1fb7f33c56aefe76bc55181ae92ca3d149754
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96006961"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Tutorial: Verwenden der systemseitig zugewiesenen verwalteten Identität eines virtuellen Windows-Computers für den Zugriff auf Azure Cosmos DB

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

In diesem Tutorial erfahren Sie, wie Sie eine systemseitig zugewiesene verwaltete Identität für einen virtuellen Windows-Computer verwenden, um auf Cosmos DB zuzugreifen. Folgendes wird vermittelt:

> [!div class="checklist"]
> * Erstellen eines Cosmos DB-Kontos
> * Gewähren des Zugriffs auf die Zugriffsschlüssel des Cosmos DB-Kontos für die systemseitig zugewiesene verwaltete Identität eines virtuellen Windows-Computers
> * Abrufen eines Zugriffstokens mithilfe der systemseitig zugewiesenen verwalteten Identität des virtuellen Windows-Computers zum Aufrufen von Azure Resource Manager
> * Abrufen von Zugriffsschlüsseln aus Azure Resource Manager für Cosmos DB-Aufrufe

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- Installieren Sie die neueste Version von [Azure PowerShell](/powershell/azure/install-az-ps).


## <a name="enable"></a>Aktivieren

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Gewähren von Zugriff


### <a name="create-a-cosmos-db-account"></a>Erstellen eines Cosmos DB-Kontos 

Erstellen Sie ein Cosmos DB-Konto, wenn Sie noch keines besitzen. Sie können diesen Schritt überspringen und ein vorhandenes Cosmos DB-Konto verwenden. 

1. Klicken Sie in der linken oberen Ecke des Azure-Portals auf die Schaltfläche **+/Neuen Dienst erstellen**.
2. Klicken Sie auf **Datenbanken** und dann auf **Azure Cosmos DB**. Es wird ein Bereich „Neues Konto“ angezeigt.
3. Geben Sie eine **ID** für das Cosmos DB-Konto ein, das Sie später verwenden.  
4. **API** sollte auf „SQL“ festgelegt werden. Der in diesem Tutorial beschriebene Ansatz kann auch mit den anderen verfügbaren API-Typen verwendet werden, die Schritte in diesem Tutorial gelten jedoch für die SQL-API.
5. Stellen Sie sicher, dass **Abonnement** und **Ressourcengruppe** dem entsprechen, was Sie bei der Erstellung Ihrer VM im vorherigen Schritt angegeben haben.  Wählen Sie einen **Standort** aus, an dem Cosmos DB verfügbar ist.
6. Klicken Sie auf **Erstellen**.

### <a name="create-a-collection"></a>Erstellen einer Sammlung 

Als Nächstes fügen Sie im Cosmos DB-Konto eine Datensammlung hinzu, die Sie in späteren Schritten abfragen können.

1. Navigieren Sie zu Ihrem neu erstellten Cosmos DB-Konto.
2. Klicken Sie auf der Registerkarte **Übersicht** auf die Schaltfläche **+ Sammlung hinzufügen**. Daraufhin wird der Bereich „Sammlung hinzufügen“ eingeblendet.
3. Weisen Sie der Sammlung eine Datenbank-ID und eine Sammlungs-ID zu, wählen Sie eine Speicherkapazität aus, geben Sie einen Partitionsschlüssel und einen Durchsatzwert ein, und klicken Sie dann auf **OK**.  Für dieses Tutorial ist es ausreichend, als Datenbank-ID und Sammlungs-ID „Test“ zu verwenden und eine feste Speicherkapazität sowie den niedrigsten Durchsatz (400 RU/s) auszuwählen.  


### <a name="grant-access-to-the-cosmos-db-account-access-keys"></a>Gewähren von Zugriff auf die Zugriffsschlüssel des Cosmos DB-Kontos

In diesem Abschnitt wird gezeigt, wie Sie der systemseitig zugewiesenen verwalteten Identität des virtuellen Windows-Computers Zugriff auf die Zugriffsschlüssel des Cosmos DB-Kontos gewähren. Cosmos DB unterstützt die Azure AD-Authentifizierung nicht nativ. Sie können eine systemseitig zugewiesene verwaltete Identität aber zum Abrufen eines Cosmos DB-Zugriffsschlüssels aus Resource Manager verwenden und mithilfe des Schlüssels auf Cosmos DB zugreifen. In diesem Schritt gewähren Sie der systemseitig zugewiesenen verwalteten Identität Ihres virtuellen Windows-Computers Zugriff auf die Schlüssel des Cosmos DB-Kontos.

Um der systemseitig zugewiesenen verwalteten Identität des virtuellen Windows-Computers über PowerShell Zugriff auf das Cosmos DB-Konto in Azure Resource Manager zu gewähren, aktualisieren Sie die Werte für `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` und `<COSMOS DB ACCOUNT NAME>` für Ihre Umgebung. Cosmos DB unterstützt beim Verwenden von Zugriffsschlüsseln zwei Granularitätsebenen: Lese-/Schreibzugriff auf das Konto und schreibgeschützter Zugriff auf das Konto.  Weisen Sie die Rolle `DocumentDB Account Contributor` zu, wenn Sie die Schlüssel für Lese-/Schreibzugriff für das Konto abrufen möchten, oder weisen Sie die Rolle `Cosmos DB Account Reader Role` zu, wenn Sie die Schlüssel für schreibgeschützten Zugriff auf das Konto abrufen möchten.  Weisen Sie für dieses Tutorial `Cosmos DB Account Reader Role` zu:

```azurepowershell
$spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Cosmos DB Account Reader Role" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>"
```

>[!NOTE]
> Beachten Sie, dass Sie möglicherweise nicht über die richtigen Berechtigungen verfügen, wenn Sie keinen Vorgang ausführen können. Wenn Sie Schreibzugriff auf Schlüssel haben möchten, müssen Sie eine Azure-Rolle (z. B. Mitwirkender des DocumentDB-Kontos) verwenden oder eine benutzerdefinierte Rolle erstellen. Weitere Informationen finden Sie unter [Rollenbasierte Azure-Zugriffssteuerung in Azure Cosmos DB](../../cosmos-db/role-based-access-control.md).

## <a name="access-data"></a>Zugreifen auf Daten

In diesem Abschnitt wird gezeigt, wie Sie mithilfe eines Zugriffstokens für die systemseitig zugewiesene verwaltete Identität des virtuellen Windows-Computers Azure Resource Manager aufrufen. Für den Rest des Tutorials arbeiten wir von dem virtuellen Computer aus, den wir zuvor erstellt haben. 

Sie müssen die aktuelle Version der [Azure CLI](/cli/azure/install-azure-cli) auf Ihrem virtuellen Windows-Computer installieren.



### <a name="get-an-access-token"></a>Abrufen eines Zugriffstokens

1. Navigieren Sie im Azure-Portal zu **Virtuelle Computer**, wechseln Sie zu Ihrem virtuellen Windows-Computer, und klicken Sie dann oben auf der Seite **Übersicht** auf **Verbinden**. 
2. Geben Sie Ihren **Benutzernamen** und Ihr **Kennwort** ein, das Sie beim Erstellen des virtuellen Windows-Computers hinzugefügt haben. 
3. Sie haben nun eine **Remotedesktopverbindung** mit dem virtuellen Computer erstellt. Öffnen Sie jetzt PowerShell in der Remotesitzung.
4. Erstellen Sie mithilfe des PowerShell-Befehls „Invoke-WebRequest“ eine Anforderung an den lokalen Endpunkt für verwaltete Identitäten für Azure-Ressourcen, um ein Zugriffstoken für Azure Resource Manager zu erhalten.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
   ```

   > [!NOTE]
   > Der Wert des „resource“-Parameters muss exakt mit dem von Azure AD erwarteten Wert übereinstimmen. Wenn Sie die Azure Resource Manager-Ressourcen-ID verwenden, müssen Sie den nachgestellten Schrägstrich im URI verwenden.
    
   Extrahieren Sie im nächsten Schritt das Element „Content“, das als JSON-formatierte (JavaScript Object Notation) Zeichenfolge im $response-Objekt gespeichert ist. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```
   Extrahieren Sie dann das Zugriffstoken aus der Antwort.
    
   ```powershell
   $ArmToken = $content.access_token
   ```

### <a name="get-access-keys"></a>Abrufen von Zugriffsschlüsseln 

In diesem Abschnitt wird gezeigt, wie Sie Zugriffsschlüssel aus Azure Resource Manager für Cosmos DB-Aufrufe abrufen. Nun rufen Sie mit PowerShell und dem Zugriffstoken, das im vorherigen Abschnitt abgerufen wurde, Resource Manager auf, um den Zugriffsschlüssel für Cosmos DB abzurufen. Nachdem wir den Zugriffsschlüssel haben, können wir Cosmos DB abfragen. Ersetzen Sie die Parameter `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, und `<COSMOS DB ACCOUNT NAME>` durch Ihre eigenen Werte. Ersetzen Sie den Wert `<ACCESS TOKEN>` durch das Zugriffstoken, das Sie zuvor abgerufen haben.  Wenn Sie Schlüssel für den Lese-/Schreibzugriff abrufen möchten, verwenden Sie den Vorgangstyp `listKeys`.  Wenn Sie Schlüssel für den schreibgeschützten Zugriff abrufen möchten, verwenden Sie den Vorgangstyp `readonlykeys`:

```powershell
Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-03-31' -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
In der Antwort wird die Liste der Schlüssel zurückgegeben.  Angenommen, Sie erhalten Schlüssel für den schreibgeschützten Zugriff:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Nachdem Sie nun über den Zugriffsschlüssel für das Cosmos DB-Konto verfügen, können Sie ihn an ein Cosmos DB SDK übergeben und Aufrufe für den Zugriff auf das Konto durchführen.  Für ein kurzes Beispiel können Sie den Zugriffsschlüssel an die Azure-Befehlszeilenschnittstelle übergeben.  Sie erhalten die `<COSMOS DB CONNECTION URL>` von der Registerkarte **Übersicht** auf dem Blatt des Cosmos DB-Kontos im Azure-Portal.  Ersetzen Sie `<ACCESS KEY>` durch den Wert, den Sie oben abgerufen haben:

```azurecli
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Mit diesem CLI-Befehl werden Details zur Sammlung zurückgegeben:

```output
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```


## <a name="disable"></a>Disable

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie Sie eine systemseitig zugewiesene Identität eines virtuellen Windows-Computers für den Zugriff auf Cosmos DB verwenden.  Weitere Informationen zu Cosmos DB finden Sie hier:

> [!div class="nextstepaction"]
>[Willkommen bei Azure Cosmos DB](../../cosmos-db/introduction.md)