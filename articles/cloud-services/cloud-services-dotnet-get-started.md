---
title: Erste Schritte mit Azure-Clouddiensten und ASP.NET | Microsoft Docs
description: Hier erfahren Sie, wie Sie mit ASP.NET MVC und Azure eine Anwendung mit mehreren Ebenen erstellen. Die Anwendung wird in einem Clouddienst mit Webrolle und Workerrolle ausgeführt. Sie verwendet Entity Framework, SQL-Datenbank und Azure Storage-Warteschlangen und -Blobs.
services: cloud-services, storage
documentationcenter: .net
author: jpconnock
manager: timlt
editor: ''
ms.assetid: d7aa440d-af4a-4f80-b804-cc46178df4f9
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: jeconnoc
ms.openlocfilehash: 3082ca34f2bcb71dd7aa02b4539899997374cfc0
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65595154"
---
# <a name="get-started-with-azure-cloud-services-and-aspnet"></a>Erste Schritte mit Azure-Clouddiensten und ASP.NET

## <a name="overview"></a>Übersicht
Dieses Tutorial behandelt die Erstellung einer .NET-Anwendung mit mehreren Ebenen und einem ASP.NET MVC-Front-End sowie die Bereitstellung dieser Anwendung in einem [Azure-Clouddienst](cloud-services-choose-me.md). Die Anwendung verwendet eine [Azure SQL-Datenbank](/previous-versions/azure/ee336279(v=azure.100)), den [Azure-Blobdienst](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) und den [Azure-Warteschlangendienst](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern). Sie können das [Visual Studio-Projekt](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) aus dem MSDN-Codekatalog herunterladen.

In diesem Tutorial erfahren Sie, wie Sie die Anwendung erstellen und lokal ausführen, in Azure bereitstellen und in der Cloud ausführen sowie von Grund auf erstellen können. Sie können auch mit der Erstellung von Grund auf beginnen und die Test- und Bereitstellungsschritte anschließend ausführen.

## <a name="contoso-ads-application"></a>Contoso Ads-Anwendung
Bei dieser Anwendung handelt es sich um ein Bulletin Board für Werbung. Benutzer können eine Werbung erstellen, indem sie Text eingeben und ein Bild hochladen. Die Anwendung zeigt eine Liste mit Werbeeinblendungen mit Miniaturbildern an, und Benutzer können auf eine Werbung klicken, um das Bild in voller Größe anzuzeigen.

![Werbungsliste](./media/cloud-services-dotnet-get-started/list.png)

Die Anwendung verwendet das [warteschlangenorientierte Arbeitsmuster](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern) , um die CPU-intensive Last der Erstellung von Miniaturbildern an einen Back-End-Prozess auszulagern.

