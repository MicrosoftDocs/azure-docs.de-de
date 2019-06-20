---
title: 'Erste Schritte mit Azure Search in Java: Azure Search'
description: Informationen zum Erstellen einer gehosteten Cloudsuchanwendung in Azure mit der Programmiersprache Java.
services: search
author: jj09
manager: jlembicz
ms.service: search
ms.topic: conceptual
ms.date: 08/26/2018
ms.author: jjed
ms.custom: seodec2018
ms.openlocfilehash: d16f20e3c2dfa3d670006e44f0072a3871d41c3f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61289800"
---
# <a name="get-started-with-azure-search-in-java"></a>Erste Schritte mit Azure Search in Java
> [!div class="op_single_selector"]
> * [Portal](search-get-started-portal.md)
> * [.NET](search-howto-dotnet-sdk.md)
> 
> 

Erfahren Sie, wie Sie eine benutzerdefinierte Java-Suchanwendung erstellen, die Azure Search zum Suchen verwendet. Dieses Lernprogramm verwendet die [REST-API für den Azure Search-Dienst](https://msdn.microsoft.com/library/dn798935.aspx) zum Erstellen der Objekte und Vorgänge, die in dieser Übung verwendet werden.

Um dieses Beispiel auszuführen, benötigen Sie einen Azure Search-Dienst, für den Sie sich beim [Azure-Portal](https://portal.azure.com)anmelden können. Schrittweise Anweisungen finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service-portal.md) .

Zum Erstellen und Testen dieses Beispiels wurde die folgende Software verwendet:

* [Eclipse-IDE für Java EE-Entwickler](https://www.eclipse.org/downloads/packages/release/photon/r/eclipse-ide-java-ee-developers). Achten Sie darauf, dass Sie die EE-Version herunterladen. Ein Überprüfungsschritt erfordert eine Funktion, die nur in dieser Version verfügbar ist.
* [JDK 8u181](https://aka.ms/azure-jdks)
* [Apache Tomcat 8.5.33](https://tomcat.apache.org/download-80.cgi#8.5.33)

## <a name="about-the-data"></a>Informationen zu den Daten
In dieser Beispielanwendung werden Daten von [United States Geological Services (USGS)](https://geonames.usgs.gov/domestic/download_data.htm)verwendet, die nach dem Bundesstaat Rhode Island gefiltert wurden, um die Größe des Datasets zu verringern. Wir verwenden diese Daten, um eine Suchanwendung zu erstellen, die markante Gebäude, wie Krankenhäuser und Schulen, sowie geologische Merkmale, wie Flüsse, Seen und Gipfel, zurückgibt.

In dieser Anwendung erstellt das Programm **SearchServlet.java** den Index und lädt ihn unter Verwendung eines [Indexer](https://msdn.microsoft.com/library/azure/dn798918.aspx) -Konstrukts, wobei das gefilterte USGS-DataSet aus einer öffentlichen Azure SQL-Datenbank abgerufen wird. Vordefinierte Anmeldeinformationen und Verbindungsinformationen zur Onlinedatenquelle werden im Programmcode bereitgestellt. Hinsichtlich des Datenzugriffs ist keine weitere Konfiguration erforderlich.

> [!NOTE]
> Wir haben einen Filter auf dieses Dataset angewendet, um unter dem Limit des kostenlosen Tarifs von maximal 10.000 Dokumenten zu bleiben. Bei Verwendung des Standardtarifs gilt dieses Limit nicht, und Sie können den Code bearbeiten, um größere Datasets verwenden zu können. Ausführliche Informationen zur Kapazität der einzelnen Tarife finden Sie unter [Limits und Einschränkungen](search-limits-quotas-capacity.md).
> 
> 

## <a name="about-the-program-files"></a>Informationen zu den Programmdateien
Die folgende Liste beschreibt die Dateien, die für dieses Beispiel relevant sind.

* Search.jsp: Stellt die Benutzeroberfläche bereit
* SearchServlet.java: Stellt Methoden bereit (vergleichbar mit einem Controller in MVC)
* SearchServiceClient.java: Behandelt HTTP-Anforderungen
* SearchServiceHelper.java: Eine Hilfsklasse, die statische Methoden enthält
* Document.java: Stellt das Datenmodell bereit
* config.properties: Legt die Suchdienst-URL und den API-Schlüssel fest
* pom.xml: Eine Maven-Abhängigkeit

<a id="sub-2"></a>

## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Ermitteln des Dienstnamens und des API-Schlüssels des Azure Search-Diensts
Bei allen REST-API-Aufrufen von Azure Search ist die Angabe der Dienst-URL und eines API-Schlüssels erforderlich. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com)an.
2. Klicken Sie in der Navigationsleiste auf **Search-Dienst** , um alle für Ihr Abonnement bereitgestellten Azure Search-Dienste aufzuführen.
3. Wählen Sie den Dienst aus, den Sie verwenden möchten.
4. Auf dem Service-Dashboard werden Kacheln für wichtige Informationen sowie das Schlüsselsymbol für den Zugriff auf die Administratorschlüssel angezeigt.
   
      ![][3]
5. Kopieren Sie die Dienst-URL und einen Administratorschlüssel. Sie benötigen diese später, wenn Sie sie der Datei **config.properties** hinzufügen.

## <a name="download-the-sample-files"></a>Herunterladen der Beispieldateien
1. Navigieren Sie zu [search-java-indexer-demo](https://github.com/Azure-Samples/search-java-indexer-demo) auf GitHub.
2. Klicken Sie auf **Download ZIP**, um die ZIP-Datei auf dem Datenträger zu speichern und dann die darin enthaltenen Dateien zu extrahieren. Es empfiehlt sich, die Dateien in den Java-Arbeitsbereich zu extrahieren, damit Sie das Projekt später leichter finden.
3. Die Beispieldateien sind schreibgeschützt. Klicken Sie mit der rechten Maustaste auf den Ordner, öffnen Sie die Eigenschaftenseite, und deaktivieren Sie das Schreibschutzattribut.

Alle nachfolgenden Dateiänderungen und Ausführungsanweisungen werden an den Dateien in diesem Ordner vorgenommen.  

## <a name="import-project"></a>Importieren des Projekts
1. Wählen Sie in Eclipse **Datei** > **Importieren** > **Allgemein** > **Vorhandene Projekte in Arbeitsbereich**.
   
    ![][4]
2. Navigieren Sie in **Select root directory**zu dem Ordner mit den Beispieldateien. Wählen Sie den Ordner aus, der den .projekt-Ordner enthält. Das Projekt sollte in der Liste **Projects** als  ausgewähltes Element angezeigt werden.
   
    ![][12]
3. Klicken Sie auf **Fertig stellen**.
4. Verwenden Sie **Project Explorer** , um die Dateien anzuzeigen und zu bearbeiten. Wenn Projektexplorer noch nicht geöffnet ist, klicken Sie auf **Fenster** > **Ansicht anzeigen** > **Projektexplorer**, oder verwenden Sie die Verknüpfung, um Projektexplorer zu öffnen.

## <a name="configure-the-service-url-and-api-key"></a>Konfigurieren von Dienst-URL und API-Schlüssel
1. Doppelklicken Sie in **Projektexplorer** auf **config.properties**, um die Konfigurationseinstellungen zu bearbeiten, die den Servernamen und den API-Schlüssel enthalten.
2. Schlagen Sie die Schritte weiter oben in diesem Artikel nach, mit denen Sie die Dienst-URL und den API-Schlüssel im [Azure-Portal](https://portal.azure.com)ermittelt haben, um die Werte zu erhalten, die Sie jetzt in **config.properties**eingeben.
3. Ersetzen Sie in der Datei **config.properties**, "API Key" durch den API-Schlüssel ihres Diensts. Anschließend ersetzt der Dienstname (die erste Komponente der URL https://servicename.search.windows.net) ) „service name“ in der gleichen Datei.
   
    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Konfigurieren der Projekt-, Build- und Laufzeitumgebungen
1. Klicken Sie in Eclipse in Projektexplorer mit der rechten Maustaste auf das Projekt > **Eigenschaften** > **Projektfacets**.
2. Wählen Sie **Dynamic Web Module**, **Java** und **JavaScript** aus.
   
    ![][6]
3. Klicken Sie auf **Anwenden**.
4. Wählen Sie **Fenster** > **Voreinstellungen** > **Server** > **Laufzeitumgebungen** > **Hinzufügen..** .
5. Erweitern Sie Apache, und wählen Sie die Version des Apache Tomcat-Servers, die Sie zuvor installiert haben. Auf unserem System wurde Version 8 installiert.
   
    ![][7]
6. Geben Sie auf der nächsten Seite das Tomcat-Installationsverzeichnis an. Auf einem Windows-Computer ist dies wahrscheinlich „C:\Programme\Apache Software Foundation\Tomcat *Version*“.
7. Klicken Sie auf **Fertig stellen**.
8. Wählen Sie **Fenster** > **Voreinstellungen** > **Java** > **Installierte JREs** > **Hinzufügen..** .
9. Wählen Sie in **JRE hinzufügen** die Option **Standard VM** aus.
10. Klicken Sie auf **Weiter**.
11. Klicken Sie auf der JRE-Startseite in "JRE Definition" auf **Directory**.
12. Navigieren Sie zu **Program Files** > **Java**, und wählen Sie das JDK aus, das Sie zuvor installiert haben. Es ist wichtig, dass Sie das JDK als JRE auswählen.
13. Wählen Sie unter "Installed JREs" die Option **JDK**. Ihre Einstellungen sollte in etwa wie folgender Screenshot aussehen.
    
    ![][9]
14. Wählen Sie optional **Fenster** > **Webbrowser** > **Internet Explorer**, um die Anwendung in einem externen Browserfenster zu öffnen. In einem externen Browser wird die Webanwendung optimal dargestellt.
    
    ![][8]

Sie haben die Konfigurationsaufgaben nun abgeschlossen. Als Nächstes erstellen Sie das Projekt und führen Sie es aus.

## <a name="build-the-project"></a>Erstellen des Projekts
1. Klicken Sie in Projektexplorer mit der rechten Maustaste auf den Projektnamen, und wählen Sie **Ausführen als** > **Maven-Build...** , um das Projekt zu konfigurieren.
   
    ![][10]
2. Geben Sie in "Edit Configuration" unter "Goals" das Ziel "clean install" ein, und klicken Sie dann auf **Run**.

Statusmeldungen werden an das Konsolenfenster ausgegeben. Sie sollten die Meldung BUILD SUCCESS sehen, die angibt, dass das Projekt fehlerfrei erstellt wurde.

## <a name="run-the-app"></a>Ausführen der App
In diesem letzten Schritt führen Sie die Anwendung in einer Laufzeitumgebung auf dem lokalen Server aus.

Wenn Sie noch keine Server-Laufzeitumgebung in Eclipse angegeben haben, müssen Sie dies zunächst tun.

1. Erweitern Sie in Project Explorer **WebContent**.
2. Klicken Sie mit der rechten Maustaste auf **Search.jsp** > **Ausführen als** > **Auf Server ausführen**. Wählen Sie den Apache Tomcat-Server aus, und klicken Sie dann auf **Run**.

> [!TIP]
> Wenn Sie das Projekt nicht im Standardarbeitsbereich gespeichert haben, müssen Sie die Einstellung für **Run Configuration** ändern, damit sie auf den Speicherort des Projekts verweist, um einen Server-Startfehler zu vermeiden. Klicken Sie in Projektexplorer mit der rechten Maustaste auf **Search.jsp** > **Ausführen als** > **Konfigurationen ausführen**. Wählen Sie den Apache Tomcat-Server aus. Klicken Sie auf **Arguments**. Klicken Sie auf **Arbeitsbereich** oder **Dateisystem**, um den Ordner, der das Projekt enthält, festzulegen.
> 
> 

Wenn Sie die Anwendung ausführen, sollte ein Browserfenster angezeigt werden, das ein Suchfeld zur Eingabe von Suchbegriffen bereitstellt.

Warten Sie etwa eine Minute, bevor Sie auf **Search** klicken, damit der Dienst Zeit zum Erstellen und Laden des Index hat. Wenn ein HTTP 404-Fehler angezeigt wird, müssen Sie nur etwas länger warten, bevor Sie es erneut versuchen.

## <a name="search-on-usgs-data"></a>Suchen nach USGS-Daten
Das USGS-Dataset enthält Datensätze, die für den Bundesstaat Rhode Island relevant sind. Wenn Sie auf **Suchen** klicken und das Suchfeld leer ist, erhalten Sie die ersten 50 Einträge. Dies ist die Standardeinstellung.

Durch die Eingabe eines Suchbegriffs erhält das Suchmodul eine Vorgabe. Geben Sie einen regionalen Namen ein. "Roger Williams" war die erste Gouverneur von Rhode Island. Zahlreiche Parks, Gebäude und Schulen sind nach ihm benannt.

![][11]

Sie können auch einen dieser Begriffe ausprobieren:

* Pawtucket
* Pembroke
* goose +cape

## <a name="next-steps"></a>Nächste Schritte
Dies ist das erste Azure Search-Lernprogramm, das auf Java und dem USGS-Dataset basiert. Im Laufe der Zeit werden wir dieses Lernprogramm erweitern, um zusätzliche Suchfunktionen zu veranschaulichen, die Sie in benutzerdefinierten Lösungen vielleicht verwenden möchten.

Wenn Sie bereits über Erfahrungen mit Azure Search verfügen, können Sie dieses Beispiel als Ausgangspunkt für weitere Experimente nutzen, um die [Suchseite](search-pagination-page-layout.md) zu erweitern oder eine [Facettennavigation](search-faceted-navigation.md) zu implementieren. Sie können auch die Suchergebnisseite verbessern, indem Sie Statistiken hinzufügen und Dokumente mit Batchvorgängen verarbeiten, damit die Benutzer die Ergebnisse seitenweise anzeigen können.

Neu bei Azure Search? Es wird empfohlen, auch andere Lernprogramme zu bearbeiten, damit Sie ein Verständnis dafür entwickeln, was Sie erstellen können. Besuchen Sie unsere [Dokumentationsseite](https://azure.microsoft.com/documentation/services/search/) , um weitere Ressourcen zu finden. 

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
