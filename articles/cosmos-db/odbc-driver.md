---
title: Herstellen einer Verbindung mit Azure Cosmos DB mithilfe von BI-Analysetools
description: Es wird beschrieben, wie Sie den Azure Cosmos DB-ODBC-Treiber zum Erstellen von Tabellen und Ansichten verwenden, damit normalisierte Daten in BI- und Datenanalysesoftware angezeigt werden können.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 10/02/2019
ms.author: sngun
ms.openlocfilehash: e7d6a67f5322c5bb640430f66ccb0917f6faada1
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96003496"
---
# <a name="connect-to-azure-cosmos-db-using-bi-analytics-tools-with-the-odbc-driver"></a>Herstellen einer Azure Cosmos DB-Verbindung mithilfe von BI-Analysetools per ODBC-Treiber
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Mit dem Azure Cosmos DB-ODBC-Treiber können Sie eine Verbindung mit Azure Cosmos DB über die BI-Analysetools herstellen, z. B. SQL Server Integration Services, Power BI Desktop und Tableau, um Visualisierungen Ihrer Azure Cosmos DB-Daten in diesen Lösungen zu analysieren und zu erstellen.

Der Azure Cosmos DB-ODBC-Treiber ist ODBC 3.8-konform und unterstützt die ANSI SQL-92-Syntax. Der Treiber verfügt über umfassende Features zum Renormalisieren von Daten in Azure Cosmos DB. Mit dem Treiber können Sie Daten in Azure Cosmos DB als Tabellen und Ansichten darstellen. Sie können damit SQL-Vorgänge für die Tabellen und Ansichten durchführen, z.B. das Gruppieren nach Abfragen, Einfügungen, Updates und Löschungen.

> [!NOTE]
> Das Herstellen einer Verbindung mit Azure Cosmos DB mit dem ODBC-Treiber wird derzeit nur für Azure Cosmos DB-SQL-API-Konten unterstützt.

## <a name="why-do-i-need-to-normalize-my-data"></a>Warum muss ich meine Daten normalisieren?
Azure Cosmos DB ist eine schemalose Datenbank, die eine schnelle Anwendungsentwicklung und das Durchlaufen von Datenmodellen ermöglicht, ohne auf ein striktes Schema beschränkt zu sein. Eine einzelne Azure Cosmos-Datenbank kann JSON-Dokumente mit unterschiedlichen Strukturen enthalten. Dies ist eine gute Voraussetzung für die schnelle Anwendungsentwicklung. Aber wenn Sie Daten analysieren und mit Datenanalyse- und BI-Tools Berichte dazu erstellen möchten, müssen die Daten häufig vereinfacht und an ein bestimmtes Schema angepasst werden.

An dieser Stelle kommt der ODBC-Treiber ins Spiel. Mit dem ODBC-Treiber können Sie Daten in Azure Cosmos DB jetzt in Tabellen und Ansichten renormalisieren, die Ihre Anforderungen an die Datenanalyse und Berichterstellung erfüllen. Die renormalisierten Schemas haben keinerlei Auswirkung auf die zugrunde liegenden Daten, und die Entwickler sind nicht auf ihre Nutzung beschränkt. Vielmehr ermöglichen Sie Ihnen die Nutzung ODBC-konformer Tools zum Zugreifen auf die Daten. Ihre Azure Cosmos-Datenbank ist dann nicht mehr nur das Lieblingstool Ihres Entwicklungsteams, sondern auch die Datenanalysten werden sie zu schätzen wissen.

Wenden wir uns nun dem ODBC-Treiber zu.

## <a name="step-1-install-the-azure-cosmos-db-odbc-driver"></a><a id="install"></a>Schritt 1: Installieren des ODBC-Treibers für Azure Cosmos DB

