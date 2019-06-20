---
title: Verwalten von Azure Cosmos DB-Ressourcen mit Azure Storage Explorer
description: Erfahren Sie, wie Sie eine Verbindung mit Azure Cosmos DB herstellen und Ihre Ressourcen mithilfe von Azure Storage-Explorer verwalten.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dech
ms.custom: seodec18
ms.openlocfilehash: 6c3c8aee51a4484013126a5959d3d275a88aa1cc
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242643"
---
# <a name="work-with-data-using-azure-storage-explorer"></a>Arbeiten mit Daten unter Verwendung von Azure Storage-Explorer

Mit der Verwendung von Azure Cosmos DB in Azure Storage-Explorer können Benutzer Azure Cosmos DB-Entitäten verwalten, Daten bearbeiten und gespeicherte Prozeduren und Trigger zusammen mit anderen Azure-Entitäten wie Speicherblobs und Warteschlangen aktualisieren. Nun können Sie mit demselben Tool Ihre verschiedenen Azure-Entitäten an einem Ort verwalten. Derzeit unterstützt Azure Storage-Explorer Cosmos-Konten, die für SQL-, MongoDB-, Graph- und Tabellen-APIs konfiguriert sind.


## <a name="prerequisites"></a>Voraussetzungen

Ein Cosmos-Konto mit der SQL-API oder der MongoDB-API von Azure Cosmos DB. Wenn Sie kein Konto haben, können Sie im Azure-Portal ein Konto erstellen, wie unter [Azure Cosmos DB: Erstellen einer SQL-API-Web-App mit .NET und dem Azure-Portal](create-sql-api-dotnet.md) beschrieben.

## <a name="installation"></a>Installation

Installieren Sie die neuesten Komponenten von Azure Storage Explorer, die Sie unter [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) finden. Unterstützt werden Windows-, Linux- und Macintosh-Versionen.

## <a name="connect-to-an-azure-subscription"></a>Herstellen einer Verbindung mit einem Azure-Abonnement

1. Klicken Sie nach dem Installieren von **Azure Storage-Explorer** links auf das Symbol **Plug-In**, wie in der folgenden Abbildung dargestellt:
       
   ![Plug-In-Symbol](./media/storage-explorer/plug-in-icon.png)
 
2. Wählen Sie **Azure-Konto hinzufügen** aus, und klicken Sie dann auf **Anmelden**.

   ![Herstellen einer Verbindung mit einem Azure-Abonnement](./media/storage-explorer/connect-to-azure-subscription.png)

2. Wählen Sie im Dialogfeld **Azure-Anmeldung** die Option **Anmelden** aus, und geben Sie Ihre Azure-Anmeldeinformationen ein.

    ![Anmelden](./media/storage-explorer/sign-in.png)

3. Wählen Sie Ihr Abonnement in der Liste aus, und klicken Sie dann auf **Anwenden**.

    ![Anwenden](./media/storage-explorer/apply-subscription.png)

    Der Explorer-Bereich wird aktualisiert und zeigt die Konten im ausgewählten Abonnement an.

    ![Kontoliste](./media/storage-explorer/account-list.png)

    Sie haben nun Ihr **Cosmos DB-Konto** mit Ihrem Azure-Abonnement verbunden.

## <a name="connect-to-azure-cosmos-db-by-using-a-connection-string"></a>Herstellen einer Verbindung mit Azure Cosmos DB mithilfe einer Verbindungszeichenfolge

Eine alternative Möglichkeit zum Herstellen einer Verbindung mit einer Azure Cosmos DB ist die Verwendung einer Verbindungszeichenfolge. Gehen Sie folgendermaßen vor, um eine Verbindung mithilfe einer Verbindungszeichenfolge herzustellen.

1. Suchen Sie in der linken Struktur nach **Local and Attached** (Lokal und angefügt), klicken Sie mit der rechten Maustaste auf **Cosmos DB-Konten**, und wählen Sie **Connect to Cosmos DB...** (Mit Cosmos DB verbinden...).

    ![Herstellen einer Verbindung mit Cosmos DB mithilfe einer Verbindungszeichenfolge](./media/storage-explorer/connect-to-db-by-connection-string.png)

