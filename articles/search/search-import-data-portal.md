---
title: Importieren von Daten in einen Suchindex über das Azure-Portal – Azure Search
description: In diesem Artikel erfahren Sie, wie Sie den Datenimport-Assistenten im Azure-Portal verwenden, um Azure-Daten aus Azure Cosmos DB, Blob Storage, Table Storage, SQL-Datenbank und SQL Server auf Azure-VMs zu durchforsten.
author: HeidiSteen
manager: cgronlun
services: search
ms.service: search
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: heidist
ms.custom: seodec2018
ms.openlocfilehash: a0eefe38fdffd04bb95826f960771bd6430ea687
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65024781"
---
# <a name="import-data-wizard-for-azure-search"></a>Datenimport-Assistent für Azure Search

Im Azure-Portal steht auf dem Azure Search-Dashboard der **Datenimport-Assistent** zur Verfügung, mit dem Sie Daten in einen Index laden können. Im Hintergrund werden durch den Assistenten eine *Datenquelle*, ein *Index* und ein *Indexer* konfiguriert und aufgerufen, um mehrere Schritte des Indizierungsprozesses zu automatisieren: 

* Herstellen einer Verbindung mit einer externen Datenquelle im gleichen Azure-Abonnement
* Integrieren von optischer Zeichenerkennung oder Verarbeitung natürlicher Sprache, um Text aus unstrukturierten Daten zu extrahieren (optional)
* Generieren eines Index auf der Grundlage von Datenstichproben und Metadaten der externen Datenquelle
* Durchforsten der Datenquelle nach durchsuchbaren Inhalten sowie Serialisieren und Laden von JSON-Dokumenten in den Index

Der Assistent kann keine Verbindung mit einem vordefinierten Index herstellen oder einen vorhandenen Indexer ausführen. Innerhalb des Assistenten können Sie jedoch einen neuen Index oder Indexer konfigurieren, der die benötigte Struktur und das benötigte Verhalten unterstützt.

Neu bei Azure Search? Führen Sie die Schritte unter [Schnellstart: Verwenden von integrierten Portaltools für Azure Search-Importe, -Indizierungen und -Abfragen](search-get-started-portal.md) aus, um das Importieren und Indizieren mit **Daten importieren** und dem integrierten Immobilien-Beispieldataset zu testen.

## <a name="start-importing-data"></a>Starten des Datenimports

In diesem Abschnitt erhalten Sie Informationen zum Starten des Assistenten sowie einen Überblick über die einzelnen Schritte.