1. Laden Sie die Treiber für Ihre Umgebung herunter:

    | Installationsprogramm | Unterstützte Betriebssysteme| 
    |---|---| 
    |[Microsoft Azure Cosmos DB ODBC 64-bit.msi](https://aka.ms/cosmos-odbc-64x64) für 64-Bit-Windows| 64-Bit-Versionen von Windows 8.1 oder höher, Windows 8, Windows 7, Windows Server 2012 R2, Windows Server 2012 und Windows Server 2008 R2.| 
    |[Microsoft Azure Cosmos DB ODBC 32x64-bit.msi](https://aka.ms/cosmos-odbc-32x64) für 32-Bit unter 64-Bit-Windows| 64-Bit-Versionen von Windows 8.1 oder höher, Windows 8, Windows 7, Windows XP, Windows Vista, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 und Windows Server 2003.| 
    |[Microsoft Azure Cosmos DB ODBC 32-bit.msi](https://aka.ms/cosmos-odbc-32x32) für 32-Bit-Windows|32-Bit-Versionen von Windows 8.1 oder höher, Windows 8, Windows 7, Windows XP und Windows Vista.|

    Führen Sie die MSI-Datei, mit der der **Installations-Assistent für den Microsoft Azure Cosmos DB-ODBC-Treiber** gestartet wird, lokal aus. 

1. Verwenden Sie im Installations-Assistenten die Standardeinstellungen, um den ODBC-Treiber zu installieren.

1. Öffnen Sie auf Ihrem Computer die App **ODBC-Datenquellenadministrator**. Hierzu können Sie im Windows-Suchfeld den Begriff **ODBC-Datenquellen** eingeben. 
    Sie können sich vergewissern, dass der Treiber installiert wurde, indem Sie auf die Registerkarte **Treiber** klicken und sicherstellen, dass der **Microsoft Azure Cosmos DB ODBC-Treiber** aufgeführt ist.

    :::image type="content" source="./media/odbc-driver/odbc-driver.png" alt-text="ODBC-Datenquellenadministrator für Azure Cosmos DB":::

## <a name="step-2-connect-to-your-azure-cosmos-database"></a><a id="connect"></a>Schritt 2: Herstellen einer Verbindung mit Ihrer Azure Cosmos-Datenbank

1. Klicken Sie nach dem [Installieren des Azure Cosmos DB ODBC-Treibers](#install) im Fenster **ODBC-Datenquellenadministrator** auf **Hinzufügen**. Sie können einen Benutzer- oder System-DSN erstellen. In diesem Beispiel erstellen Sie einen Benutzer-DSN.

1. Wählen Sie im Fenster **Neue Datenquelle erstellen** die Option **Microsoft Azure Cosmos DB ODBC-Treiber**, und klicken Sie dann auf **Fertig stellen**.

1. Geben Sie im Fenster **Azure Cosmos DB ODBC-Treiber – DSN-Setup** die folgenden Informationen ein: 

    :::image type="content" source="./media/odbc-driver/odbc-driver-dsn-setup.png" alt-text="Fenster „Azure Cosmos DB ODBC Driver DSN Setup“ (Azure Cosmos DB ODBC-Treiber – DSN-Setup)":::
    - **Datenquellenname**: Der Anzeigename für den ODBC-DSN. Dieser Name muss für Ihr Azure Cosmos DB-Konto eindeutig sein. Vergeben Sie also einen passenden Namen, falls Sie mehrere Konten verwenden.
    - **Beschreibung**: Eine kurze Beschreibung der Datenquelle.
    - **Host**: Der URI für Ihr Azure Cosmos DB-Konto. Sie können den URI im Azure-Portal über die Seite „Azure Cosmos DB Keys“ (Azure Cosmos DB-Schlüssel) abrufen. Dies ist im folgenden Screenshot dargestellt. 
    - **Zugriffsschlüssel**: Der Primär- oder Sekundärschlüssel für den Lese-/Schreibzugriff oder schreibgeschützten Zugriff über die Seite mit den Azure Cosmos DB-Schlüsseln im Azure-Portal. Dies ist im folgenden Screenshot dargestellt. Wir empfehlen Ihnen die Verwendung des Schlüssels für den schreibgeschützten Zugriff, falls der DSN nur für die schreibgeschützte Datenverarbeitung und Berichterstellung verwendet wird.
    :::image type="content" source="./media/odbc-driver/odbc-cosmos-account-keys.png" alt-text="Seite „Azure Cosmos DB Keys“ (Azure Cosmos DB-Schlüssel)":::
    - **Zugriffsschlüssel verschlüsseln für**: Wählen Sie die entsprechende Option basierend auf den Benutzern dieses Computers aus. 
    
1. Klicken Sie auf die Schaltfläche **Testen**, um sicherzustellen, dass Sie eine Verbindung mit Ihrem Azure Cosmos DB-Konto herstellen können. 

1.  Klicken Sie auf **Erweiterte Optionen**, und legen Sie die folgenden Werte fest:
    *  **REST-API-Version**: Wählen Sie die [REST-API-Version](/rest/api/cosmos-db/) für Ihre Vorgänge aus. Die Standardversion ist „2015-12-16“. Wenn Sie Container mit [großen Partitionsschlüsseln](large-partition-keys.md) haben, für die die REST-API-Version „2018-12-31“ erforderlich ist, führen Sie die folgenden Schritte aus:
        - Geben Sie **2018-12-31** als REST-API-Version ein.
        - Geben Sie im Menü **Start** die Zeichenfolge „regedit“ ein, um die Anwendung **Registrierungs-Editor** zu suchen und zu öffnen.
        - Navigieren Sie im Registrierungs-Editor zum Pfad: **Computer\HKEY_LOCAL_MACHINE\SOFTWARE\ODBC\ODBC.INI**
        - Erstellen Sie einen neuen Unterschlüssel, der denselben Namen wie Ihr DSN hat, z.B. „Contoso-Konto ODBC DSN“.
        - Navigieren Sie zum Unterschlüssel „Contoso-Konto ODBC DSN“.
        - Klicken Sie mit der rechten Maustaste, um einen neuen **Zeichenfolge**-Wert hinzuzufügen:
            - Wertname: **IgnoreSessionToken**
            - Wertdaten: **1**
            :::image type="content" source="./media/odbc-driver/cosmos-odbc-edit-registry.png" alt-text="Einstellungen für den Registrierungs-Editor":::
    - **Abfragekonsistenz**: Wählen Sie die [Konsistenzebene](consistency-levels.md) für Ihre Vorgänge aus. Die Standardeinstellung lautet „Sitzung“.
    - **Anzahl an Wiederholungen**: Geben Sie ein, wie oft für einen Vorgang ein Wiederholungsversuch durchgeführt werden soll, falls die erste Anforderung aufgrund einer Ratenbegrenzung des Diensts nicht abgeschlossen werden konnte.
    - **Schemadatei**: Hier stehen Ihnen mehrere Optionen zur Verfügung.
        - Wenn Sie diesen Eintrag unverändert (leer) lassen, überprüft der Treiber die erste Seite der Daten für alle Container, um das Schema der einzelnen Container zu ermitteln. Dieser Vorgang wird als „Containerzuordnung“ bezeichnet. Ohne definierte Schemadatei muss der Treiber den Scanvorgang bei jeder Treibersitzung durchführen. Dies kann zu einer längeren Startdauer für Anwendungen führen, die den DSN nutzen. Es ist ratsam, für einen DSN immer eine Schemadatei zuzuordnen.
        - Wenn Sie bereits über eine (möglicherweise mit dem Schema-Editor erstellte) Schemadatei verfügen, können Sie auf **Durchsuchen** klicken, zur Datei navigieren und dann auf **Speichern** und auf **OK** klicken.
        - Wenn Sie ein neues Schema erstellen möchten, klicken Sie auf **OK** und anschließend im Hauptfenster auf **Schema-Editor**. Fahren Sie dann mit den Informationen im Schema-Editor fort. Achten Sie nach dem Erstellen der neuen Schemadatei darauf, zurück zum Fenster **Erweiterte Optionen** zu wechseln, um die neu erstellte Schemadatei einzufügen.

1. Nachdem Sie die Schritte im Fenster **Azure Cosmos DB ODBC Driver DSN Setup** (Azure Cosmos DB ODBC-Treiber – DSN-Setup) ausgeführt und das Fenster geschlossen haben, wird der neue Benutzer-DSN auf der Registerkarte „Benutzer-DSN“ hinzugefügt.

    :::image type="content" source="./media/odbc-driver/odbc-driver-user-dsn.png" alt-text="Neuer Azure Cosmos DB ODBC-DSN auf der Registerkarte „Benutzer-DSN“":::

## <a name="step-3-create-a-schema-definition-using-the-container-mapping-method"></a><a id="#container-mapping"></a>Schritt 3: Erstellen einer Schemadefinition mithilfe der Containerzuordnungsmethode

Es gibt zwei Arten von Samplingmethoden, die Sie verwenden können: **Containerzuordnung** oder **Tabellentrennzeichen**. In einer Samplingsitzung können beide Samplingmethoden verwendet werden, aber für jeden Container ist nur eine bestimmte Samplingmethode zulässig. Mit den folgenden Schritten wird ein Schema für die Daten in einem oder mehreren Containern mithilfe der Methode „Containerzuordnung“ erstellt. Mit dieser Samplingmethode werden die Daten auf der Seite eines Containers abgerufen, um die Struktur der Daten zu ermitteln. Hierbei wird ein Container in eine Tabelle auf der ODBC-Seite transponiert. Diese Samplingmethode ist effizient und schnell, wenn die Daten in einem Container homogen sind. Falls ein Container heterogene Daten enthält, empfehlen wir Ihnen die Verwendung der [Tabellentrennzeichen-Zuordnungsmethode](#table-mapping) (table-delimiters). Dies ist eine robustere Samplingmethode zur Ermittlung der Datenstrukturen im Container. 

1. Klicken Sie nach Abschluss der Schritte 1 bis 4 unter [Herstellen einer Verbindung mit Ihrer Azure Cosmos-Datenbank](#connect) im Fenster **Azure Cosmos DB ODBC Driver DSN Setup** (Azure Cosmos DB ODBC-Treiber – DSN-Setup) auf **Schema-Editor**.

    :::image type="content" source="./media/odbc-driver/odbc-driver-schema-editor.png" alt-text="Schaltfläche „Schema-Editor“ im Fenster „Azure Cosmos DB ODBC Driver DSN Setup“ (Azure Cosmos DB ODBC-Treiber – DSN-Setup)":::
1. Klicken Sie im Fenster **Schema-Editor** auf **Neu erstellen**.
    Im Fenster **Generate Schema** (Schema generieren) werden alle Container im Azure Cosmos DB-Konto angezeigt. 

1. Wählen Sie einen oder mehrere Container für das Sampling aus, und klicken Sie auf **Sample** (Beispiel). 

1. Auf der Registerkarte **Entwurfsansicht** sind die Datenbank, das Schema und die Tabelle dargestellt. In der Tabellenansicht zeigt der Scanvorgang die Eigenschaften an, die den Spaltennamen zugeordnet sind (SQL-Name, Quellname usw.).
    Sie können für jede Spalte SQL-Spaltenname, SQL-Typ, SQL-Länge (falls zutreffend), Staffelung (falls zutreffend), Genauigkeit (falls zutreffend) und „Nullable“ angeben bzw. ändern.
    - Sie können **Hide Column** (Spalte ausblenden) auf **true** (wahr) festlegen, wenn Sie diese Spalte aus den Abfrageergebnissen ausschließen möchten. Spalten mit der Einstellung „Hide Column = true“ werden nicht für die Auswahl und Projektion zurückgegeben, sind aber weiterhin Teil des Schemas. Beispielsweise können Sie alle für das Azure Cosmos DB-System erforderlichen Eigenschaften ausblenden, die mit „_“ beginnen.
    - Die Spalte **id** ist das einzige Feld, das nicht ausgeblendet werden kann, da es als Primärschlüssel im normalisierten Schema verwendet wird. 

1. Klicken Sie nach dem Definieren des Schemas auf **Datei** | **Speichern**, navigieren Sie zum Verzeichnis, in dem das Schema gespeichert werden soll, und klicken Sie dann auf **Speichern**.

1. Um dieses Schema mit einem DSN zu verwenden, öffnen Sie das Fenster **Azure Cosmos DB ODBC Driver DSN Setup** (Azure Cosmos DB ODBC-Treiber – DSN-Setup) (über den ODBC-Datenquellenadministrator), klicken Sie auf **Erweiterte Optionen**, und navigieren Sie dann im Feld **Schemadatei** zum gespeicherten Schema. Mit dem Speichern einer Schemadatei unter einem vorhandenen DSN wird die DSN-Verbindung an die vom Schema definierten Daten und die dazugehörige Struktur angepasst.

## <a name="step-4-create-a-schema-definition-using-the-table-delimiters-mapping-method"></a><a id="table-mapping"></a>Schritt 4: Erstellen einer Schemadefinition mit der Tabellentrennzeichen-Zuordnungsmethode

Es gibt zwei Arten von Samplingmethoden, die Sie verwenden können: **Containerzuordnung** oder **Tabellentrennzeichen**. In einer Samplingsitzung können beide Samplingmethoden verwendet werden, aber für jeden Container ist nur eine bestimmte Samplingmethode zulässig. 

Mit den folgenden Schritten wird ein Schema für die Daten in einem oder mehreren Containern mithilfe der Zuordnungsmethode **Tabellentrennzeichen** erstellt. Wir empfehlen Ihnen die Verwendung dieser Samplingmethode, wenn Ihre Container heterogene Daten enthalten. Sie können diese Methode verwenden, um das Sampling an eine Gruppe von Attributen und die dazugehörigen Werte anzupassen. Wenn ein Dokument beispielsweise eine Type-Eigenschaft enthält, können Sie das Sampling auf die Werte dieser Eigenschaft beschränken. Das Endergebnis des Samplings ist eine Gruppe von Tabellen für jeden einzelnen Wert, den Sie für „Type“ angegeben haben. Mit „Type = Car“ wird beispielsweise eine Tabelle mit PKW erzeugt, während mit „Type = Plane“ eine Tabelle mit Flugzeugen erzeugt wird.

1. Klicken Sie nach Abschluss der Schritte 1 bis 4 unter [Herstellen einer Verbindung mit Ihrer Azure Cosmos-Datenbank](#connect) im Fenster „Azure Cosmos DB ODBC Driver DSN Setup“ (Azure Cosmos DB ODBC-Treiber – DSN-Setup) auf **Schema-Editor**.

1. Klicken Sie im Fenster **Schema-Editor** auf **Neu erstellen**.
    Im Fenster **Generate Schema** (Schema generieren) werden alle Container im Azure Cosmos DB-Konto angezeigt. 

1. Wählen Sie auf der Registerkarte **Beispielansicht** in der Spalte **Mapping Definition** (Zuordnungsdefinition) für den Container den gewünschten Container aus, und klicken Sie auf **Bearbeiten**. Wählen Sie im Fenster **Mapping Definition** (Zuordnungsdefinition) dann die Methode **Table Delimiters** (Tabellentrennzeichen). Gehen Sie wie folgt vor:

    a. Geben Sie im Feld **Attribute** den Namen einer Trennzeicheneigenschaft ein. Dies ist eine Eigenschaft in Ihrem Dokument, an die Sie das Sampling anpassen möchten, z.B. „City“. Drücken Sie anschließend die EINGABETASTE. 

    b. Wenn Sie das Sampling für das oben eingegebene Attribut nur auf bestimmte Werte beschränken möchten, wählen Sie das Attribut im Auswahlfeld aus, geben Sie im Feld **Wert** einen Wert (z. B. „Seattle“) ein, und drücken Sie dann die EINGABETASTE. Sie können fortfahren, mehrere Werte für Attribute hinzuzufügen. Stellen Sie dabei lediglich sicher, dass beim Eingeben der Werte das richtige Attribut ausgewählt ist.

    Gehen Sie wie folgt vor, wenn Sie für **Attribute** beispielsweise den Wert „City“ angeben und die Tabelle auf Zeilen mit den Städten „New York“ und „Dubai“ als Wert für „City“ beschränken möchten: Geben Sie im Feld „Attribute“ den Text „City“ und dann im Feld **Werte** die Städte „New York“ und „Dubai“ ein.

1. Klicken Sie auf **OK**. 

1. Nachdem Sie die Zuordnungsdefinitionen für die Container, für die das Sampling durchgeführt werden soll, erstellt haben, klicken Sie im Fenster **Schema-Editor** auf **Sample** (Beispiel).
     Sie können für jede Spalte SQL-Spaltenname, SQL-Typ, SQL-Länge (falls zutreffend), Staffelung (falls zutreffend), Genauigkeit (falls zutreffend) und „Nullable“ angeben bzw. ändern.
    - Sie können **Hide Column** (Spalte ausblenden) auf **true** (wahr) festlegen, wenn Sie diese Spalte aus den Abfrageergebnissen ausschließen möchten. Spalten mit der Einstellung „Hide Column = true“ werden nicht für die Auswahl und Projektion zurückgegeben, sind aber weiterhin Teil des Schemas. Beispielsweise können Sie alle für das Azure Cosmos DB-System erforderlichen Eigenschaften ausblenden, die mit `_` beginnen.
    - Die Spalte **id** ist das einzige Feld, das nicht ausgeblendet werden kann, da es als Primärschlüssel im normalisierten Schema verwendet wird. 

1. Klicken Sie nach dem Definieren des Schemas auf **Datei** | **Speichern**, navigieren Sie zum Verzeichnis, in dem das Schema gespeichert werden soll, und klicken Sie dann auf **Speichern**.

1. Klicken Sie im angezeigten Fenster **Azure Cosmos DB ODBC Driver DSN Setup** (Azure Cosmos DB ODBC-Treiber – DSN-Setup) auf **Erweiterte Optionen**. Navigieren Sie dann im Feld **Schemadatei** zur gespeicherten Schemadatei, und klicken Sie auf **OK**. Klicken Sie erneut auf **OK**, um den DSN zu speichern. Das von Ihnen erstellte Schema wird unter dem DSN gespeichert. 

## <a name="optional-set-up-linked-server-connection"></a>(Optional) Einrichten einer Verbindungsserver-Verbindung

Sie können Azure Cosmos DB über SQL Server Management Studio (SSMS) abfragen, indem Sie eine Verbindungsserver-Verbindung einrichten.

1. Erstellen Sie eine Systemdatenquelle, wie unter [Schritt 2](#connect) beschrieben (beispielsweise mit dem Namen `SDS Name`).

1. [Installieren Sie SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms), und stellen Sie eine Verbindung mit dem Server her. 

1. Erstellen Sie im SSMS-Abfrage-Editor das Verbindungsserverobjekt `DEMOCOSMOS` für die Datenquelle, indem Sie die folgenden Befehle verwenden. Ersetzen Sie `DEMOCOSMOS` durch den Namen für Ihren Verbindungsserver und `SDS Name` durch den Namen Ihrer Systemdatenquelle.

    ```sql
    USE [master]
    GO
    
    EXEC master.dbo.sp_addlinkedserver @server = N'DEMOCOSMOS', @srvproduct=N'', @provider=N'MSDASQL', @datasrc=N'SDS Name'
    
    EXEC master.dbo.sp_addlinkedsrvlogin @rmtsrvname=N'DEMOCOSMOS', @useself=N'False', @locallogin=NULL, @rmtuser=NULL, @rmtpassword=NULL
    
    GO
    ```
    
Aktualisieren Sie die Liste mit den Verbindungsservern, um den Namen des neuen Verbindungsservers anzuzeigen.

:::image type="content" source="./media/odbc-driver/odbc-driver-linked-server-ssms.png" alt-text="Verbindungsserver in SSMS":::

### <a name="query-linked-database"></a>Abfragen der verknüpften Datenbank

Geben Sie eine SSMS-Abfrage ein, um die verknüpfte Datenbank abzufragen. In diesem Beispiel wird mit der Abfrage eine Auswahl aus der Tabelle im Container `customers` getroffen:

```sql
SELECT * FROM OPENQUERY(DEMOCOSMOS, 'SELECT *  FROM [customers].[customers]')
```

Führen Sie die Abfrage aus. Das Ergebnis sollte in etwa wie folgt aussehen:

```
attachments/  1507476156    521 Bassett Avenue, Wikieup, Missouri, 5422   "2602bc56-0000-0000-0000-59da42bc0000"   2015-02-06T05:32:32 +05:00 f1ca3044f17149f3bc61f7b9c78a26df
attachments/  1507476156    167 Nassau Street, Tuskahoma, Illinois, 5998   "2602bd56-0000-0000-0000-59da42bc0000"   2015-06-16T08:54:17 +04:00 f75f949ea8de466a9ef2bdb7ce065ac8
attachments/  1507476156    885 Strong Place, Cassel, Montana, 2069       "2602be56-0000-0000-0000-59da42bc0000"   2015-03-20T07:21:47 +04:00 ef0365fb40c04bb6a3ffc4bc77c905fd
attachments/  1507476156    515 Barwell Terrace, Defiance, Tennessee, 6439     "2602c056-0000-0000-0000-59da42bc0000"   2014-10-16T06:49:04 +04:00      e913fe543490432f871bc42019663518
attachments/  1507476156    570 Ruby Street, Spokane, Idaho, 9025       "2602c156-0000-0000-0000-59da42bc0000"   2014-10-30T05:49:33 +04:00 e53072057d314bc9b36c89a8350048f3
```

> [!NOTE]
> Der Cosmos DB-Verbindungsserver unterstützt keine vierteiligen Namen. Es wird ein Fehler zurückgegeben, der der folgenden Meldung ähnelt:

```
Msg 7312, Level 16, State 1, Line 44

Invalid use of schema or catalog for OLE DB provider "MSDASQL" for linked server "DEMOCOSMOS". A four-part name was supplied, but the provider does not expose the necessary interfaces to use a catalog or schema.
``` 

## <a name="optional-creating-views"></a>(Optional) Erstellen von Ansichten
Im Rahmen des Samplingprozesses können Sie Ansichten definieren und erstellen. Diese Ansichten entsprechen SQL-Ansichten. Sie sind schreibgeschützt und auf die Auswahl und Projektionen der definierten Azure Cosmos DB-SQL-Abfrage beschränkt. 

Wenn Sie eine Ansicht für Ihre Daten erstellen möchten, klicken Sie im Fenster **Schema-Editor** in der Spalte **View Definitions** (Ansichtsdefinitionen) in der Zeile mit dem Container, für den das Sampling durchgeführt werden soll, auf **Hinzufügen**. 

:::image type="content" source="./media/odbc-driver/odbc-driver-create-view.png" alt-text="Datenansicht erstellen":::


Gehen Sie im Fenster **View Definitions** (Ansichtsdefinitionen) dann wie folgt vor:

1. Klicken Sie auf **Neu**, geben Sie einen Namen für die Ansicht ein (z.B. „EmployeesfromSeattleView“), und klicken Sie dann auf **OK**.

1. Geben Sie im Fenster **Ansicht bearbeiten** eine Azure Cosmos DB-Abfrage ein. Hierbei muss es sich um eine [Azure Cosmos DB-SQL-Abfrage](./sql-query-getting-started.md) handeln, z. B. `SELECT c.City, c.EmployeeName, c.Level, c.Age, c.Manager FROM c WHERE c.City = "Seattle"`. Klicken Sie dann auf **OK**.

    :::image type="content" source="./media/odbc-driver/odbc-driver-create-view-2.png" alt-text="Abfrage beim Erstellen einer Ansicht hinzufügen":::


Sie können beliebig viele Ansichten erstellen. Nachdem Sie die Ansichten definiert haben, können Sie das Sampling für die Daten durchführen. 

## <a name="step-5-view-your-data-in-bi-tools-such-as-power-bi-desktop"></a>Schritt 5: Anzeigen von Daten in BI-Tools wie Power BI Desktop

Sie können Ihren neuen DSN verwenden, um Azure Cosmos DB mit beliebigen ODBC-konformen Tools zu verbinden. In diesem Schritt wird gezeigt, wie Sie eine Verbindung mit Power BI Desktop herstellen und eine Power BI-Visualisierung erstellen.

1. Öffnen Sie Power BI Desktop.

1. Klicken Sie auf **Get Data** (Daten abrufen).

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data.png" alt-text="Daten in Power BI Desktop abrufen":::

1. Klicken Sie im Fenster **Get Data** (Daten abrufen) auf **Other (Andere)**  | **ODBC** | **Verbinden**.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-2.png" alt-text="ODBC-Datenquelle im Power BI-Fenster „Daten abrufen“ auswählen":::

1. Wählen Sie im Fenster **From ODBC** (Von ODBC) den von Ihnen erstellten Datenquellennamen aus, und klicken Sie anschließend auf **OK**. Sie können die Einträge unter **Erweiterte Optionen** leer lassen.

   :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-3.png" alt-text="Daten abrufen – Datenquellenname (DSN) in Power BI auswählen":::

1. Wählen Sie im Fenster **Mit einem ODBC-Treiber auf eine Datenquelle zugreifen** die Option **Standard oder Benutzerdefiniert**, und klicken Sie dann auf **Verbinden**. Es ist nicht erforderlich, die **Eigenschaften für Anmeldedaten-Verbindungszeichenfolge** einzufügen.

1. Erweitern Sie im Fenster **Navigator** im linken Bereich die Datenbank und das Schema, und wählen Sie dann die Tabelle aus. Der Ergebnisbereich enthält die Daten gemäß dem von Ihnen verwendeten Schema.

    :::image type="content" source="./media/odbc-driver/odbc-driver-power-bi-get-data-4.png" alt-text="Daten abrufen – Tabelle in Power BI auswählen":::

1. Aktivieren Sie das Kontrollkästchen vor dem Tabellennamen, und klicken Sie dann auf **Laden**, um die Daten in Power BI Desktop zu visualisieren.

1. Wählen Sie in Power BI Desktop ganz links die Registerkarte „Daten“ :::image type="icon" source="./media/odbc-driver/odbc-driver-data-tab.png"::: aus, um sich zu vergewissern, dass die Daten importiert wurden. 

1. Sie können jetzt mit Power BI visuelle Elemente erstellen, indem Sie auf die Registerkarte „Bericht“ :::image type="icon" source="./media/odbc-driver/odbc-driver-report-tab.png"::: und auf **Neues visuelles Element** klicken und anschließend die Kachel anpassen. Weitere Informationen zur Erstellung von Visualisierungen in Power BI Desktop finden Sie unter [Visualisierungstypen in Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-visualization-types-for-reports-and-q-and-a/). 

## <a name="troubleshooting"></a>Problembehandlung

Stellen Sie beim Auftreten des folgenden Fehlers sicher, dass die Werte für **Host** und **Zugriffsschlüssel**, die Sie in [Schritt 2](#connect) im Azure-Portal kopiert haben, korrekt sind. Wiederholen Sie anschließend den Vorgang. Verwenden Sie im Azure-Portal die Kopierschaltflächen rechts von den Werten **Host** und **Zugriffsschlüssel**, um die Werte frei von Fehlern zu kopieren.

```output
[HY000]: [Microsoft][Azure Cosmos DB] (401) HTTP 401 Authentication Error: {"code":"Unauthorized","message":"The input authorization token can't serve the request. Please check that the expected payload is built as per the protocol, and check the key being used. Server used the following payload to sign: 'get\ndbs\n\nfri, 20 jan 2017 03:43:55 gmt\n\n'\r\nActivityId: 9acb3c0d-cb31-4b78-ac0a-413c8d33e373"}
```


## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Cosmos DB finden Sie unter [Willkommen bei Azure Cosmos DB](introduction.md).