2. Derzeit werden nur SQL und die Tabellen-API unterstützt. Wählen Sie die API aus, fügen Sie die **Verbindungszeichenfolge** ein, und geben Sie die **Kontobezeichnung** ein. Klicken Sie auf **Weiter**, um die Zusammenfassung zur Überprüfung anzuzeigen, und klicken Sie dann auf **Verbinden**, um eine Verbindung mit dem Azure Cosmos DB-Konto herzustellen. Informationen zum Abrufen der Verbindungszeichenfolge finden Sie unter [Abrufen der Verbindungszeichenfolge](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Verbindungszeichenfolge](./media/storage-explorer/connection-string.png)

## <a name="connect-to-azure-cosmos-db-by-using-local-emulator"></a>Herstellen einer Verbindung mit Azure Cosmos DB über den lokalen Emulator

Verwenden Sie die folgenden Schritte, um eine Verbindung mit Azure Cosmos DB per Emulator herzustellen. Derzeit wird nur das SQL-Konto unterstützt.

1. Installieren Sie den Emulator, und starten Sie ihn. Informationen zur Installation des Emulators finden Sie im Artikel zum [Cosmos DB-Emulator](https://docs.microsoft.com/azure/cosmos-db/local-emulator).

2. Suchen Sie in der linken Struktur nach **Local and Attached** (Lokal und angefügt), klicken Sie mit der rechten Maustaste auf **Cosmos DB-Konten**, und wählen Sie **Connect to Cosmos DB Emulator...** (Mit Cosmos DB-Emulator verbinden...).

    ![Herstellen einer Verbindung mit Cosmos DB per Emulator](./media/storage-explorer/emulator-entry.png)

3. Derzeit wird nur die SQL-API unterstützt. Fügen Sie die **Verbindungszeichenfolge** ein, und geben Sie die **Kontobezeichnung** ein. Klicken Sie auf **Weiter**, um die Zusammenfassung zur Überprüfung anzuzeigen, und klicken Sie dann auf **Verbinden**, um eine Verbindung mit dem Azure Cosmos DB-Konto herzustellen. Informationen zum Abrufen der Verbindungszeichenfolge finden Sie unter [Abrufen der Verbindungszeichenfolge](https://docs.microsoft.com/azure/cosmos-db/manage-account).

    ![Dialogfeld zum Herstellen einer Verbindung mit Cosmos DB per Emulator](./media/storage-explorer/emulator-dialog.png)


## <a name="azure-cosmos-db-resource-management"></a>Azure Cosmos DB-Ressourcenverwaltung

Sie können ein Azure Cosmos DB-Konto über die folgenden Vorgänge verwalten:
* Öffnen des Kontos im Azure-Portal
* Hinzufügen der Ressource zur Schnellzugriffsliste
* Suchen und Aktualisieren von Ressourcen
* Erstellen und Löschen von Datenbanken
* Erstellen und Löschen von Sammlungen
* Erstellen, Bearbeiten, Löschen und Filtern von Dokumenten
* Verwalten von gespeicherten Prozeduren, Triggern und benutzerdefinierten Funktionen

### <a name="quick-access-tasks"></a>Schnellzugriffsaufgaben

Indem Sie mit der rechten Maustaste auf ein Abonnement im Explorer-Bereich klicken, können Sie viele schnelle Aktionen ausführen:

* Wenn Sie mit der rechten Maustaste auf ein Azure Cosmos DB-Konto oder eine Datenbank klicken, können Sie **Im Portal öffnen** auswählen und die Ressource über den Browser im Azure-Portal verwalten.

     ![Im Portal öffnen](./media/storage-explorer/open-in-portal.png)

* Sie können auch die Azure Cosmos DB-Konten, Datenbanken oder Sammlungen als **Schnellzugriff** hinzufügen.
* **Ab hier suchen** ermöglicht eine Schlüsselwortsuche unter dem ausgewählten Pfad.

    ![Ab hier suchen](./media/storage-explorer/search-from-here.png) 

### <a name="database-and-collection-management"></a>Datenbank- und Sammlungsverwaltung
#### <a name="create-a-database"></a>Erstellen einer Datenbank 
-   Klicken Sie mit der rechten Maustaste auf das Azure Cosmos DB-Konto, wählen Sie **Datenbank erstellen** aus, geben Sie den Datenbanknamen ein, und drücken Sie zum Abschluss die **Eingabetaste**.
       
    ![Erstellen einer Datenbank](./media/storage-explorer/create-database.png) 

#### <a name="delete-a-database"></a>Löschen einer Datenbank
- Klicken Sie mit der rechten Maustaste auf die Datenbank, klicken Sie auf **Datenbank löschen**, und klicken Sie im Popupfenster auf **Ja**. Der Datenbankknoten wird gelöscht, und das Azure Cosmos DB-Konto wird automatisch aktualisiert.

    ![database1 löschen](./media/storage-explorer/delete-database1.png)  

    ![database2 löschen](./media/storage-explorer/delete-database2.png) 

#### <a name="create-a-collection"></a>Erstellen einer Sammlung
1. Klicken Sie mit der rechten Maustaste auf die Datenbank, wählen Sie **Sammlung erstellen** aus, und geben Sie dann die benötigten Informationen ein, z.B. **Sammlungs-ID**, **Speicherkapazität** usw. Klicken Sie auf **OK**, um den Vorgang abzuschließen. 

    ![collection1 erstellen](./media/storage-explorer/create-collection.png)

    ![collection2 erstellen](./media/storage-explorer/create-collection2.png) 

2. Wählen Sie **Unbegrenzt**, um den Partitionsschlüssel angeben zu können, und klicken Sie dann auf **OK**, um den Vorgang abzuschließen.

    Wenn zum Erstellen einer Sammlung ein Partitionsschlüssel verwendet wird, kann der Partitionsschlüsselwert für die Auflistung nach Abschluss der Erstellung nicht mehr geändert werden.

    ![Partitionsschlüssel](./media/storage-explorer/partitionkey.png)

#### <a name="delete-a-collection"></a>Löschen einer Sammlung
- Klicken Sie mit der rechten Maustaste auf die Sammlung, klicken Sie auf **Sammlung löschen**, und klicken Sie dann im Popupfenster auf **Ja**. 

    Der Sammlungsknoten wird gelöscht, und die Datenbank wird automatisch aktualisiert.

    ![Sammlung löschen](./media/storage-explorer/delete-collection.png) 

### <a name="document-management"></a>Dokumentverwaltung

#### <a name="create-and-modify-documents"></a>Erstellen und Ändern von Dokumenten
- Um ein neues Dokument zu erstellen, öffnen Sie im linken Fenster **Dokumente**, klicken Sie auf **Neues Dokument**. Bearbeiten Sie den Inhalt im rechten Bereich, und klicken Sie auf **Speichern**. Sie können auch ein vorhandenes Dokument aktualisieren und dann auf **Speichern** klicken. Änderungen können durch Klicken auf **Verwerfen** verworfen werden.

    ![Dokument](./media/storage-explorer/document.png)

#### <a name="delete-a-document"></a>Löschen eines Dokuments
- Klicken Sie auf die Schaltfläche **Löschen**, um das ausgewählte Dokument zu löschen.

#### <a name="query-for-documents"></a>Abfragen von Dokumenten
- Bearbeiten Sie den Dokumentfilter durch Eingeben einer [SQL-Abfrage](how-to-sql-query.md), und klicken Sie dann auf **Anwenden**.

    ![Dokumentfilter](./media/storage-explorer/document-filter.png)



### <a name="graph-management"></a>Graphverwaltung

#### <a name="create-and-modify-vertex"></a>Erstellen und Ändern des Scheitelpunkts
1. Öffnen Sie zum Erstellen eines neuen Scheitelpunkts im linken Fenster die Option **Graph**, klicken Sie auf **New Vertex** (Neuer Scheitelpunkt) bearbeiten Sie den Inhalt, und klicken Sie dann auf **OK**.    
2. Klicken Sie im rechten Bereich auf das Stiftsymbol, um einen vorhandenen Scheitelpunkt zu ändern.   

    ![Graph](./media/storage-explorer/vertex.png)

#### <a name="delete-a-graph"></a>Löschen eines Graphen
- Klicken Sie zum Löschen eines Scheitelpunkts auf das Papierkorbsymbol neben dem Namen des Scheitelpunkts.

#### <a name="filter-for-graph"></a>Filter für Graph
- Bearbeiten Sie den Graphfilter, indem Sie eine [Gremlin-Abfrage](gremlin-support.md) eingeben und dann auf **Filter anwenden** klicken.

    ![Graphfilter](./media/storage-explorer/graph-filter.png)

### <a name="table-management"></a>Tabellenverwaltung

#### <a name="create-and-modify-table"></a>Erstellen und Ändern der Tabelle
1. Öffnen Sie zum Erstellen einer neuen Tabelle im linken Fenster die Option **Entitäten**, klicken Sie auf **Hinzufügen**, bearbeiten Sie den Inhalt im Dialogfeld **Entität hinzufügen**, fügen Sie die Eigenschaft durch das Klicken auf die Schaltfläche **Eigenschaft hinzufügen** hinzu, und klicken Sie anschließend auf **Einfügen**.
2. Klicken Sie zum Ändern einer Tabelle auf **Bearbeiten**, ändern Sie den Inhalt, und klicken Sie dann auf **Aktualisieren**.

    ![Table](./media/storage-explorer/table.png)

#### <a name="import-and-export-table"></a>Importieren und Exportieren einer Tabelle
1. Klicken Sie zum Importieren auf die Schaltfläche **Importieren**, und wählen Sie eine vorhandene Tabelle aus.
2. Klicken Sie zum Exportieren auf die Schaltfläche **Exportieren**, und wählen Sie ein Ziel aus.

    ![Tabellenimport und -export](./media/storage-explorer/table-import-export.png)

#### <a name="delete-entities"></a>Löschen von Entitäten
- Wählen Sie die Entitäten aus, und klicken Sie auf die Schaltfläche **Löschen**.

    ![Tabelle löschen](./media/storage-explorer/table-delete.png)

#### <a name="query-table"></a>Abfragen einer Tabelle
- Klicken Sie auf die Schaltfläche **Abfragen**, geben Sie eine Abfragebedingung ein, und klicken Sie dann auf die Schaltfläche **Abfrage ausführen**. Schließen Sie den Abfragebereich, indem Sie auf die Schaltfläche **Abfrage schließen** klicken.

    ![Tabellenabfrage](./media/storage-explorer/table-query.png)

### <a name="manage-stored-procedures-triggers-and-udfs"></a>Verwalten von gespeicherten Prozeduren, Triggern und UDFs
* Um eine gespeicherte Prozedur zu erstellen, klicken Sie in der linken Struktur mit der rechten Maustaste auf **Gespeicherte Prozedur**, wählen Sie **Gespeicherte Prozedur erstellen** aus, geben Sie links einen Namen ein, geben Sie die Skripts für die gespeicherte Prozedur im rechten Fenster ein, und klicken Sie dann auf **Erstellen**. 
* Sie können auch vorhandene gespeicherte Prozeduren bearbeiten, indem Sie doppelklicken, die Aktualisierung vornehmen und dann auf **Aktualisieren** klicken, um sie zu speichern, oder auf **Verwerfen**, um die Änderungen zu verwerfen.

    ![Gespeicherte Prozedur](./media/storage-explorer/stored-procedure.png)
* Die Vorgänge für **Trigger** und **UDFs** ähneln **gespeicherten Prozeduren**.

## <a name="troubleshooting"></a>Problembehandlung

[Azure Cosmos DB in Azure Storage-Explorer](https://docs.microsoft.com/azure/cosmos-db/storage-explorer) ist eine eigenständige App, mit der Sie von Windows, macOS oder Linux eine Verbindung mit Azure Cosmos DB-Konten, die in Azure und Sovereign Clouds gehostet werden, herstellen können. Sie können damit Azure Cosmos DB-Entitäten verwalten, Daten bearbeiten und gespeicherte Prozeduren und Trigger zusammen mit anderen Azure-Entitäten wie Speicherblobs und Warteschlangen aktualisieren.

Hierbei handelt es sich um Lösungen für allgemeine Probleme, die bei Azure Cosmos DB im Storage-Explorer auftreten.

### <a name="sign-in-issues"></a>Probleme bei der Anmeldung

Versuchen Sie vor dem Fortfahren, Ihre Anwendung neu zu starten, um zu prüfen, ob die Probleme behoben werden können.

#### <a name="self-signed-certificate-in-certificate-chain"></a>Selbstsigniertes Zertifikat in der Zertifikatkette

Es gibt verschiedene Gründe, warum dieser Fehler unter Umständen angezeigt wird. Die zwei häufigsten Gründe sind:

+ Sie befinden sich hinter einem *transparenten Proxy*. Dies bedeutet, dass HTTPS-Datenverkehr abgefangen (z.B. von Ihrer IT-Abteilung) und entschlüsselt und anschließend mit einem selbstsignierten Zertifikat wieder verschlüsselt wird.

+ Sie führen Software aus, z.B. Virenschutzsoftware, mit der ein selbstsigniertes Zertifikat in Ihre HTTPS-Nachrichten injiziert wird.

Wenn Storage-Explorer eines dieser „selbstsignierten Zertifikate“ erkennt, kann nicht mehr überprüft werden, ob die empfangene HTTPS-Nachricht manipuliert wurde. Falls Sie aber über eine Kopie des selbstsignierten Zertifikats verfügen, können Sie Storage-Explorer anweisen, es als vertrauenswürdig anzusehen. Wenn Sie unsicher sind, von wem das Zertifikat injiziert wird, können Sie mit den folgenden Schritten versuchen, es selbst zu finden:

1. Installieren Sie OpenSSL.
     - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (alle Light-Versionen sind geeignet)
     - Mac und Linux: Sollte im Betriebssystem enthalten sein.
2. Führen Sie OpenSSL aus.
    - Windows: Navigieren Sie zum Installationsverzeichnis und dann zu **/bin/** , und doppelklicken Sie anschließend auf **openssl.exe**.
    - Mac und Linux: Führen Sie **openssl** über ein Terminal aus.
3. Führen Sie `s_client -showcerts -connect microsoft.com:443` aus.
4. Suchen Sie nach selbstsignierten Zertifikaten. Wenn Sie nicht sicher sind, welche Zertifikate selbstsigniert sind, sollten Sie nach Stellen suchen, an denen der Antragsteller („s:“) und der Zertifikataussteller („i:“) identisch sind.
5.  Nachdem Sie selbstsignierte Zertifikate gefunden haben, kopieren Sie den gesamten Inhalt von **-----BEGIN CERTIFICATE-----** bis **-----END CERTIFICATE-----** (einschließlich) und fügen ihn für jedes Zertifikat in eine neue CER-Datei ein.
6.  Öffnen Sie Storage-Explorer, und navigieren Sie dann zu **Bearbeiten** > **SSL-Zertifikate** > **Zertifikate importieren**. Verwenden Sie die Dateiauswahl, um die von Ihnen erstellten CER-Dateien zu suchen, auszuwählen und zu öffnen.

Falls Sie mit den oben angegebenen Schritten keine selbstsignierten Zertifikate finden können, können Sie Feedback senden, um weitere Hilfe zu erhalten.

#### <a name="unable-to-retrieve-subscriptions"></a>Abonnements können nicht abgerufen werden

Gehen Sie wie folgt vor, wenn Sie nach dem erfolgreichen Anmelden Ihre Abonnements nicht abrufen können:

- Melden Sie sich am [Azure-Portal](https://portal.azure.com/) an, um zu überprüfen, ob Ihr Konto Zugriff auf die Abonnements hat.
- Stellen Sie sicher, dass die Anmeldung mit der richtigen Umgebung ([Azure](https://portal.azure.com/), [Azure China](https://portal.azure.cn/), [Azure Deutschland](https://portal.microsoftazure.de/), [Azure US-Regierung](https://portal.azure.us/) oder „Benutzerdefinierte Umgebung/Azure Stack“) erfolgt ist.
- Wenn Sie sich hinter einem Proxy befinden, sollten Sie sicherstellen, dass Sie den Storage-Explorer-Proxy richtig konfiguriert haben.
- Entfernen Sie das Konto, und fügen Sie es wieder hinzu.
- Löschen Sie die folgenden Dateien aus Ihrem Basisverzeichnis (z. B. C:\Benutzer\ContosoUser), und fügen Sie dann das Konto wieder hinzu:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Achten Sie beim Anmelden auf Fehlermeldungen in der Entwicklertools-Konsole (F12)

![console](./media/storage-explorer/console.png)

#### <a name="unable-to-see-the-authentication-page"></a>Authentifizierungsseite wird nicht angezeigt 

Gehen Sie wie folgt vor, wenn die Authentifizierungsseite nicht angezeigt wird:

- Je nach Verbindungsgeschwindigkeit kann es eine Weile dauern, bis die Anmeldeseite geladen wird. Warten Sie mindestens eine Minute, bevor Sie das Dialogfeld für die Authentifizierung schließen.
- Wenn Sie sich hinter einem Proxy befinden, sollten Sie sicherstellen, dass Sie den Storage-Explorer-Proxy richtig konfiguriert haben.
- Rufen Sie durch Drücken der Taste F12 die Entwicklerkonsole auf. Prüfen Sie in den Antworten in der Entwicklerkonsole, ob diese Aufschluss darüber geben, warum die Authentifizierung nicht funktioniert.

#### <a name="cannot-remove-account"></a>Konto kann nicht entfernt werden

Gehen Sie wie folgt vor, falls Sie ein Konto nicht entfernen können oder wenn der Link für die erneute Authentifizierung nicht funktioniert.

- Löschen Sie die folgenden Dateien aus Ihrem Basisverzeichnis, und fügen Sie das Konto dann wieder hinzu:
  - .adalcache
  - .devaccounts
  - .extaccounts
- Löschen Sie Folgendes, wenn Sie Storage-Ressourcen mit SAS-Anfügung entfernen möchten:
  - Ordner „%AppData%/StorageExplorer“ für Windows
  - „/Users/<Ihr_Name>/Library/Application Support/StorageExplorer“ für Mac
  - „~/.config/StorageExplorer“ für Linux
  - Wenn Sie diese Dateien löschen, **müssen Sie alle Ihre Anmeldeinformationen erneut eingeben**.


### <a name="httphttps-proxy-issue"></a>HTTP/HTTPS-Proxyproblem

Es ist nicht möglich, Azure Cosmos DB-Knoten in der linken Struktur aufzulisten, wenn Sie den HTTP/HTTPS-Proxy in ASE konfigurieren. Dies ist ein bekanntes Problem, das im nächsten Release behoben sein wird. Derzeit können Sie den Azure Cosmos DB-Daten-Explorer im Azure-Portal als Problemumgehung verwenden. 

### <a name="development-node-under-local-and-attached-node-issue"></a>Problem mit dem Knoten „Development“ unter „Local and Attached“

Wenn Sie in der linken Struktur unter „Local and Attached“ (Lokal und angefügt) auf den Knoten „Development“ (Entwicklung) klicken, erfolgt keine Reaktion.  Dies ist das erwartete Verhalten. Der lokale Azure Cosmos DB-Emulator wird ab dem nächsten Release unterstützt.

![Knoten „Development“ (Entwicklung)](./media/storage-explorer/development.png)

### <a name="attaching-azure-cosmos-db-account-in-local-and-attached-node-error"></a>Fehler beim Anfügen eines Azure Cosmos DB-Kontos im Knoten „Local and Attached“

Wenn nach dem Anfügen des Azure Cosmos DB-Kontos unter dem Knoten „Local and Attached“ (Lokal und angefügt) der unten angegebene Fehler angezeigt wird, sollten Sie überprüfen, ob Sie die richtige Verbindungszeichenfolge verwenden.

![Fehler beim Anfügen von Azure Cosmos DB unter „Local and Attached“](./media/storage-explorer/attached-error.png)

### <a name="expand-azure-cosmos-db-node-error"></a>Fehler beim Erweitern des Azure Cosmos DB-Knotens

Unter Umständen wird der folgende Fehler angezeigt, wenn Sie versuchen, in der linken Struktur die Knoten zu erweitern. 

![Fehler beim Erweitern](./media/storage-explorer/expand-error.png)

Versuchen Sie es mit folgenden Lösungsvorschlägen:

- Überprüfen Sie, ob die Bereitstellung des Azure Cosmos DB-Kontos ausgeführt wird, und wiederholen Sie den Vorgang, nachdem die Erstellung des Kontos erfolgreich abgeschlossen wurde.
- Wenn sich das Konto unter dem Knoten „Quick Access“ (Schnellzugriff) oder „Local and Attached“ (Lokal und angefügt) befindet, sollten Sie überprüfen, ob das Konto gelöscht wurde. Wenn dies der Fall ist, müssen Sie den Knoten manuell entfernen.

## <a name="contact-us"></a>Kontakt

Falls Ihnen keine dieser Lösungen weiterhilft, können Sie eine E-Mail mit Details zum Problem an das Azure Cosmos DB Dev Tooling-Team ([cosmosdbtooling@microsoft.com](mailto:cosmosdbtooling@microsoft.com)) senden, um Informationen zur Problemlösung zu erhalten.

## <a name="next-steps"></a>Nächste Schritte

* Sehen Sie sich das folgende Video zur Verwendung von Azure Cosmos DB in Azure Storage-Explorer an: [Verwalten von Azure Cosmos DB in Azure Storage-Explorer](https://www.youtube.com/watch?v=iNIbg1DLgWo&feature=youtu.be).
* Weitere Informationen zum Storage-Explorer und zum Herstellen einer Verbindung mit weiteren Diensten finden Sie unter [Erste Schritte mit dem Storage-Explorer (Vorschau)](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer).

