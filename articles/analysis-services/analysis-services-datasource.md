---
title: In Azure Analysis Services unterstützte Datenquellen | Microsoft-Dokumentation
description: Beschreibt Datenquellen, die für Datenmodelle in Azure Analysis Services unterstützt werden.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 7abd0ac3d95825594dffe385bccc1672d0f71c5f
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/23/2019
ms.locfileid: "66142558"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>In Azure Analysis Services unterstützte Datenquellen

Im Assistenten zum Abrufen oder Importieren von Daten in Visual Studio werden Datenquellen und Connectors für Azure Analysis Services und SQL Server Analysis Services angezeigt. Es werden jedoch nicht alle angezeigten Datenquellen und Connectors in Azure Analysis Services unterstützt. Mit welchen Typen von Datenquellen Sie eine Verbindung herstellen können, ist von vielen Faktoren abhängig, z.B. Modellkompatibilitätsgrad, verfügbare Datenconnectors, Authentifizierungstyp, Anbieter und Unterstützung lokaler Datengateways. 

## <a name="azure-data-sources"></a>Azure-Datenquellen

|Datenquelle  |In-Memory  |DirectQuery  |
|---------|---------|---------|
|Azure SQL-Datenbank<sup>[2](#azsqlmanaged)</sup>     |   Ja      |    Ja      |
|Azure SQL Data Warehouse     |   Ja      |   Ja       |
|Azure Blob Storage<sup>[1](#tab1400a)</sup>     |   Ja       |    Nein      |
|Azure-Tabellenspeicher<sup>[1](#tab1400a)</sup>    |   Ja       |    Nein      |
|Azure Cosmos DB<sup>[1](#tab1400a)</sup>     |  Ja        |  Nein        |
|Azure Data Lake Store (Gen1)<sup>[1](#tab1400a)</sup>, <sup>[4](#gen2)</sup>      |   Ja       |    Nein      |
|Azure HDInsight HDFS<sup>[1](#tab1400a)</sup>     |     Ja     |   Nein       |
|Azure HDInsight Spark<sup>[1](#tab1400a)</sup>, <sup>[3](#databricks)</sup>     |   Ja       |   Nein       |
||||

<a name="tab1400a">1</a>: Nur für tabellarische Modelle 1400 und höhere.   
<a name="azsqlmanaged">2</a>: Verwaltete Azure SQL-Datenbank-Instanz wird unterstützt. Da eine verwaltete Instanz im Azure-VNet mit einer privaten IP-Adresse ausgeführt wird, ist ein lokales Datengateway erforderlich. Die Verwendung einer verwalteten Azure SQL-Datenbank-Instanz mit einem öffentlichem Endpunkt wird zurzeit nicht unterstützt.   
<a name="databricks">3</a>: Azure Databricks unter Verwendung des Spark-Connectors wird derzeit nicht unterstützt.   
<a name="gen2">4</a>: ADLS Gen2 wird aktuell nicht unterstützt.


**Anbieter**   
In-Memory- und DirectQuery-Modelle, die eine Verbindung mit Azure-Datenquellen herstellen, verwenden den .NET Framework-Datenanbieter für SQL Server.

## <a name="on-premises-data-sources"></a>Lokale Datenquellen

Zum Herstellen einer Verbindung zwischen lokalen Datenquellen und dem Azure AS-Server ist ein lokales Gateway erforderlich. Wenn Sie ein Gateway verwenden, sind 64-Bit-Anbieter erforderlich.

### <a name="in-memory-and-directquery"></a>In-Memory und DirectQuery

|Datenquelle | In-Memory-Anbieter | DirectQuery-Anbieter |
|  --- | --- | --- |
| SQL Server |SQL Server Native Client 11.0, Microsoft OLE DB-Anbieter für SQL Server, .NET Framework-Datenanbieter für SQL Server | .NET Framework-Datenanbieter für SQL Server |
| SQL Server Data Warehouse |SQL Server Native Client 11.0, Microsoft OLE DB-Anbieter für SQL Server, .NET Framework-Datenanbieter für SQL Server | .NET Framework-Datenanbieter für SQL Server |
| Oracle | OLE DB-Anbieter für Oracle, Oracle-Datenanbieter für .NET |Oracle-Datenanbieter für .NET |
| Teradata |OLE DB-Anbieter für Teradata, Teradata-Datenanbieter für .NET |Teradata-Datenanbieter für .NET |
| | | |

### <a name="in-memory-only"></a>Nur In-Memory

|Datenquelle  |  
|---------|
|Access-Datenbank     |  
|Active Directory<sup>[1](#tab1400b)</sup>     |  
|Analysis Services     |  
|Analytics Platform System     |  
|CSV-Datei  |
|Dynamics CRM<sup>[1](#tab1400b)</sup>     |  
|Excel-Arbeitsmappe     |  
|Exchange<sup>[1](#tab1400b)</sup>     |  
|Ordner<sup>[1](#tab1400b)</sup>     |
|IBM Informix<sup>[1](#tab1400b)</sup> (Beta) |
|JSON-Dokument<sup>[1](#tab1400b)</sup>     |  
|Zeilen aus Binärdatei<sup>[1](#tab1400b)</sup>     | 
|MySQL Database     | 
|OData-Feed<sup>[1](#tab1400b)</sup>     |  
|ODBC-Abfrage     | 
|OLE DB     |   
|Postgre SQL-Datenbank<sup>[1](#tab1400b)</sup>    | 
|Salesforce-Objekte<sup>[1](#tab1400b)</sup> |  
|Salesforce-Berichte<sup>[1](#tab1400b)</sup> |
|SAP HANA<sup>[1](#tab1400b)</sup>    |  
|SAP Business Warehouse<sup>[1](#tab1400b)</sup>    |  
|SharePoint-Liste<sup>[1](#tab1400b)</sup>, <sup>[2](#filesSP)</sup>     |   
|Sybase-Datenbank     |  
|TXT-Datei  |
|XML-Tabellen<sup>[1](#tab1400b)</sup>    |  
||
 
<a name="tab1400b">1</a>: Nur für tabellarische Modelle 1400 und höhere.   
<a name="filesSP">2:</a> Dateien in lokaler SharePoint-Instanz werden nicht unterstützt.

## <a name="specifying-a-different-provider"></a>Angeben eines anderen Herstellers

Datenmodelle in Azure Analysis Services erfordern möglicherweise verschiedene Datenanbieter beim Verbinden mit bestimmten Datenquellen. Gelegentlich kann es vorkommen, dass tabellarische Modelle beim Herstellen einer Verbindung mit Datenquellen mithilfe von nativen Anbietern wie SQL Server Native Client (SQLNCLI11) einen Fehler zurückgeben. Wenn Sie andere native Anbieter als SQLOLEDB verwenden, wird möglicherweise folgende Fehlermeldung angezeigt: **Der Anbieter „SQLNCLI11.1“ ist nicht registriert**. Wenn ein DirectQuery-Modell eine Verbindung mit lokalen Datenquellen herstellt und Sie native Anbieter verwenden, wird möglicherweise folgende Fehlermeldung angezeigt: **Fehler beim Erstellen des OLE DB-Rowsets. Incorrect syntax near „LIMIT“. (Fehler beim Erstellen eines OLE DB-Rowsets. Falsche Syntax bei „LIMIT“)** .

Bei der Migration eines lokalen SQL Server Analysis Services-Tabellenmodells zu Azure Analysis Services muss möglicherweise der Anbieter gewechselt werden.

**So geben Sie einen Anbieter an**

1. Klicken Sie unter SSDT > **Tabular Model Explorer** (Explorer für tabellarisches Modell)  > **Datenquellen** mit der rechten Maustaste auf eine Datenquellenverbindung, und klicken Sie anschließend auf **Datenquelle bearbeiten**.
2. Klicken Sie unter **Verbindung bearbeiten** auf **Erweitert**, um das Fenster „Erweiterte Eigenschaften“ zu öffnen.
3. Wählen Sie anschließend unter **Erweiterte Eigenschaften festlegen** > **Anbieter** den gewünschten Anbieter aus.

## <a name="impersonation"></a>Identitätswechsel
In einigen Fällen muss möglicherweise ein anderes Identitätswechselkonto angegeben werden. Das Identitätswechselkonto kann in Visual Studio (SSDT) oder SSMS angegeben werden.

Für lokale Datenquellen:

* Wenn Sie die SQL-Authentifizierung verwenden, sollte das Identitätswechselkonto ein Dienstkonto sein.
* Wenn Sie die Windows-Authentifizierung verwenden, legen Sie Windows-Benutzername/-Kennwort fest. Bei SQL Server wird die Windows-Authentifizierung mit einem bestimmten Identitätswechselkonto nur für In-Memory-Datenmodelle unterstützt.

Für Clouddatenquellen:

* Wenn Sie die SQL-Authentifizierung verwenden, sollte das Identitätswechselkonto ein Dienstkonto sein.

## <a name="next-steps"></a>Nächste Schritte
[Lokales Gateway](analysis-services-gateway.md)   
[Manage your server (Verwalten des Servers)](analysis-services-manage.md)   

