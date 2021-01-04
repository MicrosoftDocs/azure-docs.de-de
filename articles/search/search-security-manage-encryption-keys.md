---
title: Verschlüsselung ruhender Daten mit kundenseitig verwalteten Schlüsseln
titleSuffix: Azure Cognitive Search
description: Ergänzung der serverseitigen Verschlüsselung über Indizes und Synonymzuordnungen in Azure Cognitive Search durch Schlüssel, die Sie in Azure Key Vault erstellen und verwalten.
manager: nitinme
author: NatiNimni
ms.author: natinimn
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2020
ms.custom: references_regions
ms.openlocfilehash: 8295e619cfda0d4b83a7356d5fd21d4b80f83849
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96530883"
---
# <a name="configure-customer-managed-keys-for-data-encryption-in-azure-cognitive-search"></a>Konfigurieren von kundenseitig verwalteten Schlüsseln für die Datenverschlüsselung in Azure Cognitive Search

In Azure Cognitive Search werden ruhende indizierte Inhalte automatisch mit [dienstseitig verwalteten Schlüsseln](../security/fundamentals/encryption-atrest.md#azure-encryption-at-rest-components) verschlüsselt. Wenn ein weiterer Schutz erforderlich ist, können Sie die Standardverschlüsselung durch eine zusätzliche Verschlüsselungsebene ergänzen, indem Sie Schlüssel verwenden, die Sie in Azure Key Vault erstellen und verwalten. In diesem Artikel finden Sie die Schritte zum Einrichten der Verschlüsselung mit kundenseitig verwalteten Schlüsseln.

Die Verschlüsselung mit kundenseitig verwalteten Schlüsseln erfordert [Azure Key Vault](../key-vault/general/overview.md). Sie können Ihre eigenen Verschlüsselungsschlüssel erstellen und in einem Schlüsseltresor speichern oder mit Azure Key Vault-APIs Verschlüsselungsschlüssel generieren. Mit Azure Key Vault können Sie auch die Schlüsselverwendung überwachen, wenn Sie die [Protokollierung aktivieren](../key-vault/general/logging.md).  

Die Verschlüsselung mit kundenseitig verwalteten Schlüsseln wird auf der Ebene einzelner Indizes oder Synonymzuordnungen angewandt, wenn diese Objekte erstellt werden, und nicht auf der Ebene des Suchdiensts selbst angegeben. Nur neue Objekte können verschlüsselt werden. Bereits vorhandene Inhalte können nicht verschlüsselt werden.

Die Schlüssel müssen sich nicht alle im selben Schlüsseltresor befinden. Ein einzelner Suchdienst kann mehrere verschlüsselte Indizes oder Synonymzuordnungen hosten, die jeweils mit ihren eigenen kundenseitig verwalteten Verschlüsselungsschlüsseln verschlüsselt werden, die in verschiedenen Schlüsseltresoren gespeichert sind. Im gleichen Dienst können auch Indizes und Synonymzuordnungen enthalten sein, die nicht mit kundenseitig verwalteten Schlüsseln verschlüsselt wurden.

>[!Important]
> Wenn Sie kundenseitig verwaltete Schlüssel implementieren, achten Sie darauf, dass Sie bei der Routinerotation von Key Vault-Schlüsseln und Active Directory-Anwendungsgeheimnissen und der Registrierung strikte Verfahren befolgen. Aktualisieren Sie immer den gesamten verschlüsselten Inhalt, um neue Geheimnisse und Schlüssel zu verwenden, bevor Sie die alten löschen. Wenn Sie diesen Schritt nicht ausführen, können Ihre Inhalte nicht entschlüsselt werden.

## <a name="double-encryption"></a>Doppelte Verschlüsselung

Für Dienste, die nach dem 1. August 2020 und in bestimmten Regionen erstellt werden, umfasst die Verschlüsselung mit kundenseitig verwalteten Schlüsseln auch temporäre Datenträger, sodass eine [vollständige Mehrfachverschlüsselung](search-security-overview.md#double-encryption) erreicht wird. Dies ist derzeit in den folgenden Regionen verfügbar: 

+ USA, Westen 2
+ East US
+ USA Süd Mitte
+ US Government, Virginia
+ US Gov Arizona

Wenn Sie eine andere Region oder einen Dienst verwenden, der vor dem 1. August erstellt wurde, ist die Verschlüsselung mit verwalteten Schlüsseln auf Datenträger für Ihre Daten beschränkt und umfasst nicht die vom Dienst verwendeten temporären Datenträger.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Szenario werden die folgenden Tools und Dienste verwendet.

+ [Azure Cognitive Search](search-create-service-portal.md) in einem [abrechenbaren Tarif](search-sku-tier.md#tier-descriptions) (mindestens Basic in einer beliebigen Region)
+ [Azure Key Vault](../key-vault/general/overview.md) Sie können einen Schlüsseltresor im [Azure-Portal](../key-vault//general/quick-create-portal.md), mit der [Azure-Befehlszeilenschnittstelle](../key-vault//general/quick-create-cli.md) oder mit [Azure PowerShell](../key-vault//general/quick-create-powershell.md) erstellen. im selben Abonnement wie Azure Cognitive Search. **Vorläufiges Löschen** und **Löschschutz** müssen für den Schlüsseltresor aktiviert sein.
+ [Azure Active Directory:](../active-directory/fundamentals/active-directory-whatis.md) Falls Sie nicht über eine solche Instanz verfügen, [richten Sie einen neuen Mandanten ein](../active-directory/develop/quickstart-create-new-tenant.md).

Sie sollten über eine Suchanwendung verfügen, mit der das verschlüsselte Objekt erstellt werden kann. In diesem Code verweisen Sie auf einen Key Vault-Schlüssel und Active Directory-Registrierungsinformationen. Bei diesem Code kann es sich um eine funktionierende App oder einen Prototypcode wie das [C#-Codebeispiel „DotNetHowToEncryptionUsingCMK“](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK) handeln.

> [!TIP]
> Sie können [Postman oder Visual Studio Code](search-get-started-rest.md) oder [Azure PowerShell](./search-get-started-powershell.md) verwenden, um die REST-APIs aufzurufen, mit denen Indizes und Synonymzuordnungen erstellt werden, die einen Parameter für den Verschlüsselungsschlüssel enthalten. Derzeit wird das Hinzufügen eines Schlüssels zu Indizes oder Synonymzuordnungen über das Portal nicht unterstützt.

## <a name="1---enable-key-recovery"></a>1: Aktivieren der Schlüsselwiederherstellung

Aufgrund der Art der Verschlüsselung mit kundenseitig verwalteten Schlüsseln können Ihre Daten nicht abgerufen werden, wenn der Azure Key Vault-Schlüssel gelöscht wird. Um Datenverluste aufgrund versehentlich gelöschter Key Vault-Schlüssel zu vermeiden, müssen im Schlüsseltresor die Optionen „Vorläufiges Löschen“ und „Löschschutz“ aktiviert werden. Vorläufiges Löschen ist standardmäßig aktiviert, sodass nur dann Probleme auftreten, wenn Sie das Feature absichtlich deaktiviert haben. Der Löschschutz ist standardmäßig nicht aktiviert, er ist aber für die Verschlüsselung mit kundenseitig verwalteten Schlüsseln in Cognitive Search erforderlich. Weitere Informationen finden Sie in den Übersichten zum [vorläufigen Löschen](../key-vault/general/soft-delete-overview.md) und zum [Löschschutz](../key-vault/general/soft-delete-overview.md#purge-protection).

Sie können beide Eigenschaften mithilfe von Azure-Portal-, PowerShell- oder Azure CLI-Befehlen festlegen.

### <a name="using-azure-portal"></a>Verwenden des Azure-Portals

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com), und öffnen Sie die Übersichtsseite Ihrer Key Vault-Instanz.

1. Aktivieren Sie auf der Seite **Übersicht** unter **Zusammenfassung** die Optionen **Vorläufiges Löschen** und **Purge protection** (Löschschutz).

### <a name="using-powershell"></a>PowerShell

1. Führen Sie `Connect-AzAccount` aus, um Ihre Azure-Anmeldeinformationen einzurichten.

1. Führen Sie den folgenden Befehl aus, um eine Verbindung mit Ihrem Schlüsseltresor herzustellen, und ersetzen Sie `<vault_name>` durch einen gültigen Namen:

   ```powershell
   $resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "<vault_name>").ResourceId
   ```

1. In Azure Key Vault erfolgt die Erstellung mit aktiviertem vorläufigem Löschen. Wenn das Feature in Ihrem Tresor deaktiviert ist, führen Sie den folgenden Befehl aus:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enableSoftDelete" -Value 'true'
   ```

1. Aktivieren Sie den Löschschutz:

   ```powershell
   $resource.Properties | Add-Member -MemberType NoteProperty -Name "enablePurgeProtection" -Value 'true'
   ```

1. Speichern Sie Ihre Änderungen:

   ```powershell
   Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
   ```

### <a name="using-azure-cli"></a>Verwenden der Azure-Befehlszeilenschnittstelle

+ Wenn Sie die [Azure CLI installiert](/cli/azure/install-azure-cli) haben, können Sie den folgenden Befehl ausführen, um die erforderlichen Eigenschaften zu aktivieren.

   ```azurecli-interactive
   az keyvault update -n <vault_name> -g <resource_group> --enable-soft-delete --enable-purge-protection
   ```

## <a name="2---create-a-key-in-key-vault"></a>2: Erstellen eines Schlüssels in Key Vault

Überspringen Sie diesen Schritt, wenn Sie bereits über einen Schlüssel in Azure Key Vault verfügen.

1. [Melden Sie sich beim Azure-Portal an](https://portal.azure.com), und öffnen Sie die Übersichtsseite Ihrer Key Vault-Instanz.

1. Klicken Sie auf der linken Seite auf **Schlüssel** und dann auf **+ Generate/Import** (Generieren/Importieren).

1. Wählen Sie im Bereich **Schlüssel erstellen** in der Liste mit den **Optionen** die gewünschte Schlüsselerstellungsmethode aus. Sie können einen neuen Schlüssel **generieren**, einen vorhandenen Schlüssel **hochladen** oder **Sicherung wiederherstellen** verwenden, um eine Sicherung eines Schlüssels auszuwählen.

1. Geben Sie einen **Namen** für den Schlüssel ein, und wählen Sie optional andere Schlüsseleigenschaften aus.

1. Wählen Sie **Erstellen** aus, um die Bereitstellung zu starten.

1. Notieren Sie sich den Schlüsselbezeichner, der sich aus dem **Schlüsselwert-URI**, dem **Schlüsselnamen** und der **Schlüsselversion** zusammensetzt. Sie benötigen den Bezeichner, um einen verschlüsselten Index in Azure Cognitive Search zu definieren.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-key-identifier.png" alt-text="Erstellen eines neuen Key Vault-Schlüssels":::

## <a name="3---register-an-app-in-active-directory"></a>3: Registrieren einer App in Active Directory

1. Suchen Sie im [Azure-Portal](https://portal.azure.com) nach der Azure Active Directory-Ressource für Ihr Abonnement.

1. Suchen Sie auf der linken Seite unter **Verwalten** nach **App-Registrierungen**, und klicken Sie dann auf **New registration** (Neue Registrierung).

1. Benennen Sie die Registrierung. Verwenden Sie dabei beispielsweise einen Namen, der dem Namen der Suchanwendung ähnelt. Wählen Sie **Registrieren**.

1. Sobald die App-Registrierung erstellt wurde, kopieren Sie die Anwendungs-ID. Sie müssen diese Zeichenfolge für Ihre Anwendung bereitstellen. 

   Wenn Sie [DotNetHowToEncryptionUsingCMK](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToEncryptionUsingCMK) durchlaufen, fügen Sie diesen Wert in die Datei **appsettings.json** ein.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-id.png" alt-text="Anwendungs-ID im Abschnitt „Zusammenfassung“":::

1. Klicken Sie als Nächstes auf der linken Seite auf **Certificates & secrets** (Zertifikate und Geheimnisse).

1. Wählen Sie **Neuer geheimer Clientschlüssel**. Geben Sie dem Geheimnis einen Anzeigenamen, und klicken Sie auf **Hinzufügen**.

1. Kopieren Sie das Anwendungsgeheimnis. Wenn Sie das Beispiel durchlaufen, fügen Sie diesen Wert in die Datei **appsettings.json** ein.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-application-secret.png" alt-text="Anwendungsgeheimnis":::

## <a name="4---grant-key-access-permissions"></a>4: Erteilen von Zugriffsberechtigungen für den Schlüssel

In diesem Schritt erstellen Sie eine Zugriffsrichtlinie in Key Vault. Diese Richtlinie gewährt der registrierten Anwendung die Active Directory-Berechtigung zur Verwendung Ihres kundenseitig verwalteten Schlüssels.

Die Zugriffsberechtigungen können jederzeit aufgehoben werden. Nach dem Aufheben können alle Indizes oder Synonymzuordnungen des Suchdiensts, die diese Key Vault-Instanz verwenden, nicht mehr verwendet werden. Durch Wiederherstellen der Key Vault-Zugriffsberechtigungen zu einem späteren Zeitpunkt wird der Zugriff auf die Indizes oder Synonymzuordnungen wiederhergestellt. Weitere Informationen finden Sie unter [Sicherer Zugriff auf einen Schlüsseltresor](../key-vault/general/secure-your-key-vault.md).

1. Öffnen Sie dann im Azure-Portal die Seite **Übersicht** Ihrer Key Vault-Instanz. 

1. Wählen Sie auf der linken Seite **Zugriffsrichtlinien** und dann **+ Zugriffsrichtlinie hinzufügen** aus.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-add-access-policy.png" alt-text="Hinzufügen einer neuen Key Vault-Zugriffsrichtlinie":::

1. Klicken Sie auf **Prinzipal auswählen**, und wählen Sie die Anwendung aus, die Sie in Active Directory registriert haben. Sie können nach dem Namen suchen.

   :::image type="content" source="media/search-manage-encryption-keys/cmk-access-policy-permissions.png" alt-text="Auswählen des Prinzipals für die Key Vault-Zugriffsrichtlinie":::

1. Klicken Sie unter **Schlüsselberechtigungen** auf *Abrufen*, *Schlüssel entpacken* und *Schlüssel packen*.

1. Klicken Sie unter **Geheimnisberechtigungen** auf *Abrufen*.

1. Klicken Sie unter **Zertifikatberechtigungen** auf *Abrufen*.

1. Klicken Sie auf **Hinzufügen** und dann auf **Speichern**.

> [!Important]
> Verschlüsselte Inhalte in der kognitiven Azure-Suche sind zur Verwendung eines bestimmten Azure Key Vault-Schlüssels mit einer bestimmten **Version** konfiguriert. Wenn Sie den Schlüssel oder die Version ändern, muss der Index oder die Synonymzuordnung zur Verwendung des neuen Schlüssels oder der neuen Version geändert werden, **bevor** der vorherige Schlüssel bzw. die vorherige Version gelöscht wird. Andernfalls kann der Index oder die Synonymzuordnung nicht mehr verwendet werden, da Sie die Inhalte nicht mehr entschlüsseln können, wenn der Schlüsselzugriff verloren geht.

<a name="encrypt-content"></a>

## <a name="5---encrypt-content"></a>5: Verschlüsseln von Inhalten

Wenn Sie einen kundenseitig verwalteten Schlüssel für einen Index, eine Datenquelle, ein Skillset, einen Indexer oder eine Synonymzuordnung hinzufügen möchten, müssen Sie die [Search-REST-API](/rest/api/searchservice/) oder ein SDK verwenden. Das Portal macht keine Synonymzuordnungen oder Verschlüsselungseigenschaften verfügbar. Wenn Sie eine gültige API verwenden, unterstützen Indizes, Datenquellen, Skillsets, Indexer oder Synonymzuordnungen eine **encryptionKey**-Eigenschaft auf oberster Ebene.

In diesem Beispiel wird die REST-API mit den folgenden Werten für Azure Key Vault und Azure Active Directory verwendet:

```json
{
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

> [!Note]
> Keine dieser Key Vault-Details gelten als geheim. Diese Angaben können durch Navigieren zu der entsprechenden Seite des Azure Key Vault-Schlüssels im Azure-Portal einfach abgerufen werden.

## <a name="example-index-encryption"></a>Beispiel: Indexverschlüsselung

Erstellen Sie einen verschlüsselten Index mit der [Azure Cognitive Search-REST-API „Index erstellen“](/rest/api/searchservice/create-index). Verwenden Sie die `encryptionKey`-Eigenschaft, um den zu verwendenden Verschlüsselungsschlüssel anzugeben.
> [!Note]
> Keine dieser Key Vault-Details gelten als geheim. Diese Angaben können durch Navigieren zu der entsprechenden Seite des Azure Key Vault-Schlüssels im Azure-Portal einfach abgerufen werden.

## <a name="rest-examples"></a>REST-Beispiele

Dieser Abschnitt zeigt den vollständigen JSON-Code für einen verschlüsselten Index und eine Synonymzuordnung.

### <a name="index-encryption"></a>Indexverschlüsselung

Die Angaben zum Erstellen eines neuen Index über die REST-API finden Sie unter [Erstellen eines Index (REST-API)](/rest/api/searchservice/create-index). Der einzige Unterschied besteht hier darin, dass die Angaben des Verschlüsselungsschlüssels als Teil der Indexdefinition angegeben werden:

```json
{
 "name": "hotels",
 "fields": [
  {"name": "HotelId", "type": "Edm.String", "key": true, "filterable": true},
  {"name": "HotelName", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": true, "facetable": false},
  {"name": "Description", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "en.lucene"},
  {"name": "Description_fr", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
  {"name": "Category", "type": "Edm.String", "searchable": true, "filterable": true, "sortable": true, "facetable": true},
  {"name": "Tags", "type": "Collection(Edm.String)", "searchable": true, "filterable": true, "sortable": false, "facetable": true},
  {"name": "ParkingIncluded", "type": "Edm.Boolean", "filterable": true, "sortable": true, "facetable": true},
  {"name": "LastRenovationDate", "type": "Edm.DateTimeOffset", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Rating", "type": "Edm.Double", "filterable": true, "sortable": true, "facetable": true},
  {"name": "Location", "type": "Edm.GeographyPoint", "filterable": true, "sortable": true},
 ],
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Dann können Sie die Anforderung zur Indexerstellung senden und anschließend den Index normal verwenden.

### <a name="synonym-map-encryption"></a>Verschlüsselung einer Synonymzuordnung

Erstellen Sie eine verschlüsselte Synonymzuordnung mit der [Azure Cognitive Search-REST-API „Synonymzuordnung erstellen“](/rest/api/searchservice/create-synonym-map). Verwenden Sie die `encryptionKey`-Eigenschaft, um den zu verwendenden Verschlüsselungsschlüssel anzugeben.

```json
{
  "name" : "synonymmap1",
  "format" : "solr",
  "synonyms" : "United States, United States of America, USA\n
  Washington, Wash. => WA",
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Dann können Sie die Anforderung zum Erstellen der Synonymzuordnung senden und anschließend die Synonymzuordnung normal verwenden.

## <a name="example-data-source-encryption"></a>Beispiel: Datenquellenverschlüsselung

Erstellen Sie eine verschlüsselte Datenquelle mit der [Azure Cognitive Search-REST-API „Datenquelle erstellen“](/rest/api/searchservice/create-data-source). Verwenden Sie die `encryptionKey`-Eigenschaft, um den zu verwendenden Verschlüsselungsschlüssel anzugeben.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Dann können Sie die Anforderung zum Erstellen der Datenquelle senden und anschließend die Datenquelle normal verwenden.

## <a name="example-skillset-encryption"></a>Beispiel: Skillsetverschlüsselung

Erstellen Sie ein verschlüsseltes Skillset mit der [Azure Cognitive Search-REST-API „Skillset erstellen“](/rest/api/searchservice/create-skillset). Verwenden Sie die `encryptionKey`-Eigenschaft, um den zu verwendenden Verschlüsselungsschlüssel anzugeben.

```json
{
  "name" : "datasource1",
  "type" : "azureblob",
  "credentials" :
  { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=datasource;AccountKey=accountkey;EndpointSuffix=core.windows.net"
  },
  "container" : { "name" : "containername" },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Dann können Sie die Anforderung zum Erstellen des Skillsets senden und anschließend das Skillset normal verwenden.

## <a name="example-indexer-encryption"></a>Beispiel: Indexerverschlüsselung

Erstellen Sie einen verschlüsselten Indexer mit der [Azure Cognitive Search-REST-API „Indexer erstellen“](/rest/api/searchservice/create-indexer). Verwenden Sie die `encryptionKey`-Eigenschaft, um den zu verwendenden Verschlüsselungsschlüssel anzugeben.

```json
{
  "name": "indexer1",
  "dataSourceName": "datasource1",
  "skillsetName": "skillset1",
  "parameters": {
      "configuration": {
          "imageAction": "generateNormalizedImages"
      }
  },
  "encryptionKey": {
    "keyVaultUri": "https://demokeyvault.vault.azure.net",
    "keyVaultKeyName": "myEncryptionKey",
    "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660",
    "accessCredentials": {
      "applicationId": "00000000-0000-0000-0000-000000000000",
      "applicationSecret": "myApplicationSecret"
    }
  }
}
```

Dann können Sie die Anforderung zum Erstellen des Indexers senden und anschließend den Indexer normal verwenden.

>[!Important]
> Obwohl `encryptionKey` nicht vorhandenen Suchindizes oder Synonymzuordnungen hinzugefügt werden kann, ist eine Aktualisierung möglich, indem unterschiedliche Werte für eines der drei Key Vault-Details angegeben werden (z. B. Aktualisierung der Schlüsselversion). Wenn Sie einen neuen Key Vault-Schlüssel oder eine neue Schlüsselversion angeben, müssen alle Suchindizes oder Synonymzuordnungen, die den Schlüssel verwenden, zunächst für die Verwendung des neuen Schlüssels oder der neuen Version geändert werden, **bevor** der vorherige Schlüssel bzw. die vorherige Version gelöscht wird. Andernfalls kann der Index oder die Synonymzuordnung nicht mehr verwendet werden, da Sie die Inhalte nicht mehr entschlüsseln können, wenn der Schlüsselzugriff verloren geht. Allerdings kann durch Wiederherstellen der Key Vault-Zugriffsberechtigungen zu einem späteren Zeitpunkt der Zugriff auf die Inhalte wiederhergestellt werden.

## <a name="simpler-alternative-trusted-service"></a>Einfachere Alternative: Vertrauenswürdiger Dienst

Abhängig von der Mandantenkonfiguration und den Authentifizierungsanforderungen können Sie möglicherweise einen einfacheren Ansatz für den Zugriff auf einen Key Vault-Schlüssel implementieren. Anstatt eine Active Directory-Anwendung zu erstellen und zu verwenden, können Sie einen Suchdienst zu einem vertrauenswürdigen Dienst machen, indem Sie eine vom System verwaltete Identität dafür aktivieren. Danach verwenden Sie anstelle einer bei AD registrierten Anwendung den vertrauenswürdigen Suchdienst als Sicherheitsprinzipal für den Zugriff auf den Key Vault-Schlüssel.

Mit diesem Ansatz können Sie die Schritte für die Anwendungsregistrierung und Anwendungsgeheimnisse weglassen. Außerdem wird die Verschlüsselungsschlüsseldefinition vereinfacht, die nur für die Key Vault-Komponenten gilt (URI, Tresorname, Schlüsselversion).

Im Allgemeinen kann der Suchdienst über eine verwaltete Identität bei Azure Key Vault authentifiziert werden, ohne dass Anmeldeinformationen (ApplicationID oder ApplicationSecret) im Code gespeichert werden. Der Lebenszyklus dieses Typs einer verwalteten Identität ist an den Lebenszyklus des Suchdiensts gebunden, der nur eine verwaltete Identität enthalten kann. Weitere Informationen zur Funktion verwalteter Identitäten finden Sie unter [Was sind verwaltete Identitäten für Azure-Ressourcen?](../active-directory/managed-identities-azure-resources/overview.md).

1. Machen Sie Ihren Suchdienst zu einem vertrauenswürdigen Dienst.
   ![Aktivieren einer systemseitig zugewiesenen verwalteten Identität](./media/search-managed-identities/turn-on-system-assigned-identity.png "Aktivieren einer systemseitig zugewiesenen verwalteten Identität")

1. Wenn Sie eine Zugriffsrichtlinie in Azure Key Vault einrichten, wählen Sie den vertrauenswürdigen Suchdienst als Prinzip aus (anstelle der bei AD registrierten Anwendung). Weisen Sie wie im Schritt zum Gewähren von Zugriffsschlüsselberechtigungen die gleichen Berechtigungen zu (mehrere GETs, WRAP, UNWRAP).

1. Verwenden Sie eine vereinfachte Konstruktion von `encryptionKey`, die die Active Directory-Eigenschaften auslässt.

    ```json
    {
      "encryptionKey": {
        "keyVaultUri": "https://demokeyvault.vault.azure.net",
        "keyVaultKeyName": "myEncryptionKey",
        "keyVaultKeyVersion": "eaab6a663d59439ebb95ce2fe7d5f660"
      }
    }
    ```

Folgende Bedingungen verhindern, dass Sie diesen vereinfachten Ansatz anwenden:

+ Sie können für Ihren Suchdienst nicht direkt Zugriffsberechtigungen für die Key Vault-Instanz erteilen (z. B. wenn der Suchdienst sich in einem anderen Active Directory-Mandanten befindet als die Azure Key Vault-Instanz).

+ Ein einzelner Suchdienst ist erforderlich, um mehrere verschlüsselte Indizes oder Synonymzuordnungen zu hosten, die jeweils einen anderen Schlüssel aus einem anderen Schlüsselspeicher verwenden, wobei jeder Schlüsselspeicher **eine andere Identität** zur Authentifizierung verwenden muss. Da ein Suchdienst nur über eine verwaltete Identität verfügen kann, ist der vereinfachte Ansatz für Ihr Szenario aufgrund der Anforderungen für mehrere Identitäten nicht geeignet.  

## <a name="work-with-encrypted-content"></a>Arbeiten mit verschlüsselten Inhalten

Bei der Verschlüsselung mit kundenseitig verwalteten Schlüsseln treten Wartezeiten bei der Indizierung und bei Abfragen aufgrund des zusätzlichen Aufwands für Verschlüsselung und Entschlüsselung auf. Die Verschlüsselungsaktivität wird von Azure Cognitive Search nicht protokolliert, Sie können jedoch den Schlüsselzugriff über die Key Vault-Protokollierung überwachen. Es wird empfohlen, die [Protokollierung](../key-vault/general/logging.md) schon bei der Konfiguration des Schlüsseltresors zu aktivieren.

Es wird erwartet, dass im Lauf der Zeit eine Schlüsselrotation erfolgt. Wenn Sie Schlüssel rotieren, ist es wichtig, dabei die folgende Sequenz einzuhalten:

1. [Ermitteln Sie den Schlüssel, der von einem Index oder einer Synonymzuordnung verwendet wird.](search-security-get-encryption-keys.md)
1. [Erstellen Sie einen neuen Schlüssel im Schlüsseltresor](../key-vault/keys/quick-create-portal.md), aber lassen Sie den ursprünglichen Schlüssel verfügbar.
1. [Aktualisieren Sie die Eigenschaften der encryptionKey-Eigenschaft](/rest/api/searchservice/update-index) für einen Index oder eine Synonymzuordnung, damit die neuen Werte verwendet werden. Nur Objekte, die ursprünglich mit dieser Eigenschaft erstellt wurden, können aktualisiert werden, um einen anderen Wert zu verwenden.
1. Deaktivieren oder löschen Sie den vorherigen Schlüssel aus dem Schlüsseltresor. Überwachen Sie den Schlüsselzugriff, um sicherzustellen, dass der neue Schlüssel verwendet wird.

Aus Leistungsgründen speichert der Suchdienst den Schlüssel für mehrere Stunden zwischen. Wenn Sie den Schlüssel deaktivieren oder löschen, ohne einen neuen bereitzustellen, funktionieren Abfragen weiter, bis der Cache abläuft. Wenn der Suchdienst die Inhalte aber nicht mehr entschlüsseln kann, wird diese Meldung angezeigt: „Zugriff untersagt. Der Abfrageschlüssel wurde möglicherweise widerrufen. Versuchen Sie es noch mal.“ 

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie nicht mit der Azure-Sicherheitsarchitektur vertraut sind, finden Sie entsprechende Informationen in der [Dokumentation zur Azure-Sicherheit](../security/index.yml) und insbesondere in folgendem Artikel:

> [!div class="nextstepaction"]
> [Verschlüsselung ruhender Daten](../security/fundamentals/encryption-atrest.md)