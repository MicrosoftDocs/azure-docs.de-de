---
title: Einführung in Microsoft Spark-Hilfsprogramme
description: 'Tutorial: MSSparkutils in Azure Synapse Analytics-Notebooks'
author: ruxu
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: reference
ms.subservice: spark
ms.date: 09/10/2020
ms.author: ruxu
ms.reviewer: ''
zone_pivot_groups: programming-languages-spark-all-minus-sql
ms.openlocfilehash: fee7e244e26a59a773a5d5c9c74e23acd4113ee6
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96446704"
---
# <a name="introduction-to-microsoft-spark-utilities"></a>Einführung in Microsoft Spark-Hilfsprogramme

Microsoft Spark-Hilfsprogramme (MSSparkUtils) sind ein integriertes Paket, mit dem sich gängige Aufgaben leichter erledigen lassen. Sie können MSSparkUtils verwenden, um mit Dateisystemen zu arbeiten, um Umgebungsvariablen abzurufen und um mit Geheimnissen zu arbeiten. MSSparkUtils sind in `PySpark (Python)`-, `Scala`- und `.NET Spark (C#)`-Notebooks sowie in Synapse-Pipelines verfügbar.

## <a name="pre-requisites"></a>Voraussetzungen

### <a name="configure-access-to-azure-data-lake-storage-gen2"></a>Konfigurieren des Zugriffs auf Azure Data Lake Storage Gen2 

Synapse-Notebooks verwenden Azure Active Directory (Azure AD)-Passthrough für den Zugriff auf ADLS Gen2-Konten. Sie müssen **Mitwirkender an Blob Storage** sein, um auf das ADLS Gen2-Konto (oder den Ordner) zugreifen zu können. 

Synapse-Pipelines verwenden die Arbeitsbereichsidentität (MSI) für den Zugriff auf die Speicherkonten. Um MSSparkUtils in Ihren Pipelineaktivitäten zu verwenden, muss Ihre Arbeitsbereichsidentität **Mitwirkender an Blob Storage** sein, um auf das ADLS Gen2-Konto (oder den Ordner) zuzugreifen.

Gehen Sie folgendermaßen vor, um sicherzustellen, dass Ihr Azure AD- und Arbeitsbereichs-MSI Zugriff auf das ADLS Gen2-Konto haben:
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/) und das Speicherkonto, auf das sie zugreifen möchten. Sie können zu dem spezifischen Container navigieren, auf den Sie zugreifen möchten.
2. Wählen Sie im linken Bereich **Zugriffssteuerung (IAM)** aus.
3. Weisen Sie der Rolle **Mitwirkender an Storage-Blobdaten** im Speicherkonto **Ihr Azure AD-Konto** und **Ihre Arbeitsbereichsidentität** (identisch mit Ihrem Arbeitsbereichsnamen) zu, wenn diese nicht bereits zugewiesen sind. 
4. Wählen Sie **Speichern** aus.

Sie können über die folgende URL mithilfe von Synapse Spark auf Daten in ADLS Gen2 zugreifen:

<code>abfss://<container_name>@<storage_account_name>.dfs.core.windows.net/<path></code>

### <a name="configure-access-to-azure-blob-storage"></a>Konfigurieren des Zugriffs auf Azure Blob Storage 

Synapse nutzt **SAS (Shared Access Signature)** für den Zugriff auf Azure Blob Storage. Um das Verfügbarmachen von SAS-Schlüsseln im Code zu vermeiden, empfehlen wir, im Synapse-Arbeitsbereich einen neuen verknüpften Dienst mit dem Azure Blob Storage Konto zu erstellen, auf das Sie zugreifen möchten.

Befolgen Sie diese Schritte, um einen neuen verknüpften Dienst für ein Azure Blob Storage-Konto hinzuzufügen:

1. Öffnen Sie [Azure Synapse Studio](https://web.azuresynapse.net/).
2. Wählen Sie im linken Bereich **Verwalten** aus, und wählen Sie unter **Externe Verbindungen** die Option **Verknüpfte Dienste** aus.
3. Suchen Sie rechts im Bereich **Neuer verknüpfter Dienst** nach **Azure Blob Storage**.
4. Wählen Sie **Weiter**.
5. Wählen Sie das Azure Blob Storage-Konto aus, auf das zugegriffen werden soll, und konfigurieren Sie den Namen des verknüpften Diensts. Schlagen Sie vor, **Kontoschlüssel** als **Authentifizierungsmethode** zu verwenden.
6. Wählen Sie **Verbindung testen** aus, um die Richtigkeit der Einstellungen zu überprüfen.
7. Wählen Sie zuerst **Erstellen** und dann **Alle veröffentlichen** aus, um Ihre Änderungen zu speichern. 

Sie können über die folgende URL mithilfe von Synapse Spark auf Daten in Azure Blob Storage zugreifen:

<code>wasb[s]://<container_name>@<storage_account_name>.blob.core.windows.net/<path></code>

Hier finden Sie ein Codebeispiel:


:::zone pivot = "programming-language-python"

```python
from pyspark.sql import SparkSession

# Azure storage access info
blob_account_name = 'Your account name' # replace with your blob name
blob_container_name = 'Your container name' # replace with your container name
blob_relative_path = 'Your path' # replace with your relative folder path
linked_service_name = 'Your linked service name' # replace with your linked service name

blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

# Allow SPARK to access from Blob remotely

wasb_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)

spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name), blob_sas_token)
print('Remote blob path: ' + wasb_path)
```

::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val blob_account_name = "" // replace with your blob name
val blob_container_name = "" //replace with your container name
val blob_relative_path = "/" //replace with your relative folder path
val linked_service_name = "" //replace with your linked service name


val blob_sas_token = mssparkutils.credentials.getConnectionStringOrCreds(linked_service_name)

val wasbs_path = f"wasbs://$blob_container_name@$blob_account_name.blob.core.windows.net/$blob_relative_path"
spark.conf.set(f"fs.azure.sas.$blob_container_name.$blob_account_name.blob.core.windows.net",blob_sas_token)

```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end
 
###  <a name="configure-access-to-azure-key-vault"></a>Konfigurieren des Zugriffs auf Azure Key Vault

Sie können einen Azure Key Vault als verknüpften Dienst hinzufügen, um Ihre Anmeldeinformationen in Synapse zu verwalten. Befolgen Sie diese Schritte, um einen Azure Key Vault als verknüpften Synapse-Dienst hinzuzufügen:
1. Öffnen Sie [Azure Synapse Studio](https://web.azuresynapse.net/).
2. Wählen Sie im linken Bereich **Verwalten** aus, und wählen Sie unter **Externe Verbindungen** die Option **Verknüpfte Dienste** aus.
3. Suchen Sie rechts im Bereich **Neuer verknüpfter Dienst** nach **Azure Key Vault**.
4. Wählen Sie das Azure Key Vault-Konto aus, auf das zugegriffen werden soll, und konfigurieren Sie den Namen des verknüpften Diensts.
5. Wählen Sie **Verbindung testen** aus, um die Richtigkeit der Einstellungen zu überprüfen.
6. Wählen Sie zuerst **Erstellen** und dann **Alle veröffentlichen** aus, um Ihre Änderungen zu speichern. 

Synapse-Notebooks verwenden Azure Active Directory (Azure AD)-Passthrough für den Zugriff auf Azure Key Vault. Synapse-Pipelines verwenden die Arbeitsbereichsidentität (MSI) für den Zugriff auf Azure Key Vault. Um sicherzustellen, dass Ihr Code sowohl im Notebook als auch in der Synapse-Pipeline funktioniert, empfiehlt es sich, sowohl Ihrem Azure AD-Konto als auch Ihrer Arbeitsbereichsidentität Zugriffsberechtigung auf das Geheimnis zu gewähren.

Führen Sie diese Schritte aus, um Ihrer Arbeitsbereichsidentität Zugriff auf das Geheimnis zu gewähren:
1. Öffnen Sie das [Azure-Portal](https://portal.azure.com/) und den Azure Key Vault, auf den sie zugreifen möchten. 
2. Wählen Sie im linken Bereich **Zugriffsrichtlinien** aus.
3. Wählen Sie **Zugriffsrichtlinie hinzufügen** aus: 
    - Wählen sie **Verwaltung von Schlüsseln, Geheimnissen und Zertifikaten** als Konfigurationsvorlage aus.
    - Wählen Sie in „Prinzipal auswählen“ Ihr **Azure AD-Konto** und **Ihre Arbeitsbereichsidentität** (identisch mit Ihrem Arbeitsbereichsnamen) aus, oder stellen Sie sicher, dass sie bereits zugewiesen sind. 
4. Wählen Sie **Auswählen** und **Hinzufügen** aus.
5. Wählen Sie die Schaltfläche **Speichern** aus, um Änderungen zu übernehmen.  

## <a name="file-system-utilities"></a>Dateisystem-Hilfsprogramme

`mssparkutils.fs` stellt Hilfsprogramme für die Arbeit mit verschiedenen Dateisystemen bereit, einschließlich Azure Data Lake Storage Gen2 (ADLS Gen2) und Azure Blob Storage. Stellen Sie sicher, dass Sie den Zugriff auf [Azure Data Lake Storage Gen2](#configure-access-to-azure-data-lake-storage-gen2) und [Azure Blob Storage](#configure-access-to-azure-blob-storage) entsprechend konfigurieren.

Führen Sie die folgenden Befehle aus, um eine Übersicht über die verfügbaren Methoden zu erhalten:

:::zone pivot = "programming-language-python"

```python
from notebookutils import mssparkutils
mssparkutils.fs.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
using Microsoft.Spark.Extensions.Azure.Synapse.Analytics.Notebook.MSSparkUtils;
FS.Help()
```

::: zone-end

Ergebnis:
```
mssparkutils.fs provides utilities for working with various FileSystems.

Below is overview about the available methods:

cp(from: String, to: String, recurse: Boolean = false): Boolean -> Copies a file or directory, possibly across FileSystems
mv(from: String, to: String, recurse: Boolean = false): Boolean -> Moves a file or directory, possibly across FileSystems
ls(dir: String): Array -> Lists the contents of a directory
mkdirs(dir: String): Boolean -> Creates the given directory if it does not exist, also creating any necessary parent directories
put(file: String, contents: String, overwrite: Boolean = false): Boolean -> Writes the given String out to a file, encoded in UTF-8
head(file: String, maxBytes: int = 1024 * 100): String -> Returns up to the first 'maxBytes' bytes of the given file as a String encoded in UTF-8
append(file: String, content: String, createFileIfNotExists: Boolean): Boolean -> Append the content to a file
rm(dir: String, recurse: Boolean = false): Boolean -> Removes a file or directory

Use mssparkutils.fs.help("methodName") for more info about a method.

```

### <a name="list-files"></a>Auflisten von Dateien
Listet den Inhalt eines Verzeichnisses auf.


:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.ls('Your directory path')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.ls("Your directory path")
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Ls("Your directory path")
```

::: zone-end


### <a name="view-file-properties"></a>Anzeigen von Dateieigenschaften
Gibt Dateieigenschaften zurück, einschließlich Dateiname, Dateipfad, Dateigröße, und ob es sich um ein Verzeichnis oder eine Datei handelt.

:::zone pivot = "programming-language-python"

```python
files = mssparkutils.fs.ls('Your directory path')
for file in files:
    print(file.name, file.isDir, file.isFile, file.path, file.size)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
val files = mssparkutils.fs.ls("/")
files.foreach{
    file => println(file.name,file.isDir,file.isFile,file.size)
}
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
var Files = FS.Ls("/");
foreach(var File in Files) {
    Console.WriteLine(File.Name+" "+File.IsDir+" "+File.IsFile+" "+File.Size);
}
```

::: zone-end

### <a name="create-new-directory"></a>Neues Verzeichnis erstellen

Erstellt das angegebene Verzeichnis, wenn es noch nicht vorhanden ist, sowie alle erforderlichen übergeordneten Verzeichnisse.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mkdirs('new directory name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mkdirs("new directory name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mkdirs("new directory name")
```

::: zone-end

### <a name="copy-file"></a>Datei kopieren

Kopiert eine Datei oder ein Verzeichnis. Unterstützt das Kopieren zwischen Dateisystemen.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.cp('source file or directory', 'destination file or directory', True)# Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```
::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Cp("source file or directory", "destination file or directory", true) // Set the third parameter as True to copy all files and directories recursively
```

::: zone-end

### <a name="preview-file-content"></a>Vorschau von Dateiinhalt anzeigen

Gibt bis zu den ersten „maxBytes“ Bytes der angegebenen Datei als Zeichenfolge zurück, die in UTF-8 codiert ist.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.head('file path', maxBytes to read)
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.head("file path", maxBytes to read)
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Head("file path", maxBytes to read)
```

::: zone-end

### <a name="move-file"></a>Datei verschieben

Verschiebt eine Datei oder ein Verzeichnis. Unterstützt das Verschieben zwischen Dateisystemen.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.mv('source file or directory', 'destination directory', True) # Set the last parameter as True to firstly create the parent directory if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.mv("source file or directory", "destination directory", true) // Set the last parameter as True to firstly create the parent directory if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Mv("source file or directory", "destination directory", true)
```

::: zone-end

### <a name="write-file"></a>Datei schreiben

Schreibt die angegebene Zeichenfolge als Ausgabe in eine Datei, codiert in UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.put("file path", "content to write", True) # Set the last parameter as True to overwrite the file if it existed already
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Put("file path", "content to write", true) // Set the last parameter as True to overwrite the file if it existed already
```

::: zone-end

### <a name="append-content-to-a-file"></a>Inhalt an eine Datei anfügen

Fügt die angegebene Zeichenfolge an eine Datei an, codiert in UTF-8.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.append('file path','content to append',True) # Set the last parameter as True to create the file if it does not exist
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Append("file path","content to append",true) // Set the last parameter as True to create the file if it does not exist
```

::: zone-end

### <a name="delete-file-or-directory"></a>Datei oder Verzeichnis löschen

Entfernt eine Datei oder ein Verzeichnis.

:::zone pivot = "programming-language-python"

```python
mssparkutils.fs.rm('file path', True) # Set the last parameter as True to remove all files and directories recursively 
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.fs.rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
FS.Rm("file path", true) // Set the last parameter as True to remove all files and directories recursively 
```

::: zone-end


## <a name="credentials-utilities"></a>Hilfsprogramme für Anmeldeinformationen

Sie können die MSSparkUtils-Hilfsprogramme für Anmeldeinformationen verwenden, um die Zugriffstoken von verknüpften Diensten abzurufen und Geheimnisse in Azure Key Vault zu verwalten. 

Führen Sie den folgenden Befehl aus, um eine Übersicht über die verfügbaren Methoden zu erhalten:

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.Help()
```

::: zone-end

Ergebnis abrufen:

```
getToken(audience, name): returns AAD token for a given audience, name (optional)
isValidToken(token): returns true if token hasn't expired
getConnectionStringOrCreds(linkedService): returns connection string or credentials for linked service
getSecret(akvName, secret, linkedService): returns AKV secret for a given AKV linked service, akvName, secret key
getSecret(akvName, secret): returns AKV secret for a given akvName, secret key
putSecret(akvName, secretName, secretValue, linkedService): puts AKV secret for a given akvName, secretName
putSecret(akvName, secretName, secretValue): puts AKV secret for a given akvName, secretName
```

### <a name="get-token"></a>Abrufen von Token

Gibt das Azure AD-Token für eine bestimmte Zielgruppe (optional), Name (optional), zurück. In der folgenden Tabelle sind alle verfügbaren Zielgruppentypen aufgeführt: 

|Zielgruppentyp|Zielgruppenschlüssel|
|--|--|
|Zielgruppenauflösungstyp|'Audience'|
|Storage-Zielgruppenressource|'Storage'|
|Data Warehouse-Zielgruppenressource|'DW'|
|Data Lake-Zielgruppenressource|'AzureManagement'|
|Vault-Zielgruppenressource|'DataLakeStore'|
|Azure OSSDB-Zielgruppenressource|'AzureOSSDB'|
|Azure Synapse-Ressource|'Synapse'|
|Azure Data Factory-Ressource|'ADF'|

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getToken('audience Key')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getToken("audience Key")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetToken("audience Key")
```

::: zone-end


### <a name="validate-token"></a>Token überprüfen

Gibt „true“ zurück, wenn das Token nicht abgelaufen ist.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.isValidToken('your token')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.isValidToken("your token")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.IsValidToken("your token")
```

::: zone-end


### <a name="get-connection-string-or-credentials-for-linked-service"></a>Verbindungszeichenfolge oder Anmeldeinformationen für verknüpften Dienst abrufen

Gibt die Verbindungszeichenfolge oder die Anmeldeinformationen für den verknüpften Dienst zurück. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getConnectionStringOrCreds('linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getConnectionStringOrCreds("linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetConnectionStringOrCreds("linked service name")
```

::: zone-end


### <a name="get-secret-using-workspace-identity"></a>Geheimnis mithilfe der Arbeitsbereichsidentität abrufen

Gibt das Azure Key Vault-Geheimnis für einen angegebenen Azure Key Vault-Namen, Geheimnisnamen und Namen eines verknüpften Diensts mithilfe der Arbeitsbereichsidentität zurück. Stellen Sie sicher, dass Sie den Zugriff entsprechend auf [Azure Key Vault](#configure-access-to-azure-key-vault) konfigurieren.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name","linked service name")
```

::: zone-end


### <a name="get-secret-using-user-credentials"></a>Geheimnis mithilfe der Benutzeranmeldeinformationen abrufen

Gibt das Azure Key Vault-Geheimnis für einen angegebenen Azure Key Vault-Namen, Geheimnisnamen und Namen eines verknüpften Diensts mithilfe der Benutzeranmeldeinformationen zurück. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.getSecret('azure key vault name','secret name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.getSecret("azure key vault name","secret name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Credentials.GetSecret("azure key vault name","secret name")
```

::: zone-end

### <a name="put-secret-using-workspace-identity"></a>Geheimnis mithilfe der Arbeitsbereichsidentität festlegen

Legt das Azure Key Vault-Geheimnis für einen angegebenen Azure Key Vault-Namen, Geheimnisnamen und Namen eines verknüpften Diensts mithilfe der Arbeitsbereichsidentität fest. Stellen Sie sicher, dass Sie den Zugriff entsprechend auf [Azure Key Vault](#configure-access-to-azure-key-vault) konfigurieren.

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value','linked service name')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value","linked service name")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


### <a name="put-secret-using-user-credentials"></a>Geheimnis mithilfe der Benutzeranmeldeinformationen festlegen

Legt das Azure Key Vault-Geheimnis für einen angegebenen Azure Key Vault-Namen, Geheimnisnamen und Namen eines verknüpften Diensts mithilfe der Benutzeranmeldeinformationen fest. 

:::zone pivot = "programming-language-python"

```python
mssparkutils.credentials.putSecret('azure key vault name','secret name','secret value')
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.credentials.putSecret("azure key vault name","secret name","secret value")
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp

```

::: zone-end


## <a name="environment-utilities"></a>Hilfsprogramme der Umgebung 

Führen Sie die folgenden Befehle aus, um eine Übersicht über die verfügbaren Methoden zu erhalten:

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.help()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.help()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.Help()
```

::: zone-end

Ergebnis abrufen:
```
getUserName(): returns user name
getUserId(): returns unique user id
getJobId(): returns job id
getWorkspaceName(): returns workspace name
getPoolName(): returns Spark pool name
getClusterId(): returns cluster id
```

### <a name="get-user-name"></a>Benutzernamen abrufen

Gibt den aktuellen Benutzernamen zurück.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserName()
```

::: zone-end

### <a name="get-user-id"></a>Benutzer-ID abrufen

Gibt die aktuelle Benutzer-ID zurück.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getUserId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getUserId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetUserId()
```

::: zone-end

### <a name="get-job-id"></a>Auftrags-ID abrufen

Gibt die Auftrags-ID zurück.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getJobId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getJobId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetJobId()
```

::: zone-end

### <a name="get-workspace-name"></a>Arbeitsbereichsnamen abrufen

Gibt den Arbeitsbereichsnamen zurück.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getWorkspaceName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getWorkspaceName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetWorkspaceName()
```

::: zone-end

### <a name="get-pool-name"></a>Poolnamen abrufen

Gibt den Spark-Poolnamen zurück.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getPoolName()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getPoolName()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetPoolName()
```

::: zone-end

### <a name="get-cluster-id"></a>Cluster-ID abrufen

Gibt die aktuelle Cluster-ID zurück.

:::zone pivot = "programming-language-python"

```python
mssparkutils.env.getClusterId()
```
::: zone-end

:::zone pivot = "programming-language-scala"

```scala
mssparkutils.env.getClusterId()
```

::: zone-end

:::zone pivot = "programming-language-csharp"

```csharp
Env.GetClusterId()
```

::: zone-end

## <a name="next-steps"></a>Nächste Schritte

- [Beispiele für Synapse-Notebooks](https://github.com/Azure-Samples/Synapse/tree/master/Notebooks)
- [Schnellstart: Erstellen eines Apache Spark-Pools in Azure Synapse Analytics mithilfe von Webtools](../quickstart-apache-spark-notebook.md)
- [Was ist Apache Spark in Azure Synapse Analytics?](apache-spark-overview.md)
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)