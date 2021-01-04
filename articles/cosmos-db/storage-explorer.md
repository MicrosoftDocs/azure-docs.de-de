---
title: Verwalten von Azure Cosmos DB-Ressourcen mit Azure Storage-Explorer
description: Erfahren Sie, wie Sie eine Verbindung mit Azure Cosmos DB herstellen und Ihre Ressourcen mithilfe von Azure Storage-Explorer verwalten.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/23/2020
ms.author: dech
ms.custom: seodec18, has-adal-ref
ms.openlocfilehash: 5b09ce48226b3c31efce4966ec776c10931cc391
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348652"
---
# <a name="manage-azure-cosmos-db-resources-by-using-azure-storage-explorer"></a>Verwalten von Azure Cosmos DB-Ressourcen mit Azure Storage-Explorer
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Verwenden Sie Azure Storage-Explorer, um eine Verbindung mit Azure Cosmos DB herzustellen. Dies ermöglicht Ihnen das Herstellen einer Verbindung mit Azure Cosmos DB-Konten, die von Windows, macOS oder Linux in Azure und unabhängigen Clouds gehostet werden.

Sie können mit demselben Tool Ihre verschiedenen Azure-Entitäten an einem Ort verwalten. Sie können Azure Cosmos DB-Entitäten verwalten, Daten bearbeiten und gespeicherte Prozeduren und Trigger zusammen mit anderen Azure-Entitäten wie Speicherblobs und Warteschlangen aktualisieren. Azure Storage-Explorer unterstützt Cosmos-Konten, die für SQL-, MongoDB-, Graph- und Tabellen-APIs konfiguriert sind.

