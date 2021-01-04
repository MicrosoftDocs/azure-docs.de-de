---
title: Verwenden von SQL-Datenbank-DACPAC- und -BACPAC-Paketen – Azure SQL Edge
description: Informationen zur Verwendung von DACPAC- und BACPAC-Dateien in Azure SQL Edge
keywords: SQL Edge, SqlPackage
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/03/2020
ms.openlocfilehash: 40bd0eda16f9f96dd356eef900369ab25854e9f9
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93392247"
---
# <a name="sql-database-dacpac-and-bacpac-packages-in-sql-edge"></a>SQL-Datenbank-DACPAC- und -BACPAC-Pakete in SQL Edge

Azure SQL Edge ist eine optimierte relationale Datenbank-Engine für IoT- und Edge-Bereitstellungen. Sie basiert auf den aktuellen Versionen der Microsoft SQL Server-Datenbank-Engine, die branchenführende Leistung, Sicherheit und Abfrageverarbeitung bietet. Zusammen mit den branchenführenden Funktionen für die Verwaltung relationaler Datenbanken von SQL Server bietet Azure SQL Edge integrierte Streamingfunktionen für Echtzeitanalysen und komplexe Ereignisverarbeitung.

Azure SQL Edge bietet einen nativen Mechanismus, mit dem Sie ein [SQL-Datenbank-DACPAC- und -BACPAC](/sql/relational-databases/data-tier-applications/data-tier-applications)-Paket während oder nach der Bereitstellung von SQL Edge bereitstellen können.

SQL-Datenbank-DACPAC- und -BACPAC-Pakete können über die Umgebungsvariable `MSSQL_PACKAGE` in SQL Edge bereitgestellt werden. Diese Umgebungsvariable kann mit den folgenden Elementen konfiguriert werden.  
- Dem Speicherort eines lokalen Ordners in dem SQL-Container, der die DACPAC- und BACPAC-Dateien enthält. Dieser Ordner kann mithilfe von Bereitstellungspunkten oder Datenvolumecontainern einem Hostvolume zugeordnet werden. 
- Einem lokalen Dateipfad innerhalb des SQL-Containers, der der DACPAC- oder BACPAC-Datei zugeordnet ist. Dieser Dateipfad kann mithilfe von Bereitstellungspunkten oder Datenvolumecontainern einem Hostvolume zugeordnet werden. 
- Einem lokalen Dateipfad innerhalb des SQL-Containers, der einer ZIP-Datei zugeordnet ist, die die DACPAC- oder BACPAC-Dateien enthält. Dieser Dateipfad kann mithilfe von Bereitstellungspunkten oder Datenvolumecontainern einem Hostvolume zugeordnet werden. 
- Einer Azure-Blob-SAS-URL für eine ZIP-Datei, die die DACPAC- und BACPAC-Dateien enthält
- Einer Azure-Blob-SAS-URL für eine DACPAC- oder BACPAC-Datei 

## <a name="use-a-sql-database-dac-package-with-sql-edge"></a>Verwenden eines SQL-Datenbank-DAC-Pakets mit SQL Edge

Führen Sie die folgenden Schritte aus, um ein SQL-Datenbank-DAC-Paket `(*.dacpac)` oder eine BACPAC-Datei `(*.bacpac)` mit Azure Blob Storage und einer ZIP-Datei bereitzustellen (oder zu importieren). 

1. Erstellen/extrahieren Sie ein DAC-Paket, oder exportieren Sie eine BACPAC-Datei auf die im Folgenden erläuterte Weise. 
    - Erstellen oder extrahieren Sie ein SQL-Datenbank-DAC-Paket. Informationen zum Generieren eines DAC-Pakets für eine vorhandene SQL Server-Datenbank finden Sie unter [Extrahieren einer DAC aus einer Datenbank](/sql/relational-databases/data-tier-applications/extract-a-dac-from-a-database/).
    - Exportieren eines bereitgestellten DAC-Pakets oder einer Datenbank Informationen zur Erstellung einer BACPAC-Datei für eine vorhandene SQL Server-Datenbank finden Sie unter [Exportieren einer Datenschichtanwendung](/sql/relational-databases/data-tier-applications/export-a-data-tier-application/).

2. Zippen Sie die `*.dacpac`- oder `*.bacpac`-Datei, und laden Sie sie in ein Azure Blob Storage-Konto. Weitere Informationen zum Hochladen von Dateien in Azure Blob Storage finden Sie unter [Hochladen, Herunterladen und Auflisten von Blobs über das Azure-Portal](../storage/blobs/storage-quickstart-blobs-portal.md).

3. Generieren Sie eine Shared Access Signature (SAS) für die ZIP-Datei über das Azure-Portal. Weitere Informationen finden Sie unter [Delegieren des Zugriffs mit Shared Access Signatures (SAS)](../storage/common/storage-sas-overview.md).

4. Aktualisieren Sie die Konfiguration des SQL Edge-Moduls, um den SAS-URI für das DAC-Paket einzubeziehen. Führen Sie die folgenden Schritte zum Aktualisieren des SQL Edge-Moduls aus:

    1. Navigieren Sie im Azure-Portal zu Ihrer IoT Hub-Bereitstellung.

    2. Wählen Sie im linken Bereich die Option **IoT Edge** aus.

    3. Suchen Sie auf der Seite **IoT Edge** nach der IoT Edge-Instanz, in der das SQL Edge-Modul bereitgestellt wurde, und wählen Sie sie aus.

    4. Wählen Sie auf der Geräteseite **IoT Edge-Gerät** die Option **Modul festlegen** aus.

    5. Klicken Sie auf der Seite **Module festlegen** auf das Azure SQL Edge-Modul.

    6. Klicken Sie im Bereich **IoT Edge-Modul aktualisieren** auf **Umgebungsvariablen**. Fügen Sie die Umgebungsvariable `MSSQL_PACKAGE` hinzu, und geben Sie die oben in Schritt 3 generierte SAS-URL als Wert für die Umgebungsvariable an. 

    7. Wählen Sie **Update** aus.

    8. Klicken Sie auf der Seite **Module festlegen** auf **Überprüfen + erstellen**.

    9. Klicken Sie auf der Seite **Module festlegen** auf **Erstellen**.

5. Nach dem Modulupdate werden die Paketdateien heruntergeladen, entzippt und für die SQL Edge-Instanz bereitgestellt.

Bei jedem Neustart des Azure SQL Edge-Containers versucht SQL Edge, das gezippte Dateipaket herunterzuladen und auf Änderungen zu überprüfen. Wenn eine neue Version der DACPAC-Datei gefunden wird, werden die Änderungen in der Datenbank in SQL Edge bereitgestellt.

## <a name="known-issue"></a>Bekanntes Problem

Bei einigen DACPAC- oder BACPAC-Bereitstellungen tritt möglicherweise ein Befehlstimeout auf. Dies führt zu einem Fehler beim DACPAC-Bereitstellungsvorgang. Wenn dieses Problem bei Ihnen auftritt, verwenden Sie die Datei „SQLPackage.exe“ (oder die SQL-Clienttools) zum manuellen Installieren von DACPAC oder BACPAC. 

## <a name="next-steps"></a>Nächste Schritte

- [Bereitstellen von SQL Edge über das Azure-Portal](deploy-portal.md)
- [Streamen von Daten](stream-data.md)
- [Machine Learning und KI mit ONNX in SQL Edge (Vorschau)](onnx-overview.md)