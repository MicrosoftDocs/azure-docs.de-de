---
title: Verbinden von Qlik Sense mit Azure Cosmos DB und Visualisieren Ihrer Daten
description: In diesem Artikel werden die erforderlichen Schritte zum Herstellen einer Verbindung von Azure Cosmos DB mit Qlik Sense und zum Visualisieren Ihrer Daten beschrieben.
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: eea4366ca19c01af1f79566a5ca6bcae8805b6c3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242490"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>Verbinden von Qlik Sense mit Azure Cosmos DB und Visualisieren Ihrer Daten

Qlik Sense ist ein Tool für die Datenvisualisierung, mit dem Daten aus unterschiedlichen Quellen in einer gemeinsamen Ansicht kombiniert werden. Mit Qlik Sense werden alle möglichen Beziehungen Ihrer Daten indiziert, sodass Sie sofort Erkenntnisse zu den Daten erhalten. Sie können Azure Cosmos DB-Daten visualisieren, indem Sie Qlik Sense verwenden. In diesem Artikel werden die erforderlichen Schritte zum Herstellen einer Verbindung von Azure Cosmos DB mit Qlik Sense und zum Visualisieren Ihrer Daten beschrieben. 

> [!NOTE]
> Die Verbindung von Qlik Sense mit Azure Cosmos DB wird derzeit für SQL-API-Konten und Konten mit der MongoDB-API von Azure Cosmos DB unterstützt.

Sie haben folgende Möglichkeiten, um für Qlik Sense eine Verbindung mit Azure Cosmos DB herzustellen:

* Cosmos DB-SQL-API mit dem ODBC-Connector

* Azure Cosmos DB-API für MongoDB mit dem Qlik Sense-MongoDB-Connector (derzeit in der Vorschauphase)

* Azure Cosmos DB-API für MongoDB und SQL-API mit dem REST-API-Connector in Qlik Sense

* Cosmos DB-Mongo DB-API mit dem gRPC-Connector für Qlik Core
In diesem Artikel werden die Details der Verbindungsherstellung mit der Cosmos DB-SQL-API über den ODBC-Connector beschrieben.

In diesem Artikel werden die Details der Verbindungsherstellung mit der Cosmos DB-SQL-API über den ODBC-Connector beschrieben.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie diesen Artikel durcharbeiten, sollten Sie sicherstellen, dass die folgenden Ressourcen vorhanden sind:

* Laden Sie [Qlik Sense Desktop](https://www.qlik.com/us/try-or-buy/download-qlik-sense) herunter, oder richten Sie Qlik Sense in Azure ein, indem Sie [Qlik Sense über den Marketplace installieren](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense).

* Laden Sie die [Videospieldaten](https://www.kaggle.com/gregorut/videogamesales) herunter. Dies Beispieldaten liegen im CSV-Format vor. Sie speichern diese Daten in einem Cosmos DB-Konto und visualisieren sie in Qlik Sense.

* Erstellen Sie ein Azure Cosmos DB-SQL-API-Konto mithilfe der Schritte, die im Abschnitt [Erstellen eines Datenbankkontos](create-sql-api-dotnet.md#create-account) des Schnellstartartikels beschrieben werden.

* [Erstellen einer Datenbank und einer Sammlung](create-sql-api-dotnet.md#create-collection-database): Sie können den Wert für den Sammlungsdurchsatz auf 1.000 RU/s festlegen. 

* Laden Sie die Beispielverkaufsdaten für Videospiele in Ihr Cosmos DB-Konto. Sie können die Daten importieren, indem Sie das Datenmigrationstool für Azure Cosmos DB verwenden und für die Daten einen [sequenziellen](import-data.md#SQLSeqTarget) Import oder einen [Massenimport](import-data.md#SQLBulkTarget) ausführen. Es dauert ca. drei bis fünf Minuten, um die Daten in das Cosmos DB-Konto zu importieren.

* Laden Sie den ODBC-Treiber herunter, und installieren und konfigurieren Sie ihn, indem Sie die Schritte im Artikel [Herstellen einer Azure Cosmos DB-Verbindung mithilfe von BI-Analysetools per ODBC-Treiber](odbc-driver.md) ausführen. Bei den Videospieldaten handelt es sich um ein einfaches Dataset. Sie müssen das Schema nicht ändern, sondern können einfach das Standardschema für die Sammlungszuordnung nutzen.

## <a name="connect-qlik-sense-to-cosmos-db"></a>Herstellen einer Verbindung für Qlik Sense mit Cosmos DB

1. Öffnen Sie Qlik Sense, und wählen Sie **Neue App erstellen**. Geben Sie einen Namen für Ihre App an, und wählen Sie **Erstellen**.

   ![Erstellen einer neuen Qlik Sense-App](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. Nachdem die neue App erfolgreich erstellt wurde, können Sie **App öffnen** und dann **Add data from files and other sources** (Daten aus Dateien und anderen Quellen hinzufügen) wählen. 

3. Wählen Sie aus den Datenquellen die Option **ODBC**, um das Fenster für die Einrichtung der neuen Verbindung zu öffnen. 

4. Wechseln Sie zu **Benutzer-DSN**, und wählen Sie die zuvor erstellte ODBC-Verbindung aus. Geben Sie einen Namen für die Verbindung an, und wählen Sie **Erstellen**. 

   ![Erstellen einer neuen Verbindung](./media/visualize-qlik-sense/create-new-connection.png)

5. Nachdem Sie die Verbindung erstellt haben, können Sie die Datenbank und die Sammlung mit den Videospieldaten auswählen und eine Vorschau dafür anzeigen.

   ![Auswählen der Datenbank und Sammlung](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. Wählen Sie als Nächstes die Option **Daten hinzufügen**, um die Daten in Qlik Sense zu laden. Nachdem Sie Daten in Qlik Sense geladen haben, können Sie Erkenntnisse generieren und Analysen für die Daten durchführen. Sie können entweder die Erkenntnisse nutzen oder Ihre eigene App erstellen, um die Verkäufe von Videospielen zu erkunden. In der folgenden Abbildung ist die Visualisierung der Daten dargestellt: 

   ![Visualisieren von Daten](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>Einschränkungen beim Herstellen einer Verbindung mit ODBC 

Cosmos DB ist eine schemalose verteilte Datenbank mit Treibern, die für die Anforderungen von Entwicklern ausgelegt sind. Für den ODBC-Treiber wird eine Datenbank mit Schema benötigt, um Spalten, die zugehörigen Datentypen und andere Eigenschaften abzuleiten. Die reguläre SQL-Abfrage oder die DML-Syntax mit relationaler Funktion gilt nicht für die Cosmos DB-SQL-API, da die SQL-API nicht vom Typ „ANSI-SQL“ ist. Aus diesem Grund werden die SQL-Anweisungen, die über den ODBC-Treiber ausgeführt werden, in Cosmos DB-spezifische SQL-Syntax übersetzt, die nicht über Äquivalente für alle Konstrukte verfügt. Zur Verhinderung dieser Übersetzungsprobleme müssen Sie beim Einrichten der ODBC-Verbindung ein Schema anwenden. Der Artikel [Herstellen einer Azure Cosmos DB-Verbindung mithilfe von BI-Analysetools per ODBC-Treiber](odbc-driver.md) enthält Vorschläge und Methoden zum Konfigurieren des Schemas. Achten Sie darauf, diese Zuordnung für jede Datenbank bzw. Sammlung des Cosmos DB-Kontos zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

Wenn Sie ein anderes Visualisierungstool nutzen, z.B. Power BI, können Sie eine Verbindung damit herstellen, indem Sie die Anleitung im folgenden Dokument befolgen:

* [Visualisieren von Cosmos DB-Daten mit dem Power BI-Connector](powerbi-visualize.md)
