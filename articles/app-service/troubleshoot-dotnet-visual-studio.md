---
title: Problembehandlung mit Visual Studio
description: Erfahren Sie mehr über die Problembehandlung für eine App Service-App mithilfe von Remotedebugging-, Ablaufverfolgungs- und Protokollierungstools, die in Visual Studio 2013 integriert sind.
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: a177b22f0f91d82013956bff36eaa57a084c27d1
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576581"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Problembehandlung von Apps in Azure App Service mit Visual Studio
## <a name="overview"></a>Übersicht
In diesem Tutorial lernen Sie die Visual Studio-Tools zum Debuggen von Apps in [App Service](./overview.md) kennen. Sie führen die App remote im [Debugmodus](/visualstudio/debugger/) aus oder zeigen Anwendungs- und Webserverprotokolle an.

Sie lernen Folgendes:

* In Visual Studio verfügbare App-Verwaltungsfunktionen
* Verwenden der Remoteansicht in Visual Studio für schnelle Änderungen an Remote-Apps
* Ausführen des Debugmodus im Remotebetrieb, während ein Projekt in Azure ausgeführt wird, für Apps und für WebJobs
* Erstellen von Anwendungs-Ablaufprotokollen und Live-Anzeige dieser Protokolle.
* Anzeige von Webserverprotokollen inklusive detaillierter Fehlermeldungen und Verfolgung fehlgeschlagener Anforderungen.
* Senden von Diagnoseprotokollen an ein Azure-Speicherkonto und Anzeige der Protokolle.

Wenn Sie über Visual Studio Ultimate verfügen, können Sie auch [IntelliTrace](/visualstudio/debugger/intellitrace) zum Debuggen verwenden. IntelliTrace wird in diesem Lernprogramm nicht behandelt.

## <a name="prerequisites"></a><a name="prerequisites"></a>Voraussetzungen
Dieses Tutorial verwendet die Entwicklungsumgebung, das Webprojekt und die App Service-App, die Sie unter [Erstellen von ASP.NET-Apps in Azure App Service](quickstart-dotnet-framework.md) eingerichtet haben. Für die WebJobs-Abschnitte benötigen Sie die Anwendung, die Sie in den [ersten Schritten mit dem Azure WebJobs SDK][GetStartedWJ] erstellen.

Die Codebeispiele in diesem Lernprogramm stammen aus einer C# MVC-Webanwendung. Die Prozeduren gelten jedoch auch für die Problembehandlung in Visual Basic- und Web Forms-Anwendungen.

Dieses Tutorial setzt voraus, dass Sie Visual Studio 2019 verwenden. 

Die Streamingprotokoll-Funktion funktioniert nur für Anwendungen, die das .NET Framework 4 oder später verwenden.

