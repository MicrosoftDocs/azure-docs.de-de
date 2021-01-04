---
title: Bereitstellen einer Web-App mit einer Vorlage – Azure Cosmos DB
description: Erfahren Sie, wie Sie ein Azure Cosmos-Konto, Azure App Service-Web-Apps und eine Beispielwebanwendung mithilfe einer Azure Resource Manager-Vorlage bereitstellen.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/19/2020
ms.author: mjbrown
ms.openlocfilehash: 55d58a6c4724bd01325db029ed75d77ccc96d0f8
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93333578"
---
# <a name="deploy-azure-cosmos-db-and-azure-app-service-with-a-web-app-from-github-using-an-azure-resource-manager-template"></a>Bereitstellen von Azure Cosmos DB und Azure App Service mit einer Web-App von GitHub mithilfe einer Azure Resource Manager-Vorlage
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

In diesem Tutorial wird gezeigt, wie Sie eine Bereitstellung ohne Benutzereingriff für eine Webanwendung durchführen, die bei der ersten Ausführung eine Verbindung mit Azure Cosmos DB herstellt, ohne dass Verbindungsinformationen von Azure Cosmos DB ausgeschnitten und in `appsettings.json` oder den Azure App Services-Anwendungseinstellungen im Azure-Portal eingefügt werden müssen. Alle diese Aktionen werden mithilfe einer Azure Resource Manager-Vorlage in einem einzelnen Vorgang durchgeführt. In diesem Beispiel wird das [Azure Cosmos DB-ToDo-Beispiel](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) aus einem [Web-App-Tutorial](sql-api-dotnet-application.md) bereitgestellt.

Resource Manager-Vorlagen sind relativ flexibel und ermöglichen es Ihnen, komplexe Bereitstellungen in jedem beliebigen Dienst in Azure zu erstellen. Dies umfasst erweiterte Aufgaben, wie z. B. das Bereitstellen von Anwendungen aus GitHub und das Einfügen von Verbindungsinformationen in die Azure App Service-Anwendungseinstellungen im Azure-Portal. In diesem Tutorial erfahren Sie, wie Sie mit einer einzigen Resource Manager-Vorlage Folgendes umsetzen können.

* Bereitstellen eines Azure Cosmos-Kontos
* Bereitstellen eines Azure App Service-Hostingplans
* Bereitstellen von Azure App Service
* Einfügen des Endpunkts und der Schlüssel aus dem Azure Cosmos-Konto in die App Service-Anwendungseinstellungen im Azure-Portal
* Bereitstellen einer Webanwendung aus einem GitHub-Repository für App Service

Die resultierende Bereitstellung verfügt über eine voll funktionsfähige Webanwendung, die eine Verbindung mit Azure Cosmos DB herstellen kann, ohne dass die Endpunkt-URL von Azure Cosmos DB oder die Authentifizierungsschlüssel aus dem Azure-Portal ausgeschnitten und eingefügt werden müssen.

## <a name="prerequisites"></a>Voraussetzungen

> [!TIP]
> Zwar wird für dieses Tutorial keine Erfahrung im Umgang mit Azure Resource Manager-Vorlagen oder JSON vorausgesetzt, wenn Sie jedoch die hierin verwendeten Vorlagen oder Bereitstellungsoptionen ändern möchten, sind Kenntnisse in jedem dieser Bereiche erforderlich.

## <a name="step-1-deploy-the-template"></a>Schritt 1: Bereitstellen der Vorlage

Wählen Sie zunächst unten die Schaltfläche **Bereitstellung in Azure** aus, um das Azure-Portal zum Erstellen einer benutzerdefinierten Bereitstellung zu öffnen. Sie können die Azure Resource Manager-Vorlage auch im [Katalog mit Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp) anzeigen.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Bereitstellen in Azure":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-cosmosdb-webapp%2Fazuredeploy.json)

Wählen Sie im Azure-Portal das Abonnement für die Bereitstellung aus, und wählen Sie eine neue Ressourcengruppe aus, oder erstellen Sie sie. Geben Sie anschließend die folgenden Werte ein.

:::image type="content" source="./media/create-website/template-deployment.png" alt-text="Screenshot der Benutzeroberfläche für die Vorlagenbereitstellung":::

* **Region** – dies ist für Resource Manager erforderlich. Geben Sie dieselbe Region ein, die vom Standortparameter verwendet wird, wo sich die Ressourcen befinden.
* **Anwendungsname** – dieser Name wird von allen Ressourcen für diese Bereitstellung verwendet. Wählen Sie einen eindeutigen Namen aus, um Konflikte mit vorhandenen Azure Cosmos DB- und App Service-Konten zu vermeiden.
* **Standort** – die Region, an der die Ressourcen bereitgestellt werden
* **App Service Plan Tier** (App Service-Plantarif) – die Preisstufe des App Service-Plans
* **App Service Plan Instances** (App Service-Planinstanzen) – Anzahl von Workern im App Service-Plan.
* **Repository-URL** – das Repository für die Webanwendung auf GitHub
* **Verzweigung** – die Verzweigung für das GitHub-Repository
* **Datenbankname** – der Name der Azure Cosmos-Datenbank
* **Containername** – der Name des Azure Cosmos-Containers

Wenn Sie alle Werte eingegeben haben, klicken Sie auf **Erstellen** , um mit der Bereitstellung zu beginnen. Dieser Schritt sollte zwischen 5 und 10 Minuten dauern.

> [!TIP]
> Die Vorlage überprüft nicht, ob die in der Vorlage eingegebenen Namen des Azure App Service und des Azure Cosmos-Kontos gültig und verfügbar sind. Sie sollten unbedingt die Verfügbarkeit der gewünschten Namen überprüfen, bevor Sie die Bereitstellung ausführen.


## <a name="step-2-explore-the-resources"></a>Schritt 2: Erkunden der Ressourcen

### <a name="view-the-deployed-resources"></a>Anzeigen der bereitgestellten Ressourcen

Nachdem die Vorlage die Ressourcen bereitgestellt hat, können Sie sie in Ihrer Ressourcengruppe anzeigen.

:::image type="content" source="./media/create-website/resource-group.png" alt-text="Ressourcengruppe":::

### <a name="view-cosmos-db-endpoint-and-keys"></a>Anzeigen des Cosmos DB-Endpunkts und der Schlüssel

Öffnen Sie als Nächstes das Azure Cosmos-Konto im Portal. Der folgende Screenshot zeigt den Endpunkt und die Schlüssel für ein Azure Cosmos-Konto an.

:::image type="content" source="./media/create-website/cosmos-keys.png" alt-text="Cosmos-Schlüssel":::

### <a name="view-the-azure-cosmos-db-keys-in-application-settings"></a>Anzeigen der Azure Cosmos DB-Schlüssel in den Anwendungseinstellungen

Navigieren Sie als Nächstes in der Ressourcengruppe zu Azure App Service. Klicken Sie auf die Registerkarte „Konfiguration“, um die Anwendungseinstellungen für App Service anzuzeigen. Die Anwendungseinstellungen enthalten das Cosmos DB-Konto und die Primärschlüsselwerte, die zum Herstellen einer Verbindung mit Cosmos DB erforderlich sind, sowie die Datenbank- und Containernamen, die von der Vorlagenbereitstellung übermittelt wurden.

:::image type="content" source="./media/create-website/application-settings.png" alt-text="Anwendungseinstellungen":::

### <a name="view-web-app-in-deployment-center"></a>Anzeigen der Web-App im Bereitstellungscenter

Navigieren Sie als Nächstes zum Bereitstellungscenter für App Service. Hier werden Repositorypunkte zum GitHub-Repository angezeigt, das an die Vorlage übermittelt wurde. Außerdem wird als Status „Success (Active)“ (Erfolgreich (Aktiv)) angezeigt, was bedeutet, dass die Anwendung erfolgreich bereitgestellt und gestartet wurde.

:::image type="content" source="./media/create-website/deployment-center.png" alt-text="Bereitstellungscenter":::

### <a name="run-the-web-application"></a>Ausführen der Webanwendung

Klicken Sie oben im Bereitstellungscenter auf **Durchsuchen** , um die Webanwendung zu öffnen. Die Webanwendung wird auf dem Startbildschirm geöffnet. Klicken Sie auf **Neu erstellen** , geben Sie Daten in die Felder ein, und klicken Sie auf „Speichern“. Auf dem resultierenden Bildschirm werden die in Cosmos DB gespeicherten Daten angezeigt.

:::image type="content" source="./media/create-website/app-home-screen.png" alt-text="Startbildschirm":::

## <a name="step-3-how-does-it-work"></a>Schritt 3: Funktionsweise

Hierfür sind drei Elemente erforderlich.

### <a name="reading-app-settings-at-runtime"></a>Lesen der App-Einstellungen zur Runtime

Als Erstes muss die Anwendung den Cosmos DB-Endpunkt und den Schlüssel in der `Startup`-Klasse in der ASP.NET MVC-Webanwendung anfordern. Das [Cosmos DB To Do-Beispiel](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app) kann lokal ausgeführt werden, wo Sie die Verbindungsinformationen in „appsettings.json“ eingeben können. Bei der Bereitstellung wird diese Datei jedoch mit der App bereitgestellt. Wenn die in Rot hervorgehobenen Zeilen nicht über „appsettings.json“ auf die Einstellungen zugreifen können, versuchen sie es von den Anwendungseinstellungen in Azure App Service.

:::image type="content" source="./media/create-website/startup.png" alt-text="Screenshot einer Methode mit mehreren rot markierten Zeichenfolgenvariablen, einschließlich „databaseName“, „containerName“, „account“ und „key“":::

### <a name="using-special-azure-resource-management-functions"></a>Verwenden spezieller Funktionen der Azure-Ressourcenverwaltung

Damit diese Werte bei der Bereitstellung für die Anwendung verfügbar sind, kann die Azure Resource Manager-Vorlage diese Werte vom Cosmos DB-Konto mithilfe spezieller Azure-Ressourcenverwaltungsfunktionen abfragen. Dazu zählen [reference](../azure-resource-manager/templates/template-functions-resource.md#reference) und [listKeys](../azure-resource-manager/templates/template-functions-resource.md#listkeys), die die Werte des Cosmos DB-Kontos erfassen und in die Werte der Anwendungseinstellungen mit Schlüsselnamen einfügen, die mit den Werten übereinstimmen, die in der Anwendung oben im Format „{section:key}“ verwendet werden. Beispiel: `CosmosDb:Account`.

:::image type="content" source="./media/create-website/template-keys.png" alt-text="Vorlagenschlüssel":::

### <a name="deploying-web-apps-from-github"></a>Bereitstellen von Web-Apps über GitHub

Schließlich müssen wir die Webanwendung aus GitHub in App Service bereitstellen. Dies geschieht mithilfe des folgenden JSON-Codes. Zwei Punkte, auf die Sie dabei achten müssen, sind der Typ und der Name der Ressource. Die Eigenschaftswerte `"type": "sourcecontrols"` und `"name": "web"` sind hartcodiert und dürfen nicht geändert werden.

:::image type="content" source="./media/create-website/deploy-from-github.png" alt-text="Bereitstellen über GitHub":::

## <a name="next-steps"></a>Nächste Schritte

Glückwunsch! Sie haben Azure Cosmos DB, Azure App Service und eine Beispielwebanwendung bereitgestellt, die automatisch über die Verbindungsinformationen verfügt, die zum Herstellen einer Verbindung mit Cosmos DB erforderlich sind, und zwar in einem einzigen Vorgang und ohne dass es notwendig war, vertrauliche Informationen auszuschneiden und einzufügen. Wenn Sie diese Vorlage als Ausgangspunkt verwenden, können Sie sie so anpassen, dass Sie Ihre eigenen Webanwendungen auf die gleiche Weise bereitstellen.

* Die Azure Resource Manager-Vorlage für dieses Beispiel finden Sie im [Katalog der Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates/tree/master/101-cosmosdb-webapp).
* Den Quellcode für die Beispiel-App finden Sie unter [Cosmos DB To Do-App auf GitHub](https://github.com/Azure-Samples/cosmos-dotnet-core-todo-app).
