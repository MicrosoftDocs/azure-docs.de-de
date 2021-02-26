---
title: In Azure Analysis Services unterstützte Datenquellen | Microsoft-Dokumentation
description: Beschreibt Datenquellen und Connectors, die für tabellarische Datenmodelle mit dem Kompatibilitätsgrad 1200 und höher in Azure Analysis Services unterstützt werden.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 03f4d151fc948e4c060989a7d101ad91aecdecb6
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99981492"
---
# <a name="data-sources-supported-in-azure-analysis-services"></a>In Azure Analysis Services unterstützte Datenquellen

Datenquellen und -connectors, die im Datenabruf- oder Tabellenimport-Assistenten in Visual Studio mit Analysis Services-Projekten angezeigt werden, werden sowohl für Azure Analysis Services als auch für SQL Server Analysis Services angezeigt. Allerdings werden nicht alle angezeigten Datenquellen und -connectors in Azure Analysis Services unterstützt. Mit welchen Arten von Datenquellen Sie eine Verbindung herstellen können, ist von vielen Faktoren abhängig. Hierzu zählen etwa der Modellkompatibilitätsgrad, verfügbare Datenconnectors, der Authentifizierungstyp und die Unterstützung lokaler Datengateways. In den folgenden Tabellen werden die unterstützten Datenquellen für Azure Analysis Services beschrieben:

## <a name="azure-data-sources"></a>Azure-Datenquellen