## <a name="alternative-architecture-app-service-and-webjobs"></a>Alternative Architektur: App Service und WebJobs
In diesem Tutorial erfahren Sie, wie Sie Front-End und Back-End in einem Azure-Clouddienst ausführen können. Alternativ können Sie das Front-End in [Azure App Service](/azure/app-service/) ausführen und die [WebJobs](https://go.microsoft.com/fwlink/?LinkId=390226)-Funktion für das Back-End verwenden. Ein Lernprogramm zu WebJobs finden Sie unter [Erste Schritte mit dem Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/wiki). Informationen zur Auswahl der optimalen Dienste für Ihr Szenario finden Sie unter [Azure App Service, Azure Virtual Machines, Service Fabric und Azure Cloud Services im Vergleich](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="what-youll-learn"></a>Sie lernen Folgendes
* Ermöglichen der Azure-Entwicklung auf Ihrem Computer durch Installieren des Azure SDK
* Erstellen eines Clouddienst-Projekts in Visual Studio mit einer ASP.NET MVC-Webrolle und einer Workerrolle.
* Lokale Tests des Clouddienstprojekts mithilfe des Azure-Speicheremulators.
* Veröffentlichen des Cloudprojekts in einem Azure-Clouddienst und Tests mit einem Azure-Speicherkonto.
* Hochladen von Dateien und Speicherung der Dateien im Azure-Blobdienst.
* Verwenden des Azure-Warteschlangendiensts für die Kommunikation zwischen Ebenen.

## <a name="prerequisites"></a>Voraussetzungen
Dieses Tutorial setzt voraus, dass Sie mit den [Grundkonzepten von Azure-Clouddiensten](cloud-services-choose-me.md), z.B. den Begriffen *Webrolle* und *Workerrolle*, vertraut sind.  Außerdem wird vorausgesetzt, dass Sie mit der Arbeit mit [ASP.NET MVC](https://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)- oder [Web Forms](https://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview)-Projekten in Visual Studio vertraut sind. Die Beispielanwendung verwendet MVC, aber ein Großteil des Tutorial gilt auch für Web Forms.

Sie können die Anwendung ohne Azure-Abonnement lokal ausführen, aber für die Bereitstellung der Anwendung in der Cloud benötigen Sie ein Abonnement. Wenn Sie kein Konto haben, können Sie [Ihre MSDN-Abonnentenvorteile aktivieren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A55E3C668) oder [sich für eine kostenlose Testversion registrieren](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A55E3C668).

Die Anweisungen im Tutorial funktionieren mit allen der folgenden Produkte:

* Visual Studio 2013
* Visual Studio 2015
* Visual Studio 2017
* Visual Studio 2019

Falls Sie keines dieser Produkte besitzen, wird Visual Studio möglicherweise zusammen mit dem Azure SDK installiert.

## <a name="application-architecture"></a>Anwendungsarchitektur
Die Anwendung speichert Werbungen in einer SQL-Datenbank und verwendet Entity Framework Code First, um Tabellen zu erstellen und auf Daten zuzugreifen. Pro Werbung werden in der Datenbank zwei URLs gespeichert: eine für das Bild in voller Größe und eine für die Miniaturansicht.

![Ad-Tabelle](./media/cloud-services-dotnet-get-started/adtable.png)

Wenn ein Benutzer ein Bild hochlädt, speichert das in einer Webrolle ausgeführte Front-End das Bild in einem [Azure-Blob](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage)und speichert die Werbeinformationen in der Datenbank zusammen mit einer URL, die auf das Blob zeigt. Gleichzeitig wird eine Nachricht in eine Azure-Warteschlange geschrieben. Ein in einer Workerrolle ausgeführter Back-End-Prozess prüft die Warteschlange regelmäßig auf neue Nachrichten. Wenn eine neue Nachricht vorhanden ist, erstellt die Workerrolle eine Miniaturansicht für das Bild und aktualisiert das entsprechende Datenbankfeld mit der URL für diese Werbung. Das folgende Diagramm zeigt die Interaktion der verschiedenen Anwendungskomponenten:

![Contoso Ads-Architektur](./media/cloud-services-dotnet-get-started/apparchitecture.png)

[!INCLUDE [install-sdk](../../includes/install-sdk-2017-2015-2013.md)]

## <a name="download-and-run-the-completed-solution"></a>Herunterladen und Ausführen der abgeschlossenen Lösung
1. Laden Sie die [abgeschlossene Lösung](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4)herunter und entzippen Sie das Archiv.
2. Starten Sie Visual Studio.
3. Wählen Sie im Menü **Datei** die Option **Projekt öffnen**, navigieren Sie zu dem Ort, an dem Sie die Lösung gespeichert haben, und öffnen Sie die Lösungsdatei.
4. Drücken Sie STRG+UMSCH+B, um die Lösung zu erstellen.

    Standardmäßig stellt Visual Studio den Inhalt des NuGet-Pakets automatisch wieder her, das nicht in der *.zip* -Datei enthalten war. Wenn die Pakete nicht wiederhergestellt werden, installieren Sie diese manuell, indem Sie das Dialogfeld **NuGet-Pakete verwalten** öffnen und rechts oben auf **Wiederherstellen** klicken.
5. Vergewissern Sie sich im **Projektmappen-Explorer**, dass **ContosoAdsCloudService** als Startprojekt ausgewählt ist.
6. Wenn Sie Visual Studio 2015 oder höher verwenden, ändern Sie die SQL Server-Verbindungszeichenfolge in der Anwendungsdatei *Web.config* des ContosoAdsWeb-Projekts und in der Datei *ServiceConfiguration.Local.cscfg* des ContosoAdsCloudService-Projekts. Ändern Sie in beiden Fällen „(localdb)\v11.0“ in „(localdb)\MSSQLLocalDB“.
7. Drücken Sie STRG+F5, um die Anwendung auszuführen.

    Wenn Sie ein Clouddienstprojekt lokal ausführen, startet Visual Studio automatisch *Azure-Serveremulator* und *Azure-Speicheremulator*. Der Serveremulator verwendet die Ressourcen Ihres Computers, um die Umgebungen von Web- und Workerrolle zu simulieren. Der Speicheremulator verwendet eine [SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb) -Datenbank, um den Azure-Cloudspeicher zu simulieren.

    Wenn Sie ein Clouddienstprojekt zum ersten Mal ausführen, kann der Start der Emulatoren ca. eine Minute in Anspruch nehmen. Wenn die Emulatoren gestartet sind, wird der Standardbrowser mit der Startseite der Anwendung geöffnet.

    ![Contoso Ads-Architektur](./media/cloud-services-dotnet-get-started/home.png)
8. Klicken Sie auf **Werbung erstellen**.
9. Geben Sie einige Testdaten ein, wählen Sie ein hochzuladendes *.jpg* -Bild aus, und klicken Sie auf **Erstellen**.

    ![Seite erstellen](./media/cloud-services-dotnet-get-started/create.png)

    Die Anwendung wechselt zur Indexseite, zeigt jedoch noch keine Miniaturansicht für die neue Werbung an, da diese Verarbeitung noch nicht erfolgt ist.
10. Warten Sie einen Moment und aktualisieren Sie die Indexseite, um die Miniaturansicht zu sehen.

     ![Indexseite](./media/cloud-services-dotnet-get-started/list.png)
11. Klicken Sie auf **Details** für Ihre Werbung, um das Bild in voller Größe anzuzeigen.

     ![Detailseite](./media/cloud-services-dotnet-get-started/details.png)

Sie haben die Anwendung bislang komplett auf Ihrem lokalen Computer ausgeführt, ohne Verbindung mit der Cloud. Der Speicheremulator speichert die Warteschlangen- und Blobdaten in einer SQL Server Express LocalDB-Datenbank, und die Anwendung speichert die Werbedaten in einer weiteren LocalDB-Datenbank. Entity Framework Code First hat die Werbedatenbank automatisch erstellt, als die Webanwendung zum ersten Mal versucht hat, darauf zuzugreifen.

Im folgenden Abschnitt werden Sie die Lösung so konfigurieren, dass diese Azure-Cloudressourcen für Warteschlangen, Blobs und die Anwendungsdatenbank verwendet, wenn sie in der Cloud ausgeführt wird. Falls die Ausführung weiterhin lokal erfolgen soll, Sie aber Speicher- und Datenbankressourcen in der Cloud nutzen möchten, ist dies möglich. Hierfür müssen lediglich die Verbindungszeichenfolgen entsprechend festgelegt werden. Dies wird weiter unten beschrieben.

## <a name="deploy-the-application-to-azure"></a>Bereitstellen der Anwendung für Azure
Führen Sie folgende Schritte aus, um die Anwendung in der Cloud auszuführen:

* Erstellen eines Azure-Clouddiensts
* Erstellen einer Azure SQL-Datenbank
* Erstellen eines Azure-Speicherkontos
* Konfigurieren der Lösung zur Verwendung Ihrer Azure SQL-Datenbank bei der Ausführung in Azure
* Konfigurieren der Lösung zur Verwendung Ihres Azure-Speicherkontos bei der Ausführung in Azure
* Bereitstellen des Projekts in Ihrem Azure-Clouddienst

### <a name="create-an-azure-cloud-service"></a>Erstellen eines Azure-Clouddiensts
Ein Azure-Clouddienst ist die Umgebung, in der die Anwendung ausgeführt wird.

1. Öffnen Sie das [Azure-Portal](https://portal.azure.com) in Ihrem Browser.
2. Klicken Sie auf **„Ressource erstellen“ > „Compute“ > „Clouddienst“** .

3. Geben Sie im Eingabefeld für den DNS-Namen ein URL-Präfix für den Clouddienst ein.

    Diese URL muss eindeutig sein.  Sie erhalten eine Fehlermeldung, falls das eingegebene Präfix bereits verwendet wird.
4. Geben Sie eine neue Ressourcengruppe für den Dienst an. Klicken Sie auf **Neu erstellen**, und geben Sie dann einen Namen im Eingabefeld „Ressourcengruppe“ ein, z.B. „CS_contososadsRG“.

5. Wählen Sie die Region aus, in der Sie Ihre Anwendung bereitstellen möchten.

    Dieses Feld gibt an, in welchem Datencenter der Clouddienst gehostet wird. Für eine Produktionsanwendung würden Sie die für Ihre Kunden am nächsten gelegene Region auswählen. Wählen Sie für dieses Tutorial die Ihnen am nächsten gelegene Region aus.
5. Klicken Sie auf **Create**.

    In der folgenden Abbildung wird ein Clouddienst mit der URL „CSvccontosoads.cloudapp.net“ erstellt.

    ![Neuer Clouddienst](./media/cloud-services-dotnet-get-started/newcs.png)

### <a name="create-an-azure-sql-database"></a>Erstellen einer Azure SQL-Datenbank
Wenn die Anwendung in der Cloud ausgeführt wird, verwendet sie eine cloudbasierte Datenbank.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **„Ressource erstellen“ > „Datenbanken“ > „SQL-Datenbank“** .
2. Geben Sie unter **Datenbankname** den Wert *contosoads*ein.
3. Klicken Sie in der **Ressourcengruppe** auf **Vorhandene verwenden**, und wählen Sie die Ressourcengruppe aus, die für den Clouddienst verwendet wird.
4. Klicken Sie in der folgenden Abbildung auf **Server – Erforderliche Einstellungen konfigurieren** und dann auf **Neuen Server erstellen**.

    ![Tunnel zum Datenbankserver](./media/cloud-services-dotnet-get-started/newdb.png)

    Falls Ihr Abonnement bereits einen Server enthält, können Sie auch diesen Server aus der Dropdownliste auswählen.
5. Geben Sie im Feld **Servername** den Namen *csvccontosodbserver* ein.

6. Geben Sie unter **Anmeldename** und **Kennwort** die Anmeldedaten eines Administrators ein.

    Wenn Sie **Neuen Server erstellen** ausgewählt haben, geben Sie hier keinen vorhandenen Namen und kein Kennwort ein. Sie geben einen neuen Namen und das dazugehörige Kennwort ein. Diese Angaben werden hier zur späteren Verwendung für den Zugriff auf die Datenbank definiert. Wenn Sie einen zuvor erstellten Server ausgewählt haben, werden Sie aufgefordert, das Kennwort für den zuvor erstellten Administratorbenutzer einzugeben.
7. Wählen Sie denselben **Standort** wie für Ihren Clouddienst aus.

    Wenn sich Clouddienst und Datenbank in unterschiedlichen Datencentern (d. h. unterschiedlichen Regionen) befinden, steigt die Latenz an und Sie müssen für die Bandbreite außerhalb des Datencenters bezahlen. Die Bandbreite innerhalb eines Datencenters ist kostenlos.
8. Aktivieren Sie die Option **Azure-Diensten Zugriff auf den Server erlauben**.
9. Klicken Sie für den neuen Server auf **Auswählen**.

    ![Neuer SQL-Datenbank-Server](./media/cloud-services-dotnet-get-started/newdbserver.png)
10. Klicken Sie auf **Create**.

### <a name="create-an-azure-storage-account"></a>Erstellen eines Azure-Speicherkontos
Azure-Speicherkonten bieten Ressourcen zum Speichern von Warteschlangen- und Blobdaten in der Cloud.

In einer tatsächlichen Anwendung würden Sie normalerweise separate Konten für Anwendungsdaten und Protokolldaten sowie für Test- und Produktionsdaten erstellen. In diesem Tutorial verwenden wir nur ein einzelnes Konto.

1. Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **„Ressource erstellen“ > „Storage“ > „Speicherkonto – Blob, Datei, Tabelle, Warteschlange“** .
2. Geben Sie im Feld **Name** ein URL-Präfix ein.

    Dieses Präfix zusammen mit dem Text unterhalb des Eingabefelds ergibt die eindeutige URL Ihres Speicherkontos. Falls das eingegebene Präfix bereits von einer anderen Person verwendet wird, müssen Sie ein anderes Präfix auswählen.
3. Legen Sie das **Bereitstellungsmodell** auf *Klassisch* fest.

4. Wählen Sie in der Dropdownliste **Replikation** den Wert **Lokal redundanter Speicher** aus.

    Wenn die Georeplikation für ein Speicherkonto aktiviert ist, werden dessen Inhalte in einem sekundären Datencenter repliziert, damit ein Failover möglich ist, falls es am primären Standort zu einem Katastrophenfall kommt. Für die Georeplikation können zusätzliche Kosten anfallen. Für Test- und Entwicklungskonten macht es wenig Sinn, für Georeplikation zu bezahlen. Weitere Informationen finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos](../storage/common/storage-create-storage-account.md).

5. Klicken Sie in der **Ressourcengruppe** auf **Vorhandene verwenden**, und wählen Sie die Ressourcengruppe aus, die für den Clouddienst verwendet wird.
6. Wählen Sie in der Dropdownliste **Standort** dieselbe Region aus, die Sie für den Clouddienst ausgewählt haben.

    Wenn sich Clouddienst und Speicherkonto in unterschiedlichen Datencentern (d. h. unterschiedlichen Regionen) befinden, steigt die Latenz an und Sie müssen für die Bandbreite außerhalb des Datencenters bezahlen. Die Bandbreite innerhalb eines Datencenters ist kostenlos.

    Mit Azure-Affinitätsgruppen können Sie die Distanz zwischen Ressourcen in einem Datencenter und somit die Latenz minimieren. Dieses Tutorial verwendet keine Affinitätsgruppen. Anweisungen finden Sie unter [Erstellen einer Affinitätsgruppe in Azure](/previous-versions/azure/reference/gg715317(v=azure.100)).
7. Klicken Sie auf **Create**.

    ![Neues Speicherkonto](./media/cloud-services-dotnet-get-started/newstorage.png)

    In der Abbildung wird ein Speicherkonto mit der URL `csvccontosoads.core.windows.net`erstellt.

### <a name="configure-the-solution-to-use-your-azure-sql-database-when-it-runs-in-azure"></a>Konfigurieren der Lösung zur Verwendung Ihrer Azure SQL-Datenbank bei der Ausführung in Azure
Webprojekt und Workerrollenprojekt haben je eine eigene Verbindungszeichenfolge für die Datenbank. Beide müssen auf die Azure SQL-Datenbank zeigen, wenn die Anwendung in Azure ausgeführt wird.

Sie werden eine [Web.config-Transformation](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations) für die Webrolle und eine Clouddienst-Umgebungseinstellung für die Workerrolle verwenden.

> [!NOTE]
> In diesem und im nächsten Abschnitt speichern Sie Anmeldeinformationen in Projektdateien. [Sie sollten keine sensiblen Daten in öffentlichen Quellcode-Repositorys speichern](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets).
>
>

1. Öffnen Sie im ContosoAdsWeb-Projekt die Transformationsdatei *Web.Release.config* für die Anwendungsdatei *Web.config*, löschen Sie den Kommentarblock, der ein `<connectionStrings>`-Element enthält, und fügen Sie den folgenden Code an derselben Stelle ein.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="{connectionstring}"
        providerName="System.Data.SqlClient" xdt:Transform="SetAttributes" xdt:Locator="Match(name)"/>
    </connectionStrings>
    ```

    Lassen Sie die Datei geöffnet.
2. Klicken Sie im [Azure-Portal](https://portal.azure.com) im linken Bereich auf **SQL-Datenbanken**, und klicken Sie auf die Datenbank, die Sie für dieses Tutorial erstellt haben, und dann auf **Verbindungszeichenfolgen anzeigen**.

    ![Verbindungszeichenfolgen anzeigen](./media/cloud-services-dotnet-get-started/showcs.png)

    Die Verbindungszeichenfolgen werden im Portal angezeigt, mit einem Platzhalter für das Kennwort.

    ![Verbindungszeichenfolgen](./media/cloud-services-dotnet-get-started/connstrings.png)
3. Löschen Sie in der Transformationsdatei *Web.Release.config* den Teil `{connectionstring}`, und fügen Sie stattdessen die ADO.NET-Verbindungszeichenfolge aus dem Azure-Portal ein.
4. Ersetzen Sie in der Verbindungszeichenfolge, die Sie in die Transformationsdatei *Web.Release.config* eingefügt haben, `{your_password_here}` durch das Kennwort, das Sie für die neue SQL-Datenbank eingerichtet haben.
5. Speichern Sie die Datei .  
6. Kopieren Sie die Verbindungszeichenfolge (ohne die umgebenden Anführungszeichen), um diese in den folgenden Schritten bei der Konfiguration des Workerrollen-Projekts zur Verfügung zu haben.
7. Klicken Sie im **Projektmappen-Explorer** unter **Rollen** im Clouddienstprojekt mit der rechten Maustaste auf **ContosoAdsWorker**, und klicken Sie auf **Eigenschaften**.

    ![Rolleneigenschaften](./media/cloud-services-dotnet-get-started/rolepropertiesworker.png)
8. Klicken Sie auf die Registerkarte **Einstellungen** .
9. Ändern Sie die **Dienstkonfiguration** in **Cloud**.
10. Wählen Sie für die `ContosoAdsDbConnectionString`-Einstellung das Feld **Wert** aus, und fügen Sie die Verbindungszeichenfolge ein, die Sie im vorherigen Abschnitt des Tutorials kopiert haben.

     ![Datenbank-Verbindungszeichenfolge für Workerrolle](./media/cloud-services-dotnet-get-started/workerdbcs.png)
11. Speichern Sie die Änderungen.  

### <a name="configure-the-solution-to-use-your-azure-storage-account-when-it-runs-in-azure"></a>Konfigurieren der Lösung zur Verwendung Ihres Azure-Speicherkontos bei der Ausführung in Azure
Die Verbindungszeichenfolgen für das Azure-Speicherkonto für das Web- und Workerrollenprojekt werden in Umgebungseinstellungen für das Clouddienstprojekt gespeichert. Für jedes Projekt existiert je ein separater Satz mit Einstellungen für die lokale Ausführung der Anwendung und für die Ausführung in der Cloud. Sie werden die Cloudumgebungseinstellungen für Web- und Workerrollenprojekt ändern.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **ContosoAdsWeb** unter **Rollen** im Projekt **ContosoAdsCloudService**, und klicken Sie dann auf **Eigenschaften**.

    ![Rolleneigenschaften](./media/cloud-services-dotnet-get-started/roleproperties.png)
2. Klicken Sie auf die Registerkarte **Settings** . Wählen Sie in der Dropdownliste **Dienstkonfiguration** den Wert **Cloud** aus.

    ![Cloudkonfiguration](./media/cloud-services-dotnet-get-started/sccloud.png)
3. Wenn Sie den Eintrag **StorageConnectionString** auswählen, sehen Sie am Ende der Zeile eine Schaltfläche mit Auslassungspunkten ( **...** ). Klicken Sie auf die elliptische Schaltfläche, um das Dialogfeld **Verbindungszeichenfolge für Speicherkonto erstellen** zu öffnen.

    ![Erstellungsdialog für Verbindungszeichenfolge](./media/cloud-services-dotnet-get-started/opencscreate.png)
4. Klicken Sie im Dialogfeld **Verbindungszeichenfolge für Speicherkonto** auf **Ihr Abonnement**, wählen Sie das zuvor erstellte Speicherkonto aus, und klicken Sie auf **OK**. Falls Sie noch nicht angemeldet sind, werden Sie zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

    ![Speicherkonto-Verbindungszeichenfolge erstellen](./media/cloud-services-dotnet-get-started/createstoragecs.png)
5. Speichern Sie die Änderungen.
6. Führen Sie zum Einrichten der `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`-Verbindungszeichenfolge die gleichen Schritte aus, die Sie auch für die `StorageConnectionString`-Verbindungszeichenfolge ausgeführt haben.

    Diese Verbindungszeichenfolge wird für die Protokollierung verwendet.
7. Wiederholen Sie die Prozedur, die Sie für die **ContosoAdsWeb**-Rolle verwendet haben, um die beiden Verbindungszeichenfolgen für die **ContosoAdsWorker**-Rolle zu setzen. Vergessen Sie nicht, die **Dienstkonfiguration** auf **Cloud** zu setzen.

Die Einstellungen, die Sie für die Rollenumgebung in der Visual Studio-GUI konfiguriert haben, werden in den folgenden Dateien im ContosoAdsCloudService-Projekt gespeichert:

* *ServiceDefinition.csdef* - Definiert die Einstellungsnamen.
* *ServiceConfiguration.Cloud.cscfg* - Enthält die Werte für die Ausführung der Anwendung in der Cloud.
* *ServiceConfiguration.Local.cscfg* - Enthält die Werte für die lokale Ausführung der Anwendung.

Die Datei „Servicedefinition.csdef“ enthält beispielsweise die folgenden Definitionen:

```xml
<ConfigurationSettings>
    <Setting name="StorageConnectionString" />
    <Setting name="ContosoAdsDbConnectionString" />
</ConfigurationSettings>
```

Und die Datei *ServiceConfiguration.Cloud.cscfg* enthält die Werte, die Sie für diese Einstellungen in Visual Studio eingegeben haben:

```xml
<Role name="ContosoAdsWorker">
    <Instances count="1" />
    <ConfigurationSettings>
        <Setting name="StorageConnectionString" value="{yourconnectionstring}" />
        <Setting name="ContosoAdsDbConnectionString" value="{yourconnectionstring}" />
        <!-- other settings not shown -->

    </ConfigurationSettings>
    <!-- other settings not shown -->

</Role>
```

Die `<Instances>` -Einstellung definiert die Anzahl der virtuellen Computer, auf denen Azure den Code der Workerrolle ausführt. Im Bereich [Nächste Schritte](#next-steps) finden Sie Links zu weiteren Hinweisen zur Skalierung von Clouddiensten.

### <a name="deploy-the-project-to-azure"></a>Bereitstellen des Projekts in Azure
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Cloudprojekt **ContosoAdsCloudService**, und wählen Sie **Veröffentlichen** aus.

   ![Veröffentlichungsmenü](./media/cloud-services-dotnet-get-started/pubmenu.png)
2. Klicken Sie im Schritt **Anmelden** des **Assistenten zum Veröffentlichen einer Azure-Anwendung** auf **Weiter**.

    ![Anmeldung](./media/cloud-services-dotnet-get-started/pubsignin.png)
3. Klicken Sie im Schritt **Einstellungen** des Assistenten auf **Weiter**.

    ![Schritt "Einstellungen"](./media/cloud-services-dotnet-get-started/pubsettings.png)

    Die Standardeinstellungen in der Registerkarte **Erweitert** brauchen für dieses Tutorial nicht geändert werden. Weitere Informationen zur Registerkarte Erweitert finden Sie unter [Assistent zum Veröffentlichen einer Azure-Anwendung](https://docs.microsoft.com/azure/vs-azure-tools-publish-azure-application-wizard).
4. Klicken Sie auf der Seite **Zusammenfassung** auf **Veröffentlichen**.

    ![Zusammenfassung](./media/cloud-services-dotnet-get-started/pubsummary.png)

   Das **Azure-Aktivitätsprotokoll** wird in Visual Studio geöffnet.
5. Klicken Sie auf den Pfeil nach rechts, um die Bereitstellungsdetails zu erweitern.

    Die Bereitstellung kann 5 oder mehr Minuten in Anspruch nehmen.

    ![Azure-Aktivitätsprotokoll](./media/cloud-services-dotnet-get-started/waal.png)
6. Klicken Sie nach Abschluss der Bereitstellung auf die **Web-App-URL** , um die Anwendung zu starten.
7. Sie können die Anwendung nun testen und Werbungen erstellen, anzeigen und bearbeiten, wie Sie dies bereits bei der lokalen Ausführung getan haben.

> [!NOTE]
> Nach Abschluss der Tests sollten Sie den Clouddienst anhalten oder löschen. Selbst wenn Sie den Clouddienst nicht nutzen, fallen Kosten an, da Ressourcen auf virtuellen Computern für den Dienst reserviert sind. Wenn Sie den Dienst laufen lassen, kann jeder, der die URL findet, Werbungen erstellen und anzeigen. Navigieren Sie im [Azure-Portal](https://portal.azure.com) zur Registerkarte **Übersicht** für Ihren Clouddienst, und klicken Sie dann oben auf der Seite auf die Schaltfläche **Löschen**. Klicken Sie stattdessen auf **Anhalten** , um vorübergehend zu verhindern, dass andere Personen auf die Website zugreifen. In diesem Fall entstehen weiterhin Kosten. Sie können die SQL-Datenbank und das Speicherkonto auf ähnliche Weise löschen, wenn Sie diese nicht mehr benötigen.
>
>

## <a name="create-the-application-from-scratch"></a>Neuerstellen der Anwendung
Falls Sie die [komplette Anwendung](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4)noch nicht heruntergeladen haben, tun Sie dies jetzt. Sie kopieren Dateien aus dem heruntergeladenen Projekt in das neue Projekt.

Die Erstellung der Contoso-Werbeanwendung umfasst die folgenden Schritte:

* Erstellen einer Clouddienst-Lösung in Visual Studio
* Aktualisieren und Hinzufügen von NuGet-Paketen
* Festlegen von Projektverweisen
* Konfigurieren von Verbindungszeichenfolgen
* Hinzufügen von Codedateien

Nach der Erstellung der Lösung werden Sie den Code prüfen, der speziell für Clouddienst-Projekte, Azure-Blobs und -Warteschlangen gilt.

### <a name="create-a-cloud-service-visual-studio-solution"></a>Erstellen einer Clouddienst-Lösung in Visual Studio
1. Klicken Sie in Visual Studio im Menü **Neues Projekt** from the **Neues Projekt** .
2. Erweitern Sie im linken Bereich des Dialogfelds **Neues Projekt** den Eintrag **Visual C#** , wählen Sie den Vorlagentyp **Cloud** aus, und klicken Sie auf die Vorlage **Azure Cloud Service**.
3. Geben Sie den Namen ContosoAdsCloudService für Projekt und Lösung ein, und klicken Sie auf **OK**.

    ![Neues Projekt](./media/cloud-services-dotnet-get-started/newproject.png)
4. Fügen Sie im Dialogfeld **Neuer Azure-Clouddienst** eine Web- und eine Workerrolle hinzu. Nennen Sie die Webrolle ContosoAdsWeb und die Workerrolle ContosoAdsWorker. (Klicken Sie auf das Stiftsymbol auf der rechten Seite, um die Standardnamen der Rollen zu ändern.)

    ![Neues Clouddienst-Projekt](./media/cloud-services-dotnet-get-started/newcsproj.png)
5. Wenn das Dialogfeld **Neues ASP.NET-Projekt** für die Webrolle angezeigt wird, wählen Sie die MVC-Vorlage aus, und klicken Sie auf **Authentifizierung ändern**.

    ![Authentifizierung ändern](./media/cloud-services-dotnet-get-started/chgauth.png)
6. Klicken Sie im Dialogfeld **Authentifizierung ändern** auf **Keine Authentifizierung** und dann auf **OK**.

    ![Keine Authentifizierung](./media/cloud-services-dotnet-get-started/noauth.png)
7. Klicken Sie im Dialogfeld **Neues ASP.Net-Projekt** auf **OK**.
8. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Lösung (nicht auf eines der Projekte), und klicken Sie auf **Hinzufügen – Neues Projekt**.
9. Klicken Sie im Dialogfeld **Neues Projekt hinzufügen** unter **Visual C#** im linken Bereich auf **Windows**, und klicken Sie auf die Vorlage **Klassenbibliothek**.  
10. Benennen Sie das Projekt *ContosoAdsCommon*, und klicken Sie auf **OK**.

    Sie müssen in Web- und Workerrolle auf den Entity Framework-Kontext und das Datenmodell verweisen. Alternativ könnten Sie die EF-bezogenen Klassen im Webrollenprojekt definieren und im Workerrollenprojekt auf dieses Projekt verweisen. In diesem Fall würde Ihr Workerrollenprojekt aber auf Webassemblys verweisen, die es nicht benötigt.

### <a name="update-and-add-nuget-packages"></a>Aktualisieren und Hinzufügen von NuGet-Paketen
1. Öffnen Sie das Dialogfeld **NuGet-Pakete verwalten** für die Lösung.
2. Wählen Sie oben im Fenster **Updates**aus.
3. Suchen Sie nach dem Paket *WindowsAzure.Storage*. Wenn es sich in der Liste befindet, wählen Sie es aus, und wählen Sie anschließend die Web- und Workerprojekte aus, in denen es aktualisiert werden soll. Klicken Sie dann auf **Aktualisieren**.

    Die Speicherclientbibliothek wird häufiger aktualisiert als die Visual Studio-Projektvorlagen. Daher wird es öfter vorkommen, dass Sie die Version in einem neu erstellten Projekt aktualisieren müssen.
4. Wählen Sie oben im Fenster **Durchsuchen**aus.
5. Suchen Sie das NuGet-Paket *EntityFramework* und installieren Sie es in allen drei Projekten.
6. Suchen Sie das NuGet-Paket *Microsoft.WindowsAzure.ConfigurationManager* , und installieren Sie es im Workerrollenprojekt.

### <a name="set-project-references"></a>Setzen von Projektverweisen
1. Fügen Sie im Projekt ContosoAdsWeb einen Verweis auf das Projekt ContosoAdsCommon ein. Klicken Sie mit der rechten Maustaste auf das Projekt ContosoAdsWeb, und klicken Sie auf **Verweise** - **Verweise hinzufügen**. Wählen Sie im Dialogfeld **Verweis-Manager** im linken Bereich **Projektmappe – Projekte** und dann **ContosoAdsCommon** aus, und klicken Sie auf **OK**.
2. Fügen Sie im Projekt ContosoAdsWorker einen Verweis auf das Projekt ContosoAdsCommon ein.

    ContosoAdsCommon wird Datenmodell und Kontextklasse für das Entity Framework enthalten, das sowohl von Front-End als auch von Back-End verwendet wird.
3. Fügen Sie im Projekt "ContosoAdsWorker" einen Verweis auf `System.Drawing`ein.

    Diese Assembly wird vom Back-End für die Konvertierung von Bildern zu Miniaturansichten verwendet.

### <a name="configure-connection-strings"></a>Konfigurieren von Verbindungszeichenfolgen
In diesem Abschnitt konfigurieren Sie die Verbindungszeichenfolgen für Azure Storage und SQL für lokale Tests. In den Bereitstellungsanweisungen weiter oben in diesem Tutorial wurde beschrieben, wie Sie die Verbindungszeichenfolgen für die Ausführung der Anwendung in der Cloud einrichten.

1. Öffnen Sie im Projekt "ContosoAdsWeb" die Datei "Web.config" der Anwendung, und fügen Sie das folgende `connectionStrings`-Element nach dem `configSections`-Element ein.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    Wenn Sie Visual Studio 2015 oder höher verwenden, ersetzen Sie „v11.0“ durch „MSSQLLocalDB“.
2. Speichern Sie die Änderungen.
3. Klicken Sie im Projekt ContosoAdsCloudService unter **Rollen** mit der rechten Maustaste auf ContosoAdsWeb, und klicken Sie auf **Eigenschaften**.

    ![Rolleneigenschaften](./media/cloud-services-dotnet-get-started/roleproperties.png)
4. Klicken Sie im Eigenschaftenfenster **ContosoAdsWeb [Rolle]** auf die Registerkarte **Einstellungen**, und klicken Sie auf **Einstellung hinzufügen**.

    Behalten Sie für **Dienstkonfiguration** die Einstellung **Alle Konfigurationen** bei.
5. Fügen Sie eine Einstellung mit dem Namen *StorageConnectionString* hinzu. Legen Sie **Typ** auf *ConnectionString* fest, und legen Sie den **Wert** auf *UseDevelopmentStorage=true* fest.

    ![Neue Verbindungszeichenfolge](./media/cloud-services-dotnet-get-started/scall.png)
6. Speichern Sie die Änderungen.
7. Wiederholen Sie die Prozedur, mit der Sie eine Speicher-Verbindungszeichenfolge zu den ContosoAdsWorker-Rolleneigenschaften hinzugefügt haben.
8. Fügen Sie ebenfalls im **ContosoAdsWorker [Role]** -Eigenschaftenfenster eine weitere Verbindungszeichenfolge hinzu:

   * Name: ContosoAdsDbConnectionString
   * Geben Sie Folgendes ein:  string
   * Wert: Fügen Sie dieselbe Verbindungszeichenfolge ein, die Sie auch für das Webrollenprojekt verwendet haben. (Das folgende Beispiel bezieht sich auf Visual Studio 2013. Wenn Sie Visual Studio 2015 oder höher verwenden und dieses Beispiel kopieren, sollten sich daran denken, den Wert für „Data Source“ zu ändern.)

       ```
       Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;
       ```

### <a name="add-code-files"></a>Hinzufügen von Codedateien
In diesem Abschnitt kopieren Sie Codedateien aus der heruntergeladenen Lösung in die neue Lösung. In den folgenden Abschnitten werden die wichtigsten Bereiche des Codes erläutert.

Um Dateien einem Projekt oder einem Ordner hinzuzufügen, klicken Sie mit der rechten Maustaste auf das Projekt bzw. den Ordner und klicken dann auf **Hinzufügen** - **Vorhandenes Element**. Wählen Sie die gewünschten Dateien aus, und klicken Sie auf **Hinzufügen**. Klicken Sie auf **Ja**, wenn Sie gefragt werden, ob Sie vorhandene Dateien ersetzen möchten.

1. Löschen Sie im Projekt ContosoAdsCommon die Datei *Class1.cs*, und fügen Sie stattdessen die Dateien *Ad.cs* und *ContosoAdscontext.cs* aus dem heruntergeladenen Projekt hinzu.
2. Fügen Sie im Projekt ContosoAdsWeb die folgenden Dateien aus dem heruntergeladenen Projekt hinzu.

   * *Global.asax.cs*.  
   * Im Ordner *Views\Shared*: *\_Layout.cshtml*.
   * Im Ordner *Views\Home*: *Index.cshtml*.
   * Im Ordner *Controllers*: *AdController.cs*.
   * Im Ordner *Views\Ad* (Ordner zuerst erstellen): fünf *CSHTML*-Dateien.
3. Fügen Sie im Projekt ContosoAdsWorker die Datei *WorkerRole.cs* aus dem heruntergeladenen Projekt hinzu.

Sie können die Anwendung nun anhand der weiter oben in diesem Tutorial beschriebenen Prozedur erstellen und ausführen, und die Anwendung wird eine lokale Datenbank und den Speicheremulator verwenden.

In den folgenden Abschnitten wird der Code für die Arbeit mit Azure-Umgebung, Blobs und Warteschlangen beschrieben. Dieses Tutorial befasst sich nicht mit der Erstellung von MVC-Controllern und -Ansichten mithilfe eines Gerüsts, dem Entity Framework-Code für die Arbeit mit SQL Server-Datenbanken und den Grundsätzen asynchroner Programmierung in ASP.NET 4.5. Informationen zu diesen Themen finden Sie in den folgenden Ressourcen:

* [Erste Schritte mit MVC 5](https://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Erste Schritte mit EF 6 und MVC 5](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc)
* [Einführung in die asynchrone Programmierung in .NET 4.5](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).

### <a name="contosoadscommon---adcs"></a>ContosoAdsCommon - Ad.cs
Die Datei Ad.cs definiert eine Enumeration für Werbekategorien und eine POCO-Entitätsklasse für Werbeinformationen.

```csharp
public enum Category
{
    Cars,
    [Display(Name="Real Estate")]
    RealEstate,
    [Display(Name = "Free Stuff")]
    FreeStuff
}

public class Ad
{
    public int AdId { get; set; }

    [StringLength(100)]
    public string Title { get; set; }

    public int Price { get; set; }

    [StringLength(1000)]
    [DataType(DataType.MultilineText)]
    public string Description { get; set; }

    [StringLength(1000)]
    [DisplayName("Full-size Image")]
    public string ImageURL { get; set; }

    [StringLength(1000)]
    [DisplayName("Thumbnail")]
    public string ThumbnailURL { get; set; }

    [DataType(DataType.Date)]
    [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
    public DateTime PostedDate { get; set; }

    public Category? Category { get; set; }
    [StringLength(12)]
    public string Phone { get; set; }
}
```

### <a name="contosoadscommon---contosoadscontextcs"></a>ContosoAdsCommon - ContosoAdsContext.cs
Die Klasse ContosoAdsContext gibt an, dass die Ad-Klasse in einer DbSet-Sammlung verwendet wird, die von Entity Framework in einer SQL-Datenbank gespeichert wird.

```csharp
public class ContosoAdsContext : DbContext
{
    public ContosoAdsContext() : base("name=ContosoAdsContext")
    {
    }
    public ContosoAdsContext(string connString)
        : base(connString)
    {
    }
    public System.Data.Entity.DbSet<Ad> Ads { get; set; }
}
```

Die Klasse besitzt zwei Konstruktoren. Der erste Konstruktor wird vom Webprojekt verwendet und gibt den Namen einer Verbindungszeichenfolge an, die in der Datei Web.config gespeichert ist. Mit dem zweiten Konstruktor können Sie die tatsächliche Verbindungszeichenfolge übergeben, die vom Workerrollenprojekt verwendet wird, weil dafür keine Web.config-Datei vorhanden ist. Sie haben weiter oben gesehen, wo diese Verbindungszeichenfolge gespeichert wird, und weiter unten werden Sie sehen, wie der Code die Verbindungszeichenfolge beim Instanziieren der DbContext-Klasse abruft.

### <a name="contosoadsweb---globalasaxcs"></a>ContosoAdsWeb - Global.asax.cs
Der aus der `Application_Start`-Methode aufgerufene Code erstellt einen *images*-Blobcontainer und eine *images*-Warteschlange, falls diese noch nicht vorhanden sind. Damit wird sichergestellt, dass Blobcontainer und Warteschlange immer automatisch erstellt werden, wenn Sie ein neues Speicherkonto verwenden oder den Speicheremulator auf einem neuen Computer verwenden.

Der Code verwendet die Speicher-Verbindungszeichenfolge aus der *.cscfg* -Datei für den Zugriff auf das Speicherkonto.

```csharp
var storageAccount = CloudStorageAccount.Parse
    (RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
```

Anschließend ruft er einen Verweis auf den *images* -Blobcontainer ab, erstellt den Container, falls dieser noch nicht existiert, und setzt die Zugriffsberechtigungen für den neuen Container. Standardmäßig erlauben neue Container nur Clients mit Speicherkonto-Anmeldeinformationen den Zugriff auf Blobs. Für die Website müssen die Blobs öffentlich sein, um die Bilder mithilfe der URLs anzuzeigen, die auf die Bild-Blobs zeigen.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var imagesBlobContainer = blobClient.GetContainerReference("images");
if (imagesBlobContainer.CreateIfNotExists())
{
    imagesBlobContainer.SetPermissions(
        new BlobContainerPermissions
        {
            PublicAccess =BlobContainerPublicAccessType.Blob
        });
}
```

Ein ähnlicher Codeteil ruft einen Verweis auf die *images* -Warteschlange ab und erstellt eine neue Warteschlange. In diesem Fall sind keine Berechtigungsänderungen erforderlich.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
var imagesQueue = queueClient.GetQueueReference("images");
imagesQueue.CreateIfNotExists();
```

### <a name="contosoadsweb---layoutcshtml"></a>ContosoAdsWeb – \_Layout.cshtml
Die Datei *_Layout.cshtml* legt den App-Namen in der Kopf- und Fußzeile fest und erstellt den Menüeintrag „Ads“.

### <a name="contosoadsweb---viewshomeindexcshtml"></a>ContosoAdsWeb - Views\Home\Index.cshtml
Die Datei *Views\Home\Index.cshtml* zeigt Links zu den Kategorien auf der Startseite an. Die Links übergeben den ganzzahligen Wert aus der `Category` -Enumeration in einer querystring-Variable an die Ads-Indexseite.

```razor
<li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
<li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
<li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
<li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>
```

### <a name="contosoadsweb---adcontrollercs"></a>ContosoAdsWeb - AdController.cs
In der Datei *AdController.cs* ruft der Konstruktor die `InitializeStorage`-Methode auf, um Objekte der Azure Storage-Clientbibliothek zu erstellen, die eine API für die Arbeit mit Blobs und Warteschlangen bereitstellen.

Anschließend ruft der Code einen Verweis auf den *images*-Blobcontainer ab, wie weiter oben in *Global.asax.cs* gezeigt. Gleichzeitig wird eine angemessene Standard- [Wiederholungs-Richtlinie](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling) für eine Webanwendung gesetzt. Mit der Standardrichtlinie (exponentiell ansteigende Wartezeiten) kann es passieren, dass die Webanwendung im Fall eines vorübergehenden Fehlers über eine Minute lang stehen bleibt. Bei der hier gezeigten Richtlinie wird nach jedem Versuch drei Sekunden lang gewartet (für bis zu drei Versuche).

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesBlobContainer = blobClient.GetContainerReference("images");
```

Ein ähnlicher Codeteil ruft einen Verweis auf die *images* -Warteschlange ab.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesQueue = queueClient.GetQueueReference("images");
```

Ein Großteil des Controller-Codes ist typisch für die Arbeit mit einem Entity Framework-Datenmodell und einer DbContext-Klasse. Eine Ausnahme ist die HttpPost `Create` -Methode, die eine Datei hochlädt und im Blobspeicher ablegt. Die Modellbindung stellt ein [HttpPostedFileBase](/dotnet/api/system.web.httppostedfilebase) -Objekt für die Methode bereit.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<ActionResult> Create(
    [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
    HttpPostedFileBase imageFile)
```

Wenn ein Benutzer eine hochzuladende Datei auswählt, lädt der Code die Datei hoch, speichert sie in einem Blob und aktualisiert den Ad-Datenbankeintrag mit einer URL, die auf den Blob zeigt.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    blob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = blob.Uri.ToString();
}
```

Der Code für den Upload befindet sich in der `UploadAndSaveBlobAsync` -Methode. Er erstellt einen GUID-Namen für den Blob, lädt die Datei hoch und speichert sie, und gibt einen Verweis auf den gespeicherten Blob zurück.

```csharp
private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
{
    string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
    CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
    using (var fileStream = imageFile.InputStream)
    {
        await imageBlob.UploadFromStreamAsync(fileStream);
    }
    return imageBlob;
}
```

Nachdem mit HttpPost `Create` -Methode ein Blob hochgeladen und die Datenbank aktualisiert wurde, wird eine Nachricht in der Warteschlange erstellt, um den Back-End-Prozess darüber zu informieren, dass ein Bild für die Konvertierung in eine Miniaturansicht vorliegt.

```csharp
string queueMessageString = ad.AdId.ToString();
var queueMessage = new CloudQueueMessage(queueMessageString);
await queue.AddMessageAsync(queueMessage);
```

Der Code für die HttpPost `Edit` -Methode ist ähnlich, nur müssen hier alle vorhandenen Blobs gelöscht werden, wenn der Benutzer eine neue Bilddatei auswählt.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    await DeleteAdBlobsAsync(ad);
    imageBlob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = imageBlob.Uri.ToString();
}
```

Das nächste Beispiel zeigt den Code, der Blobs löscht, wenn Sie eine Werbeanzeige löschen.

```csharp
private async Task DeleteAdBlobsAsync(Ad ad)
{
    if (!string.IsNullOrWhiteSpace(ad.ImageURL))
    {
        Uri blobUri = new Uri(ad.ImageURL);
        await DeleteAdBlobAsync(blobUri);
    }
    if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
    {
        Uri blobUri = new Uri(ad.ThumbnailURL);
        await DeleteAdBlobAsync(blobUri);
    }
}
private static async Task DeleteAdBlobAsync(Uri blobUri)
{
    string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
    CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
    await blobToDelete.DeleteAsync();
}
```

### <a name="contosoadsweb---viewsadindexcshtml-and-detailscshtml"></a>ContosoAdsWeb - Views\Ad\Index.cshtml und Details.cshtml
Die Datei *Index.cshtml* zeigt Miniaturansichten zusammen mit den restlichen Werbedaten an.

```razor
<img src="@Html.Raw(item.ThumbnailURL)" />
```

Die Datei *Details.cshtml* zeigt das Bild in voller Größe an.

```razor
<img src="@Html.Raw(Model.ImageURL)" />
```

### <a name="contosoadsweb---viewsadcreatecshtml-and-editcshtml"></a>ContosoAdsWeb - Views\Ad\Create.cshtml und Edit.cshtml
Die Dateien *Create.cshtml* und *Edit.cshtml* geben die Formularcodierung an, mit der der Controller das `HttpPostedFileBase`-Objekt abruft.

```razor
@using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))
```

Ein `<input>` -Element weist den Browser an, ein Dateiauswahl-Dialogfeld zu öffnen.

```razor
<input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />
```

### <a name="contosoadsworker---workerrolecs---onstart-method"></a>ContosoAdsWorker - WorkerRole.cs - OnStart-Methode
Die Azure Workerrollen-Umgebung ruft die `OnStart`-Methode in der `WorkerRole`-Klasse auf, wenn die Workerrolle gestartet wird, und ruft die `Run`-Methode auf, wenn die `OnStart`-Methode abgeschlossen wird.

Die `OnStart` -Methode ruft die Datenbank-Verbindungszeichenfolge aus der *CSCFG* -Datei ab und übergibt sie an die DbContext-Klasse des Entity Framework. Der SQLClient-Anbieter wird standardmäßig verwendet, daher muss kein Anbieter angegeben werden.

```csharp
var dbConnString = CloudConfigurationManager.GetSetting("ContosoAdsDbConnectionString");
db = new ContosoAdsContext(dbConnString);
```

Anschließend ruft die Methode einen Verweis auf das Speicherkonto ab und erstellt den Blobcontainer und die Warteschlange, falls diese Elemente noch nicht vorhanden sind. Dieser Code ähnelt dem Code, den Sie bereits in der `Application_Start` -Methode der Webrolle gesehen haben.

### <a name="contosoadsworker---workerrolecs---run-method"></a>ContosoAdsWorker - WorkerRole.cs - Run-Methode
Die `Run`-Methode wird aufgerufen, wenn die Initialisierung der `OnStart`-Methode abgeschlossen ist. Die Methode führt eine Endlosschleife aus, die nach neuen Nachrichten in der Warteschlange sucht und diese beim Eintreffen verarbeitet.

```csharp
public override void Run()
{
    CloudQueueMessage msg = null;

    while (true)
    {
        try
        {
            msg = this.imagesQueue.GetMessage();
            if (msg != null)
            {
                ProcessQueueMessage(msg);
            }
            else
            {
                System.Threading.Thread.Sleep(1000);
            }
        }
        catch (StorageException e)
        {
            if (msg != null && msg.DequeueCount > 5)
            {
                this.imagesQueue.DeleteMessage(msg);
            }
            System.Threading.Thread.Sleep(5000);
        }
    }
}
```

Nach jedem Durchgang der Schleife geht das Programm für eine Sekunde in den Ruhezustand, wenn keine Nachricht gefunden wurde. So wird verhindert, dass die Workerrolle übermäßige Kosten für CPU-Zeit und Speichertransaktionen verursacht. Das Microsoft-Kundenberatungsteam weiß von einem Entwickler zu berichten, der diesen Mechanismus vergessen hat, seine Anwendung bereitgestellt hat und in den Urlaub gefahren ist. Als er zurückkam, war sein Versehen teurer als der gesamte Urlaub.

Manchmal kann der Inhalt einer Nachricht in der Warteschlange einen Verarbeitungsfehler verursachen. Dies nennt man eine *nicht verarbeitbare Nachricht*. Wenn Sie in diesem Fall nur einen Fehler protokollieren und die Schleife neu starten, kann es passieren, dass Sie endlos versuchen, diese Nachricht zu verarbeiten.  Daher enthält der catch-Block eine Anweisung, die prüft, wie oft die Anwendung bereits versucht hat, die aktuelle Nachricht zu verarbeiten. Wenn diese Anzahl größer als 5 ist, wird die Nachricht aus der Warteschlange gelöscht.

`ProcessQueueMessage` wird aufgerufen, wenn eine Warteschlangennachricht gefunden wurde.

```csharp
private void ProcessQueueMessage(CloudQueueMessage msg)
{
    var adId = int.Parse(msg.AsString);
    Ad ad = db.Ads.Find(adId);
    if (ad == null)
    {
        throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", adId.ToString()));
    }

    CloudBlockBlob inputBlob = this.imagesBlobContainer.GetBlockBlobReference(ad.ImageURL);

    string thumbnailName = Path.GetFileNameWithoutExtension(inputBlob.Name) + "thumb.jpg";
    CloudBlockBlob outputBlob = this.imagesBlobContainer.GetBlockBlobReference(thumbnailName);

    using (Stream input = inputBlob.OpenRead())
    using (Stream output = outputBlob.OpenWrite())
    {
        ConvertImageToThumbnailJPG(input, output);
        outputBlob.Properties.ContentType = "image/jpeg";
    }

    ad.ThumbnailURL = outputBlob.Uri.ToString();
    db.SaveChanges();

    this.imagesQueue.DeleteMessage(msg);
}
```

Der Code liest die Bild-URL aus der Datenbank, konvertiert das Bild zu einer Miniaturansicht, speichert die Miniaturansicht in einem Blob, aktualisiert die Datenbank mit der URL der Miniaturansicht und löscht die Warteschlangen-Nachricht.

> [!NOTE]
> Der Code in der `ConvertImageToThumbnailJPG`-Methode verwendet zur Vereinfachung Klassen aus dem System.Drawing-Namespace. Die Klassen in diesem Namespace wurden jedoch für den Einsatz mit Windows Forms entwickelt. Sie werden nicht für die Verwendung in einem Windows- oder ASP.NET-Dienst unterstützt. Weitere Informationen zu Bildverarbeitungsoptionen finden Sie unter [Dynamic Image Generation](https://www.hanselman.com/blog/BackToBasicsDynamicImageGenerationASPNETControllersRoutingIHttpHandlersAndRunAllManagedModulesForAllRequests.aspx) (Dynamische Imageerstellung) und [Deep Inside Image Resizing](https://www.hanselminutes.com/313/deep-inside-image-resizing-and-scaling-with-aspnet-and-iis-with-imageresizingnet-author-na) (Ausführliche Informationen zur Bildgrößenanpassung).
>
>

## <a name="troubleshooting"></a>Problembehandlung
Falls bei der Ausführung der Anweisungen in diesem Tutorial Probleme auftreten, finden Sie hier einige gängige Fehler und Ansätze zu deren Lösung.

### <a name="serviceruntimeroleenvironmentexception"></a>ServiceRuntime.RoleEnvironmentException
Das `RoleEnvironment` -Objekt wird von Azure bereitgestellt, wenn Sie eine Anwendung in Azure ausführen oder den Azure-Serveremulator lokal verwenden.  Wenn Sie diesen Fehler bei der lokalen Ausführung erhalten, vergewissern Sie sich, dass Sie das ContosoAdsCloudService-Projekt als Startprojekt gesetzt haben. Damit wird festgelegt, dass die Anwendung im Azure-Serveremulator ausgeführt werden soll.

Die Anwendung verwendet die Azure-Rollenumgebung z.B. zum Abrufen der Verbindungszeichenfolgen aus den *CSCFG*-Dateien. Diese Ausnahme kann also auch durch eine fehlende Verbindungszeichenfolge verursacht werden. Stellen Sie sicher, dass Sie die StorageConnectionString-Einstellung sowohl für die lokale als auch für die Cloudkonfiguration im ContosoAdsWeb-Projekt erstellt haben und dass Sie beide Verbindungszeichenfolgen für beide Konfigurationen im ContosoAdsWorker-Projekt erstellt haben. Mit einer **Alles durchsuchen**-Suche nach StorageConnectionString in der gesamten Lösung sollten Sie die Zeichenfolge neunmal in sechs Dateien finden.

### <a name="cannot-override-to-port-xxx-new-port-below-minimum-allowed-value-8080-for-protocol-http"></a>Port xxx kann nicht überschrieben werden. Neuer Port unterhalb des zulässigen Mindestwerts 8080 für das HTTP-Protokoll
Versuchen Sie, die Portnummer für das Webprojekt zu ändern. Klicken Sie mit der rechten Maustaste auf das ContosoAdsWeb-Projekt, und klicken Sie auf **Eigenschaften**. Klicken Sie auf die Registerkarte **Web**, und ändern Sie die Portnummer in der Einstellung **Projekt-URL**.

Eine weitere Möglichkeit zur Behebung dieses Problems finden Sie im nächsten Abschnitt.

### <a name="other-errors-when-running-locally"></a>Sonstige Fehler bei der lokalen Ausführung
Standardmäßig verwenden neue Clouddienstprojekte den Azure-Serveremulator Express, um die Azure-Umgebung zu simulieren. Dies ist eine abgespeckte Version der Serveremulator-Vollversion. Unter bestimmten Umständen kann es passieren, dass die Vollversion funktioniert und die Express-Version einen Fehler verursacht.  

Um das Projekt mit der Emulator-Vollversion zu verwenden, klicken Sie mit der rechten Maustaste auf das ContosoAdsCloudService-Projekt, und klicken anschließend auf **Eigenschaften**. Klicken Sie im Fenster **Eigenschaften** auf die Registerkarte **Web**, und klicken Sie auf das Optionsfeld **Emulator-Vollversion verwenden**.

Um die Anwendung mit der Emulator-Vollversion zu verwenden, müssen Sie Visual Studio mit Administratorrechten öffnen.

## <a name="next-steps"></a>Nächste Schritte
Die Contoso Ads-Anwendung wurde für dieses Tutorial bewusst einfach gehalten. Sie implementiert z.B. keine [Abhängigkeitsinjektion](https://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection) oder [Repository und Arbeitseinheit](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo), verwendet keine [Schnittstelle für die Protokollierung](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log) und keine [EF Code First-Migrationen](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) zur Verwaltung von Datenmodellen oder [EF-Verbindungsstabilität](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) für vorübergehende Verbindungsfehler usw.

Hier finden Sie einige Beispielanwendungen für Clouddienste mit realitätsnäheren Programmierpraktiken, geordnet vom einfachsten hin zum komplexesten Projekt:

* [PhluffyFotos](https://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31). Ähnlich der Contoso Ads-Anwendung, jedoch mit mehr Funktionen und realitätsnäheren Programmierpraktiken.
* [Azure-Clouddienst-Anwendung mit mehreren Ebenen mit Tabellen, Warteschlangen und Blobs](https://code.msdn.microsoft.com/windowsazure/Windows-Azure-Multi-Tier-eadceb36). Bietet eine Einführung in Azure-Speichertabellen sowie in Blobs und Warteschlangen. Dieser Artikel basiert auf einer älteren Version des Azure SDK für .NET. Es müssen einige Änderungen vorgenommen werden, damit die Anweisungen für die aktuelle Version funktionieren.

Weitere Informationen zur Entwicklung für die Cloud finden Sie unter [Erstellen von Cloud-Apps mit Azure](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction).

Ein Einführungsvideo zu bewährten Methoden und Vorlagen für Azure Storage finden Sie unter [Microsoft Azure Storage – Neuigkeiten, bewährte Methoden und Vorlagen](https://channel9.msdn.com/Events/Build/2014/3-628).

Weitere Informationen finden Sie in den folgenden Ressourcen:

* [Azure Cloud Services Part 1: Einführung](https://justazure.com/microsoft-azure-cloud-services-part-1-introduction/)
* [Verwalten von Cloud Services](cloud-services-how-to-manage-portal.md)
* [Azure Storage (in englischer Sprache)](https://docs.microsoft.com/azure/storage/)
* [Wie wähle ich den richtigen Clouddienstanbieter?](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/)