> [!NOTE]
> Die Azure Cosmos DB-Integration in Storage-Explorer ist veraltet. Vorhandene Funktionen werden für mindestens ein Jahr ab diesem Release nicht entfernt. Sie sollten stattdessen das [Azure-Portal](https://portal.azure.com/), die [Desktop-App für das Azure-Portal](https://portal.azure.com/App/Download) oder den eigenständigen [Azure Cosmos DB-Explorer](data-explorer.md) verwenden. Die alternativen Optionen enthalten viele neue Features, die derzeit in Storage-Explorer nicht unterstützt werden.

## <a name="prerequisites"></a>Voraussetzungen

Ein Cosmos-Konto mit einer SQL-API oder der MongoDB-API von Azure Cosmos DB. Falls Sie kein Konto besitzen, können Sie im Azure-Portal eines erstellen. Weitere Informationen finden Sie unter [Azure Cosmos DB: Erstellen einer SQL-API-Web-App mit .NET und dem Azure-Portal](create-sql-api-dotnet.md).

## <a name="installation"></a>Installation

Informationen zum Installieren der neuesten Komponenten für Azure Storage-Explorer finden Sie unter [Azure Storage-Explorer](https://azure.microsoft.com/features/storage-explorer/). Es werden Versionen für Windows, Linux und macOS unterstützt.

## <a name="connect-to-an-azure-subscription"></a>Herstellen einer Verbindung mit einem Azure-Abonnement

1. Wählen Sie nach der Installation von **Azure Storage-Explorer** im linken Bereich das Symbol **Plug-In** aus.

   :::image type="content" source="./media/storage-explorer/plug-in-icon.png" alt-text="Screenshot des Symbols „Plug-In“ im linken Bereich":::

1. Wählen Sie **Azure-Konto hinzufügen** und dann **Anmelden**.

   :::image type="content" source="./media/storage-explorer/connect-to-azure-subscription.png" alt-text="Screenshot des Fensters „Verbinden mit Azure Storage“, in dem das Optionsfeld „Azure-Konto hinzufügen“ aktiviert ist, und des Dropdownmenüs „Azure-Umgebung“":::

1. Wählen Sie im Dialogfeld **Azure-Anmeldung** die Option **Anmelden** aus, und geben Sie Ihre Azure-Anmeldeinformationen ein.

    :::image type="content" source="./media/storage-explorer/sign-in.png" alt-text="Screenshot des Anmeldefensters mit Hinweis darauf, wo Sie Ihre Anmeldeinformationen für Ihr Azure-Abonnement eingeben":::

1. Wählen Sie Ihr Abonnement in der Liste aus, und wählen Sie anschließend **Übernehmen**.

    :::image type="content" source="./media/storage-explorer/apply-subscription.png" alt-text="Screenshot des Bereichs „Kontoverwaltung“ mit einer Liste der Abonnements und der Schaltfläche „Anwenden“":::

    Der Explorer-Bereich wird aktualisiert und zeigt die Konten im ausgewählten Abonnement an.

    :::image type="content" source="./media/storage-explorer/account-list.png" alt-text="Screenshot des aktualisierten Explorer-Bereichs mit den Konten im ausgewählten Abonnement":::

    Ihr **Cosmos DB-Konto** ist nun mit Ihrem Azure-Abonnement verbunden.

## <a name="use-a-connection-string-to-connect-to-azure-cosmos-db"></a>Herstellen einer Verbindung mit Azure Cosmos DB mithilfe einer Verbindungszeichenfolge

Sie können eine Verbindungszeichenfolge verwenden, um eine Verbindung mit Azure Cosmos DB herzustellen. Diese Methode unterstützt nur SQL- und Tabellen-APIs. Führen Sie diese Schritte aus, um eine Verbindung mithilfe einer Verbindungszeichenfolge herzustellen:

1. Suchen Sie in der linken Struktur nach **Lokal und angefügt**, klicken Sie mit der rechten Maustaste auf **Cosmos DB-Konten**, und wählen Sie dann **Verbindung mit Cosmos DB herstellen** aus.

    :::image type="content" source="./media/storage-explorer/connect-to-db-by-connection-string.png" alt-text="Screenshot des Dropdownmenüs nach dem Klicken mit der rechten Maustaste mit hervorgehobener Option „Verbindung mit Azure Cosmos DB herstellen“":::

2. Gehen Sie im Fenster **Verbindung mit Cosmos DB herstellen** folgendermaßen vor:
   1. Wählen Sie die API im Dropdownmenü aus.
   1. Fügen Sie Ihre **Verbindungszeichenfolge** im entsprechenden Feld ein. Informationen zum Abrufen der primären Verbindungszeichenfolge finden Sie unter [Abrufen der Verbindungszeichenfolge](manage-with-powershell.md#list-keys).
   1. Geben Sie eine **Kontobezeichnung** ein, und wählen Sie dann **Weiter** aus, um die Zusammenfassung zu überprüfen.
   1. Wählen Sie **Verbinden** aus, um eine Verbindung mit Azure Cosmos DB herzustellen.

      :::image type="content" source="./media/storage-explorer/connection-string.png" alt-text="Screenshot des Fensters „Verbindung mit Cosmos DB herstellen“ mit dem Dropdownmenü „API“, dem Feld „Verbindungszeichenfolge“ und dem Feld „Kontobezeichnung“":::

> [!NOTE]
> Wenn der Azure Storage-Explorer anzeigt, dass die Azure Cosmos DB-Verbindungszeichenfolge ein ungültiges Format hat, stellen Sie sicher, dass die Verbindungszeichenfolge mit einem Semikolon (`;`) endet. Ein Beispiel für eine gültige Azure Cosmos DB-Verbindungszeichenfolge lautet wie folgt: `AccountEndpoint=https://accountname.documents.azure.com:443;AccountKey=accountkey==;`

## <a name="use-a-local-emulator-to-connect-to-azure-cosmos-db"></a>Herstellen einer Verbindung mit Azure Cosmos DB über einen lokalen Emulator

Führen Sie die folgenden Schritte aus, um mit einem Emulator eine Verbindung mit Azure Cosmos DB herzustellen. Diese Methode unterstützt nur SQL-Konten.

1. Installieren Sie den Cosmos DB-Emulator, und öffnen Sie ihn. Informationen zum Installieren des Emulators finden Sie unter [Cosmos DB-Emulator](./local-emulator.md).

1. Suchen Sie in der linken Struktur nach **Lokal und angefügt**, klicken Sie mit der rechten Maustaste auf **Cosmos DB-Konten**, und wählen Sie dann **Connect to Cosmos DB Emulator** (Verbindung mit Cosmos DB-Emulator herstellen) aus.

    :::image type="content" source="./media/storage-explorer/emulator-entry.png" alt-text="Screenshot des Menüs nach dem Klicken mit der rechten Maustaste mit hervorgehobener Option zum Herstellen einer Verbindung mit dem Azure Cosmos DB-Emulator":::

1. Gehen Sie im Fenster **Verbindung mit Cosmos DB herstellen** folgendermaßen vor:
   1. Fügen Sie Ihre **Verbindungszeichenfolge** im entsprechenden Feld ein. Informationen zum Abrufen der primären Verbindungszeichenfolge finden Sie unter [Abrufen der Verbindungszeichenfolge](manage-with-powershell.md#list-keys).
   1. Geben Sie eine **Kontobezeichnung** ein, und wählen Sie dann **Weiter** aus, um die Zusammenfassung zu überprüfen.
   1. Wählen Sie **Verbinden** aus, um eine Verbindung mit Azure Cosmos DB herzustellen.

      :::image type="content" source="./media/storage-explorer/emulator-dialog.png" alt-text="Screenshot des Fensters „Verbindung mit Cosmos DB herstellen“ mit dem Feld „Verbindungszeichenfolge“ und dem Feld „Kontobezeichnung“":::

## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB-Ressourcenverwaltung

Mit den folgenden Vorgängen können Sie ein Azure Cosmos DB-Konto verwalten:

* Öffnen des Kontos im Azure-Portal
* Hinzufügen der Ressource zur Schnellzugriffsliste
* Suchen und Aktualisieren von Ressourcen
* Erstellen und Löschen von Datenbanken
* Erstellen und Löschen von Sammlungen
* Erstellen, Bearbeiten, Löschen und Filtern von Dokumenten
* Verwalten von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen

### <a name="quick-access-tasks"></a>Schnellzugriffsaufgaben

Indem Sie mit der rechten Maustaste auf ein Abonnement im Explorer-Bereich klicken, können Sie viele schnelle Aktionen ausführen, z. B.:

* Wenn Sie mit der rechten Maustaste auf ein Azure Cosmos DB-Konto oder eine Datenbank klicken und dann **Im Portal öffnen** auswählen, können Sie die Ressource über den Browser im Azure-Portal verwalten.

  :::image type="content" source="./media/storage-explorer/open-in-portal.png" alt-text="Screenshot des Menüs nach dem Klicken mit der rechten Maustaste mit hervorgehobener Option „Im Portal öffnen“":::

* Klicken Sie mit der rechten Maustaste auf ein Azure Cosmos DB-Konto, eine Datenbank oder eine Sammlung, und wählen Sie dann **Zu Schnellzugriff hinzufügen** aus, um es bzw. sie dem Schnellzugriffsmenü hinzuzufügen.

* Wählen Sie **Ab hier suchen** aus, um eine Schlüsselwortsuche unter dem ausgewählten Pfad auszuführen.

    :::image type="content" source="./media/storage-explorer/search-from-here.png" alt-text="Screenshot mit hervorgehobenem Suchfeld":::

### <a name="database-and-collection-management"></a>Datenbank- und Sammlungsverwaltung

#### <a name="create-a-database"></a>Erstellen einer Datenbank

1. Klicken Sie mit der rechten Maustaste auf das Azure Cosmos DB-Konto, und wählen Sie dann **Datenbank erstellen** aus.

   :::image type="content" source="./media/storage-explorer/create-database.png" alt-text="Screenshot des Menüs nach dem Klicken mit der rechten Maustaste mit hervorgehobener Option „Datenbank erstellen“":::

1. Geben Sie den Datenbanknamen ein, und drücken Sie zum Abschließen die **EINGABETASTE**.

#### <a name="delete-a-database"></a>Löschen einer Datenbank

1. Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie dann **Datenbank löschen** aus. 

   :::image type="content" source="./media/storage-explorer/delete-database1.png" alt-text="Screenshot des Menüs nach dem Klicken mit der rechten Maustaste mit hervorgehobener Option „Datenbank löschen“":::

1. Wählen Sie im Popupfenster **Ja** aus. Der Datenbankknoten wird gelöscht, und das Azure Cosmos DB-Konto wird automatisch aktualisiert.

   :::image type="content" source="./media/storage-explorer/delete-database2.png" alt-text="Screenshot des Bestätigungsfensters mit hervorgehobener Schaltfläche „Ja“":::

#### <a name="create-a-collection"></a>Erstellen einer Sammlung

1. Klicken Sie mit der rechten Maustaste auf die Datenbank, und wählen Sie dann **Sammlung erstellen** aus.

   :::image type="content" source="./media/storage-explorer/create-collection.png" alt-text="Screenshot des Menüs nach dem Klicken mit der rechten Maustaste mit hervorgehobener Option „Sammlung erstellen“":::

1. Geben Sie im Fenster „Sammlung erstellen“ die geforderten Informationen wie **Sammlungs-ID**, **Speicherkapazität** usw. ein. Klicken Sie auf **OK**, um den Vorgang abzuschließen.

   :::image type="content" source="./media/storage-explorer/create-collection2.png" alt-text="Screenshot des Fensters „Sammlung erstellen“ mit dem Feld „Sammlungs-ID“ und der Schaltfläche „Speicherkapazität“":::

1. Wählen Sie **Unbegrenzt** aus, um den Partitionsschlüssel angeben zu können, und wählen Sie dann **OK** aus, um den Vorgang abzuschließen.

   > [!NOTE]
   > Wenn zum Erstellen einer Sammlung ein Partitionsschlüssel verwendet wird, kann der Partitionsschlüsselwert für die Sammlung nach Abschluss der Erstellung nicht mehr geändert werden.

    :::image type="content" source="./media/storage-explorer/partitionkey.png" alt-text="Screenshot des Fensters „Sammlung erstellen“ mit ausgewählter Option „Unbegrenzt“ für „Speicherkapazität“ und hervorgehobenem Feld „Partitionsschlüssel“":::

#### <a name="delete-a-collection"></a>Löschen einer Sammlung

- Klicken Sie mit der rechten Maustaste auf die Sammlung, wählen Sie **Sammlung löschen** aus, und klicken Sie dann im Popupfenster auf **Ja**.

    Der Sammlungsknoten wird gelöscht, und die Datenbank wird automatisch aktualisiert.

    :::image type="content" source="./media/storage-explorer/delete-collection.png" alt-text="Screenshot des Menüs nach dem Klicken mit der rechten Maustaste mit hervorgehobener Option „Sammlung löschen“":::

### <a name="document-management"></a>Dokumentverwaltung

#### <a name="create-and-modify-documents"></a>Erstellen und Ändern von Dokumenten

- Öffnen Sie im linken Bereich die Option **Dokumente**, und wählen Sie **Neues Dokument** aus. Bearbeiten Sie den Inhalt im rechten Bereich, und wählen Sie dann **Speichern** aus.
- Sie können auch ein vorhandenes Dokument aktualisieren und dann auf **Speichern** klicken. Um Ihre Änderungen zu verwerfen, wählen Sie **Verwerfen** aus.

  :::image type="content" source="./media/storage-explorer/document.png" alt-text="Screenshot mit hervorgehobener Option „Dokumente“ im linken Bereich. Im rechten Bereich sind „Neues Dokument“, „Speichern“ und „Verwerfen“ hervorgehoben.":::

#### <a name="delete-a-document"></a>Löschen eines Dokuments

* Wählen Sie die Schaltfläche **Löschen** aus, um das ausgewählte Dokument zu löschen.

#### <a name="query-for-documents"></a>Abfragen von Dokumenten

* Bearbeiten Sie den Dokumentfilter, indem Sie eine [SQL-Abfrage](./sql-query-getting-started.md) eingeben und dann **Anwenden** auswählen.

  :::image type="content" source="./media/storage-explorer/document-filter.png" alt-text="Screenshot des rechten Bereichs mit den Schaltflächen „Filter“ und „Anwenden“. Die ID-Nummer und das Abfragefeld sind hervorgehoben.":::

### <a name="graph-management"></a>Graphverwaltung

#### <a name="create-and-modify-a-vertex"></a>Erstellen und Ändern eines Scheitelpunkts

* Öffnen Sie zum Erstellen eines neuen Scheitelpunkts im linken Bereich die Option **Graph**, und wählen Sie **Neuer Vertex** aus. Bearbeiten Sie den Inhalt, und wählen Sie dann **OK** aus.
* Wählen Sie im rechten Bereich das Stiftsymbol aus, um einen vorhandenen Scheitelpunkt zu ändern.

   :::image type="content" source="./media/storage-explorer/vertex.png" alt-text="Screenshot mit ausgewähltem Graph im linken Bereich. Die Optionen „Neuer Vertex“ und das Stiftsymbol im rechten Bereich sind hervorgehoben.":::

#### <a name="delete-a-graph"></a>Löschen eines Graphen

* Klicken Sie zum Löschen eines Scheitelpunkts auf das Papierkorbsymbol neben dem Namen des Scheitelpunkts.

#### <a name="filter-for-graph"></a>Filter für Graph

* Zum Bearbeiten des Graphfilters geben Sie eine [Gremlin-Abfrage](gremlin-support.md) ein und wählen dann **Filter anwenden** aus.

   :::image type="content" source="./media/storage-explorer/graph-filter.png" alt-text="Screenshot mit ausgewähltem Graph im linken Bereich. Die Option „Filter anwenden“ und das Abfragefeld im rechten Bereich sind hervorgehoben.":::

### <a name="table-management"></a>Tabellenverwaltung

#### <a name="create-and-modify-a-table"></a>Erstellen und Ändern einer Tabelle

* So erstellen Sie eine neue Tabelle
   1. Öffnen Sie im linken Bereich **Entitäten**, und wählen Sie dann **Hinzufügen** aus.
   1. Bearbeiten Sie den Inhalt im Dialogfeld **Entität hinzufügen**.
   1. Wählen Sie die Schaltfläche **Eigenschaft hinzufügen** aus, um eine Eigenschaft hinzuzufügen.
   1. Wählen Sie **Einfügen** aus.

      :::image type="content" source="./media/storage-explorer/table.png" alt-text="Screenshot mit hervorgehobener Option „Entitäten“ im linken Bereich. Im rechten Bereich sind die Optionen „Hinzufügen“, „Bearbeiten“, „Eigenschaft hinzufügen“ und „Einfügen“ hervorgehoben.":::

* Wenn Sie eine Tabelle ändern möchten, wählen Sie **Bearbeiten** aus, ändern Sie den Inhalt, und wählen Sie dann **Aktualisieren** aus.

   

#### <a name="import-and-export-table"></a>Importieren und Exportieren einer Tabelle

* Wählen Sie zum Importieren die Schaltfläche **Importieren** und dann eine vorhandene Tabelle aus.
* Wählen Sie zum Exportieren die Schaltfläche **Exportieren** und dann ein Ziel aus.

   :::image type="content" source="./media/storage-explorer/table-import-export.png" alt-text="Screenshot mit hervorgehobenen Schaltflächen „Importieren“ und „Exportieren“ im rechten Bereich":::

#### <a name="delete-entities"></a>Löschen von Entitäten

* Wählen Sie die Entitäten und dann die Schaltfläche **Löschen** aus.

  :::image type="content" source="./media/storage-explorer/table-delete.png" alt-text="Screenshot mit hervorgehobener Schaltfläche „Löschen“ im rechten Bereich und einem Popupfenster zur Bestätigung mit der hervorgehobenen Angabe „Ja“":::

#### <a name="query-a-table"></a>Abfragen einer Tabelle

- Wählen Sie die Schaltfläche **Abfrage** aus, geben Sie eine Abfragebedingung ein, und wählen Sie dann die Schaltfläche **Abfrage ausführen** aus. Um den Abfragebereich zu schließen, wählen Sie die Schaltfläche **Abfrage schließen** aus.

  :::image type="content" source="./media/storage-explorer/table-query.png" alt-text="Screenshot des rechten Bereichs mit den hervorgehobenen Schaltflächen „Abfrage ausführen“ und „Abfrage schließen“":::

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Verwalten von gespeicherten Prozeduren, Triggern und UDFs

* So erstellen Sie eine gespeicherte Prozedur
  1. Klicken Sie in der linken Struktur mit der rechten Maustaste auf **Gespeicherte Prozeduren**, und wählen Sie dann **Gespeicherte Prozedur erstellen** aus.
  
     :::image type="content" source="./media/storage-explorer/stored-procedure.png" alt-text="Screenshot des linken Bereichs mit dem Menü, das nach dem Klicken mit der rechten Maustaste angezeigt wird. Die Option „Gespeicherte Prozedur erstellen“ ist hervorgehoben.":::
  
  1. Geben Sie auf der linken Seite einen Namen und im rechten Bereich die Skripts für die gespeicherten Prozeduren ein. Wählen Sie anschließend **Erstellen** aus.
  
* Um eine vorhandene gespeicherte Prozedur zu bearbeiten, doppelklicken Sie auf die Prozedur, nehmen Sie die Änderung vor, und wählen Sie dann zum Speichern **Aktualisieren** aus. Sie können auch **Verwerfen** auswählen, um die Änderung abzubrechen.

* Die Vorgänge für **Trigger** und **UDFs** ähneln denen für **gespeicherte Prozeduren**.

## <a name="troubleshooting"></a>Problembehandlung

Im Folgenden finden Sie Lösungen für häufige Probleme, die bei der Verwendung von Azure Cosmos DB in Storage-Explorer auftreten.

### <a name="sign-in-issues"></a>Probleme bei der Anmeldung

Starten Sie zunächst die Anwendung neu, um festzustellen, ob damit das Problem behoben wird. Wenn das Problem weiterhin besteht, setzen Sie die Problembehandlung fort.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Selbstsigniertes Zertifikat in der Zertifikatkette

Es gibt verschiedene Gründe, warum dieser Fehler unter Umständen angezeigt wird. Die zwei häufigsten Gründe sind:

* Sie befinden sich hinter einem *transparenten Proxy*. Jemand (z. B. Ihre IT-Abteilung) fängt den HTTPS-Datenverkehr ab, entschlüsselt ihn und verschlüsselt ihn anschließend mithilfe eines selbstsignierten Zertifikats.

* Sie führen Software wie z. B. Antivirussoftware aus. Die Software fügt ein selbstsigniertes TLS/SSL-Zertifikat in die empfangenen HTTPS-Nachrichten ein.

Wenn Storage-Explorer ein selbstsigniertes Zertifikat findet, ist nicht bekannt, ob die empfangene HTTPS-Nachricht manipuliert wurde. Falls Sie über eine Kopie des selbstsignierten Zertifikats verfügen, können Sie Storage-Explorer anweisen, es als vertrauenswürdig anzusehen. Wenn Sie nicht sicher sind, wer das Zertifikat eingefügt hat, können Sie dies anhand der folgenden Schritte ermitteln:

1. Installieren Sie OpenSSL:

     - [Windows](https://slproweb.com/products/Win32OpenSSL.html): Alle Light-Versionen sind geeignet.
     - macOS und Linux: Sollte im Betriebssystem enthalten sein.

1. Führen Sie OpenSSL aus:
    * Windows: Navigieren Sie zum Installationsverzeichnis und dann zu **/bin/** , und doppelklicken Sie anschließend auf **openssl.exe**.
    * Mac und Linux: Führen Sie **openssl** über ein Terminal aus.
1. Führen Sie `s_client -showcerts -connect microsoft.com:443` aus.
1. Suchen Sie nach selbstsignierten Zertifikaten. Wenn Sie nicht sicher sind, welche Zertifikate selbstsigniert sind, sollten Sie nach Stellen suchen, an denen der Antragsteller („s:“) und der Zertifikataussteller („i:“) identisch sind.
1. Wenn Sie selbstsignierte Zertifikate gefunden haben, kopieren Sie den gesamten Inhalt von **-----BEGIN CERTIFICATE-----** bis **-----END CERTIFICATE-----** (einschließlich) und fügen ihn für jedes Zertifikat in eine neue CER-Datei ein.
1. Öffnen Sie Storage-Explorer, und navigieren Sie dann zu **Bearbeiten** > **SSL-Zertifikate** > **Zertifikate importieren**. Verwenden Sie dann die Dateiauswahl, um die erstellten CER-Dateien zu suchen, auszuwählen und zu öffnen.

Wenn Sie keine selbstsignierten Zertifikate finden, können Sie Feedback senden, um weitere Informationen zu erhalten.

#### <a name="unable-to-retrieve-subscriptions"></a>Abonnements können nicht abgerufen werden

Nutzen Sie die folgenden Vorschläge, wenn Sie nach dem erfolgreichen Anmelden Ihre Abonnements nicht abrufen können:

* Überprüfen Sie, ob Ihr Konto Zugriff auf die Abonnements hat. Dazu melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
* Vergewissern Sie sich, dass Sie in der richtigen Umgebung angemeldet sind:
  * [Azure](https://portal.azure.com/)
  * [Azure China](https://portal.azure.cn/)
  * [Azure Deutschland](https://portal.microsoftazure.de/)
  * [Azure US Government](https://portal.azure.us/)
  * Benutzerdefinierte Umgebung/Azure Stack
* Wenn Sie sich hinter einem Proxy befinden, stellen Sie sicher, dass der Storage-Explorer-Proxy richtig konfiguriert ist.
* Entfernen Sie das Konto, und fügen Sie es dann erneut hinzu.
* Löschen Sie die folgenden Dateien aus Ihrem Basisverzeichnis (z. B. „C:\Benutzer\ContosoUser“), und fügen Sie dann das Konto wieder hinzu:
  * .adalcache
  * .devaccounts
  * .extaccounts
* Drücken Sie die Taste F12, um die Entwicklerkonsole zu öffnen. Achten Sie bei der Anmeldung an der Konsole auf eventuelle Fehlermeldungen.

   :::image type="content" source="./media/storage-explorer/console.png" alt-text="Screenshot der Entwicklertools-Konsole mit hervorgehobener Konsole":::

#### <a name="unable-to-see-the-authentication-page"></a>Authentifizierungsseite wird nicht angezeigt

Gehen Sie wie folgt vor, wenn die Authentifizierungsseite nicht angezeigt wird:

* Abhängig von der Verbindungsgeschwindigkeit kann es eine Weile dauern, bis die Anmeldeseite geladen wird. Warten Sie mindestens eine Minute, bevor Sie das Dialogfeld für die Authentifizierung schließen.
* Wenn Sie sich hinter einem Proxy befinden, stellen Sie sicher, dass der Storage-Explorer-Proxy richtig konfiguriert ist.
* Überprüfen Sie in den Antworten in der Entwicklertools-Konsole (F12), ob diese Aufschluss darüber geben, warum die Authentifizierung nicht funktioniert.

#### <a name="cant-remove-an-account"></a>Entfernen eines Kontos nicht möglich

Gehen Sie wie folgt vor, falls Sie ein Konto nicht entfernen können oder der Link für die erneute Authentifizierung nicht funktioniert:

* Löschen Sie die folgenden Dateien aus Ihrem Basisverzeichnis, und fügen Sie das Konto dann erneut hinzu:
  * .adalcache
  * .devaccounts
  * .extaccounts

* Löschen Sie Folgendes, wenn Sie Storage-Ressourcen mit SAS-Anfügung entfernen möchten:
  * Ordner „%AppData%/StorageExplorer“ für Windows
  * „/Users/<Ihr_Name>/Library/Application Support/StorageExplorer“ für macOS
  * „~/.config/StorageExplorer“ für Linux
  
  > [!NOTE]
  > Wenn Sie diese Dateien löschen, **müssen Sie alle Ihre Anmeldeinformationen erneut eingeben**.

### <a name="httphttps-proxy-issue"></a>HTTP/HTTPS-Proxyproblem

Es ist nicht möglich, Azure Cosmos DB-Knoten in der linken Struktur aufzulisten, wenn Sie in der ASE einen HTTP/HTTPS-Proxy konfigurieren. Sie können als Problemumgehung den Azure Cosmos DB-Daten-Explorer im Azure-Portal verwenden.

### <a name="development-node-under-local-and-attached-node-issue"></a>Problem mit dem Knoten „Development“ unter „Local and Attached“

Wenn Sie in der linken Struktur unter **Lokal und angefügt** den Knoten **Entwicklung** auswählen, erfolgt keine Reaktion. Dies ist das erwartete Verhalten.

:::image type="content" source="./media/storage-explorer/development.png" alt-text="Screenshot des ausgewählten Knotens „Entwicklung“":::

### <a name="attach-an-azure-cosmos-db-account-in-the-local-and-attached-node-error"></a>Fehler beim Anfügen eines Azure Cosmos DB-Kontos im Knoten **Lokal und angefügt**

Wenn nach dem Anfügen des Azure Cosmos DB-Kontos unter dem Knoten **Lokal und angefügt** der unten angegebene Fehler angezeigt wird, sollten Sie überprüfen, ob Sie die richtige Verbindungszeichenfolge verwenden.

:::image type="content" source="./media/storage-explorer/attached-error.png" alt-text="Screenshot des Popupfensters „Fehler beim Abrufen von untergeordneten Ressourcen“ mit einem Hinweis auf „getaddrinfo ENOTFOUND“":::

### <a name="expand-azure-cosmos-db-node-error"></a>Fehler beim Erweitern des Azure Cosmos DB-Knotens

Möglicherweise wird der folgende Fehler angezeigt, wenn Sie versuchen, Knoten in der linken Struktur zu erweitern.

:::image type="content" source="./media/storage-explorer/expand-error.png" alt-text="Screenshot des Popupfensters „Fehler beim Abrufen von untergeordneten Ressourcen“ mit einem Hinweis darauf, dass keine Verbindung mit diesem Cosmos DB-Konto hergestellt werden kann":::

Probieren Sie diese Vorschläge aus:

* Überprüfen Sie, ob das Azure Cosmos DB-Konto derzeit bereitgestellt wird. Wiederholen Sie den Vorgang, nachdem das Konto erfolgreich erstellt wurde.
* Wenn sich das Konto unter einem der Knoten **Schnellzugriff** oder **Lokal und angefügt** befindet, sollten Sie überprüfen, ob das Konto gelöscht wurde. In diesem Fall müssen Sie den Knoten manuell entfernen.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich dieses Video zur Verwendung von Azure Cosmos DB in Azure Storage-Explorer an: [Verwalten von Azure Cosmos DB in Azure Storage-Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Weitere Informationen zum Storage-Explorer und zum Herstellen einer Verbindung mit weiteren Diensten finden Sie unter [Erste Schritte mit dem Storage-Explorer (Vorschau)](../vs-azure-tools-storage-manage-with-storage-explorer.md).