|Datenquelle  |In-Memory  |DirectQuery  |Notizen |
|---------|---------|---------|---------|
|Azure SQL-Datenbank      |   Ja      |    Ja      |<sup>[2](#azprovider)</sup>, <sup>[3](#azsqlmanaged)</sup>|
|Azure Synapse Analytics (SQL Data Warehouse)      |   Ja      |   Ja       |<sup>[2](#azprovider)</sup>|
|Azure Blob Storage      |   Ja       |    Nein      | <sup>[1](#tab1400a)</sup> |
|Azure Table Storage     |   Ja       |    Nein      | <sup>[1](#tab1400a)</sup>|
|Azure Cosmos DB     |  Ja        |  Nein        |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen1      |   Ja       |    Nein      |<sup>[1](#tab1400a)</sup> |
|Azure Data Lake Store Gen2       |   Ja       |    Nein      |<sup>[1](#tab1400a)</sup>, <sup>[5](#gen2)</sup>|
|Azure HDInsight HDFS    |     Ja     |   Nein       |<sup>[1](#tab1400a)</sup> |
|Azure HDInsight Spark     |   Ja       |   Nein       |<sup>[1](#tab1400a)</sup>, <sup>[4](#databricks)</sup>|
||||

**Hinweise:**

<a name="tab1400a">1</a>: Nur für tabellarische Modelle 1400 und höhere.  
<a name="azprovider">2</a>: Bei Angabe als *Anbieterdatenquelle* in tabellarischen Modellen ab 1200 ist sowohl für In-Memory- als auch für DirectQuery-Modelle der Microsoft OLE DB-Treiber für SQL Server (MSOLEDBSQL; empfohlen) oder der .NET Framework-Datenanbieter für SQL Server erforderlich.  
<a name="azsqlmanaged">3</a>: Verwaltete Azure SQL-Instanz wird unterstützt. Da SQL Managed Instance innerhalb von Azure-VNET mit einer privaten IP-Adresse ausgeführt wird, muss der öffentliche Endpunkt für die Instanz aktiviert sein. Andernfalls ist ein [lokales Datengateway](analysis-services-gateway.md) erforderlich.  
<a name="databricks">4</a>: Azure Databricks mit Spark-Connector wird derzeit nicht unterstützt.  
<a name="gen2">5</a>: Der ADLS Gen2-Connector wird derzeit zwar nicht unterstützt, aber der Azure Blob Storage-Connector kann mit einer ADLS Gen2-Datenquelle verwendet werden.

## <a name="other-data-sources"></a>Weitere Datenquellen

|Datenquelle | In-Memory | DirectQuery |Notizen   |
|  --- | --- | --- | --- |
|Access-Datenbank     |  Ja | Nein |  |
|Active Directory     |  Ja | Nein | <sup>[6](#tab1400b)</sup>  |
|Analysis Services     |  Ja | Nein |  |
|Analyseplattformsystem     |  Ja | Nein |  |
|CSV-Datei  |Ja | Nein |  |
|Dynamics 365     |  Ja | Nein | <sup>[6](#tab1400b)</sup> |
|Excel-Arbeitsmappe     |  Ja | Nein |  |
|Exchange      |  Ja | Nein | <sup>[6](#tab1400b)</sup> |
|Ordner      |Ja | Nein | <sup>[6](#tab1400b)</sup> |
|IBM Informix  |Ja | Nein |  |
|JSON-Dokument      |  Ja | Nein | <sup>[6](#tab1400b)</sup> |
|Zeilen aus Binärdatei      | Ja | Nein | <sup>[6](#tab1400b)</sup> |
|MySQL-Datenbank     | Ja | Nein |  |
|OData-Feed      |  Ja | Nein | <sup>[6](#tab1400b)</sup> |
|ODBC-Abfrage     | Ja | Nein |  |
|OLE DB     |   Ja | Nein |  |
|Oracle  | Ja  |Ja  | <sup>[9](#oracle)</sup> |
|PostgreSQL-Datenbank   | Ja | Nein | <sup>[6](#tab1400b)</sup> |
|Salesforce-Objekte|  Ja | Nein | <sup>[6](#tab1400b)</sup> |
|Salesforce-Berichte |Ja | Nein | <sup>[6](#tab1400b)</sup> |
|SAP HANA     |  Ja | Nein |  |
|SAP Business Warehouse    |  Ja | Nein | <sup>[6](#tab1400b)</sup> |
|SharePoint-Liste      |   Ja | Nein | <sup>[6](#tab1400b)</sup>, <sup>[11](#filesSP)</sup> |
|SQL Server |Ja   | Ja  | <sup>[7](#sqlim)</sup>, <sup>[8](#instgw)</sup> |
|SQL Server Data Warehouse |Ja   | Ja  | <sup>[7](#sqlim)</sup>, <sup>[8](#instgw)</sup> |
|Sybase-Datenbank     |  Ja | Nein |  |
|Teradata | Ja  | Ja  | <sup>[10](#teradata)</sup> |
|TXT-Datei  |Ja | Nein |  |
|XML-Tabelle    |  Ja | Nein | <sup>[6](#tab1400b)</sup> |
| | | |

**Hinweise:**  
<a name="tab1400b">6</a>: Nur für tabellarische Modelle ab 1400.  
<a name="sqlim">7</a>: Geben Sie bei Angabe als *Anbieterdatenquelle* in tabellarischen Modellen ab 1200 den Microsoft OLE DB-Treiber für SQL Server (MSOLEDBSQL; empfohlen), SQL Server Native Client 11.0 oder den .NET Framework-Datenanbieter für SQL Server an.  
<a name="instgw">8</a>: Bei Angabe von MSOLEDBSQL als Datenanbieter muss ggf. der [Microsoft OLE DB-Treiber für SQL Server](/sql/connect/oledb/oledb-driver-for-sql-server) heruntergeladen und auf dem Computer installiert werden, auf dem sich auch das lokale Datengateway befindet.  
<a name="oracle">9</a>: Geben Sie für tabellarische Modelle vom Typ 1200 oder als *Anbieterdatenquelle* in tabellarischen Modellen ab 1400 den Oracle-Datenanbieter für .NET an. Wenn er als strukturierte Datenquelle angegeben ist, stellen Sie sicher, dass Sie den [von Oracle verwalteten Anbieter aktivieren](#enable-oracle-managed-provider).   
<a name="teradata">10</a>: Geben Sie für tabellarische Modelle vom Typ 1200 oder als *Anbieterdatenquelle* in tabellarischen Modellen ab 1400 den Teradata-Datenanbieter für .NET an.  
<a name="filesSP">11</a>: Dateien in der lokalen SharePoint-Instanz werden nicht unterstützt.

Zum Herstellen einer Verbindung zwischen lokalen Datenquellen und dem Azure Analysis Services-Server ist ein [lokales Gateway](analysis-services-gateway.md) erforderlich. Wenn Sie ein Gateway verwenden, sind 64-Bit-Anbieter erforderlich.

## <a name="understanding-providers"></a>Grundlegendes zu Anbietern

Bei der Erstellung eines tabellarischen Modellprojekts ab 1400 in Visual Studio geben Sie standardmäßig keinen Datenanbieter an, wenn mithilfe von **Daten abrufen** eine Verbindung mit einer Datenquelle hergestellt wird. Tabellarische Modelle ab 1400 verwenden [Power Query](/power-query/power-query-what-is-power-query)-Connectors, um Verbindungen, Datenabfragen und Mashups zwischen Datenquelle und Analysis Services zu verwalten. Diese werden gelegentlich als *strukturierte* Datenquellenverbindungen bezeichnet, da Verbindungseigenschaftseinstellungen für Sie festgelegt werden. Sie können jedoch Legacydatenquellen für ein Modellprojekt in Visual Studio aktivieren. In diesem Fall können Sie den **Tabellenimport-Assistenten** verwenden, um eine Verbindung mit bestimmten Datenquellen herzustellen, die in tabellarischen Modellen bis 1200 traditionell als *Legacy-* oder *Anbieterdatenquelle* unterstützt werden. Bei Angabe als Anbieterdatenquelle können Sie einen bestimmten Datenanbieter sowie andere erweiterte Verbindungseigenschaften angeben. So können Sie beispielsweise eine Verbindung mit einer SQL Server Data Warehouse-Instanz oder sogar mit einer Azure SQL-Datenbank als Legacydatenquelle herstellen. Anschließend können Sie den Datenanbieter „OLE DB-Treiber für SQL Server“ (MSOLEDBSQL) auswählen. In diesem Fall kann durch die Wahl eines OLE DB-Datenanbieters anstelle des Power Query-Connectors ggf. eine Verbesserung der Leistung erzielt werden. 

Wenn Sie den Tabellenimport-Assistenten in Visual Studio verwenden, ist für Verbindungen mit einer Datenquelle ein Datenanbieter erforderlich. Es wird automatisch ein Standarddatenanbieter ausgewählt. Der Datenanbieter kann bei Bedarf geändert werden. Entscheidende Faktoren für die Wahl des Anbietertyps können die Leistung, die Verwendung von In-Memory-Speicher oder DirectQuery durch das Modell und die Analysis Services-Plattform sein, auf der das Modell bereitgestellt wird.

### <a name="specify-provider-data-sources-in-tabular-1400-and-higher-model-projects"></a>Angeben von Anbieterdatenquellen in tabellarischen Modellprojekten ab 1400

Klicken Sie zum Aktivieren von Anbieterdatenquellen in Visual Studio auf **Tools** > **Optionen** > **Analysis Services Tabular** > **Datenimport**, und wählen Sie **Legacydatenquellen aktivieren** aus.

![Aktivieren von Legacydatenquellen](media/analysis-services-datasource/aas-enable-legacy-datasources.png)

Klicken Sie nach Aktivierung von Legacydatenquellen im **tabellarischen Modell-Explorer** auf **Datenquellen** > **Aus Datenquelle importieren (Legacy)** .

![Legacydatenquellen im tabellarischen Modell-Explorer](media/analysis-services-datasource/aas-import-legacy-datasources.png)

Verwenden Sie genau wie bei tabellarischen Modellprojekten vom Typ 1200 den **Tabellenimport-Assistenten**, um eine Verbindung mit einer Datenquelle herzustellen. Klicken Sie auf der Seite für die Verbindungsherstellung auf **Erweitert**. Geben Sie unter **Erweiterte Eigenschaften festlegen** den Datenanbieter und andere Verbindungseinstellungen an.

![Legacy-Datenquellen: erweiterte Eigenschaften](media/analysis-services-datasource/aas-import-legacy-advanced.png)

## <a name="impersonation"></a>Identitätswechsel
In einigen Fällen muss möglicherweise ein anderes Identitätswechselkonto angegeben werden. Das Identitätswechselkonto kann in Visual Studio oder SQL Server Management Studio (SSMS) angegeben werden.

Für lokale Datenquellen:

* Wenn Sie die SQL-Authentifizierung verwenden, sollte das Identitätswechselkonto ein Dienstkonto sein.
* Wenn Sie die Windows-Authentifizierung verwenden, legen Sie Windows-Benutzername/-Kennwort fest. Bei SQL Server wird die Windows-Authentifizierung mit einem bestimmten Identitätswechselkonto nur für In-Memory-Datenmodelle unterstützt.

Für Clouddatenquellen:

* Wenn Sie die SQL-Authentifizierung verwenden, sollte das Identitätswechselkonto ein Dienstkonto sein.


## <a name="oauth-credentials"></a>OAuth-Anmeldeinformationen

Für tabellarische Modelle mit dem Kompatibilitätsgrad 1.400 und höher, die den *In-Memory*-Modus verwenden, bieten Azure SQL-Datenbank, Azure Synapse, Dynamics 365 und SharePoint Unterstützung für Listen mit OAuth-Anmeldeinformationen. Legen Sie Anmeldeinformationen mithilfe von Power Query fest, um gültige Token zu generieren. Azure Analysis Services verwaltet die Tokenaktualisierung für OAuth-Datenquellen, um Timeouts für Aktualisierungsvorgänge mit langer Laufzeit zu vermeiden. 
> [!NOTE]
> Die Aktualisierung verwalteter Token wird nicht für Datenquellen unterstützt, auf die über ein Gateway zugegriffen wird. Dies trifft z. B. zu, wenn der Zugriff auf Datenquellen für Mashupabfragen über ein Gateway erfolgt oder wenn die [ASPaaS\AlwaysUseGateway](analysis-services-vnet-gateway.md)-Eigenschaft auf **true** festgelegt ist. 

Der Direktabfragemodus wird mit OAuth-Anmeldeinformationen nicht unterstützt.

## <a name="enable-oracle-managed-provider"></a>Aktivieren des von Oracle verwalteten Anbieters

In einigen Fällen können DAX-Abfragen an eine Oracle-Datenquelle unerwartete Ergebnisse zurückgeben. Dies kann an dem für die Datenquellenverbindung verwendeten Anbieter liegen.

Wie im Abschnitt [Grundlegendes zu Anbietern](#understanding-providers) beschrieben, stellen tabellarische Modelle eine Verbindung mit Datenquellen entweder als *strukturierte* Datenquelle oder als *Anbieter* datenquelle her. Stellen Sie bei Modellen mit einer Oracle-Datenquelle, die als Anbieterdatenquelle angegeben ist, sicher, dass der angegebene Anbieter Oracle-Datenanbieter für .NET (Oracle.DataAccess.Client) ist. 

Wenn die Oracle-Datenquelle als strukturierte Datenquelle angegeben ist, aktivieren Sie die Servereigenschaft **MDataEngine\UseManagedOracleProvider**. Durch Festlegen dieser Eigenschaft wird sichergestellt, dass Ihr Modell mithilfe des empfohlenen vom Oracle-Datenanbieter für .NET verwalteten Anbieter eine Verbindung mit der Oracle-Datenquelle herstellt.
 
So aktivieren Sie den von Oracle verwalteten Anbieter

1. Stellen Sie in SQL Server Management Studio eine Verbindung mit Ihrem Server her.
2. Erstellen Sie eine XMLA-Abfrage mit dem folgenden Skript. Ersetzen Sie **ServerName** durch den vollständigen Servernamen, und führen Sie dann die Abfrage aus.

    ```xml
    <Alter AllowCreate="true" ObjectExpansion="ObjectProperties" xmlns="http://schemas.microsoft.com/analysisservices/2003/engine">
        <Object />
        <ObjectDefinition>
            <Server xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:ddl2="http://schemas.microsoft.com/analysisservices/2003/engine/2" xmlns:ddl2_2="http://schemas.microsoft.com/analysisservices/2003/engine/2/2" 
    xmlns:ddl100_100="http://schemas.microsoft.com/analysisservices/2008/engine/100/100" xmlns:ddl200="http://schemas.microsoft.com/analysisservices/2010/engine/200" xmlns:ddl200_200="http://schemas.microsoft.com/analysisservices/2010/engine/200/200" 
    xmlns:ddl300="http://schemas.microsoft.com/analysisservices/2011/engine/300" xmlns:ddl300_300="http://schemas.microsoft.com/analysisservices/2011/engine/300/300" xmlns:ddl400="http://schemas.microsoft.com/analysisservices/2012/engine/400" 
    xmlns:ddl400_400="http://schemas.microsoft.com/analysisservices/2012/engine/400/400" xmlns:ddl500="http://schemas.microsoft.com/analysisservices/2013/engine/500" xmlns:ddl500_500="http://schemas.microsoft.com/analysisservices/2013/engine/500/500">
                <ID>ServerName</ID>
                <Name>ServerName</Name>
                <ServerProperties>
                    <ServerProperty>
                        <Name>MDataEngine\UseManagedOracleProvider</Name>
                        <Value>1</Value>
                    </ServerProperty>
                </ServerProperties>
            </Server>
        </ObjectDefinition>
    </Alter>
    ```

3. Starten Sie den Server neu.


## <a name="next-steps"></a>Nächste Schritte

* [Lokales Gateway](analysis-services-gateway.md)
* [Manage your server (Verwalten des Servers)](analysis-services-manage.md)