## <a name="app-configuration-and-management"></a><a name="sitemanagement"></a>App-Konfiguration und -Verwaltung
Visual Studio bietet Zugriff auf einen Teil der App-Verwaltungsfunktionen und -Konfigurationseinstellungen, die im [Azure-Portal](https://go.microsoft.com/fwlink/?LinkId=529715) verfügbar sind. In diesem Abschnitt lernen Sie die Optionen kennen, die bei Verwendung des **Server-Explorers** verfügbar sind. Um die neuesten Azure-Integrationsfeatures kennen zu lernen, probieren Sie auch den **Cloud-Explorer** aus. Sie können beide Fenster über das Menü **Ansicht** öffnen.

1. Wenn Sie in Visual Studio noch nicht bei Azure angemeldet sind, klicken Sie mit der rechten Maustaste auf **Azure**, und wählen Sie die Verbindung mit Ihrem **Microsoft Azure-Abonnement** im **Server-Explorer** aus.

    Als Alternative können Sie ein Verwaltungszertifikat installieren, das den Zugriff auf Ihr Konto ermöglicht. Wenn Sie ein Zertifikat installieren möchten, klicken Sie im **Server-Explorer** mit der rechten Maustaste auf den Knoten **Azure**, und wählen Sie im Kontextmenü **Abonnements verwalten und filtern** aus. Klicken Sie im Dialogfeld **Microsoft Azure-Abonnements verwalten** auf die Registerkarte **Zertifikate** und dann auf **Importieren**. Befolgen Sie die Anweisungen zum Herunterladen und Importieren einer Abonnementdatei (auch *.publishsettings* -Datei genannt) für Ihr Azure-Konto.

   > [!NOTE]
   > Wenn Sie eine Abonnementdatei herunterladen, sollten Sie diese in einem Ordner außerhalb Ihrer Quellcodeverzeichnisse speichern (beispielsweise im Ordner "Downloads") und nach Abschluss des Importvorgangs löschen. Böswillige Benutzer, die Zugriff auf die Abonnementdatei erlangen, können Ihre Azure-Services bearbeiten, erstellen und löschen.
   >
   >

    Weitere Informationen für Verbindungen zwischen Visual Studio und Azure-Ressourcen finden Sie unter [Verwalten von Konten, Abonnements und Administratorrollen](../role-based-access-control/role-assignments-portal.md).
2. Erweitern Sie im **Server-Explorer** den Knoten **Azure**, und erweitern Sie dann **App Service**.
3. Erweitern Sie die Ressourcengruppe, die die in [Erstellen einer ASP.NET-App in Azure App Service](quickstart-dotnet-framework.md) erstellte App enthält, klicken Sie mit der rechten Maustaste auf den App-Knoten, und klicken Sie dann auf **Anzeigeeinstellungen**.

    ![Anzeigeeinstellungen im Server-Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    Die Registerkarte **Azure-Web-App** wird angezeigt, und Sie sehen dort die in Visual Studio verfügbaren Aufgaben für die App-Verwaltung und -Konfiguration.

    ![Fenster "Azure-Web-App"](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    In diesem Tutorial verwenden Sie die Auswahllisten für Protokollierung und Ablaufverfolgung. Außerdem werden Sie Remotedebugging verwenden, allerdings wird diese Option auf andere Weise aktiviert.

    Weitere Informationen zu den Feldern für App-Einstellungen und Verbindungszeichenfolgen in diesem Fenster finden Sie unter [Azure App Service: Funktionsweise von Anwendungs- und Verbindungszeichenfolgen](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/).

    Wenn Sie eine App-Verwaltungsaufgabe ausführen möchten, die in diesem Fenster nicht ausgeführt werden kann, klicken Sie auf **Im Verwaltungsportal öffnen**, um ein Browserfenster im Azure-Portal zu öffnen.

## <a name="access-app-files-in-server-explorer"></a><a name="remoteview"></a>Zugreifen auf App-Dateien im Server-Explorer
Das `customErrors`-Kennzeichen in der Datei "Web.config" ist bei der Bereitstellung eines Webprojekts üblicherweise auf `On` oder `RemoteOnly` festgelegt. Das bedeutet, dass Sie bei einem Problem keine erklärende Fehlermeldung erhalten. Für viele Fehler wird eine der folgenden Seiten angezeigt:

**Serverfehler in '/'-Anwendung:**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png" alt-text="Screenshot eines Serverfehlers in der '/'-Anwendung in einem Webbrowser":::

**Ein Fehler ist aufgetreten:**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png" alt-text="Screenshot eines Beispiels für einen allgemeinen Fehler in einem Webbrowser":::

**Die Website kann die Seite nicht anzeigen**

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png" alt-text="Screenshot des Fehlers „Die Website kann die Seite nicht anzeigen“ in einem Webbrowser":::

Häufig ist der einfachste Weg für die Suche nach der Fehlerursache die Aktivierung detaillierter Fehlermeldungen. Im ersten der vorherigen Screenshots wird dies erläutert. Dazu ist eine Änderung an der bereitgestellten Web.config-Datei erforderlich. Sie können die Datei *Web.config* im Projekt bearbeiten und das Projekt neu bereitstellen oder eine [Web.config-Transformation](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) erstellen und eine Debugversion bereitstellen. Es gibt aber noch einen einfacheren Weg: Im **Projektmappen-Explorer** können Sie Dateien in der Remote-App mithilfe des Features *Remoteansicht* direkt anzeigen und bearbeiten.

1. Erweitern Sie im **Server-Explorer** den Knoten **Azure**, erweitern Sie **App Service**, erweitern Sie die Ressourcengruppe, in der sich die App befindet, und erweitern Sie dann den Knoten für die App.

    Daraufhin werden Knoten angezeigt, über die Sie auf die Inhalts- und Protokolldateien der App zugreifen können.
2. Erweitern Sie den Knoten **Dateien** und doppelklicken Sie auf die Datei *Web.config* .

    ![Öffnen der Datei Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio öffnet die Datei „Web.config“ in der Remote-App und zeigt den Text „[Remote]“ auf der Titelleiste neben dem Dateinamen an.
3. Fügen Sie dem `system.web` -Element die folgende Zeile hinzu:

    `<customErrors mode="Off"></customErrors>`

    ![Bearbeiten der Datei Web.config](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. Aktualisieren Sie den Browser, der die wenig hilfreiche Fehlermeldung anzeigt, und Sie erhalten eine detailliertere Fehlermeldung, wie im folgenden Beispiel gezeigt:

    ![Detaillierte Fehlermeldung](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (Dieser Fehler wurde durch Hinzufügen der in rot angezeigten Zeile zu *Views\Home\Index.cshtml* erstellt.)

Änderungen an der Datei „Web.config“ sind nur eines der Szenarien, in denen die Möglichkeit zum Lesen und Bearbeiten von Dateien in Ihrer App Service-App die Problembehandlung erleichtert.

## <a name="remote-debugging-apps"></a><a name="remotedebug"></a>Remotedebuggen von Apps
Falls die detaillierte Fehlermeldung nicht genügend Informationen liefert und sich der Fehler nicht lokal reproduzieren lässt, können Sie die Website remote im Debugmodus ausführen. Im Debugmodus können Sie Breakpoints setzen, den Speicher direkt manipulieren, Code schrittweise durchlaufen und sogar den Codepfad ändern.

Remotedebuggen funktioniert nicht in den Express-Editionen von Visual Studio.

Dieser Abschnitt veranschaulicht das Remotedebuggen anhand des Projekts, das Sie in [Erstellen einer ASP.NET-App in Azure App Service](quickstart-dotnet-framework.md) erstellt haben.

1. Öffnen Sie das Webprojekt, das Sie in [Erstellen einer ASP.NET-App in Azure App Service](quickstart-dotnet-framework.md) erstellt haben.

1. Öffnen Sie *Controllers\HomeController.cs*.

1. Löschen Sie die `About()` -Methode, und fügen Sie stattdessen den folgenden Code ein.

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. [Setzen Sie einen Haltepunkt](/visualstudio/debugger/) in der Zeile: `ViewBag.Message`.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und klicken Sie anschließend auf **Veröffentlichen**.

1. Wählen Sie in der Dropdownliste **Profil** dasselbe Profil aus, das Sie in [Erstellen einer ASP.NET-App in Azure App Service](quickstart-dotnet-framework.md) verwendet haben. Klicken Sie dann auf „Einstellungen“.

1. Klicken Sie im Dialogfeld **Veröffentlichen** auf die Registerkarte **Einstellungen**, ändern Sie **Konfiguration** in **Debug**, und klicken Sie anschließend auf **Speichern**.

    ![Veröffentlichen im Debugmodus](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. Klicken Sie auf **Veröffentlichen**. Nachdem die Bereitstellung abgeschlossen ist und Ihr Browser mit der Azure-URL Ihrer App geöffnet wird, schließen Sie den Browser.

1. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf Ihre App, und klicken Sie dann auf **Debugger anfügen**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png" alt-text="Screenshot des Server-Explorer-Fensters, in dem nach dem Auswählen einer App „Debugger anfügen“ ausgewählt wird":::

    Der Browser öffnet automatisch Ihre Startseite in Azure. Möglicherweise müssen Sie ca. 20 Sekunden warten, während Azure die den Server zum Debuggen einrichtet. Diese Verzögerung tritt nur bei der ersten Ausführung einer App im Debugmodus innerhalb von 48 Stunden auf. Wenn Sie das Debuggen im gleichen Zeitraum erneut starten, tritt keine Verzögerung auf.

    > [!NOTE] 
    > Wenn beim Starten des Debuggers Probleme auftreten, versuchen Sie es über den **Cloud-Explorer** anstelle des **Server-Explorers**.
    >

1. Klicken Sie im Menü auf **Info** .

    Visual Studio hält am Breakpoint an, wobei der Code nicht auf Ihrem lokalen Computer läuft, sondern unter Azure.

1. Zeigen Sie auf die Variable `currentTime` , um den Zeitwert anzuzeigen.

    ![Anzeigen von Variablen im Debugmodus in Azure](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    Bei der angezeigten Zeit handelt es sich um die Azure-Serverzeit, deren Zeitzone sich von Ihrer lokalen Einstellung unterscheiden kann.

1. Geben Sie einen neuen Wert für die Variable `currentTime` ein, z. B. "Ausführung unter Azure".

1. Drücken Sie F5, um die Ausführung fortzusetzen.

     Die Info-Seite unter Azure zeigt daraufhin den neuen Wert an, den Sie für die Variable currentTime eingegeben haben.

     ![Info-Seite mit neuem Wert](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remote-debugging-webjobs"></a><a name="remotedebugwj"></a> Remotedebuggen von WebJobs
Dieser Abschnitt zeigt, wie Sie das Projekt und die App, die Sie in [Erste Schritte mit dem Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki) erstellen, remote debuggen können.

Die in diesem Abschnitt dargestellten Funktionen sind nur in Visual Studio 2013 mit Update 4 oder höher verfügbar.

Remotedebuggen funktioniert nur bei kontinuierlichen WebJobs. Geplante und bedarfsabhängige WebJobs unterstützen Debuggen nicht.

1. Öffnen Sie das Webprojekt, das Sie in den [ersten Schritten mit dem Azure WebJobs SDK][GetStartedWJ] erstellt haben.

2. Öffnen Sie *Functions.cs* im Projekt "ContosoAdsWebJob".

3. [Legen Sie einen Haltepunkt](/visualstudio/debugger/) für die erste Anweisung in der `GnerateThumbnail`-Methode fest.

    ![Haltepunkt setzen](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Webprojekt (nicht das WebJob-Projekt), und klicken Sie auf **Veröffentlichen**.

5. Wählen Sie in der Dropdownliste **Profil** das gleiche Profil aus, das Sie in [Erste Schritte mit dem Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki)verwendet haben.

6. Wechseln Sie zur Registerkarte **Einstellungen**, ändern Sie **Konfiguration** in **Debug**, und klicken Sie anschließend auf **Veröffentlichen**.

    Visual Studio stellt die Web- und WebJob-Projekte bereit, und Ihr Browser wird mit der Azure-URL Ihrer App geöffnet.

7. Erweitern Sie im **Server-Explorer** nacheinander **Azure > App Service > Ihre Ressourcengruppe > Ihre App > WebJobs > Fortlaufend**, und klicken Sie dann mit der rechten Maustaste auf **ContosoAdsWebJob**.

8. Klicken Sie auf **Debugger anfügen**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png" alt-text="Screenshot von Server-Explorer mit ausgewähltem ContosoAdsWebJob im Dropdownmenü und ausgewählter Option „Debugger anfügen“":::

    Der Browser öffnet automatisch Ihre Startseite in Azure. Möglicherweise müssen Sie ca. 20 Sekunden warten, während Azure die den Server zum Debuggen einrichtet. Diese Verzögerung tritt nur bei der ersten Ausführung einer App im Debugmodus innerhalb von 48 Stunden auf. Wenn Sie das Debuggen im gleichen Zeitraum erneut starten, tritt keine Verzögerung auf.

9. Erstellen Sie in dem Webbrowser, in dem die Contoso Ads-Startseite geöffnet ist, eine neue Anzeige.

    Durch Erstellen einer Anzeige wird eine Warteschlangennachricht erstellt, die vom WebJob übernommen und bearbeitet wird. Wenn das WebJobs SDK die Funktion zur Verarbeitung der Warteschlangennachricht aufruft, erreicht der Code den Haltepunkt.

10. Wenn der Debugger am Breakpoint unterbrochen wird, können Sie Variablenwerte überprüfen und ändern, während das Programm die Cloud ausführt. In der folgenden Abbildung zeigt der Debugger den Inhalt des blobInfo-Objekts an, das an die `GenerateThumbnail`-Methode übergeben wurde.

     ![BlobInfo-Objekt im Debugger](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. Drücken Sie F5, um die Ausführung fortzusetzen.

     Die `GenerateThumbnail`-Methode schließt die Erstellung der Miniaturansicht ab.

12. Aktualisieren Sie im Browser die Indexseite, um die Miniaturansicht anzuzeigen.

13. Drücken Sie in Visual Studio UMSCHALT + F5, um das Debuggen zu beenden.

14. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf den Knoten „ContosoAdsWebJob“, und klicken Sie dann auf **Dashboard anzeigen**.

15. Melden Sie sich mit Ihren Azure-Anmeldeinformationen an, und klicken Sie dann auf den WebJob-Namen, um zu der Seite für den WebJob zu wechseln.

     ![Auf ContosoAdsWebJob klicken](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     Das Dashboard zeigt, dass die `GenerateThumbnail`-Funktion vor Kurzem ausgeführt wurde.

     (Wenn Sie nächstes Mal auf **Dashboard anzeigen** klicken, müssen Sie sich nicht anmelden, und der Browser wechselt direkt zur Seite für Ihren WebJob.)

16. Klicken Sie auf den Funktionsnamen, um Details zur Ausführung der Funktion anzuzeigen.

     ![Funktionsdetails](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

Wenn die Funktion [Protokolle geschrieben hat](https://github.com/Azure/azure-webjobs-sdk/wiki), können Sie auf **ToggleOutput** klicken, um sie anzuzeigen.

## <a name="notes-about-remote-debugging"></a>Hinweise zum Remotedebuggen

* Vermeiden Sie es, den Debugmodus in Produktion einzusetzen. Wenn Ihre Produktions-App nicht auf mehrere Serverinstanzen dezentral skaliert wird, verhindert das Debuggen, dass der Webserver auf andere Anforderungen reagiert. Falls Sie mehrere Webserverinstanzen betreiben, erhalten Sie beim Anhängen des Debuggers eine zufällige Instanz, und Sie können nicht garantieren, dass Ihre Browseranforderungen an dieselbe Instanz gehen. Außerdem ist es unüblich, Debugversionen in Produktion bereitzustellen, und Compileroptimierungen für Releaseversionen verhindern die zeilenweise Anzeige der Vorgänge in Ihrem Quellcode. Die beste Möglichkeit zur Problembehandlung von Produktionsproblemen sind Ablaufverfolgung und Webserverprotokolle.
* Vermeiden Sie es beim Remotedebuggen, lange an Breakpoints anzuhalten. Azure behandelt Prozesse, die länger als einige Minuten angehalten sind, als nicht reagierend und beendet diese.
* Beim Debuggen schickt der Server Daten an Visual Studio und verursacht möglicherweise zusätzliche Kosten für Bandbreite. Weitere Informationen zu Bandbreitentarifen finden Sie unter [Azure Pricing](https://azure.microsoft.com/pricing/calculator/)(Azure-Preisübersicht, in englischer Sprache).
* Stellen Sie sicher, dass das `debug`-Attribut im `compilation`-Element in der Datei *Web.config* auf „true“ festgelegt ist. Dieses Attribut ist beim Veröffentlichen einer Debug-Buildkonfiguration standardmäßig "true".

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* Falls der Debugger nicht in den gewünschten Code wechselt, müssen Sie möglicherweise die Einstellung „Nur eigenen Code“ ändern.  Weitere Informationen finden Sie unter [Angeben, ob nur das Debuggen von Benutzercode mit „Nur eigenen Code“ in Visual Studio erfolgen soll](/visualstudio/debugger/just-my-code).
* Bei Aktivierung der Remotedebuggen-Funktion startet ein Timer auf dem Server, und die Funktion wird nach 48 Stunden automatisch abgeschaltet. Dieses Limit von 48 Stunden existiert aus Sicherheits- und Leistungsgründen. Sie können die Funktion jederzeit und beliebig oft aktivieren. Wenn Sie nicht aktiv debuggen, sollten Sie die Funktion jedoch deaktivieren.
* Sie können den Debugger manuell an einen beliebigen Prozess anfügen, nicht nur an den App-Prozess („w3wp.exe“). Weitere Informationen zum Debugmodus in Visual Studio finden Sie unter [Debuggen in Visual Studio](/visualstudio/debugger/debugging-in-visual-studio).

## <a name="diagnostic-logs-overview"></a><a name="logsoverview"></a>Übersicht über Diagnoseprotokolle
ASP.NET-Anwendungen in App Service-Apps können die folgenden Arten von Protokollen generieren:

* **Anwendungsnachverfolgungsprotokolle**<br/>
  Anwendungen erzeugen diese Protokolle, indem sie Methoden der Klasse [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) aufrufen.
* **Webserverprotokolle**<br/>
  Der Webserver erstellt einen Protokolleintrag für jede HTTP-Anforderung an die App.
* **Ausführliche Fehlerprotokolle**<br/>
  Der Webserver erstellt eine HTML-Seite mit zusätzlichen Informationen für fehlerhafte HTTP-Anforderungen (Anforderungen mit einem Statuscode von 400 oder höher).
* **Nachverfolgungsprotokolle für Anforderungen mit Fehlern**<br/>
  Der Webserver erstellt eine XML-Datei mit detaillierten Ablaufverfolgungsinformationen für fehlgeschlagene HTTP-Anforderungen. Der Webserver liefert außerdem eine XSL-Datei zur Formatierung der XML-Datei in einem Browser.

Protokollierung beeinträchtigt die Leistung von Apps. Daher können Sie die verschiedenen Protokolltypen unter Azure bei Bedarf einzeln aktivieren und deaktivieren. Für Anwendungsprotokolle können Sie angeben, dass nur Protokolleinträge oberhalb eines bestimmten Schweregrads geschrieben werden sollen. Bei der Erstellung neuer Apps ist sämtliche Protokollierung standardmäßig deaktiviert.

Die Protokolle werden in den Ordner *LogFiles* im Dateisystem Ihrer App geschrieben und sind über FTP zugänglich. Webserver- und Anwendungsprotokolle können auch in ein Azure Storage-Konto geschrieben werden. Speicherkonten bieten mehr Kapazität für Protokolle als das Dateisystem. Protokolle im Dateisystem sind beschränkt auf 100 Megabyte. (Protokolle im Dateisystem werden nur für kurze Zeit aufbewahrt. Azure löscht alte Protokolldateien, um Platz für neue Dateien zu machen, wenn das Limit erreicht ist.)  

## <a name="create-and-view-application-trace-logs"></a><a name="apptracelogs"></a>Erstellen und Anzeigen von Anwendungs-Ablaufprotokollen
In diesem Abschnitt führen Sie die folgenden Aufgaben aus:

* Fügen Sie dem Webprojekt, das Sie in [Erste Schritte mit Azure und ASP.NET](quickstart-dotnet-framework.md) erstellt haben, Ablaufverfolgungsanweisungen hinzu.
* Anzeigen der Protokolle, wenn Sie das Projekt lokal ausführen.
* Anzeigen der Protokolle, während diese von der Anwendung unter Azure generiert werden.

Informationen zum Erstellen von Anwendungsprotokollen in WebJobs finden Sie unter [Verwenden des Azure-Warteschlangenspeichers mithilfe des WebJobs SDK - Schreiben von Protokollen](https://github.com/Azure/azure-webjobs-sdk/wiki). Die folgenden Anweisungen für das Anzeigen von Protokollen und das Speichern in Azure gelten auch für Anwendungsprotokolle, die von WebJobs erstellt werden.

### <a name="add-tracing-statements-to-the-application"></a>Hinzufügen von Ablaufverfolgungs-Anweisungen zur Anwendung
1. Öffnen Sie *Controllers\HomeController.cs*, und ersetzen Sie die Methoden `Index`, `About`, und `Contact` durch den folgenden Code, um `Trace`-Anweisungen und eine `using`-Anweisung für `System.Diagnostics` hinzuzufügen:

    ```csharp
    public ActionResult Index()
    {
        Trace.WriteLine("Entering Index method");
        ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
        Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Index method");
        return View();
    }
    
    public ActionResult About()
    {
        Trace.WriteLine("Entering About method");
        ViewBag.Message = "Your app description page.";
        Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
        Trace.WriteLine("Leaving About method");
        return View();
    }
    
    public ActionResult Contact()
    {
        Trace.WriteLine("Entering Contact method");
        ViewBag.Message = "Your contact page.";
        Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
        Trace.WriteLine("Leaving Contact method");
        return View();
    }        
    ```

1. Fügen Sie oben in der Datei eine `using System.Diagnostics;` -Anweisung hinzu.

### <a name="view-the-tracing-output-locally"></a>Lokale Anzeige der Ablaufverfolgungs-Ausgabe
1. Drücken Sie F5, um die Anwendung im Debugmodus auszuführen.

    Der Standard-Ablaufverfolgungs-Listener schreibt sämtliche Ausgaben in das **Ausgabefenster** , zusammen mit anderen Debugausgaben. Die folgende Abbildung zeigt die Ausgaben der Ablaufverfolgungsanweisungen, die Sie der `Index` -Methode hinzugefügt haben.

    ![Ablaufverfolgung im Debugfenster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    Anhand der folgenden Schritte können Sie Ablaufverfolgungs-Ausgaben in einer Webseite anzeigen, ohne im Debugmodus zu kompilieren.
1. Öffnen Sie die Web.config-Datei der Anwendung (die Datei im Projektordner) und fügen Sie das Element `<system.diagnostics>` am Ende der Datei direkt vor dem abschließenden `</configuration>`-Element ein:

    ``` xml
    <system.diagnostics>
    <trace>
      <listeners>
        <add name="WebPageTraceListener"
            type="System.Web.WebPageTraceListener,
            System.Web,
            Version=4.0.0.0,
            Culture=neutral,
            PublicKeyToken=b03f5f7f11d50a3a" />
      </listeners>
    </trace>
    </system.diagnostics>
    ```

Über `WebPageTraceListener` können Sie die Ausgabe der Ablaufverfolgung anzeigen, indem Sie zu `/trace.axd` navigieren.
1. Fügen Sie ein <a href="/previous-versions/dotnet/netframework-4.0/6915t83k(v=vs.100)">trace-Element</a> unter `<system.web>` in die Web.config-Datei ein, wie im folgenden Beispiel gezeigt:

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Drücken Sie STRG+F5, um die Anwendung auszuführen.
1. Fügen Sie der URL in der Adressleiste des Browserfensters *trace.axd* hinzu, und drücken Sie die EINGABETASTE (die URL sollte dieser ähneln: `http://localhost:53370/trace.axd`).
1. Klicken Sie auf der Seite **Anwendungsablaufverfolgung** auf **Details anzeigen** in der ersten Zeile (nicht in der BrowserLink-Zeile).

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png" alt-text="Screenshot der Seite „Anwendungsüberwachung“ in einem Webbrowser mit ausgewählter Option „Details anzeigen“ in der ersten Zeile":::

    Daraufhin wird die Seite **Anforderungsdetails** angezeigt, und im Bereich **Überwachungsinformationen** sehen Sie die Ausgabe der Ablaufverfolgungs-Anweisungen, die Sie der `Index`-Methode hinzugefügt haben.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png" alt-text="Screenshot der Seite „Anforderungsdetails“ in einem Webbrowser mit einer im Abschnitt „Ablaufverfolgungsinformationen.“ hervorgehobenen Meldung":::

    `trace.axd` ist standardmäßig nur lokal verfügbar. Sie können dem `trace`-Element in der Datei *Web.config* die Zeile `localOnly="false"` hinzufügen, um die Datei in einer Remote-App verfügbar zu machen, wie im folgenden Beispiel gezeigt:

    ```xml
    <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />
    ```

    Das Aktivieren von `trace.axd` in einer Produktions-App wird jedoch aus Sicherheitsgründen nicht empfohlen. In den folgenden Abschnitten finden Sie eine einfachere Möglichkeit zum Lesen von Ablaufverfolgungsprotokollen in einer App Service-App.

### <a name="view-the-tracing-output-in-azure"></a>Anzeige der Ablaufverfolgungs-Ausgabe in Azure
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Webprojekt und anschließend auf **Veröffentlichen**.
2. Klicken Sie im Dialogfeld **Web veröffentlichen** auf **Veröffentlichen**.

    Nachdem Visual Studio Ihr Update veröffentlicht hat, wird ein Browserfenster mit Ihrer Startseite geöffnet (sofern Sie **Ziel-URL** auf der Registerkarte **Verbindung** nicht gelöscht haben).
3. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf die App, und wählen Sie **Streamingprotokolle anzeigen** aus.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png" alt-text="Screenshot von Server-Explorer nach dem Klicken mit der rechten Maustaste auf Ihre App mit ausgewählter Option „Streamingprotokolle anzeigen“ in einem neuen Fenster":::

    Das **Ausgabefenster** zeigt an, dass Sie mit dem Protokollstreamingdienst verbunden sind und fügt eine Benachrichtigungszeile für jede Minute hinzu, in der kein anzuzeigendes Protokoll eingeht.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png" alt-text="Screenshot des Ausgabefensters mit einem Beispiel für eine Verbindung mit einem Protokollstreamingdienst mit Benachrichtigungszeilen":::

4. Klicken Sie im Browserfenster, in dem die Startseite Ihrer Anwendung geöffnet ist, auf **Contact**.

    Innerhalb weniger Sekunden wird die Ausgabe der Ablaufverfolgung mit dem Schweregrad „Fehler“, die Sie der `Contact`-Methode hinzugefügt haben, im **Ausgabefenster** angezeigt.

    ![Fehler-Ablaufverfolgung im Ausgabefenster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio zeigt standardmäßig nur Ablaufverfolgungsprotokolle mit dem Schweregrad Fehler an, wenn der Protokollüberwachungsdienst gestartet wird. Beim Erstellen einer neuen App Service-App ist sämtliche Protokollierung standardmäßig deaktiviert, wie Sie zuvor auf der Einstellungsseite gesehen haben:

    ![Anwendungsprotokollierung deaktiviert](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    Bei Auswahl von **Streamingprotokolle anzeigen** wurde jedoch von Visual Studio automatisch **Anwendungsprotokollierung (Dateisystem)** auf **Fehler** festgelegt, sodass Protokolle mit dem Schweregrad „Fehler“ gemeldet werden. Sie können diese Einstellung auf **Ausführlich** ändern, falls Sie sämtliche Ablaufverfolgungs-Protokolle sehen möchten. Wenn Sie einen Schweregrad unterhalb von Fehler auswählen, werden die Protokolleinträge der höheren Schweregrade ebenfalls geschrieben. Wenn Sie also ausführlich auswählen, werden die Protokolleinträge für Information, Warnung und Fehler geschrieben.  

5. Klicken Sie im **Server-Explorer** mit der rechten Maustaste auf die App, und klicken Sie dann wie zuvor auf **Anzeigeeinstellungen**.
6. Ändern Sie **Anwendungsprotokollierung (Dateisystem)** in **Ausführlich**, und klicken Sie auf **Speichern**.

    ![Einstellen des Ablaufverfolgungs-Schweregrads auf Ausführlich](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. Klicken Sie im Browserfenster, in dem Ihre **Kontakt**-Seite angezeigt wird, auf **Start**, anschließend auf **Info** und dann auf **Kontakt**.

    Innerhalb weniger Sekunden wird im **Ausgabefenster** Ihre gesamte Ablaufverfolgungsausgabe angezeigt.

    ![Ablaufverfolgungs-Schweregrad Ausführlich](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    In diesem Abschnitt haben Sie die Protokollierung über die App-Einstellungen aktiviert und deaktiviert. Sie können die Ablaufverfolgungs-Listener auch über die Datei Web.config aktivieren bzw. deaktivieren. Wenn Sie jedoch die Datei „Web.config“ ändern, wird die App-Domäne neu gestartet. Dies geschieht bei Aktivierung der Protokollierung über die App-Konfiguration nicht. Falls das Problem schwer zu reproduzieren ist oder nur zeitweilig auftritt, kann es passieren, dass das Problem beim Neustart der Domäne verschwindet und Sie warten müssen, bis es erneut auftritt. Durch Aktivieren der Diagnose in Azure können Sie sofort mit der Erfassung der Fehlerinformationen beginnen, ohne die App-Domäne neu starten zu müssen.

### <a name="output-window-features"></a>Funktionen des Ausgabefensters
Die Registerkarte **Microsoft Azure-Protokolle** im **Ausgabefenster** enthält verschiedene Schaltflächen und ein Textfeld:

:::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png" alt-text="Screenshot der Schaltflächen und des Textfelds auf der Registerkarte „Microsoft Azure-Protokolle“ im Ausgabefenster":::

Diese Schaltflächen bieten die folgenden Funktionen:

* Löschen des **Ausgabefensters** .
* Aktivieren bzw. Deaktivieren des Zeilenumbruchs.
* Starten bzw. Stoppen der Protokollüberwachung.
* Angeben, welche Protokolle überwacht werden sollen.
* Herunterladen von Protokollen.
* Filtern von Protokollen anhand von Suchzeichenfolgen oder regulären Ausdrücken.
* Schließen des **Ausgabefensters** .

Wenn Sie eine Suchzeichenfolge oder einen regulären Ausdruck eingeben, filtert Visual Studio die Protokollinformationen clientseitig. Daher können Sie die Kriterien eingeben, nachdem die Protokolle im **Ausgabefenster** angezeigt wurden, und Sie können die Filterkriterien ändern, ohne die Protokolle neu generieren zu müssen.

## <a name="view-web-server-logs"></a><a name="webserverlogs"></a>Erstellen von Webserverprotokollen
Webserverprotokolle zeichnen sämtliche HTTP-Aktivitäten für die App auf. Sie müssen diese Protokolle für die App aktivieren und Visual Studio mitteilen, dass Sie sie überwachen möchten, um sie im **Ausgabefenster** anzeigen zu können.

1. Ändern Sie auf der Registerkarte **Azure Web App Configuration** (Azure-Web-App-Konfiguration), die Sie im **Server-Explorer** geöffnet haben, die Webserverprotokollierung in **Ein**, und klicken Sie dann auf **Speichern**.

    ![Aktivieren der Webserverprotokollierung](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. Klicken Sie im **Ausgabefenster** auf die Schaltfläche **Geben Sie an, welche Microsoft Azure-Protokolle überwacht werden sollen**.

    ![Angeben, welche Azure-Protokolle überwacht werden sollen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. Wählen Sie im Dialogfeld **Microsoft Azure-Protokollierungsoptionen** die Option **Webserverprotokolle** aus, und klicken Sie dann auf **OK**.

    ![Überwachen von Webserverprotokollen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. Klicken Sie im Browserfenster, in dem Ihre App angezeigt wird, auf **Start**, anschließend auf **Info** und dann auf **Kontakt**.

    Normalerweise werden die Anwendungsprotokolle zuerst angezeigt, gefolgt von den Webserverprotokollen. Möglicherweise müssen Sie kurz warten, bis die Protokolle angezeigt werden.

    ![Webserverprotokolle im Ausgabefenster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Bei der ersten Aktivierung der Webserverprotokolle in Visual Studio schreibt Azure die Protokolle standardmäßig in das Dateisystem. Alternativ können Sie im Azure-Portal angeben, dass die Webserverprotokolle in einen Blobcontainer in einem Speicherkonto geschrieben werden sollen.

Wenn Sie die Webserverprotokollierung für ein Azure-Speicherkonto im Portal aktivieren und die Protokollierung anschließend in Visual Studio deaktivieren, werden die Speicherkontoeinstellungen bei der nächsten Aktivierung wiederhergestellt.

## <a name="view-detailed-error-message-logs"></a><a name="detailederrorlogs"></a>Anzeigen detaillierter Fehlermeldungsprotokolle
Die detaillierten Fehlerprotokolle liefern zusätzliche Informationen über HTTP-Anforderungen, die zu einer Fehlerantwort geführt haben (400 oder höher). Sie müssen diese Protokolle für die App aktivieren und Visual Studio mitteilen, dass Sie sie überwachen möchten, um sie im **Ausgabefenster** anzeigen zu können.

1. Ändern Sie auf der Registerkarte **Azure Web App Configuration** (Azure-Web-App-Konfiguration), die Sie im **Server-Explorer** geöffnet haben, die Option **Detaillierte Fehlermeldungen** in **Ein**, und klicken Sie dann auf **Speichern**.

    ![Aktivieren der detaillierten Fehlermeldungen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. Klicken Sie im **Ausgabefenster** auf die Schaltfläche **Geben Sie an, welche Microsoft Azure-Protokolle überwacht werden sollen**.

3. Klicken Sie im Dialogfeld **Microsoft Azure-Protokollierungsoptionen** auf **Alle Protokolle** und dann auf **OK**.

    ![Überwachen aller Protokolle](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. Fügen Sie in der Adressleiste Ihres Browserfensters ein zusätzliches Zeichen an die URL an, um einen 404-Fehler zu verursachen (z. B. `http://localhost:53370/Home/Contactx`), und drücken Sie die EINGABETASTE.

    Nach wenigen Sekunden wird das detaillierte Fehlerprotokoll im **Ausgabefenster** von Visual Studio angezeigt.

    ![Detailliertes Fehlerprotokoll – Ausgabefenster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Klicken Sie bei gedrückter STRG-Taste auf den Link, um das formatierte Protokoll im Browser anzuzeigen:

    ![Detailliertes Fehlerprotokoll – Browserfenster](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="download-file-system-logs"></a><a name="downloadlogs"></a>Herunterladen von Dateisystemprotokollen
Alle Protokolle, die Sie im **Ausgabefenster** überwachen können, lassen sich auch als *.zip* -Datei herunterladen.

1. Klicken Sie im **Ausgabefenster** auf **Streamingprotokolle herunterladen**.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png" alt-text="Screenshot des Ausgabefensters mit hervorgehobener Schaltfläche „Datenstromprotokolle herunterladen“":::

    Der Datei-Explorer öffnet Ihren *Downloads* -Ordner, und die heruntergeladene Datei ist ausgewählt.

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png" alt-text="Screenshot des Ordners „Downloads“ im Datei-Explorer mit ausgewählter heruntergeladener Datei":::

2. Wenn Sie die *.zip* -Datei extrahieren, sehen Sie die folgende Ordnerstruktur:

    :::image type="content" source="./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png" alt-text="Screenshot der Ordnerstruktur der ZIP-Datei, nachdem diese extrahiert wurde":::

   * Ablaufverfolgungsprotokolle von Anwendungen befinden sich in *TXT*-Dateien im Ordner *LogFiles\Application*.
   * Webserverprotokolle befinden sich in *LOG*-Dateien im Ordner *LogFiles\http\RawLogs*. Sie können diese Dateien mit Werkzeugen wie [Log Parser](https://www.iis.net/downloads/community/2010/04/log-parser-22) anzeigen und bearbeiten.
   * Ausführliche Fehlerprotokolle befinden sich in *HTML*-Dateien im Ordner *LogFiles\DetailedErrors*.

     (Der Ordner *deployments* enthält Dateien der Quellcodeverwaltung und hat nichts mit der Veröffentlichung in Visual Studio zu tun. Der Ordner *Git* enthält Ablaufverfolgungsprotokolle für die Quellcodeverwaltung und den Protokollstreamingdienst.)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-account-create.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](https://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="view-failed-request-tracing-logs"></a><a name="failedrequestlogs"></a>Anzeigen von fehlgeschlagenen Anforderungsablaufverfolgungsprotokollen
Anhand der Protokolle für fehlgeschlagene Anforderungen können Sie im Detail herausfinden, wie IIS HTTP-Anforderungen bearbeitet, z. B. in Szenarien mit URL-Neuschreibung oder bei Authentifizierungsproblemen.

App Service-Apps verwenden die gleiche Funktion zum Verfolgen fehlerhafter Anforderungen, die in IIS 7.0 und höher verfügbar ist. Sie haben jedoch keinen Zugriff auf die IIS-Einstellungen, in denen festgelegt wird, welche Fehler protokolliert werden. Wenn Sie die Verfolgung fehlgeschlagener Anforderungen aktivieren, werden alle Fehler erfasst.

Sie können die Protokolle für fehlgeschlagene Anforderungen in Visual Studio aktivieren, allerdings lassen sich diese Protokolle nicht in Visual Studio anzeigen. Diese Protokolle liegen in Form von XML-Dateien vor. Der Streamingprotokolldienst überwacht nur Dateien, die im Nur-Text-Modus lesbar sind: *TXT*-, *HTML*- und *LOG*-Dateien.

Sie können die Protokolle für fehlgeschlagene Anforderungen entweder direkt über FTP im Browser anzeigen oder mit einem FTP-Client auf Ihren lokalen Computer herunterladen. In diesem Abschnitt werden Sie die Protokolle direkt im Browser anzeigen.

1. Ändern Sie auf der Registerkarte **Konfiguration** im Fenster **Azure-Web-App**, das Sie über den **Server-Explorer** geöffnet haben, die Option **Ablaufverfolgung für Anforderungsfehler** in **Ein**, und klicken Sie dann auf **Speichern**.

    ![Aktivieren der Verfolgung fehlgeschlagener Anforderungen](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Fügen Sie der URL der App in der Adressleiste ein zusätzliches Zeichen hinzu, und drücken Sie die EINGABETASTE, um einen 404-Fehler auszulösen.

    Daraufhin wird ein Protokoll für die fehlgeschlagene Anforderung erstellt. In den folgenden Schritten lernen Sie, wie Sie dieses Protokoll anzeigen oder herunterladen können.

3. Klicken Sie in Visual Studio auf der Registerkarte **Konfiguration** im Fenster **Azure-Web-App** auf **Im Verwaltungsportal öffnen**.

4. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf der Seite **Einstellungen** für Ihre App auf **Anmeldeinformationen für die Bereitstellung**, und geben Sie einen neuen Benutzernamen und ein neues Kennwort ein.

    ![Neuer FTP-Benutzername und neues Passwort](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > Wenn Sie sich anmelden, müssen Sie den vollständigen Benutzernamen mit dem vorangestellten Namen der App verwenden. Angenommen, Sie geben „meine-id" als einen Benutzernamen ein, und die Website ist „meinbeispiel“, dann melden Sie sich als „meinbeispiel\meine-id“ an.
    >

5. Öffnen Sie in einem neuen Browserfenster die URL, die auf der Seite **Übersicht** für die App unter **FTP-Hostname** bzw. unter **FTPS-Hostname** angezeigt wird.

6. Melden Sie sich mit den zuvor erstellten FTP-Anmeldeinformationen an (einschließlich des Namens der App als Präfix des Benutzernamens).

    Daraufhin wird im Browser der Stammordner der App angezeigt.

7. Öffnen Sie den Ordner *LogFiles* .

    ![Öffnen Sie den Ordner LogFiles](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. Öffnen Sie den Ordner mit dem Namen W3SVC plus nachgestelltem numerischem Wert.

    ![Öffnen Sie den Ordner W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    Der Ordner enthält XML-Dateien für sämtliche protokollierten Fehler, nachdem Sie die Protokollierung fehlgeschlagener Anforderungen aktiviert haben, sowie eine XSL-Datei für die XML-Formatierung im Browser.

    ![Ordner W3SVC](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. Klicken Sie auf die XML-Datei für die fehlgeschlagene Anforderung, deren Ablaufverfolgungsinformationen Sie anzeigen möchten.

    Die folgende Abbildung zeigt einen Teil der Ablaufverfolgungsinformationen für einen Beispielfehler.

    ![Verfolgung fehlgeschlagener Anforderungen im Browser](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="next-steps"></a><a name="nextsteps"></a>Nächste Schritte
Sie haben erfahren, wie Visual Studio die Anzeige der Protokolle von App Service-Apps erleichtert. Die folgenden Abschnitte enthalten Links zu weiteren Ressourcen zu verwandten Themen:

* Problembehandlung in App Service
* Debuggen in Visual Studio
* Remotedebuggen in Azure
* Ablaufverfolgung in ASP.NET-Anwendungen
* Analyse von Webserverprotokollen
* Analyse der Ablaufverfolgungsprotokolle mit fehlgeschlagenen Anforderungen
* Debuggen von Cloud-Diensten.

### <a name="app-service-troubleshooting"></a>Problembehandlung in App Service
Weitere Informationen zur Problembehandlung von Apps in Azure App Service finden Sie in den folgenden Ressourcen:

* [Überwachen von Apps](web-sites-monitor.md)
* [Investigating Memory Leaks in Azure App Service with Visual Studio 2013](https://devblogs.microsoft.com/devops/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013/) (Untersuchen von Arbeitsspeicherverlusten in Azure App Service mit Visual Studio 2013). Microsoft ALM-Blogbeiträge über Visual Studio-Funktionen für die Untersuchung von Problemen mit verwaltetem Speicher.
* [Azure App Service online tools you should know about](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/) (Onlinetools für Azure App Service, die Sie kennen sollten). Blogbeitrag von Amit Apple.

Falls Sie spezifische Fragen zur Problembehandlung haben, können Sie diese in einem der folgenden Foren stellen:

* [Das Azure-Forum auf der ASP.NET-Website](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET).
* [Das Azure-Forum bei Microsoft Q&A (Fragen und Antworten)](/answers/topics/azure-webapps.html).
* [StackOverflow.com](https://www.stackoverflow.com).

### <a name="debugging-in-visual-studio"></a>Debuggen in Visual Studio
Weitere Informationen zum Debugmodus in Visual Studio finden Sie unter [Debuggen in Visual Studio](/visualstudio/debugger/debugging-in-visual-studio) und unter [Debugging Tips with Visual Studio 2010](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx) (Tipps zum Debuggen in Visual Studio 2010).

### <a name="remote-debugging-in-azure"></a>Remotedebuggen in Azure
Weitere Informationen zum Remotedebuggen für App Service-Apps und WebJobs finden Sie in den folgenden Ressourcen:

* [Introduction to Remote Debugging Azure App Service](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/) (Einführung in das Remotedebuggen von Azure App Service).
* [Introduction to Remote Debugging Azure App Service part 2 – Inside Remote debugging](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/) (Einführung in das Remotedebuggen von Azure App Service Teil 2 – Einblick in das Remotedebuggen)
* [Introduction to Remote Debugging on Azure App Service part 3 – Multi-Instance environment and GIT](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/) (Einführung in das Remotedebuggen von Azure App Service Teil 3 – Mehrinstanzenumgebung und GIT)
* [Debuggen von WebJobs (Video)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Wenn Ihre App eine Azure-Web-API oder ein Mobile Services-Back-End verwendet und Sie diese Komponenten debuggen möchten, finden Sie weitere Informationen unter [Debugging .NET Backend in Visual Studio](/archive/blogs/azuremobile/debugging-net-backend-in-visual-studio) (Debuggen des .NET Back-Ends in Visual Studio).

### <a name="tracing-in-aspnet-applications"></a>Ablaufverfolgung in ASP.NET-Anwendungen
Momentan sind keine vollständigen und aktuellen Einführungen zur Ablaufverfolgung in ASP.NET im Internet verfügbar. Beginnen Sie daher mit den älteren Einführungen für Web Forms, als MVC noch nicht existierte, und ergänzen Sie diese Lektüre mit neueren Blogeinträgen zu speziellen Themen. Die folgenden Ressourcen bieten gute Einstiegspunkte:

* [Überwachung und Telemetrie (Erstellen realer Cloud-Apps mit Azure).](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)<br>
  Dieses E-Book-Kapitel enthält Empfehlungen zur Ablaufverfolgung in Azure-Cloudanwendungen.
* [ASP.NET-Ablaufverfolgung](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  Ein älterer Artikel, der sich jedoch immer noch gut als Einstieg in das Thema eignet.
* [Ablaufverfolgungslistener](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  Informationen zu Ablaufverfolgungslistenern, [WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener) wird jedoch nicht erwähnt.
* [Exemplarische Vorgehensweise: Integrieren der ASP.NET-Ablaufverfolgung mit der System.Diagnostics-Ablaufverfolgung](/previous-versions/b0ectfxd(v=vs.140))<br/>
  Dieser Artikel ist ebenfalls älteren Datums, enthält jedoch zusätzliche Informationen, die in der Einführung nicht behandelt werden.
* [Ablaufverfolgung in ASP.NET MVC-Razoransichten](https://devblogs.microsoft.com/aspnet/tracing-in-asp-net-mvc-razor-views/)<br/>
  (Ablaufverfolgung in den ASP.NET MVC-Razoransichten, in englischer Sprache) Dieser Blogeintrag behandelt neben der Ablaufverfolgung in Razoransichten auch die Erstellung von Fehlerfiltern zur Protokollierung aller Ausnahmefehler in MVC-Anwendungen. Informationen zur Protokollierung aller Ausnahmefehler in Web Forms-Anwendungen finden Sie im Global.asax-Beispiel unter [Vollständiges Beispiel für Fehlerhandler](/previous-versions/bb397417(v=vs.140)) auf MSDN. Falls Sie in MVC oder Web Forms bestimmte Ausnahmen protokollieren möchten, diese Ausnahmen jedoch vom Standard-Framework behandelt werden sollen, können Sie diese wie im folgenden Beispiel abfangen und erneut auslösen:

    ```csharp
    try
    {
       // Your code that might cause an exception to be thrown.
    }
    catch (Exception ex)
    {
        Trace.TraceError("Exception: " + ex.ToString());
        throw;
    }
    ```

* [Ablaufprotokollierung und Streamingdiagnose über die Azure-Befehlszeile (plus Glimpse)](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  Verwenden der Befehlszeile für die Visual Studio-Aktionen aus diesem Lernprogramm. [Glimpse](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) ist ein Tool zum Debuggen von ASP.NET-Anwendungen.
* [Using Web Apps Logging and Diagnostics – with David Ebbo](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/) (Verwenden der Web-App-Protokollierung – mit David Ebbo) und [Streaming Logs from Web Apps – with David Ebbo](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/) (Protokollstreaming aus Web-Apps – mit David Ebbo)<br>
  Videos von Scott Hanselman und David Ebbo.

Für die Fehlerprotokollierung können Sie Open-Source-Protokollframeworks wie [ELMAH](https://nuget.org/packages/elmah/) verwenden, anstatt Ihren eigenen Ablaufverfolgungscode zu schreiben. Weitere Informationen finden Sie in [Scott Hanselmans Blogbeiträgen zu ELMAH](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx).

Darüber hinaus müssen Sie nicht ASP.NET oder die `System.Diagnostics`-Ablaufverfolgung verwenden, um Streamingprotokolle von Azure abzurufen. Der Streamingprotokolldienst von App Service-Apps ist in der Lage, beliebige *TXT*-, *HTML*- oder *LOG*-Dateien aus dem Ordner *LogFiles* zu streamen. Sie können daher ein eigenes Protokollsystem erstellen, das in das Dateisystem der App schreibt, und Ihre Dateien werden automatisch gestreamt und heruntergeladen. Dazu müssen Sie nur den Anwendungscode schreiben, der Dateien im Ordner *D:\Home\LogFiles* erstellt.

### <a name="analyzing-web-server-logs"></a>Analyse von Webserverprotokollen
Weitere Informationen zur Analyse von Webserverprotokollen finden Sie in den folgenden Ressourcen:

* [LogParser](https://www.iis.net/downloads/community/2010/04/log-parser-22)<br/>
  Ein Tool zum Anzeigen von Daten in Webserverprotokollen ( *.log* -Dateien).
* [Problembehandlung bei IIS-Leistungsproblemen oder Anwendungsfehlern mithilfe von LogParser](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  (Problembehandlung von IIS-Leistungsproblemen oder Anwendungsfehlern mithilfe von LogParser, in englischer Sprache) Eine Einführung in das LogParser-Tool, das Sie bei der Analyse von Webserverprotokollen unterstützt.
* [Blogbeiträge von Robert McMurray zur Verwendung von LogParser](/archive/blogs/robert_mcmurray/using-logparser-with-ftp-7-x-sessions)<br/>
* [HTTP-Statuscodes in IIS 7.0, IIS 7.5 und IIS 8.0](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>Analyse der Ablaufverfolgungsprotokolle mit fehlgeschlagenen Anforderungen
Die Microsoft TechNet-Website enthält einen Abschnitt zum Thema [Ablaufverfolgung von Anforderungen mit Fehlerrückgabe](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing), der für das Verständnis dieser Protokolle hilfreich ist. Diese Dokumentation konzentriert sich jedoch hauptsächlich auf die Ablaufverfolgung fehlerhafter Anforderungen in IIS. Diese Option ist in Azure App Service nicht verfügbar.

[GetStarted]: quickstart-dotnetcore.md?pivots=platform-windows
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki