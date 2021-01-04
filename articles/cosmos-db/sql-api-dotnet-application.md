---
title: ASP.NET Core MVC-Web-App-Tutorial mit Azure Cosmos DB
description: 'ASP.NET Core MVC-Tutorial zum Erstellen einer MVC-Webanwendung mit Azure Cosmos DB Speichern von JSON- und Zugriffsdaten über eine To-Do-App, die in Azure App Service gehostet wird: Schrittanleitung im ASP NET Core MVC-Tutorial'
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 05/08/2020
ms.author: sngun
ms.custom: devx-track-dotnet
ms.openlocfilehash: 528cab915a1ac3918146e428e9ae6b3c401324c8
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/24/2020
ms.locfileid: "96010356"
---
# <a name="tutorial-develop-an-aspnet-core-mvc-web-application-with-azure-cosmos-db-by-using-net-sdk"></a>Tutorial: Entwickeln einer ASP.NET Core MVC-Webanwendung mit Azure Cosmos DB unter Verwendung des .NET SDK
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](./create-sql-api-python.md)
> * [Xamarin](mobile-apps-with-xamarin.md)

In diesem Tutorial erfahren Sie, wie Sie mithilfe von Azure Cosmos DB Daten aus einer in Azure gehosteten ASP.NET MVC-Webanwendung speichern und abrufen. In diesem Tutorial verwenden Sie .NET SDK V3. In der folgenden Abbildung ist die Webseite dargestellt, die Sie anhand des Beispiels in diesem Artikel erstellen:

:::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-image01.png" alt-text="Screenshot der in diesem Tutorial erstellten MVC-Webanwendung für Aufgabenlisten: Schrittanleitung im ASP.NET Core MVC-Tutorial":::

Sollten Sie keine Zeit für das Tutorial haben, können Sie das gesamte Beispielprojekt von [GitHub][GitHub] herunterladen.

In diesem Lernprogramm wird Folgendes behandelt:

> [!div class="checklist"]
>
> * Erstellen eines Azure Cosmos-Kontos
> * Erstellen einer ASP.NET Core MVC-App
> * Herstellen einer Verbindung zwischen App und Azure Cosmos DB
> * Durchführen von Erstellungs-, Lese-, Aktualisierungs- und Löschvorgängen (Create, Read, Update, Delete (CRUD)) für die Daten

> [!TIP]
> Dieses Tutorial setzt Erfahrung mit der Verwendung von ASP.NET Core MVC und Azure App Service voraus. Sollten Sie noch nicht mit ASP.NET Core oder den [erforderlichen Tools](#prerequisites) vertraut sein, empfiehlt es sich, das vollständige Beispielprojekt von [GitHub][GitHub] herunterzuladen, die erforderlichen NuGet-Pakete hinzuzufügen und das Projekt auszuführen. Nachdem Sie das Projekt erstellt haben, können Sie den Artikel lesen, um sich im Kontext des Projekts mit dem Code vertraut zu machen.

## <a name="prerequisites"></a><a name="prerequisites"></a>Voraussetzungen

Vergewissern Sie sich zunächst, dass Sie über die folgenden Ressourcen verfügen:

* Ein aktives Azure-Konto. Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* Visual Studio 2019: [!INCLUDE [cosmos-db-emulator-vs](../../includes/cosmos-db-emulator-vs.md)]  

Alle Screenshots in diesem Artikel stammen aus Microsoft Visual Studio Community 2019. Bei Verwendung einer anderen Version stimmen Ihre Bildschirme und Optionen ggf. nicht vollständig überein. Die Projektmappe sollte funktionieren, sofern Sie die Voraussetzungen erfüllt haben.

## <a name="step-1-create-an-azure-cosmos-account"></a><a name="create-an-azure-cosmos-account"></a>Schritt 1: Erstellen eines Azure Cosmos-Kontos

Erstellen Sie zunächst ein Azure Cosmos-Konto. Falls Sie bereits über ein Azure Cosmos DB-SQL-API-Konto verfügen oder den Azure Cosmos DB-Emulator verwenden, können Sie mit [Schritt 2: Erstellen einer neuen ASP.NET MVC-Anwendung](#create-a-new-mvc-application) fortfahren.

[!INCLUDE [create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [keys](../../includes/cosmos-db-keys.md)]

Im nächsten Abschnitt erstellen Sie eine neue ASP.NET Core MVC-Anwendung.

## <a name="step-2-create-a-new-aspnet-core-mvc-application"></a><a name="create-a-new-mvc-application"></a>Schritt 2: Erstellen einer neuen ASP.NET Core MVC-Anwendung

1. Öffnen Sie Visual Studio, und wählen Sie **Neues Projekt erstellen** aus.

1. Suchen Sie unter **Neues Projekt erstellen** nach der Option **ASP.NET Core-Webanwendung**  für C#, und wählen Sie sie aus. Klicken Sie auf **Weiter**, um fortzufahren.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-new-project-dialog.png" alt-text="Erstellen eines neuen ASP.NET Core-Webanwendungsprojekts":::

1. Geben Sie dem Projekt unter **Neues Projekt konfigurieren** den Namen *todo*, und wählen Sie **Erstellen** aus.

1. Wählen Sie unter **Neue ASP.NET Core-Webanwendung erstellen** die Option **Webanwendung (Model-View-Controller)** aus. Klicken Sie auf **Erstellen** , um fortzufahren.

   Visual Studio erstellt eine leere MVC-Anwendung.

1. Wählen Sie **Debuggen** > **Debugging starten** aus (oder F5), um Ihre ASP.NET-Anwendung lokal auszuführen.

## <a name="step-3-add-azure-cosmos-db-nuget-package-to-the-project"></a><a name="add-nuget-packages"></a>Schritt 3: Hinzufügen des Azure Cosmos DB-NuGet-Pakets zum Projekt

Nachdem Sie nun über den Großteil des ASP.NET Core MVC-Frameworkcodes verfügen, der für diese Lösung erforderlich ist, fügen Sie als Nächstes die NuGet-Pakete hinzu, die für die Verbindungsherstellung mit Azure Cosmos DB benötigt werden.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.

1. Suchen Sie im **NuGet-Paket-Manager** nach der Option **Microsoft.Azure.Cosmos**, und wählen Sie sie aus. Wählen Sie **Installieren** aus.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-nuget.png" alt-text="Installieren des NuGet-Pakets":::

   Visual Studio führt den Download und die Installation des Azure Cosmos DB-Pakets und der zugehörigen Abhängigkeiten durch.

   Sie können auch die Option **Paket-Manager-Konsole** verwenden, um das NuGet-Paket zu installieren. Klicken Sie hierzu auf **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**. Geben Sie an der Eingabeaufforderung den folgenden Befehl ein:

   ```ps
   Install-Package Microsoft.Azure.Cosmos
   ```
  
## <a name="step-4-set-up-the-aspnet-core-mvc-application"></a><a name="set-up-the-mvc-application"></a>Schritt 4: Einrichten der ASP.NET Core MVC-Anwendung

Fügen Sie der MVC-Anwendung als Nächstes die Modelle, Ansichten und Controller hinzu.

### <a name="add-a-model"></a><a name="add-a-model"></a> Hinzufügen eines Modells

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Modelle**, und wählen Sie **Hinzufügen** > **Klasse** aus.

1. Geben Sie Ihrer neuen Klasse im Feld **Neues Element hinzufügen** den Namen *Item.cs*, und wählen Sie die Option **Hinzufügen**.

1. Ersetzen Sie den Inhalt der *Item.cs*-Klasse durch den folgenden Code:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Models/Item.cs":::

In Azure Cosmos DB wird JSON-Code zum Verschieben und Speichern von Daten verwendet. Sie können das `JsonProperty`-Attribut verwenden, um zu steuern, wie Objekte mit JSON-Code serialisiert und deserialisiert werden. Anhand der `Item`-Klasse wird das `JsonProperty`-Attribut demonstriert. Mit diesem Code wird das Format des Eigenschaftsnamens gesteuert, der in den JSON-Code eingeht. Außerdem wird die .NET-Eigenschaft in `Completed` umbenannt.

### <a name="add-views"></a><a name="add-views"></a>Hinzufügen von Ansichten

Als Nächstes fügen wir die folgenden Ansichten hinzu.

* Eine Ansicht zum Erstellen von Elementen
* Eine Ansicht zum Löschen von Elementen
* Eine Ansicht zum Abrufen der Details eines Elements
* Eine Ansicht zum Bearbeiten von Elementen
* Eine Ansicht zum Auflisten aller Elemente

#### <a name="create-item-view"></a><a name="AddNewIndexView"></a>Erstellen einer Elementansicht

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Ansichten**, und wählen Sie **Hinzufügen** > **Neuer Ordner** aus. Geben Sie dem Ordner den Namen *Element*.

1. Klicken Sie mit der rechten Maustaste auf den leeren Ordner **Element**, und wählen Sie anschließend **Hinzufügen** > **Ansicht** aus.

1. Nehmen Sie unter **Add MVC View** (MVC-Ansicht hinzufügen) die folgenden Änderungen vor:

   * Geben Sie unter **Sichtname** den Namen *Erstellen* ein.
   * Wählen Sie unter **Vorlage** die Option **Erstellen** aus.
   * Wählen Sie unter **Modellklasse** die Option **Item (todo.Models)** aus.
   * Wählen Sie **Use a layout page** (Layoutseite verwenden) aus, und geben Sie *~/Views/Shared/_Layout.cshtml* ein.
   * Wählen Sie **Hinzufügen**.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-add-mvc-view.png" alt-text="Screenshot: Dialogfeld „MVC-Ansicht hinzufügen“":::

1. Wählen Sie als Nächstes **Hinzufügen** aus. Daraufhin erstellt Visual Studio eine neue Vorlagenansicht. Ersetzen Sie den Code in der Datei durch die folgenden Inhalte:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Create.cshtml":::

#### <a name="delete-item-view"></a><a name="AddEditIndexView"></a>Löschen einer Elementansicht

1. Klicken Sie im **Projektmappen-Explorer** erneut mit der rechten Maustaste auf den Ordner **Element**, und wählen Sie **Hinzufügen** > **Ansicht** aus.

1. Nehmen Sie unter **Add MVC View** (MVC-Ansicht hinzufügen) die folgenden Änderungen vor:

   * Geben Sie im Feld **Ansichtsname** *Löschen* ein.
   * Wählen Sie im Feld **Vorlage** die Option **Löschen** aus.
   * Wählen Sie im Feld **Modellklasse** die Option **Item (todo.Models)** aus.
   * Wählen Sie **Use a layout page** (Layoutseite verwenden) aus, und geben Sie *~/Views/Shared/_Layout.cshtml* ein.
   * Wählen Sie **Hinzufügen**.

1. Wählen Sie als Nächstes **Hinzufügen** aus. Daraufhin erstellt Visual Studio eine neue Vorlagenansicht. Ersetzen Sie den Code in der Datei durch die folgenden Inhalte:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Delete.cshtml":::

#### <a name="add-a-view-to-get-an-item-details"></a><a name="AddItemIndexView"></a>Hinzufügen einer Ansicht zum Abrufen der Details eines Elements

1. Klicken Sie im **Projektmappen-Explorer** erneut mit der rechten Maustaste auf den Ordner **Element**, und wählen Sie **Hinzufügen** > **Ansicht**.

1. Geben Sie unter **Add MVC View** (MVC-Ansicht hinzufügen) die folgenden Werte an:

   * Geben Sie unter **Anzeigename** *Details* ein.
   * Wählen Sie unter **Vorlage** die Option **Details** aus.
   * Wählen Sie unter **Modellklasse** die Option **Item (todo.Models)** aus.
   * Wählen Sie **Use a layout page** (Layoutseite verwenden) aus, und geben Sie *~/Views/Shared/_Layout.cshtml* ein.

1. Wählen Sie als Nächstes **Hinzufügen** aus. Daraufhin erstellt Visual Studio eine neue Vorlagenansicht. Ersetzen Sie den Code in der Datei durch die folgenden Inhalte:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Details.cshtml":::

#### <a name="add-an-edit-item-view"></a><a name="AddEditIndexView"></a>Hinzufügen einer Ansicht zum Bearbeiten von Elementen

1. Klicken Sie im **Projektmappen-Explorer** erneut mit der rechten Maustaste auf den Ordner **Element**, und wählen Sie **Hinzufügen** > **Ansicht** aus.

1. Nehmen Sie unter **Add MVC View** (MVC-Ansicht hinzufügen) die folgenden Änderungen vor:

   * Geben Sie im Feld **Ansichtsname** den Namen *Edit* ein.
   * Wählen Sie im Feld **Vorlage** die Option **Bearbeiten** aus.
   * Wählen Sie im Feld **Modellklasse** die Option **Item (todo.Models)** aus.
   * Wählen Sie **Use a layout page** (Layoutseite verwenden) aus, und geben Sie *~/Views/Shared/_Layout.cshtml* ein.
   * Wählen Sie **Hinzufügen**.

1. Wählen Sie als Nächstes **Hinzufügen** aus. Daraufhin erstellt Visual Studio eine neue Vorlagenansicht. Ersetzen Sie den Code in der Datei durch die folgenden Inhalte:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Edit.cshtml":::

#### <a name="add-a-view-to-list-all-the-items"></a><a name="AddEditIndexView"></a>Hinzufügen einer Ansicht zum Auflisten aller Elemente

Fügen Sie abschließend eine Ansicht zum Abrufen aller Elemente mit den folgenden Schritten hinzu:

1. Klicken Sie im **Projektmappen-Explorer** erneut mit der rechten Maustaste auf den Ordner **Element**, und wählen Sie **Hinzufügen** > **Ansicht** aus.

1. Nehmen Sie unter **Add MVC View** (MVC-Ansicht hinzufügen) die folgenden Änderungen vor:

   * Geben Sie im Feld **Ansichtsname** den Namen *Index* ein.
   * Wählen Sie im Feld **Vorlage** die Option **Liste** aus.
   * Wählen Sie im Feld **Modellklasse** die Option **Item (todo.Models)** aus.
   * Wählen Sie **Use a layout page** (Layoutseite verwenden) aus, und geben Sie *~/Views/Shared/_Layout.cshtml* ein.
   * Wählen Sie **Hinzufügen**.

1. Wählen Sie als Nächstes **Hinzufügen** aus. Daraufhin erstellt Visual Studio eine neue Vorlagenansicht. Ersetzen Sie den Code in der Datei durch die folgenden Inhalte:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Views/Item/Index.cshtml":::

Schließen Sie nach dem Ausführen dieser Schritte alle *CSHTML*-Dokumente in Visual Studio.

### <a name="declare-and-initialize-services"></a><a name="initialize-services"></a>Dienste deklarieren und initialisieren

Zunächst fügen wir eine Klasse hinzu, die die Logik zur Verbindungsherstellung mit bzw. zur Verwendung von Azure Cosmos DB enthält. In diesem Tutorial wird die entsprechende Logik in einer Klasse namens `CosmosDbService` gekapselt und eine Schnittstelle namens `ICosmosDbService` aufgerufen. Dieser Dienst führt die CRUD-Vorgänge durch. Darüber hinaus führt er Lesefeedvorgänge aus, z. B. das Auflisten unvollständiger Elemente sowie das Erstellen, Bearbeiten und Löschen der Elemente.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Hinzufügen** > **Neuer Ordner** aus. Geben Sie dem Ordner den Namen *Dienste*.

1. Klicken Sie mit der rechten Maustaste auf den Ordner **Dienste**, und wählen Sie **Hinzufügen** > **Klasse** aus. Nennen Sie die neue Klasse *CosmosDbService*, und wählen Sie **Hinzufügen** aus.

1. Ersetzen Sie den Inhalt von *CosmosDbService.cs* durch den folgenden Code:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/CosmosDbService.cs":::

1. Klicken Sie mit der rechten Maustaste auf den Ordner **Dienste**, und wählen Sie **Hinzufügen** > **Klasse** aus. Nennen Sie die neue Klasse *ICosmosDbService*, und wählen Sie **Hinzufügen** aus.

1. Fügen Sie der *ICosmosDbService*-Klasse den folgenden Code hinzu:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Services/ICosmosDbService.cs":::

1. Öffnen Sie die Datei *Startup.cs* in Ihrer Lösung, und fügen Sie die **InitializeCosmosClientInstanceAsync**-Methode hinzu, mit der die Konfiguration gelesen und der Client initialisiert wird.

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="InitializeCosmosClientInstanceAsync" :::

1. Ersetzen Sie in dieser Datei die `ConfigureServices`-Methode durch Folgendes:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Startup.cs" id="ConfigureServices":::

   Mit dem Code in diesem Schritt wird der Client basierend auf der Konfiguration als Singletoninstanz initialisiert, um per [Dependency Injection in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection) injiziert zu werden.

   Stellen Sie außerdem sicher, dass Sie den Standard-Model View Controller (MVC) in `Item` ändern, indem Sie die Routen in der Methode `Configure` derselben Datei bearbeiten:

   ```csharp
    app.UseEndpoints(endpoints =>
          {
                endpoints.MapControllerRoute(
                   name: "default",
                   pattern: "{controller=Item}/{action=Index}/{id?}");
          });
   ```


1. Definieren Sie die Konfiguration in der Datei *appsettings.json* des Projekts wie im folgenden Ausschnitt:

   :::code language="json" source="~/samples-cosmosdb-dotnet-core-web-app/src/appsettings.json":::

### <a name="add-a-controller"></a><a name="add-a-controller"></a>Hinzufügen eines Controllers

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Controller**, und wählen Sie **Hinzufügen** > **Controller** aus.

1. Wählen Sie unter **Gerüst hinzufügen** die Option **MVC-Controller – leer** und dann **Hinzufügen** aus.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-controller-add-scaffold.png" alt-text="Auswahl von „MVC-Controller – leer“ unter „Gerüst hinzufügen“":::

1. Geben Sie dem neuen Controller den Namen *ItemController*.

1. Ersetzen Sie den Inhalt von *ItemController.cs* durch den folgenden Code:

   :::code language="csharp" source="~/samples-cosmosdb-dotnet-core-web-app/src/Controllers/ItemController.cs":::

Das Attribut **ValidateAntiForgeryToken** wird hier verwendet, um den Schutz dieser Anwendung vor websiteübergreifenden Anforderungsfälschungen zu unterstützen. Ihre Ansichten sollten auch mit diesem Fälschungssicherheitstoken funktionieren. Weitere Informationen und Beispiele finden Sie unter [Preventing Cross-Site Request Forgery (CSRF) Attacks in ASP.NET MVC Application][Preventing Cross-Site Request Forgery] (Verhindern von CSRF-Angriffen (Cross-Site Request Forgery) in ASP.NET MVC-Anwendungen). Der auf [GitHub][GitHub] bereitgestellte Quellcode enthält die vollständige Implementierung.

Zum Schutz vor Overposting-Angriffen wird außerdem das Attribut **Bind** im Methodenparameter verwendet. Weitere Informationen finden Sie im [Tutorial: Implement CRUD Functionality with the Entity Framework in ASP.NET MVC][Basic CRUD Operations in ASP.NET MVC] (Tutorial: Implementieren der CRUD-Funktionalität mit dem Entity Framework in ASP.NET MVC).

## <a name="step-5-run-the-application-locally"></a><a name="run-the-application"></a>Schritt 5: Lokales Ausführen der Anwendung

Gehen Sie wie folgt vor, um die Anwendung auf Ihrem lokalen Computer zu testen:

1. Drücken Sie in Visual Studio F5, um die Anwendung im Debugmodus zu erstellen. Die Anwendung sollte erstellt, und ein Browser mit der zuvor angezeigten leeren Rasterseite sollte geöffnet werden:

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item-a.png" alt-text="Screenshot der in diesem Tutorial erstellten Aufgabenlisten-Webanwendung":::
   
   Wenn sich stattdessen die Startseite der Anwendung öffnet, fügen Sie `/Item` an die URL an.

1. Wählen Sie den Link **Neu erstellen** aus, und fügen Sie Werte in die Felder **Name** und **Beschreibung** ein. Lassen Sie das Kontrollkästchen **Abgeschlossen** deaktiviert. Wenn Sie es aktivieren, fügt die App das neue Element im abgeschlossenen Zustand hinzu. Das Element wird dann nicht mehr in der ursprünglichen Liste angezeigt.

1. Klicken Sie auf **Erstellen**. Sie erhalten von der App die Ansicht **Index**, und Ihr Element wird in der Liste angezeigt. Sie können Ihrer **Aufgabenliste** noch einige weitere Elemente hinzufügen.

    :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-an-item.png" alt-text="Screenshot der Ansicht „Index“":::
  
1. Wählen Sie neben einem **Element** in der Liste die Option **Bearbeiten** aus. Die App öffnet die Ansicht **Bearbeiten**, in der Sie alle Eigenschaften Ihres Objekts aktualisieren können, einschließlich des Flags **Abgeschlossen**. Wenn Sie **Abgeschlossen** und dann **Speichern** auswählen, zeigt die App das **Element** in der Liste als „Abgeschlossen“ an.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-completed-item.png" alt-text="Screenshot der Ansicht „Index“ mit aktiviertem Feld „Abgeschlossen“":::

1. Überprüfen Sie den Zustand der Daten im Azure Cosmos DB-Dienst mit dem [Cosmos-Explorer](https://cosmos.azure.com) oder dem Daten-Explorer des Azure Cosmos DB-Emulators.

1. Nachdem Sie die App getestet haben, wählen Sie STRG+F5 aus, um das Debuggen der App zu beenden. Jetzt können Sie Ihre App bereitstellen.

## <a name="step-6-deploy-the-application"></a><a name="deploy-the-application-to-azure"></a>Schritt 6: Bereitstellen der Anwendung

Nachdem die vollständige Anwendung korrekt mit Azure Cosmos DB zusammenarbeitet, stellen wir diese Web-App in Azure App Service bereit.  

1. Klicken Sie zum Veröffentlichen der Anwendung im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

1. Wählen Sie unter **Veröffentlichungsziel auswählen** die Option **App Service** aus.

1. Wählen Sie zum Verwenden eines vorhandenen App Service-Profils die Option **Vorhandene auswählen** und dann **Veröffentlichen** aus.

1. Wählen Sie unter **App Service** ein **Abonnement** aus. Sortieren Sie mithilfe des Filters **Ansicht** nach der Ressourcengruppe oder dem Ressourcentyp.

1. Suchen Sie nach Ihrem Profil, und wählen Sie anschließend **OK** aus. Suchen Sie dann nach der erforderlichen Azure App Service-Instanz, und wählen Sie **OK** aus.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-app-service-2019.png" alt-text="Dialogfeld „App Service“ in Visual Studio":::

Eine weitere Möglichkeit ist das Erstellen eines neuen Profils:

1. Klicken Sie wie in der vorherigen Prozedur im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.
  
1. Wählen Sie unter **Veröffentlichungsziel auswählen** die Option **App Service** aus.

1. Wählen Sie unter **Veröffentlichungsziel auswählen** die Option **Neu erstellen** und dann **Veröffentlichen** aus.

1. Geben Sie unter **App Service** den Namen Ihrer Web-App und das entsprechende Abonnement, die Ressourcengruppe und den Hostingplan ein, und wählen Sie anschließend **Erstellen** aus.

   :::image type="content" source="./media/sql-api-dotnet-application/asp-net-mvc-tutorial-create-app-service-2019.png" alt-text="Dialogfeld „App Service erstellen“ in Visual Studio":::

Nach wenigen Sekunden veröffentlicht Visual Studio Ihre Webanwendung und startet einen Browser mit Ihrem in Azure ausgeführten Projekt.

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial wurde beschrieben, wie Sie eine ASP.NET Core MVC-Webanwendung erstellen. Ihre Anwendung kann auf Daten zugreifen, die in Azure Cosmos DB gespeichert sind. Sie können nun mit diesen Ressourcen fortfahren:

* [Partitioning in Azure Cosmos DB](./partitioning-overview.md) (Partitionierung in Azure Cosmos DB)
* [Erste Schritte mit SQL-Abfragen](./sql-query-getting-started.md)
* [Modellieren und Partitionieren von Daten in Azure Cosmos DB anhand eines praktischen Beispiels](./how-to-model-partition-example.md)

[Visual Studio Express]: https://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft Web Platform Installer]: https://www.microsoft.com/web/downloads/platform.aspx
[Preventing Cross-Site Request Forgery]: /aspnet/web-api/overview/security/preventing-cross-site-request-forgery-csrf-attacks
[Basic CRUD Operations in ASP.NET MVC]: /aspnet/mvc/overview/getting-started/getting-started-with-ef-using-mvc/implementing-basic-crud-functionality-with-the-entity-framework-in-asp-net-mvc-application
[GitHub]: https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app
