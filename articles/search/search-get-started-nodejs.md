---
title: Erste Schritte mit Azure Search in Node.js – Azure Search
description: Exemplarische Vorgehensweise zur Erstellung einer Suchanwendung in einem gehosteten Cloudsuchdienst mit der Programmiersprache Node.js.
author: jj09
manager: jlembicz
services: search
ms.service: search
ms.topic: conceptual
ms.date: 04/26/2017
ms.author: jjed
ms.custom: seodec2018
ms.openlocfilehash: 1b37b3c52abd3750c3452a46bdf5b0c5954de4dd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61289183"
---
# <a name="get-started-with-azure-search-in-nodejs"></a>Erste Schritte mit Azure Search in Node.js
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Erfahren Sie, wie Sie eine benutzerdefinierte Node.js-Suchanwendung erstellen, die Azure Search zum Suchen verwendet. Dieses Lernprogramm verwendet die [REST-API für den Azure Search-Dienst](https://msdn.microsoft.com/library/dn798935.aspx) zum Erstellen der Objekte und Vorgänge, die in dieser Übung verwendet werden.

Zum Entwickeln und Testen dieses Codes wurden [Node.js](https://Nodejs.org) und NPM, [Sublime Text 3](https://www.sublimetext.com/3) und Windows PowerShell unter Windows 8.1 verwendet.

Um dieses Beispiel auszuführen, benötigen Sie einen Azure Search-Dienst, für den Sie sich im [Azure-Portal](https://portal.azure.com) anmelden können. Schrittweise Anweisungen finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service-portal.md) .

## <a name="about-the-data"></a>Informationen zu den Daten
In dieser Beispielanwendung werden Daten von [United States Geological Services (USGS)](https://geonames.usgs.gov/domestic/download_data.htm)verwendet, die nach dem Bundesstaat Rhode Island gefiltert wurden, um die Größe des Datasets zu verringern. Wir verwenden diese Daten, um eine Suchanwendung zu erstellen, die markante Gebäude, wie Krankenhäuser und Schulen, sowie geologische Merkmale, wie Flüsse, Seen und Gipfel, zurückgibt.

In dieser Anwendung erstellt das Programm **DataIndexer** den Index und lädt ihn unter Verwendung eines [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx) -Konstrukts, wobei das gefilterte USGS-DataSet aus einer öffentlichen Azure SQL-Datenbank abgerufen wird. Anmeldeinformationen und Verbindungsinformationen zur Onlinedatenquelle werden im Programmcode bereitgestellt. Es ist keine weitere Konfiguration erforderlich.

> [!NOTE]
> Wir haben einen Filter auf dieses Dataset angewendet, um unter dem Limit des kostenlosen Tarifs von maximal 10.000 Dokumenten zu bleiben. Wenn Sie den Standardtarif verwenden, gilt dieses Limit nicht. Ausführliche Informationen zur Kapazität der einzelnen Tarife finden Sie unter [Einschränkungen für Suchdienste](search-limits-quotas-capacity.md).
> 
> 

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Ermitteln des Dienstnamens und des API-Schlüssels des Azure Search-Diensts
Nachdem Sie den Dienst erstellt haben, kehren Sie zum Portal zurück, um die URL oder den `api-key`zu ermitteln. Für Verbindungen mit Ihrem Search-Dienst benötigen Sie sowohl die URL als auch einen `api-key` , um den Aufruf zu authentifizieren.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie in der Navigationsleiste auf **Search-Dienst**, um alle für Ihr Abonnement bereitgestellten Azure Search-Dienste aufzuführen.
3. Wählen Sie den Dienst aus, den Sie verwenden möchten.
4. Auf dem Dienstdashboard werden Kacheln für wichtige Informationen, etwa das Schlüsselsymbol für den Zugriff auf die Administratorschlüssel, angezeigt.
5. Kopieren Sie die Dienst-URL, einen Administratorschlüssel und einen Abfrageschlüssel. Sie benötigen diese später, wenn Sie sie der Datei „config.js“ hinzufügen.

## <a name="download-the-sample-files"></a>Herunterladen der Beispieldateien
Verwenden Sie eine der folgenden Vorgehensweisen zum Herunterladen des Beispiels.

1. Navigieren Sie zu [search-node-indexer-demo](https://github.com/Azure-Samples/search-node-indexer-demo).
2. Klicken Sie auf **Download ZIP**, um die ZIP-Datei auf dem Datenträger zu speichern, und extrahieren Sie dann die darin enthaltenen Dateien.

Alle nachfolgenden Dateiänderungen und Ausführungsanweisungen werden an den Dateien in diesem Ordner vorgenommen.

## <a name="update-the-configjs-with-your-search-service-url-and-api-key"></a>Aktualisieren von „config.js“ mit Ihrer Search-Dienst-URL und dem API-Schlüssel
Unter Verwendung der URL und des API-Schlüssels, die Sie zuvor kopiert haben, geben Sie in der Konfigurationsdatei die URL, den Administratorschlüssel und den Abfrageschlüssel an.

Administratorschlüssel gewähren Ihnen uneingeschränkte Kontrolle über die Dienstvorgänge, einschließlich Erstellen oder Löschen eines Indexes und Laden von Dokumenten. Abfrageschlüssel sind hingegen nur für schreibgeschützte Vorgänge vorgesehen und werden in der Regel von Clientanwendungen verwendet, die eine Verbindung mit Azure Search herstellen.

In diesem Beispiel geben wir den Abfrageschlüssel an, um das empfohlene Verfahren der Verwendung von Abfrageschlüsseln in Clientanwendungen hervorzuheben.

Der folgende Screenshot zeigt die in einem Text-Editor geöffnete Datei **config.js** , wobei die relevanten Einträge abgegrenzt wurden, damit Sie sehen können, wo Sie die Datei mit den Werten, die für Ihren Suchdienst gültig sind, aktualisieren müssen.

![][5]

## <a name="host-a-runtime-environment-for-the-sample"></a>Hosten eine Laufzeitumgebung für das Beispiel
Das Beispiel erfordert einen HTTP-Server, den Sie mit Npm global installieren können.

Verwenden Sie ein PowerShell-Fenster für die folgenden Befehle:

1. Navigieren Sie zu dem Ordner mit der **package.json** -Datei.
2. Geben Sie `npm install`ein.
3. Geben Sie `npm install -g http-server`ein.

## <a name="build-the-index-and-run-the-application"></a>Erstellen Sie den Index, und führen Sie die Anwendung aus.
1. Geben Sie `npm run indexDocuments`ein.
2. Geben Sie `npm run build`ein.
3. Geben Sie `npm run start_server`ein.
4. Wechseln Sie mit dem Browser zu `http://localhost:8080/index.html`

## <a name="search-on-usgs-data"></a>Suchen nach USGS-Daten
Das USGS-Dataset enthält Datensätze, die für den Bundesstaat Rhode Island relevant sind. Wenn Sie auf **Suchen** klicken und das Suchfeld leer ist, erhalten Sie die ersten 50 Einträge. Dies ist die Standardeinstellung.

Durch die Eingabe eines Suchbegriffs erhält das Suchmodul eine Vorgabe. Geben Sie einen regionalen Namen ein. "Roger Williams" war die erste Gouverneur von Rhode Island. Zahlreiche Parks, Gebäude und Schulen sind nach ihm benannt.

![][9]

Sie können auch einen dieser Begriffe ausprobieren:

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>Nächste Schritte
Dies ist das erste auf Node.js und dem USGS-Dataset basierende Azure Search-Tutorial. Im Laufe der Zeit werden wir dieses Lernprogramm erweitern, um zusätzliche Suchfunktionen zu veranschaulichen, die Sie in benutzerdefinierten Lösungen vielleicht verwenden möchten.

Wenn Sie bereits über Erfahrungen mit Azure Search verfügen, können Sie dieses Beispiel als Ausgangspunkt für das Testen von Vorschlägen (Typ-ahead- oder AutoVervollständigen-Abfragen), Filtern und Facettennavigation verwenden. Sie können auch die Suchergebnisseite verbessern, indem Sie Statistiken hinzufügen und Dokumente mit Batchvorgängen verarbeiten, damit die Benutzer die Ergebnisse seitenweise anzeigen können.

Neu bei Azure Search? Es wird empfohlen, auch andere Lernprogramme zu bearbeiten, damit Sie ein Verständnis dafür entwickeln, was Sie erstellen können. Besuchen Sie unsere [Dokumentationsseite](https://azure.microsoft.com/documentation/services/search/) , um weitere Ressourcen zu finden. 

<!--Image references-->
[1]: ./media/search-get-started-Nodejs/create-search-portal-1.PNG
[2]: ./media/search-get-started-Nodejs/create-search-portal-2.PNG
[3]: ./media/search-get-started-Nodejs/create-search-portal-3.PNG
[5]: ./media/search-get-started-Nodejs/AzSearch-Nodejs-configjs.png
[9]: ./media/search-get-started-Nodejs/rogerwilliamsschool.png
