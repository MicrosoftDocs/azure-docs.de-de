---
title: Kopieren von Daten aus HDFS mithilfe von Azure Data Factory
description: Hier erfahren Sie, wie Daten aus einer Cloud- oder lokalen HDFS-Quelle mithilfe einer Kopieraktivität in einer Azure Data Factory-Pipeline in unterstützte Senkendatenspeicher kopiert werden.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 12/18/2020
ms.author: jingwang
ms.openlocfilehash: 3ee1b1f48d91ba1245c0173d2e00a20778932d35
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100367083"
---
# <a name="copy-data-from-the-hdfs-server-by-using-azure-data-factory"></a>Kopieren von Daten von einem HDFS-Server mithilfe von Azure Data Factory

> [!div class="op_single_selector" title1="Wählen Sie die Version des Data Factory-Diensts aus, den Sie verwenden:"]
> * [Version 1](v1/data-factory-hdfs-connector.md)
> * [Aktuelle Version](connector-hdfs.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In diesem Artikel wird beschrieben, wie Daten vom HDFS-Server (Hadoop Distributed File System) kopiert werden. Informationen zu Azure Data Factory finden Sie im [Einführungsartikel](introduction.md).

## <a name="supported-capabilities"></a>Unterstützte Funktionen

Der HDFS-Connector wird für die folgenden Aktivitäten unterstützt:

- [Kopieraktivität](copy-activity-overview.md) mit [unterstützter Quellen- und Senkenmatrix](copy-activity-overview.md)
- [Lookup-Aktivität](control-flow-lookup-activity.md)
- [Delete-Aktivität](delete-activity.md)

Der HDFS-Connector unterstützt insbesondere Folgendes:

- Kopieren von Dateien mithilfe der *Windows*-Authentifizierung (Kerberos) oder der *anonymen* Authentifizierung
- Kopieren von Dateien mithilfe des *WebHDFS*-Protokolls oder der *integrierten DistCp*-Unterstützung
- Kopieren von Dateien im jeweiligen Zustand oder Analysieren oder Generieren von Dateien mit den [unterstützten Dateiformaten und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs.md)

## <a name="prerequisites"></a>Voraussetzungen

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

> [!NOTE]
> Stellen Sie sicher, dass die Integration Runtime auf *alle* folgenden Komponenten des Hadoop-Clusters zugreifen kann: [Namenknotenserver]:[Namenknotenport] und [Datenknotenserver]:[Datenknotenport]. Der Standardwert für den [Namenknotenport] ist 50070, der Standardwert für den [Datenknotenport] ist 50075.

## <a name="get-started"></a>Erste Schritte

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Die folgenden Abschnitte enthalten Details zu Eigenschaften, die zum Definieren von Data Factory-Entitäten speziell für HDFS verwendet werden.

## <a name="linked-service-properties"></a>Eigenschaften des verknüpften Diensts

Folgende Eigenschaften werden für den mit HDFS verknüpften Dienst unterstützt:

| Eigenschaft | Beschreibung | Erforderlich |
|:--- |:--- |:--- |
| type | Die *type*-Eigenschaft muss auf *Hdfs* festgelegt werden. | Ja |
| url |Die HDFS-URL |Ja |
| authenticationType | Die zulässigen Werte sind *Anonymous* oder *Windows*. <br><br> Informationen zum Einrichten Ihrer lokalen Umgebung finden Sie im Abschnitt [Verwenden der Kerberos-Authentifizierung für den HDFS-Connector](#use-kerberos-authentication-for-the-hdfs-connector). |Ja |
| userName |Diese Eigenschaft gibt den Benutzernamen für die Windows-Authentifizierung an. Geben Sie für die Kerberos-Authentifizierung **\<username>@\<domain>.com** an. |Ja (für die Windows-Authentifizierung) |
| password |Diese Eigenschaft gibt das Kennwort für die Windows-Authentifizierung an. Markieren Sie dieses Feld als SecureString, um es sicher in Data Factory zu speichern, oder [verweisen Sie auf ein in Azure Key Vault gespeichertes Geheimnis](store-credentials-in-key-vault.md). |Ja (für die Windows-Authentifizierung) |
| connectVia | Die [Integration Runtime](concepts-integration-runtime.md), die zum Herstellen einer Verbindung mit dem Datenspeicher verwendet werden soll. Weitere Informationen finden Sie im Abschnitt [Voraussetzungen](#prerequisites). Wenn die Integration Runtime nicht angegeben ist, verwendet der Dienst die Standard-Azure Integration Runtime. |Nein |

**Beispiel: Verwenden der anonymen Authentifizierung**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Anonymous",
            "userName": "hadoop"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Beispiel: Verwenden der Windows-Authentifizierung**

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "url" : "http://<machine>:50070/webhdfs/v1/",
            "authenticationType": "Windows",
            "userName": "<username>@<domain>.com (for Kerberos auth)",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Dataset-Eigenschaften

Eine vollständige Liste mit den Abschnitten und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie unter [Datasets in Azure Data Factory](concepts-datasets-linked-services.md). 

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Folgende Eigenschaften werden für HDFS unter den `location`-Einstellungen im formatbasierten Dataset unterstützt:

| Eigenschaft   | Beschreibung                                                  | Erforderlich |
| ---------- | ------------------------------------------------------------ | -------- |
| type       | Die *type*-Eigenschaft unter `location` im Dataset muss auf *HdfsLocation* festgelegt werden. | Ja      |
| folderPath | Diese Eigenschaft gibt den Pfad zum Ordner an. Wenn Sie einen Platzhalter verwenden möchten, um den Ordner zu filtern, überspringen Sie diese Einstellung, und geben Sie den Pfad in den Aktivitätsquelleneinstellungen an. | Nein       |
| fileName   | Der Name der Datei unter dem angegebenen folderPath. Wenn Sie einen Platzhalter verwenden möchten, um Dateien zu filtern, überspringen Sie diese Einstellung, und geben Sie den Dateinamen in den Aktivitätsquelleneinstellungen an. | Nein       |

**Beispiel:**

```json
{
    "name": "DelimitedTextDataset",
    "properties": {
        "type": "DelimitedText",
        "linkedServiceName": {
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, auto retrieved during authoring > ],
        "typeProperties": {
            "location": {
                "type": "HdfsLocation",
                "folderPath": "root/folder/subfolder"
            },
            "columnDelimiter": ",",
            "quoteChar": "\"",
            "firstRowAsHeader": true,
            "compressionCodec": "gzip"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Eigenschaften der Kopieraktivität

Eine vollständige Liste mit den verfügbaren Abschnitten und Eigenschaften zum Definieren von Aktivitäten finden Sie unter [Pipelines und Aktivitäten in Azure Data Factory](concepts-pipelines-activities.md). Dieser Abschnitt enthält eine Liste der Eigenschaften, die von der HDFS-Quelle unterstützt werden.

### <a name="hdfs-as-source"></a>HDFS als Quelle

[!INCLUDE [data-factory-v2-file-formats](../../includes/data-factory-v2-file-formats.md)] 

Folgende Eigenschaften werden für HDFS unter den `storeSettings`-Einstellungen in der formatbasierten Kopierquelle unterstützt:

| Eigenschaft                 | Beschreibung                                                  | Erforderlich                                      |
| ------------------------ | ------------------------------------------------------------ | --------------------------------------------- |
| type                     | Die *type*-Eigenschaft unter `storeSettings` muss auf **HdfsReadSettings** festgelegt werden. | Ja                                           |
| ***Suchen nach den zu kopierenden Dateien*** |  |  |
| OPTION 1: statischer Pfad<br> | Kopieren Sie aus dem im Dataset angegebenen Ordner oder Dateipfad. Wenn Sie alle Dateien aus einem Ordner kopieren möchten, geben Sie zusätzlich für `wildcardFileName` den Wert `*` an. |  |
| OPTION 2: Platzhalter<br>– wildcardFolderPath | Der Ordnerpfad mit Platzhalterzeichen, um Quellordner zu filtern. <br>Folgende Platzhalter sind zulässig: `*` (entspricht null [0] oder mehr Zeichen) und `?` (entspricht null [0] oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Ordnername einen Platzhalter oder dieses Escapezeichen enthält. <br>Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Nein                                            |
| OPTION 2: Platzhalter<br>– wildcardFileName | Der Dateiname mit Platzhalterzeichen unter dem angegebenen folderPath/wildcardFolderPath für das Filtern von Quelldateien. <br>Zulässige Platzhalter sind: `*` (entspricht 0 oder mehr Zeichen) und `?` (entspricht 0 oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr tatsächlicher Dateiname einen Platzhalter oder dieses Escapezeichen enthält.  Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). | Ja |
| OPTION 3: eine Liste von Dateien<br>– fileListPath | Diese Eigenschaft gibt an, dass eine angegebene Dateigruppe kopiert werden soll. Verweisen Sie auf eine Textdatei, die eine Liste der zu kopierenden Dateien enthält, und zwar eine Datei pro Zeile, mit dem relativen Pfad zu dem im Dataset konfigurierten Pfad.<br/>Wenn Sie diese Option verwenden, geben Sie keinen Dateinamen im Dataset an. Weitere Beispiele finden Sie unter [Beispiele für Dateilisten](#file-list-examples). |Nein |
| ***Zusätzliche Einstellungen*** |  | |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Wenn `recursive` auf *true* festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert oder dort erstellt. <br>Zulässige Werte sind *true* (Standard) und *false*.<br>Diese Eigenschaft gilt nicht, wenn Sie `fileListPath` konfigurieren. |Nein |
| deleteFilesAfterCompletion | Gibt an, ob die Binärdateien nach dem erfolgreichen Verschieben in den Zielspeicher aus dem Quellspeicher gelöscht werden. Die Dateien werden einzeln gelöscht, sodass Sie bei einem Fehler der Kopieraktivität feststellen werden, dass einige Dateien bereits ins Ziel kopiert und aus der Quelle gelöscht wurden, wohingegen sich andere weiter im Quellspeicher befinden. <br/>Diese Eigenschaft ist nur im Szenario zum Kopieren von Binärdateien gültig. Standardwert: FALSE. |Nein |
| modifiedDatetimeStart    | Die Dateien werden anhand des Attributs *Zuletzt bearbeitet* gefiltert. <br>Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung im Bereich zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format *2018-12-01T05:00:00Z* angewandt. <br> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` einen datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.<br/>Diese Eigenschaft gilt nicht, wenn Sie `fileListPath` konfigurieren. | Nein                                            |
| modifiedDatetimeEnd      | Wie oben.  
| enablePartitionDiscovery | Geben Sie bei partitionierten Dateien an, ob die Partitionen anhand des Dateipfads analysiert und als zusätzliche Quellspalten hinzugefügt werden sollen.<br/>Zulässige Werte sind **false** (Standard) und **true**. | Nein                                            |
| partitionRootPath | Wenn die Partitionsermittlung aktiviert ist, geben Sie den absoluten Stammpfad an, um partitionierte Ordner als Datenspalten zu lesen.<br/><br/>Ohne Angabe gilt standardmäßig Folgendes:<br/>- Wenn Sie den Dateipfad im Dataset oder die Liste der Dateien in der Quelle verwenden, ist der Partitionsstammpfad der im Dataset konfigurierte Pfad.<br/>Wenn Sie einen Platzhalterordnerfilter verwenden, ist der Stammpfad der Partition der Unterpfad vor dem ersten Platzhalter.<br/><br/>Angenommen, Sie konfigurieren den Pfad im Dataset als „root/folder/year=2020/month=08/day=27“:<br/>- Wenn Sie den Stammpfad der Partition als „root/folder/year=2020“ angeben, generiert die Kopieraktivität zusätzlich zu den Spalten in den Dateien die beiden weiteren Spalten `month` und `day` mit den Werten „08“ bzw. „27“.<br/>- Wenn kein Stammpfad für die Partition angegeben ist, wird keine zusätzliche Spalte generiert. | Nein                                            |
| maxConcurrentConnections | Diese Eigenschaft gibt die Anzahl von Verbindungen an, die gleichzeitig mit einem Speicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein                                            |
| ***DistCp-Einstellungen*** |  | |
| distcpSettings | Diese Eigenschaftengruppe sollte bei Verwendung von HDFS DistCp verwendet werden. | Nein |
| resourceManagerEndpoint | Der YARN-Endpunkt (Yet Another Resource Negotiator) | Ja, wenn DistCp verwendet wird |
| tempScriptPath | Diese Eigenschaft gibt einen Ordnerpfad zum Speichern der temporären DistCp-Befehlsskripts an. Die Skriptdatei wird von Data Factory generiert und nach Abschluss des Kopierauftrags entfernt. | Ja, wenn DistCp verwendet wird |
| distcpOptions | Zusätzliche für den DistCp-Befehl bereitgestellte Optionen | Nein |

**Beispiel:**

```json
"activities":[
    {
        "name": "CopyFromHDFS",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Delimited text input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "DelimitedTextSource",
                "formatSettings":{
                    "type": "DelimitedTextReadSettings",
                    "skipLineCount": 10
                },
                "storeSettings":{
                    "type": "HdfsReadSettings",
                    "recursive": true,
                    "distcpSettings": {
                        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
                        "tempScriptPath": "/usr/hadoop/tempscript",
                        "distcpOptions": "-m 100"
                    }
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="folder-and-file-filter-examples"></a>Beispiele für Ordner- und Dateifilter

Dieser Abschnitt beschreibt das sich ergebende Verhalten, wenn Sie für den Ordnerpfad und den Dateinamen einen Platzhalterfilter verwenden.

| folderPath | fileName             | recursive | Quellordnerstruktur und Filterergebnis (Dateien mit **Fettformatierung** werden abgerufen.) |
| :--------- | :------------------- | :-------- | :----------------------------------------------------------- |
| `Folder*`  | (empty, use default) | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*`  | (empty, use default) | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei2.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei4.json**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*`  | `*.csv`              | false     | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3.csv<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5.csv<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |
| `Folder*`  | `*.csv`              | true      | FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>AndererOrdnerB<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei6.csv |

### <a name="file-list-examples"></a>Beispiele für Dateilisten

In diesem Abschnitt wird das aus der Verwendung eines Dateilistenpfads in der Quelle einer Kopieraktivität resultierende Verhalten beschrieben. Es wird angenommen, dass Sie die folgende Quellordnerstruktur verwenden und die Dateien kopieren möchten, deren Namen fett formatiert sind:

| Beispielquellstruktur                                      | Inhalt in „FileListToCopy.txt“                             | Azure Data Factory-Konfiguration                                            |
| ------------------------------------------------------------ | --------------------------------------------------------- | ------------------------------------------------------------ |
| root<br/>&nbsp;&nbsp;&nbsp;&nbsp;FolderA<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei1.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei2.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei3.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4.json<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**Datei5.csv**<br/>&nbsp;&nbsp;&nbsp;&nbsp;Metadaten<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;FileListToCopy.txt | Datei1.csv<br>Unterordner1/Datei3.csv<br>Unterordner1/Datei5.csv | **Im Dataset:**<br>– Ordnerpfad: `root/FolderA`<br><br>**In der Quelle der Kopieraktivität:**<br>– Dateilistenpfad: `root/Metadata/FileListToCopy.txt` <br><br>Der Dateilistenpfad verweist auf eine Textdatei im selben Datenspeicher, der eine Liste der zu kopierenden Dateien enthält, und zwar eine Datei pro Zeile, mit dem relativen Pfad zu dem im Dataset konfigurierten Pfad. |

## <a name="use-distcp-to-copy-data-from-hdfs"></a>Kopieren von Daten aus einem HDFS mithilfe von DistCp

[DistCp](https://hadoop.apache.org/docs/current3/hadoop-distcp/DistCp.html) ist ein natives Hadoop-Befehlszeilentool, um verteilte Kopiervorgänge in einem Hadoop-Cluster durchzuführen. Wenn Sie einen Befehl in DistCp ausführen, werden zunächst alle zu kopierenden Dateien aufgelistet und dann mehrere Zuordnungsaufträge im Hadoop-Cluster erstellt. Jeder Zuordnungsauftrag führt eine Binärkopieraktivität von der Quelle in die Senke durch.

Die Kopieraktivität unterstützt mithilfe von DistCp das Kopieren von Dateien im jeweiligen Zustand in Azure Blob Storage (einschließlich der [gestaffelten Kopie](copy-activity-performance.md)) oder einen Azure Data Lake-Speicher. In diesem Fall kann DistCp von der Leistung Ihres Clusters profitieren, anstatt über die selbstgehostete Integration Runtime ausgeführt zu werden. Durch die Verwendung von DistCp erreichen Sie einen besseren Kopierdurchsatz, insbesondere dann, wenn Sie über einen äußerst leistungsstarken Cluster verfügen. Basierend auf der Konfiguration in Ihrer Data Factory-Instanz erstellt die Kopieraktivität automatisch einen DistCp-Befehl, sendet diesen an Ihren Hadoop-Cluster und überwacht den Kopierstatus.

### <a name="prerequisites"></a>Voraussetzungen

Wenn Sie mithilfe von DistCp Dateien im jeweiligen Zustand aus HDFS in Azure Blob Storage (einschließlich der gestaffelten Kopie) oder Azure Data Lake Store kopieren möchten, müssen Sie sicherstellen, dass Ihr Hadoop-Cluster die folgenden Anforderungen erfüllt:

* Die Dienste MapReduce und YARN sind aktiviert.  
* Die YARN-Version ist 2.5 oder höher.  
* Der HDFS-Server ist mit Ihrem Zieldatenspeicher integriert: **Azure Blob Storage** oder **Azure Data Lake Storage (ADLS Gen1)** : 

    - Das Azure-Blobdateisystem wird nativ ab Hadoop 2.7 unterstützt. Sie müssen lediglich den JAR-Pfad bei der Konfiguration der Hadoop-Umgebung angeben.
    - Das Azure Data Lake Store-Dateisystem wird ab Hadoop 3.0.0-alpha1 verpackt. Wenn Sie eine frühere Version des Hadoop-Clusters verwenden, müssen Sie zu Azure Data Lake Storage gehörige JAR-Pakete (azure-datalake-store.jar) von [hier](https://hadoop.apache.org/releases.html) aus manuell in den Cluster importieren und den Pfad der JAR-Datei bei der Konfiguration der Hadoop-Umgebung angeben.

* Bereiten Sie einen temporären Ordner in HDFS vor. In diesem temporären Ordner wird ein DistCp-Shellskript gespeichert, sodass Speicherplatz im KB-Bereich belegt wird.
* Stellen Sie sicher, dass das Benutzerkonto, das im mit HDFS verknüpften Dienst bereitgestellt wird, über die Berechtigungen für Folgendes verfügt:
   * Übermitteln einer Anwendung in YARN
   * Erstellen eines Unterordners und Lesen/Schreiben von Dateien im temporären Ordner

### <a name="configurations"></a>Configurations

Informationen zu DistCp-bezogenen Konfigurationen sowie Beispiele finden Sie im Abschnitt [HDFS als Quelle](#hdfs-as-source).

## <a name="use-kerberos-authentication-for-the-hdfs-connector"></a>Verwenden der Kerberos-Authentifizierung für den HDFS-Connector

Es gibt zwei Optionen zum Einrichten der lokalen Umgebung zur Verwendung der Kerberos-Authentifizierung für den HDFS-Connector. Wählen Sie die Option, die für Ihren Fall besser geeignet ist.
* Option 1: [Beitreten zum Computer mit der selbstgehosteten Integration Runtime im Kerberosbereich](#kerberos-join-realm)
* Option 2: [Aktivieren von gegenseitiger Vertrauensstellung zwischen der Windows-Domäne und dem Kerberos-Bereich](#kerberos-mutual-trust)

Stellen Sie für beide Optionen sicher, dass Sie webhdfs für Hadoop-Cluster aktivieren:

1. Erstellen Sie den HTTP-Prinzipal und die Schlüsseltabelle für webhdfs.

    > [!IMPORTANT]
    > Der HTTP-Kerberos-Prinzipal muss gemäß der Kerberos-HTTP-SPNEGO-Spezifikation mit „**HTTP/** “ beginnen. Weitere Informationen dazu finden Sie [hier](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#HDFS_Configuration_Options).

    ```bash
    Kadmin> addprinc -randkey HTTP/<namenode hostname>@<REALM.COM>
    Kadmin> ktadd -k /etc/security/keytab/spnego.service.keytab HTTP/<namenode hostname>@<REALM.COM>
    ```

2. HDFS-Konfigurationsoptionen: Fügen Sie die folgenden drei Eigenschaften in `hdfs-site.xml` hinzu.
    ```xml
    <property>
        <name>dfs.webhdfs.enabled</name>
        <value>true</value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.principal</name>
        <value>HTTP/_HOST@<REALM.COM></value>
    </property>
    <property>
        <name>dfs.web.authentication.kerberos.keytab</name>
        <value>/etc/security/keytab/spnego.service.keytab</value>
    </property>
    ```

### <a name="option-1-join-a-self-hosted-integration-runtime-machine-in-the-kerberos-realm"></a><a name="kerberos-join-realm"></a>Option 1: Beitreten zum Computer mit der selbstgehosteten Integration Runtime im Kerberosbereich

#### <a name="requirements"></a>Requirements (Anforderungen)

* Der Computer mit der selbstgehosteten Integration Runtime muss dem Kerberosbereich beitreten und darf in keine Windows-Domäne eingebunden sein.

#### <a name="how-to-configure"></a>Vorgehensweise zur Konfiguration

**KDC-Server:**

Erstellen Sie einen Prinzipal für Azure Data Factory, um das Kennwort zu verwenden und anzugeben.

> [!IMPORTANT]
> Der Benutzername darf nicht den Hostnamen enthalten.

```bash
Kadmin> addprinc <username>@<REALM.COM>
```

**Auf dem Computer mit der selbstgehosteten Integration Runtime:**

1.  Führen Sie das Ksetup-Hilfsprogramm aus, um den Kerberos-KDC-Server (Key Distribution Center) und -Bereich zu konfigurieren.

    Der Computer muss als Mitglied einer Arbeitsgruppe konfiguriert werden, da sich Kerberos-Bereiche von Windows-Domänen unterscheiden. Diese Konfiguration erreichen Sie, indem Sie mithilfe der folgenden Befehle den Kerberosbereich festlegen und einen KDC-Server hinzufügen. Ersetzen Sie *REALM.COM* durch Ihren eigenen Bereichsnamen.

    ```cmd
    C:> Ksetup /setdomain REALM.COM
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    ```

    Nachdem Sie diese Befehle ausgeführt haben, starten Sie den Computer neu.

2.  Überprüfen Sie die Konfiguration mit dem `Ksetup`-Befehl. Die Ausgabe sollte wie folgt sein:

    ```cmd
    C:> Ksetup
    default realm = REALM.COM (external)
    REALM.com:
        kdc = <your_kdc_server_address>
    ```

**In Ihrer Data Factory-Instanz:**

* Konfigurieren Sie den HDFS-Connector mithilfe der Windows-Authentifizierung zusammen mit dem Namen und Kennwort Ihres Kerberos-Prinzipals, um eine Verbindung mit der HDFS-Datenquelle herzustellen. Genaue Informationen zur Konfiguration finden Sie im Abschnitt [Eigenschaften des verknüpften Diensts](#linked-service-properties).

### <a name="option-2-enable-mutual-trust-between-the-windows-domain-and-the-kerberos-realm"></a><a name="kerberos-mutual-trust"></a>Option 2: Aktivieren von gegenseitiger Vertrauensstellung zwischen der Windows-Domäne und dem Kerberos-Bereich

#### <a name="requirements"></a>Requirements (Anforderungen)

*   Der Computer mit der selbstgehosteten Integration Runtime muss einer Windows-Domäne beitreten.
*   Sie benötigen die Berechtigung zum Aktualisieren der Einstellungen des Domänencontrollers.

#### <a name="how-to-configure"></a>Vorgehensweise zur Konfiguration

> [!NOTE]
> Ersetzen Sie REALM.COM und AD.COM im folgenden Tutorial durch Ihren eigenen Bereichsnamen und Domänencontroller.

**KDC-Server:**

1. Bearbeiten Sie die KDC-Konfiguration in der Datei *krb5.conf* mithilfe der folgenden Konfigurationsvorlage so, dass KDC der Windows-Domäne vertraut. Standardmäßig befindet sich die Konfiguration unter */etc/krb5.conf*.

   ```config
   [logging]
    default = FILE:/var/log/krb5libs.log
    kdc = FILE:/var/log/krb5kdc.log
    admin_server = FILE:/var/log/kadmind.log
            
   [libdefaults]
    default_realm = REALM.COM
    dns_lookup_realm = false
    dns_lookup_kdc = false
    ticket_lifetime = 24h
    renew_lifetime = 7d
    forwardable = true
            
   [realms]
    REALM.COM = {
     kdc = node.REALM.COM
     admin_server = node.REALM.COM
    }
   AD.COM = {
    kdc = windc.ad.com
    admin_server = windc.ad.com
   }
            
   [domain_realm]
    .REALM.COM = REALM.COM
    REALM.COM = REALM.COM
    .ad.com = AD.COM
    ad.com = AD.COM
            
   [capaths]
    AD.COM = {
     REALM.COM = .
    }
    ```

   Nachdem Sie die Datei konfiguriert haben, starten Sie den KDC-Dienst neu.

2. Bereiten Sie mit dem folgenden Befehl einen Prinzipal namens *krbtgt/REALM.COM\@AD.COM* auf dem KDC-Server vor:

    ```cmd
    Kadmin> addprinc krbtgt/REALM.COM@AD.COM
    ```

3. Fügen Sie `RULE:[1:$1@$0](.*\@AD.COM)s/\@.*//` zur HDFS-Dienstkonfigurationsdatei *hadoop.security.auth_to_local* hinzu.

**Domänencontroller:**

1.  Führen Sie die folgenden `Ksetup`-Befehle aus, um einen Bereichseintrag hinzuzufügen:

    ```cmd
    C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
    C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
    ```

2.  Stellen Sie Vertrauensstellung zwischen der Windows-Domäne und dem Kerberos-Bereich her. [password] ist das Kennwort für den Prinzipal *krbtgt/REALM.COM\@AD.COM*.

    ```cmd
    C:> netdom trust REALM.COM /Domain: AD.COM /add /realm /password:[password]
    ```

3.  Wählen Sie den in Kerberos verwendeten Verschlüsselungsalgorithmus aus.

    a. Klicken Sie auf **Server Manager** > **Group Policy Management** > **Domain** > **Group Policy Objects** > **Default or Active Domain Policy** (Server-Manager > Gruppenrichtlinienverwaltung > Domäne > Gruppenrichtlinienobjekte > Standard- oder aktive Domänenrichtlinie), und klicken Sie dann auf **Bearbeiten**.

    b. Klicken Sie im Bereich **Gruppenrichtlinienverwaltungs-Editor** auf **Computerkonfiguration** > **Richtlinien** > **Windows-Einstellungen** > **Sicherheitseinstellungen** > **Lokale Richtlinien** > **Sicherheitsoptionen**, und konfigurieren Sie die Einstellung **Netzwerksicherheit: Für Kerberos zulässige Verschlüsselungstypen konfigurieren**.

    c. Wählen Sie den Verschlüsselungsalgorithmus aus, den Sie beim Herstellen einer Verbindung mit KDC verwenden möchten. Sie können alle Optionen auswählen.

    ![Screenshot: Bereich „Netzwerksicherheit: Für Kerberos zulässige Verschlüsselungstypen konfigurieren“](media/connector-hdfs/config-encryption-types-for-kerberos.png)

    d. Verwenden Sie den Befehl `Ksetup`, um den Verschlüsselungsalgorithmus anzugeben, der in dem bestimmten Bereich verwendet werden soll.

    ```cmd
    C:> ksetup /SetEncTypeAttr REALM.COM DES-CBC-CRC DES-CBC-MD5 RC4-HMAC-MD5 AES128-CTS-HMAC-SHA1-96 AES256-CTS-HMAC-SHA1-96
    ```

4.  Erstellen Sie die Zuordnung zwischen dem Domänenkonto und dem Kerberos-Prinzipal, sodass Sie den Kerberos-Prinzipal in der Windows-Domäne verwenden können.

    a. Klicken Sie auf **Verwaltung** > **Active Directory-Benutzer und -Computer**.

    b. Konfigurieren Sie erweiterte Funktionen unter **Ansicht** > **Erweiterte Funktionen**.

    c. Klicken Sie im Bereich **Erweiterte Features** mit der rechten Maustaste auf das Konto, für das Sie Zuordnungen erstellen möchten, und klicken Sie im Bereich **Namenszuordnungen** auf die Registerkarte **Kerberos-Namen**.

    d. Fügen Sie einen Prinzipal aus dem Bereich hinzu.

       ![Bereich „Sicherheitsidentitätszuordnung“](media/connector-hdfs/map-security-identity.png)

**Auf dem Computer mit der selbstgehosteten Integration Runtime:**

* Führen Sie die folgenden `Ksetup`-Befehle aus, um einen Bereichseintrag hinzuzufügen.

   ```cmd
   C:> Ksetup /addkdc REALM.COM <your_kdc_server_address>
   C:> ksetup /addhosttorealmmap HDFS-service-FQDN REALM.COM
   ```

**In Ihrer Data Factory-Instanz:**

* Konfigurieren Sie den HDFS-Connector mithilfe der Windows-Authentifizierung zusammen mit Ihrem Domänenkonto oder Ihrem Kerberos-Prinzipal, um eine Verbindung mit der HDFS-Datenquelle herzustellen. Genaue Informationen zur Konfiguration finden Sie im Abschnitt [Eigenschaften des verknüpften Diensts](#linked-service-properties).

## <a name="lookup-activity-properties"></a>Eigenschaften der Lookup-Aktivität

Weitere Informationen zu den Eigenschaften der Lookup-Aktivität finden Sie unter [Lookup-Aktivität in Azure Data Factory](control-flow-lookup-activity.md).

## <a name="delete-activity-properties"></a>Eigenschaften der Delete-Aktivität

Weitere Informationen zu den Eigenschaften der Löschaktivität finden Sie unter [Löschaktivität in Azure Data Factory](delete-activity.md).

## <a name="legacy-models"></a>Legacy-Modelle

>[!NOTE]
>Die folgenden Modelle werden aus Gründen der Abwärtskompatibilität weiterhin unverändert unterstützt. Es wird empfohlen, das zuvor beschriebene neue Modell zu verwenden, da auf der Erstellungsbenutzeroberfläche von Azure Data Factory nun das neue Modell generiert wird.

### <a name="legacy-dataset-model"></a>Legacy-Datasetmodell

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die *type*-Eigenschaft des Datasets muss auf *FileShare* festgelegt werden. |Ja |
| folderPath | Diese Eigenschaft gibt den Pfad zum Ordner an. Ein Platzhalterfilter wird unterstützt. Zulässige Platzhalter sind: `*` (entspricht 0 oder mehr Zeichen) und `?` (entspricht 0 oder einem einzelnen Zeichen). Verwenden Sie `^` als Escapezeichen, wenn Ihr tatsächlicher Dateiname einen Platzhalter oder dieses Escapezeichen enthält. <br/><br/>Beispiele: Stammordner/Unterordner/. Weitere Beispiele finden Sie unter [Beispiele für Ordner- und Dateifilter](#folder-and-file-filter-examples). |Ja |
| fileName |  Diese Eigenschaft gibt den Namen oder Platzhalterfilter für die Dateien unter dem angegebenen Wert für „folderPath“ an. Wenn Sie für diese Eigenschaft keinen Wert angeben, verweist das Dataset auf alle Dateien im Ordner. <br/><br/>Für Filter sind folgende Platzhalter zulässig: `*` (entspricht 0 oder mehr Zeichen) und `?` (entspricht 0 oder einem einzelnen Zeichen).<br/>- Beispiel 1: `"fileName": "*.csv"`<br/>- Beispiel 2: `"fileName": "???20180427.txt"`<br/>Verwenden Sie `^` als Escapezeichen, wenn der tatsächliche Ordnername einen Platzhalter oder dieses Escapezeichen enthält. |Nein |
| modifiedDatetimeStart | Die Dateien werden anhand des Attributs *Zuletzt bearbeitet* gefiltert. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung im Bereich zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format *2018-12-01T05:00:00Z* angewandt. <br/><br/> Beachten Sie, dass die generelle Leistung der Datenverschiebung beeinträchtigt wird, wenn Sie diese Einstellung aktivieren und einen Dateifilter auf eine große Anzahl von Dateien anwenden möchten. <br/><br/> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` einen datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein |
| modifiedDatetimeEnd | Die Dateien werden anhand des Attributs *Zuletzt bearbeitet* gefiltert. Die Dateien werden ausgewählt, wenn der Zeitpunkt der letzten Änderung im Bereich zwischen `modifiedDatetimeStart` und `modifiedDatetimeEnd` liegt. Die Zeit wird auf die UTC-Zeitzone im Format *2018-12-01T05:00:00Z* angewandt. <br/><br/> Beachten Sie, dass die generelle Leistung der Datenverschiebung beeinträchtigt wird, wenn Sie diese Einstellung aktivieren und einen Dateifilter auf eine große Anzahl von Dateien anwenden möchten. <br/><br/> Die Eigenschaften können NULL sein, was bedeutet, dass kein Dateiattributfilter auf das Dataset angewandt wird.  Wenn `modifiedDatetimeStart` einen datetime-Wert aufweist, aber `modifiedDatetimeEnd` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung größer oder gleich dem datetime-Wert ist.  Wenn `modifiedDatetimeEnd` einen datetime-Wert aufweist, aber `modifiedDatetimeStart` NULL ist, bedeutet dies, dass die Dateien ausgewählt werden, deren Attribut für die letzte Änderung kleiner als der datetime-Wert ist.| Nein |
| format | Wenn Sie Dateien unverändert zwischen dateibasierten Speichern kopieren möchten (binäre Kopie), überspringen Sie den Formatabschnitt in den Definitionen der Eingabe- und Ausgabedatasets.<br/><br/>Für das Analysieren von Dateien mit einem bestimmten Format werden die folgenden Dateiformattypen unterstützt: *TextFormat*, *JsonFormat*, *AvroFormat*, *OrcFormat* und *ParquetFormat*. Sie müssen die *type* -Eigenschaft unter „format“ auf einen dieser Werte festlegen. Weitere Informationen finden Sie in den Abschnitten [Textformat](supported-file-formats-and-compression-codecs-legacy.md#text-format), [JSON-Format](supported-file-formats-and-compression-codecs-legacy.md#json-format), [Avro-Format](supported-file-formats-and-compression-codecs-legacy.md#avro-format), [ORC-Format](supported-file-formats-and-compression-codecs-legacy.md#orc-format) und [Parquet-Format](supported-file-formats-and-compression-codecs-legacy.md#parquet-format). |Nein (nur für Szenarien mit Binärkopien) |
| compression | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Weitere Informationen finden Sie unter [Unterstützte Dateiformate und Codecs für die Komprimierung](supported-file-formats-and-compression-codecs-legacy.md#compression-support).<br/>Folgende Typen werden unterstützt: *Gzip*, *Deflate*, *Bzip2* und *ZipDeflate*.<br/>Folgende Ebenen werden unterstützt: *Optimal* und *Fastest*. |Nein |

>[!TIP]
>Wenn Sie alle Dateien eines Ordners kopieren möchten, geben Sie nur **folderPath** an.<br>Wenn Sie eine einzelne Datei mit einem bestimmten Namen kopieren möchten, geben Sie **folderPath** mit der Ordnerkomponente und **fileName** mit dem Dateinamen an.<br>Wenn Sie eine Teilmenge der Dateien eines Ordners kopieren möchten, geben Sie **folderPath** mit dem Ordner und **fileName** mit dem Platzhalterfilter an.

**Beispiel:**

```json
{
    "name": "HDFSDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName":{
            "referenceName": "<HDFS linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "folderPath": "folder/subfolder/",
            "fileName": "*",
            "modifiedDatetimeStart": "2018-12-01T05:00:00Z",
            "modifiedDatetimeEnd": "2018-12-01T06:00:00Z",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ",",
                "rowDelimiter": "\n"
            },
            "compression": {
                "type": "GZip",
                "level": "Optimal"
            }
        }
    }
}
```

### <a name="legacy-copy-activity-source-model"></a>Legacy-Quellenmodell der Kopieraktivität

| Eigenschaft | BESCHREIBUNG | Erforderlich |
|:--- |:--- |:--- |
| type | Die *type*-Eigenschaft der Quelle der Kopieraktivität muss auf *HdfsSource* festgelegt werden. |Ja |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. Wenn „recursive“ auf *true* festgelegt ist und es sich bei der Senke um einen dateibasierten Speicher handelt, wird ein leerer Ordner oder Unterordner nicht in die Senke kopiert oder dort erstellt.<br/>Zulässige Werte sind *true* (Standard) und *false*. | Nein |
| distcpSettings | Dies ist die Eigenschaftengruppe bei der Verwendung von HDFS DistCp. | Nein |
| resourceManagerEndpoint | Der YARN-Ressourcen-Manager-Endpunkt | Ja, wenn DistCp verwendet wird |
| tempScriptPath | Diese Eigenschaft gibt einen Ordnerpfad zum Speichern der temporären DistCp-Befehlsskripts an. Die Skriptdatei wird von Data Factory generiert und nach Abschluss des Kopierauftrags entfernt. | Ja, wenn DistCp verwendet wird |
| distcpOptions | Mit dieser Eigenschaft werden zusätzliche Optionen für den DistCp-Befehl bereitgestellt. | Nein |
| maxConcurrentConnections | Diese Eigenschaft gibt die Anzahl von Verbindungen an, die gleichzeitig mit einem Speicher hergestellt werden können. Geben Sie diesen Wert nur an, wenn Sie die gleichzeitigen Verbindungen mit dem Datenspeicher begrenzen möchten. | Nein |

**Beispiel: HDFS-Quelle in der Kopieraktivität unter Verwendung von DistCp**

```json
"source": {
    "type": "HdfsSource",
    "distcpSettings": {
        "resourceManagerEndpoint": "resourcemanagerendpoint:8088",
        "tempScriptPath": "/usr/hadoop/tempscript",
        "distcpOptions": "-m 100"
    }
}
```

## <a name="next-steps"></a>Nächste Schritte
Eine Liste der Datenspeicher, die als Quellen und Senken für die Kopieraktivität in Azure Data Factory unterstützt werden, finden Sie unter [Unterstützte Datenspeicher und Formate](copy-activity-overview.md#supported-data-stores-and-formats).