1. Öffnen Sie im [Azure-Portal](https://portal.azure.com) im Dashboard die Seite mit dem Suchdienst, oder [suchen Sie Ihren Dienst](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) in der Liste mit den Diensten.

2. Klicken Sie am oberen Rand der Übersichtsseite des Diensts auf **Daten importieren**.

   ![Befehl „Daten importieren“ im Portal](./media/search-import-data-portal/import-data-cmd2.png "Starten des Datenimport-Assistenten")

   > [!NOTE]
   > Sie können den **Datenimport** auch über andere Azure-Dienste wie etwa Azure Cosmos DB, Azure SQL-Datenbank und Azure Blob Storage starten. Suchen Sie auf der Übersichtsseite des Diensts im linken Navigationsbereich nach **Azure Search hinzufügen**.

3. Der Assistent wird mit dem Schritt **Daten verbinden** geöffnet. In diesem Schritt können Sie eine externe Datenquelle für den Importvorgang auswählen. Zu diesem Schritt gibt es einige wichtige Informationen. Diese finden Sie im Abschnitt [Datenquelleneingaben](#data-source-inputs).

   ![Datenimport-Assistent im Portal](./media/search-import-data-portal/import-data-wizard-startup.png "Datenimport-Assistent für Azure Search")

4. Als Nächstes folgt **Kognitive Suche hinzufügen (Optional)** , falls Sie die optische Zeichenerkennung (Optical Character Recognition, OCR) für Text in Bilddateien oder die Textanalyse für unstrukturierte Daten einschließen möchten. Für diese Aufgabe werden KI-Algorithmen aus Cognitive Services verwendet. Dieser Schritt besteht aus zwei Teilen:
  
   Erstens: [Anfügen einer Cognitive Services-Ressource](cognitive-search-attach-cognitive-services.md) an eine Azure Search-Qualifikationsgruppe
  
   Zweitens: Auswählen, welche KI-Anreicherungen in die Qualifikationsgruppe eingeschlossen werden sollen Eine exemplarische Vorgehensweise finden Sie in dieser [Schnellstartanleitung](cognitive-search-quickstart-blob.md).

   Falls Sie lediglich Daten importieren möchten, überspringen Sie diesen Schritt, und fahren Sie direkt mit der Indexdefinition fort.

5. Als Nächstes folgt **Zielindex anpassen**. In diesem Schritt können Sie das im Assistenten angezeigte Indexschema akzeptieren oder ändern. Der Assistent leitet die Felder und Datentypen anhand von Datenstichproben sowie anhand der gelesenen Metadaten aus der externen Datenquelle ab.

   [Überprüfen Sie die Indexattribute](#index-definition) für die einzelnen Felder, um bestimmte Verhaltensweisen zu ermöglichen. Wenn Sie keine Attribute auswählen, kann der Index nicht verwendet werden. 

6. Als Nächstes folgt **Indexer erstellen**. Hierbei handelt es sich um ein Produkt dieses Assistenten. Ein Indexer ist ein Crawler, der durchsuchbare Daten und Metadaten aus einer externen Azure-Datenquelle extrahiert. Durch Auswählen der Datenquelle sowie Anfügen von Qualifikationsgruppen (optional) und Index haben Sie im Rahmen der einzelnen Schritte des Assistenten einen Indexer konfiguriert.

   Benennen Sie den Indexer, und klicken Sie auf **Übermitteln**, um den Importvorgang zu starten. 

Wenn Sie die Indizierung überwachen möchten, klicken Sie im Portal in der Liste **Indexer** auf den Indexer. Während Dokumente geladen werden, steigt die Dokumentanzahl für den definierten Index. Manchmal dauert es einige Minuten, bis die Portalseite die neuesten Aktualisierungen widerspiegelt.

Der Index kann abgefragt werden, sobald das erste Dokument geladen wurde. Für diese Aufgabe können Sie den [Suchexplorer](search-explorer.md) verwenden.

<a name="data-source-inputs"></a>

## <a name="data-source-inputs"></a>Datenquelleneingaben

Der **Datenimport-Assistent** erstellt ein persistentes Datenquellenobjekt, das Verbindungsinformationen für eine externe Datenquelle angibt. Das Datenquellenobjekt wird ausschließlich mit [Indexern](search-indexer-overview.md) verwendet und kann für folgende Datenquellen erstellt werden: 

* [Azure SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Azure Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Azure Table Storage](search-howto-indexing-azure-tables.md) (wird nicht für Pipelines vom Typ [Kognitive Suche](cognitive-search-concept-intro.md) unterstützt)

Sie können Daten nur aus einer einzelnen Tabelle, Datenbanksicht oder entsprechenden Datenstruktur importieren, die Struktur kann jedoch hierarchische oder geschachtelte Unterstrukturen enthalten. Weitere Informationen finden Sie unter [Modellieren komplexer Datentypen in Azure Search](search-howto-complex-data-types.md).

Diese Datenstruktur muss vor dem Ausführen des Assistenten erstellt werden und über Inhalt verfügen. Führen Sie den **Datenimport**-Assistenten nicht für eine leere Datenquelle aus.

|  Auswahl | BESCHREIBUNG |
| ---------- | ----------- |
| **Vorhandene Datenquelle** |Wenn Sie in Ihrem Suchdienst bereits Indexer definiert haben, können Sie eine vorhandene Datenquellendefinition für einen weiteren Importvorgang auswählen. In Azure Search werden Datenquellenobjekte nur von Indexern verwendet. Datenquellenobjekte können programmgesteuert oder über den **Datenimport**-Assistenten erstellt werden.|
| **Beispiele**| Azure Search hostet eine kostenlose globale Azure SQL-Datenbank, anhand der Sie sich mit dem Importieren sowie mit Abfrageanforderungen in Azure Search vertraut machen können. Weitere Informationen finden Sie unter [Schnellstart: Verwenden von integrierten Portaltools für Azure Search-Importe, -Indizierungen und -Abfragen](search-get-started-portal.md) finden Sie eine exemplarische Vorgehensweise. |
| **Azure SQL-Datenbank** |Dienstname, Anmeldeinformationen für einen Datenbankbenutzer mit Leseberechtigung und ein Datenbankname können entweder auf der Seite oder über eine ADO.NET-Verbindungszeichenfolge angegeben werden. Wenn Sie Eigenschaften anzeigen oder anpassen möchten, verwenden Sie die Verbindungszeichenfolgen-Option. <br/><br/>Die Tabelle oder Sicht, die das Rowset bereitstellt, muss auf der Seite angegeben werden. Diese Option wird nach erfolgreicher Verbindungsherstellung in einer Dropdownliste angezeigt. |
| **SQL Server auf virtuellen Azure-Computern** |Geben Sie einen vollqualifizierten Dienstnamen, eine Benutzer-ID, ein Kennwort und eine Datenbank als Verbindungszeichenfolge an. Um diese Datenquelle verwenden zu können, müssen Sie zuvor ein Zertifikat im lokalen Speicher installieren haben, das die Verbindung verschlüsselt. Eine Anleitung finden Sie unter [SQL-VM-Verbindung mit Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>Die Tabelle oder Sicht, die das Rowset bereitstellt, muss auf der Seite angegeben werden. Diese Option wird nach erfolgreicher Verbindungsherstellung in einer Dropdownliste angezeigt. |
| **Cosmos DB** |Zu den erforderlichen Angaben gehören das Konto, die Datenbank und die Sammlung. Alle Dokumente in der Sammlung werden in den Index aufgenommen. Sie können eine Abfrage zum Vereinfachen oder Filtern des Rowsets definieren oder die Abfrage leer lassen. Eine Abfrage ist in diesem Assistenten nicht erforderlich.|
| **Azure Blob Storage** |Zu den erforderlichen Angaben gehören das Speicherkonto und ein Container. Optional: Wenn zu Gruppierungszwecken für Blobnamen eine virtuelle Benennungskonvention verwendet wird, können Sie den virtuellen Verzeichnisteil des Namens als Ordner unter dem Container angeben. Weitere Informationen finden Sie unter [Indizieren von Dokumenten in Azure Blob Storage mit Azure Search](search-howto-indexing-azure-blob-storage.md). |
| **Azure Table Storage** |Zu den erforderlichen Angaben gehören das Speicherkonto und ein Tabellenname. Optional können Sie eine Abfrage zum Abrufen einer Teilmenge der Tabellen angeben. Weitere Informationen finden Sie unter [Indizieren von Azure Table Storage mit Azure Search](search-howto-indexing-azure-tables.md). |


<a name="index-definition"></a>

## <a name="index-attributes"></a>Indexattribute

Der **Datenimport-Assistent** generiert einen Index, der mit Dokumenten aus der Eingabedatenquelle aufgefüllt wird. 

Für einen funktionsfähigen Index müssen die folgenden Elemente definiert sein:

1. Ein Feld muss zur eindeutigen Identifizierung der einzelnen Dokumente als **Schlüssel** markiert sein. Der **Schlüssel** muss *Edm.string* lauten. 

   Wenn Feldwerte Leerzeichen oder Bindestriche enthalten, muss im Schritt **Indexer erstellen** unter **Erweiterte Optionen** die Option **Base64-codierte Schlüssel** festgelegt werden, um die Überprüfung für diese Zeichen zu unterdrücken.

1. Legen Sie Indexattribute für die einzelnen Felder fest. Wenn Sie keine Attribute auswählen, ist Ihr Index im Wesentlichen leer (mit Ausnahme des erforderlichen Schlüsselfelds). Wählen Sie für jedes Feld mindestens eines oder mehrere der folgenden Attribute aus:
   
   + **Abrufbar** gibt das Feld in Suchergebnissen zurück. Jedes Feld, das Inhalt für Suchergebnisse bereitstellt, muss über dieses Attribut verfügen. Das Festlegen dieses Felds wirkt sich nicht nennenswert auf die Indexgröße aus.
   + **Filterbar** ermöglicht die Verwendung von Verweisen auf das Feld in Filterausdrücken. Jedes Feld, das in einem Ausdruck vom Typ **$filter** verwendet wird, muss über dieses Attribut verfügen. Filterausdrücke werden für exakte Übereinstimmungen verwendet. Da Textzeichenfolgen intakt bleiben, ist zusätzlicher Speicher für die ausführlichen Inhalte erforderlich.
   + **Durchsuchbar** ermöglicht eine Volltextsuche. Jedes Feld, das in Freiformabfragen oder in Abfrageausdrücken verwendet wird, muss über dieses Attribut verfügen. Für jedes als **Durchsuchbar** markierte Feld werden invertierte Indizes erstellt.

1. Folgende Attribute können bei Bedarf ebenfalls festgelegt werden:

   + **Sortierbar** ermöglicht die Verwendung des Felds in einer Sortierung. Jedes Feld, das in einem Ausdruck vom Typ **$Orderby** verwendet wird, muss über dieses Attribut verfügen.
   + **Facettierbar** ermöglicht die Verwendung des Felds in einer Facettennavigation. Als **Facettierbar** können nur Felder markiert werden, die auch als **Filterbar** markiert sind.

1. Legen Sie ggf. ein **Analysetool** fest, wenn Sie Indizes und Abfragen mit Spracherweiterung nutzen möchten. Standardmäßig wird die *Standardanalyse von Lucene* verwendet. Sie können aber auch *Englisch - Microsoft* auswählen, wenn Sie das Analysetool von Microsoft für eine erweiterte lexikalische Verarbeitung (etwa zur Auflösung unregelmäßiger Nomen oder Verben) verwenden möchten.

   + Wählen Sie **Durchsuchbar** aus, um die Liste mit den **Analysetools** zu aktivieren.
   + Wählen Sie ein Analysetool aus der Liste aus. 
   
   Derzeit können nur Sprachanalysen angegeben werden. Zur Verwendung benutzerdefinierter Analysen oder einer sprachfremden Analyse wie Schlüsselwort oder Muster muss Code geschrieben werden. Weitere Informationen zu Analysetools finden Sie unter [Erstellen eines Index für Dokumente in mehreren Sprachen in Azure Search](search-language-support.md).

1. Aktivieren Sie das Kontrollkästchen **Vorschlagsfunktion**, um für ausgewählte Felder Textvervollständigungsfunktionen bei der Abfrage zu aktivieren.


## <a name="next-steps"></a>Nächste Schritte
Unter den folgenden Links finden Sie weitere Informationen zu Indexern:

* [Verbinden von Azure SQL-Datenbank mit Azure Search mithilfe von Indexern](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indizieren von Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Indizieren von Dokumenten in Azure Blob Storage mit Azure Search](search-howto-indexing-azure-blob-storage.md)
* [Indizieren von Azure Table Storage mit Azure Search](search-howto-indexing-azure-tables.md)