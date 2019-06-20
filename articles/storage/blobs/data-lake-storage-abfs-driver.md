---
title: Der Azure-Blobdateisystemtreiber für Azure Data Lake Storage Gen2
description: Der ABFS-Hadoop-Dateisystemtreiber
services: storage
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 57cda4d07315c6c37c2ce51f530fb081949b628c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64939502"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Der Azure-Blobdateisystemtreiber (ABFS): Ein dedizierter Azure Storage-Treiber für Hadoop

Eine der Hauptmethoden für den Zugriff auf Daten in Azure Data Lake Storage Gen2 ist das [Hadoop-Dateisystem](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Mit Data Lake Storage Gen2 erhalten Benutzer von Azure Blob Storage Zugriff auf einen neuen Treiber, den Azure-Blobdateisystem- oder `ABFS`-Treiber. ABFS ist Teil von Apache Hadoop und in vielen kommerziellen Hadoop-Distributionen enthalten. Dank dieses Treibers können zahlreiche Anwendungen und Frameworks ganz ohne Code, der explizit auf den Azure Blob Storage-Dienst verweist, auf Daten in Data Lake Storage Gen2 zugreifen.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Vorherige Funktion: Der Windows Azure Storage Blob-Treiber

Die ursprüngliche Unterstützung von Azure Blob Storage wurde durch den Windows Azure Storage Blob-Treiber (oder [WASB-Treiber](https://hadoop.apache.org/docs/current/hadoop-azure/index.html)) bereitgestellt. Dieser Treiber hatte die komplexe Aufgabe, die für die Hadoop-Dateisystemschnittstelle erforderliche Dateisystemsemantik der objektspeicherorientierten Schnittstelle zuzuordnen, die von Azure Blob Storage verfügbar gemacht wird. Dieser Treiber unterstützt das Modell zwar weiterhin und bietet blitzschnellen Zugriff auf Daten in Blobs, enthält aber auch eine große Menge an Code für die entsprechende Zuordnung, was ihn nicht gerade wartungsfreundlich macht. Darüber hinaus muss der Treiber bei einigen Vorgängen (beispielsweise bei [FileSystem.rename()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) und [FileSystem.delete()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) für Verzeichnisse) eine große Anzahl von Vorgängen ausführen, da Objektspeicher keine Verzeichnisse unterstützen. Dies wirkt sich nachteilig auf die Leistung aus. Der ABFS-Treiber wurde entwickelt, um die Mängel in WASB zu bewältigen.

## <a name="the-azure-blob-file-system-driver"></a>Der Azure-Blobdateisystemtreiber

Die [Azure Data Lake Storage-REST-Schnittstelle](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) unterstützt Dateisystemsemantik über Azure Blob Storage. Da das Hadoop-Dateisystem die gleiche Semantik unterstützt, ist keine komplexe Zuordnung im Treiber erforderlich. Der ABFS-Treiber (Azure Blob File System, Azure-Blobdateisystem) ist daher lediglich ein Client-Shim für die REST-API.

Es gibt jedoch einige Funktionen, die der Treiber weiterhin ausführen muss:

### <a name="uri-scheme-to-reference-data"></a>URI-Schema für Datenverweise

Genau wie bei anderen Dateisystemimplementierungen in Hadoop definiert der ABFS-Treiber ein eigenes URI-Schema, um eine eindeutige Adressierung von Ressourcen (Verzeichnisse und Dateien) zu ermöglichen. Das URI-Schema ist unter [Use the Azure Data Lake Storage Gen2 URI](./data-lake-storage-introduction-abfs-uri.md) (Verwenden des Azure Data Lake Storage Gen2-URI) dokumentiert. Struktur des URI: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Mit dem obigen URI-Format können standardmäßige Hadoop-Tools und -Frameworks verwendet werden, um auf folgende Ressourcen zu verweisen:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data 
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/ 
```

Intern übersetzt der ABFS-Treiber die im URI angegebenen Ressourcen in Dateien und Verzeichnisse und führt Aufrufe für die Azure Data Lake Storage-REST-API mit diesen Verweisen aus.

### <a name="authentication"></a>Authentication

Der ABFS-Treiber unterstützt zwei Formen der Authentifizierung, sodass die Hadoop-Anwendung sicher auf Ressourcen in einem Data Lake Storage Gen2-fähigen Konto zugreifen kann. Einzelheiten zu den verfügbaren Authentifizierungsschemas finden Sie im [Azure Storage-Sicherheitsleitfaden](../common/storage-security-guide.md). Sie lauten wie folgt:

- **Gemeinsam verwendeter Schlüssel:** Ermöglicht Benutzern den Zugriff auf ALLE Ressourcen im Konto. Der Schlüssel ist verschlüsselt und wird in der Hadoop-Konfiguration gespeichert.

- **Azure Active Directory OAuth-Bearertoken:** Azure AD-Bearertoken werden durch den Treiber abgerufen und aktualisiert, indem die Identität des Endbenutzers oder ein konfigurierter Dienstprinzipal verwendet wird. Mit diesem Authentifizierungsmodell wird jeder Zugriff auf der Basis der Identität des angegebenen Tokens pro Aufruf autorisiert und anhand der zugewiesenen POSIX-ACL (Zugriffssteuerungsliste) ausgewertet.

### <a name="configuration"></a>Konfiguration

Die gesamte Konfiguration für den ABFS-Treiber ist in der Konfigurationsdatei <code>core-site.xml</code> gespeichert. Bei Hadoop-Distributionen mit [Ambari](https://ambari.apache.org/) kann die Konfiguration auch über das Webportal oder über die Ambari-REST-API verwaltet werden.

Details zu allen unterstützten Konfigurationseinträgen finden Sie in der [offiziellen Hadoop-Dokumentation](https://hadoop.apache.org/docs/current/hadoop-azure/index.html).

### <a name="hadoop-documentation"></a>Hadoop-Dokumentation

Der ABFS-Treiber ist vollständig in der [offiziellen Hadoop-Dokumentation](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) dokumentiert.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen eines Azure Databricks-Clusters](./data-lake-storage-quickstart-create-databricks-account.md)
- [Verwenden des Azure Data Lake Storage Gen2-URI](./data-lake-storage-introduction-abfs-uri.md)
