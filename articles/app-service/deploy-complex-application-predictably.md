---
title: Vorhersagbares Bereitstellen von Microservices – Azure App Service
description: Erfahren Sie, wie Sie eine aus Microservices bestehende Anwendung in Azure App Service als eine Einheit und in vorhersagbarer Weise mithilfe von JSON-Ressourcengruppenvorlagen und PowerShell-Skripts bereitstellen.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: bb51e565-e462-4c60-929a-2ff90121f41d
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: e6d18222e15f62f12592362827b6dbc4a3d7dfbc
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60766926"
---
# <a name="provision-and-deploy-microservices-predictably-in-azure"></a>Vorhersagbares Bereitstellen von Microservices in Azure
Dieses Tutorial zeigt, wie eine aus [Microservices](https://en.wikipedia.org/wiki/Microservices) bestehende Anwendung in [Azure App Service](https://azure.microsoft.com/services/app-service/) als eine Einheit und in vorhersagbarer Weise mithilfe von JSON-Ressourcengruppenvorlagen und PowerShell-Skripts bereitgestellt wird. 

Beim Bereitstellen umfassender Anwendungen, die aus stark entkoppelten Microservices bestehen, sind Wiederholbarkeit und Vorhersagbarkeit von entscheidender Bedeutung für den Erfolg. Mit [Azure App Service](https://azure.microsoft.com/services/app-service/) können Sie Microservices erstellen, die Web-Apps, mobile Back-Ends und API-Apps umfassen. Mit dem [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) können Sie alle Microservices zusammen mit Ressourcenabhängigkeiten, z.B. Datenbank- und Quellcodeverwaltungseinstellungen, als Einheit verwalten. Jetzt können Sie auch eine solche Anwendung mit JSON-Vorlagen und einfachen PowerShell-Skripts bereitstellen. 

## <a name="what-you-will-do"></a>Aufgaben
In diesem Lernprogramm werden Sie eine Anwendung bereitstellen, die Folgendes enthält:

* Zwei App Service-Apps (d.h. zwei Microservices)
* Eine Back-End-SQL-Datenbank
* App-Einstellungen, Verbindungszeichenfolgen und Quellcodeverwaltung
* Application Insights, Warnungen und Einstellungen für die automatische Skalierung

## <a name="tools-you-will-use"></a>Verwendete Tools
In diesem Lernprogramm verwenden Sie die folgenden Tools. Da dies keine umfassende Erläuterung der Tools ist, wird schwerpunktmäßig das komplette Szenario dargestellt. Sie erhalten eine kurze Einführung zu allen Tools und erfahren, wo Sie weiterführende Informationen finden. 

### <a name="azure-resource-manager-templates-json"></a>Azure-Ressourcen-Manager-Vorlagen (JSON)
Jedes Mal, wenn Sie beispielsweise eine App in Azure App Service erstellen, verwendet Azure Resource Manager eine JSON-Vorlage zum Erstellen der gesamten Ressourcengruppe mit den Komponentenressourcen. Eine komplexe Vorlage aus dem [Azure Marketplace](/azure/marketplace) kann die Datenbank, Speicherkonten, den App Service-Plan, die App selbst, Warnregeln, App-Einstellungen, Einstellungen für die automatische Skalierung und vieles mehr enthalten. All diese Vorlagen stehen Ihnen über PowerShell zur Verfügung. Weitere Informationen zu den Azure-Ressourcen-Manager-Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../azure-resource-manager/resource-group-authoring-templates.md)

### <a name="azure-sdk-26-for-visual-studio"></a>Azure SDK 2.6 für Visual Studio
Das neueste SDK enthält Verbesserungen für die Unterstützung von Ressourcen-Manager-Vorlagen im JSON-Editor. Sie können damit schnell eine Ressourcengruppenvorlage ganz neu erstellen oder eine vorhandene JSON-Vorlage (z. B. eine heruntergeladene Katalogvorlage) zur Bearbeitung öffnen, die Parameterdatei füllen und sogar die Ressourcengruppe direkt aus einer Azure-Ressourcengruppenlösung bereitstellen.

Weitere Informationen finden Sie im [Azure SDK 2.6 für Visual Studio](https://azure.microsoft.com/blog/2015/04/29/announcing-the-azure-sdk-2-6-for-net/).

### <a name="azure-powershell-080-or-later"></a>Azure PowerShell 0.8.0 oder höher
Ab Version 0.8.0 enthält die Azure PowerShell-Installation zusätzlich zum Azure-Modul das Azure-Ressourcen-Manager-Modul. Mit diesem neuen Modul können Sie Skripts für der Bereitstellung von Ressourcengruppen verwenden.

Weitere Informationen finden Sie im [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md)

### <a name="azure-resource-explorer"></a>Azure-Ressourcen-Explorer
Mit diesem [Vorschautool](https://resources.azure.com) können Sie die JSON-Definitionen aller Ressourcengruppen in Ihrem Abonnement und der einzelnen Ressourcen untersuchen. Im Tool können Sie die JSON-Definitionen einer Ressource bearbeiten, eine vollständige Hierarchie von Ressourcen löschen und neue Ressourcen erstellen.  Die in diesem Tool verfügbaren Informationen sind sehr hilfreich für die Vorlagenerstellung, da sie zeigen, welche Eigenschaften Sie für einen bestimmten Typ von Ressource festlegen müssen, die richtigen Werte vorgeben usw. Sie können sogar die Ressourcengruppe im [Azure-Portal](https://portal.azure.com/) erstellen und dann ihre JSON-Definitionen im Explorer-Tool untersuchen, um aus der Ressourcengruppe eine Vorlage zu machen.

### <a name="deploy-to-azure-button"></a>Schaltfläche zum Bereitstellen in Azure
Wenn Sie GitHub für die Quellcodeverwaltung verwenden, können Sie eine [Schaltfläche zum Bereitstellen in Azure (Deploy to Azure)](https://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/) in README.MD aufnehmen, wodurch eine sofort einsetzbare Bereitstellungsoberfläche in Azure aktiviert wird. Sie können dies für jede einfache App durchführen. Es ist aber auch eine Erweiterung möglich, um die Bereitstellung einer vollständigen Ressourcengruppe zu ermöglichen, indem Sie eine azuredeploy.json-Datei in den Repositorystamm einfügen. Diese JSON-Datei, die die Ressourcengruppenvorlage enthält, wird von der Schaltfläche zum Bereitstellen in Azure für die Erstellung der Ressourcengruppe verwendet. Ein Beispiel ist [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp), das in diesem Tutorial verwendet wird.

## <a name="get-the-sample-resource-group-template"></a>Abrufen des Beispiels einer Ressourcengruppenvorlage
Lassen Sie uns jetzt beginnen.

1. Navigieren Sie zum App Service-Beispiel [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) .
2. Klicken Sie in "readme.md" auf **Deploy to Azure**.
3. Die Website [deploy-to-azure](https://deploy.azure.com) wird aufgerufen, und Sie werden zur Eingabe von Bereitstellungsparametern aufgefordert. Beachten Sie, dass die meisten Felder mit dem Namen des Repositorys und einigen zufälligen Zeichenfolgen gefüllt sind. Sie können bei Bedarf alle Felder ändern, aber Sie müssen nur den SQL Server-Administratoranmeldenamen und das Kennwort eingeben. Klicken Sie dann auf **Next**.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-1-deploybuttonui.png)
4. Klicken Sie als Nächstes auf **Deploy** , um den Bereitstellungsprozess zu starten. Sobald der Prozess abgeschlossen wurde, klicken Sie auf den Link „http://todoapp*XXXX*.azurewebsites.net“, um die bereitgestellte Anwendung anzuzeigen. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-2-deployprogress.png)
   
   Beim ersten Aufruf ist die Benutzeroberfläche etwas langsam, da die Apps gerade gestartet werden, aber Sie können sich überzeugen, dass es eine voll funktionsfähige Anwendung ist.
5. Klicken Sie auf der Bereitstellungsseite auf den Link **Manage** , um die neue Anwendung im Azure-Vorschauportal anzuzeigen.
6. Klicken Sie in der Dropdownliste **Essentials** auf den Link der Ressourcengruppe. Beachten Sie, dass die App bereits unter **External Project**mit dem GitHub-Repository verbunden ist. 
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-3-portalresourcegroup.png)
7. Beachten Sie auf dem Blatt der Ressourcengruppe, dass bereits zwei Apps und eine SQL-Datenbank in der Ressourcengruppe vorhanden sind.
   
   ![](./media/app-service-deploy-complex-application-predictably/gettemplate-4-portalresourcegroupclicked.png)

Alles, was Sie gerade in wenigen Minuten gesehen haben, ist eine vollständig bereitgestellte komplexe Anwendung mit zwei Microservices, bei der alle Komponenten, Abhängigkeiten, Einstellungen, die Datenbank und die kontinuierliche Veröffentlichung von einer automatisierten Orchestrierung im Azure-Ressourcen-Manager eingerichtet wurden. All dies wurde mit zwei Dingen erreicht:

* Der Schaltfläche zum Bereitstellen in Azure
* azuredeploy.json im Stammverzeichnis des Repositorys

Sie können diese Anwendung Dutzende, Hunderte oder Tausende Male bereitstellen und erhalten jedes Mal genau die gleiche Konfiguration. Durch die Wiederholbarkeit und die Vorhersagbarkeit dieses Ansatzes können Sie hochgradig skalierbare Anwendungen einfach und zuverlässig bereitstellen.

## <a name="examine-or-edit-azuredeployjson"></a>Untersuchen (oder Bearbeiten) von AZUREDEPLOY.JSON
Jetzt sehen wir uns an, wie das GitHub-Repository eingerichtet wurde. Verwenden Sie den JSON-Editor im Azure .NET SDK. Wenn Sie das [Azure .NET SDK 2.6](https://azure.microsoft.com/downloads/) noch nicht installiert haben, holen Sie dies jetzt nach.

1. Klonen Sie das [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) -Repository mit Ihrem bevorzugten Git-Tool. Im Screenshot unten wird Team Explorer in Visual Studio 2013 verwendet.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-1-vsclone.png)
2. Öffnen Sie aus dem Stammverzeichnis des Repositorys "azuredeploy.json" in Visual Studio. Wenn die JSON-Gliederungsansicht nicht angezeigt wird, müssen Sie das Azure .NET SDK installieren.
   
   ![](./media/app-service-deploy-complex-application-predictably/examinejson-2-vsjsoneditor.png)

Hier werden nicht alle Details des JSON-Formats beschrieben, aber der Abschnitt [Weitere Ressourcen](#resources) enthält Links zum Kennenlernen der Sprache für Ressourcengruppenvorlagen. Hier werden nur die interessanten Funktionen gezeigt, mit denen Sie beginnen können, eine eigene benutzerdefinierte Vorlage für die App-Bereitstellung zu erstellen.

### <a name="parameters"></a>Parameter
Sehen Sie sich den Parameterabschnitt an. Dort erkennen Sie, dass die meisten dieser Parameter deshalb vorhanden sind, weil die Schaltfläche **Deploy to Azure** Sie zur Eingabe aufgefordert hat. Die Website hinter der Schaltfläche **Deploy to Azure** füllt die Eingabebenutzeroberfläche mit den Parametern, die in „azuredeploy.json“ definiert wurden. Diese Parameter werden in den Ressourcendefinitionen verwendet, z. B. als Ressourcennamen, Eigenschaftswerte usw.

### <a name="resources"></a>Ressourcen
Im Ressourcenknoten sehen Sie, dass vier Ressourcen der obersten Ebene definiert wurden, einschließlich einer SQL Server-Instanz, eines App Service-Plans und zweier Apps. 

#### <a name="app-service-plan"></a>App Service-Plan
Beginnen wir mit einer einfachen Ressource auf Stammebene in JSON. Klicken Sie in der JSON-Gliederung auf den App Service-Plan mit dem Namen **[hostingPlanName]** , um den entsprechenden JSON-Code zu markieren. 

![](./media/app-service-deploy-complex-application-predictably/examinejson-3-appserviceplan.png)

Beachten Sie, dass das `type`-Element die Zeichenfolge für einen App Service-Plan angibt (wurde früher als Serverfarm bezeichnet). Andere Elemente und Eigenschaften werden mit den in der JSON-Datei definierten Parametern ausgefüllt, und diese Ressource weist keine geschachtelten Ressourcen auf.

> [!NOTE]
> Beachten Sie außerdem, dass der Wert von `apiVersion` Azure mitteilt, mit welcher Version der REST-API die JSON-Ressourcendefinition verwendet wird. Dies kann beeinflussen, wie die Ressource innerhalb von `{}` formatiert werden muss. 
> 
> 

#### <a name="sql-server"></a>SQL Server
Klicken Sie als Nächstes in der JSON-Gliederung auf die SQL Server-Ressource mit dem Namen **SQLServer** .

![](./media/app-service-deploy-complex-application-predictably/examinejson-4-sqlserver.png)

Beachten Sie Folgendes im Zusammenhang mit dem hervorgehobenen JSON-Code:

* Durch die Verwendung von Parametern wird sichergestellt, dass die erstellten Ressourcen so benannt und konfiguriert werden, dass sie mit anderen konsistent sind.
* Die SQLServer-Ressource hat zwei geschachtelte Ressourcen, die jeweils einen anderen Wert für `type` aufweisen.
* Die geschachtelten Ressourcen in `“resources”: […]`, in denen die Datenbank und die Firewallregeln definiert werden, haben ein `dependsOn`-Element, das die Ressourcen-ID der SQLServer-Ressource auf Stammebene angibt. Dadurch wird der Azure-Ressourcen-Manager angewiesen, dass vor dem Erstellen dieser Ressource, die andere Ressource bereits vorhanden sein muss; und wenn diese andere Ressource in der Vorlage definiert ist, muss sie zuerst erstellt werden.
  
  > [!NOTE]
  > Ausführliche Informationen zur Verwendung der `resourceId()`-Funktion finden Sie unter [Azure Resource Manager template functions](../azure-resource-manager/resource-group-template-functions-resource.md#resourceid) (Funktionen von Azure Resource Manager-Vorlagen).
  > 
  > 
* Die Wirkung des `dependsOn` -Elements ist, dass der Azure-Ressourcen-Manager weiß, welche Ressourcen parallel erstellt werden können und welche Ressourcen sequenziell erstellt werden müssen. 

#### <a name="app-service-app"></a>App Service-App
Betrachten wir nun die tatsächlichen Apps selbst, die komplizierter sind. Klicken Sie in der JSON-Gliederung auf die [variables('apiSiteName')]-App, um den zugehörigen JSON-Code zu markieren. Sie werden feststellen, dass die Dinge viel interessanter werden. Daher werden die Features einzeln erläutert:

##### <a name="root-resource"></a>Stammressource
Die App benötigt zwei verschiedene Ressourcen. Dies bedeutet, dass Azure Resource Manager die App erst erstellt, nachdem sowohl der App Service-Plan als auch die SQL Server-Instanz erstellt wurden.

![](./media/app-service-deploy-complex-application-predictably/examinejson-5-webapproot.png)

##### <a name="app-settings"></a>App-Einstellungen
Die App-Einstellungen werden auch als geschachtelte Ressource definiert.

![](./media/app-service-deploy-complex-application-predictably/examinejson-6-webappsettings.png)

Im `properties`-Element für `config/appsettings` sind zwei App-Einstellungen im Format `"<name>" : "<value>"` vorhanden.

* `PROJECT` ist eine [KUDU-Einstellung](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) , die der Azure-Bereitstellung mitteilt, welches Projekt in einer Visual Studio-Projektmappe mit mehreren Projekten verwendet werden soll. Später erfahren Sie, wie die Quellcodeverwaltung konfiguriert wird, aber da sich der ToDoApp-Code in einer Visual Studio-Projektmappe mit mehreren Projekten befindet, ist diese Einstellung erforderlich.
* `clientUrl` ist einfach eine App-Einstellung, die vom Anwendungscode verwendet wird.

##### <a name="connection-strings"></a>Verbindungszeichenfolgen
Die Verbindungszeichenfolgen werden auch als geschachtelte Ressource definiert.

![](./media/app-service-deploy-complex-application-predictably/examinejson-7-webappconnstr.png)

Im `properties`-Element für `config/connectionstrings` ist jede Verbindungszeichenfolge auch als Name-Wert-Paar im Format `"<name>" : {"value": "…", "type": "…"}` definiert. Mögliche Werte für das `type`-Element sind `MySql`, `SQLServer`, `SQLAzure` und `Custom`.

> [!TIP]
> Eine endgültige Liste der Typen von Verbindungszeichenfolgen erhalten Sie, indem Sie den folgenden Befehl in Azure PowerShell ausführen: \[Enum]::GetNames("Microsoft.WindowsAzure.Commands.Utilities.Websites.Services.WebEntities.DatabaseType")
> 
> 

##### <a name="source-control"></a>Quellcodeverwaltung
Die Einstellungen für die Quellcodeverwaltung werden auch als geschachtelte Ressource definiert. Der Azure-Ressourcen-Manager verwendet diese Ressource, um die kontinuierliche Veröffentlichung zu konfigurieren (weitere Informationen finden Sie unter den Hinweisen zu `IsManualIntegration` weiter unten), und auch, um die automatische Bereitstellung von Anwendungscode während der Verarbeitung der JSON-Datei zu starten.

![](./media/app-service-deploy-complex-application-predictably/examinejson-8-webappsourcecontrol.png)

`RepoUrl` und `branch` sollten ziemlich selbsterklärend sein und auf das Git-Repository und den Namen der Verzweigung für die Veröffentlichung verweisen. Auch sie werden durch Eingabeparameter definiert. 

Beachten Sie im `dependsOn`-Element, dass `sourcecontrols/web` nicht nur von der App-Ressource selbst, sondern auch von `config/appsettings` und `config/connectionstrings` abhängig ist. Der Grund hierfür ist, dass, sobald `sourcecontrols/web` konfiguriert ist, der Azure-Bereitstellungsprozess automatisch versucht, den Anwendungscode bereitzustellen, zu erstellen und zu starten. Daher können Sie durch das Einfügen dieser Abhängigkeit sicherstellen, dass die Anwendung über Zugriff auf die erforderlichen App-Einstellungen und Verbindungszeichenfolgen verfügt, bevor der Anwendungscode ausgeführt wird. 

> [!NOTE]
> Beachten Sie auch, dass `IsManualIntegration` auf `true` festgelegt ist. Diese Eigenschaft ist in diesem Tutorial erforderlich, da Sie das GitHub-Repository nicht besitzen und daher Azure nicht die Berechtigung erteilen können, die kontinuierliche Veröffentlichung von [ToDoApp](https://github.com/azure-appservice-samples/ToDoApp) zu konfigurieren (d.h. automatische Repositoryupdates in Azure mithilfe von Push zu übertragen). Sie können den Standardwert `false` für das angegebene Repository nur verwenden, wenn Sie die GitHub-Anmeldeinformationen für den Besitzer zuvor im [Azure-Portal](https://portal.azure.com/) konfiguriert haben. Anders ausgedrückt: Wenn Sie zuvor mit Ihren Benutzeranmeldeinformationen die Quellcodeverwaltung für GitHub oder BitBucket für eine App im [Azure-Portal](https://portal.azure.com/) eingerichtet haben, werden die Anmeldeinformationen in Azure gespeichert und verwendet, wenn Sie später eine App über GitHub oder BitBucket bereitstellen. Wenn dies jedoch noch nicht geschehen ist, tritt bei der Bereitstellung der JSON-Vorlage ein Fehler auf, wenn Azure Resource Manager versucht, die App-Einstellungen für die Quellcodeverwaltung zu konfigurieren, da mit den Anmeldeinformationen des Repositorybesitzers eine Anmeldung bei GitHub oder BitBucket nicht möglich ist.
> 
> 

## <a name="compare-the-json-template-with-deployed-resource-group"></a>Vergleichen der JSON-Vorlage mit der bereitgestellten Ressourcengruppe
Hier können Sie alle Blätter der App im [Azure-Portal](https://portal.azure.com/) durchgehen, es gibt aber ein weiteres Tool, das möglicherweise noch nützlicher ist. Wechseln Sie zum Vorschautool [Azure-Ressourcen-Explorer](https://resources.azure.com) , in dem Sie eine JSON-Darstellung aller Ressourcengruppen in Ihren Abonnements erhalten, so wie sie tatsächlich im Azure-Back-End vorhanden sind. Sie können auch sehen, dass die JSON-Hierarchie der Ressourcengruppe in Azure der Hierarchie in der Vorlagendatei entspricht, die für die Erstellung verwendet wurde.

Beispiel: Wenn das Tool [Azure-Ressourcen-Explorer](https://resources.azure.com) aufgerufen und die Knoten im Explorer erweitern werden, werden die Ressourcengruppe und die Ressourcen auf Stammebene angezeigt, die unter den jeweiligen Ressourcentypen gesammelt werden.

![](./media/app-service-deploy-complex-application-predictably/ARM-1-treeview.png)

Wenn Sie einen Drilldown zu einer App ausführen, sollten die Konfigurationsdetails der App angezeigt werden, wie im folgenden Screenshot veranschaulicht:

![](./media/app-service-deploy-complex-application-predictably/ARM-2-jsonview.png)

Die geschachtelten Ressourcen sollten wieder eine Hierarchie aufweisen, die der in der JSON-Vorlagendatei sehr ähnlich ist, und die App-Einstellungen, Verbindungszeichenfolgen usw. sollten richtig im JSON-Bereich wiedergegeben werden. Wenn hier Einstellungen fehlen, ist dies möglicherweise ein Hinweis auf ein Problem mit der JSON-Datei, der bei der Problembehandlung in der JSON-Vorlagendatei hilfreich sein kann.

## <a name="deploy-the-resource-group-template-yourself"></a>Bereitstellen einer eigenen Ressourcengruppenvorlage
Die Schaltfläche **Deploy to Azure** ist großartig, aber sie können damit die Ressourcengruppenvorlage nur in "azuredeploy.json" bereitstellen, wenn Sie "azuredeploy.json" bereits in GitHub abgelegt haben. Das Azure .NET SDK stellt außerdem Tools zur Verfügung, um JSON-Vorlagendateien direkt über Ihren lokalen Computer bereitzustellen. Führen Sie dazu die folgenden Schritte aus:

1. Klicken Sie in Visual Studio auf **Datei** > **Neu** > **Projekt**.
2. Klicken Sie auf **Visual C#**  > **Cloud** > **Azure-Ressourcengruppe** und dann auf **OK**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-1-vsproject.png)
3. Wählen Sie unter **Azure-Vorlage auswählen** die Option **Leere Vorlage** aus, und klicken Sie auf **OK**.
4. Ziehen Sie "azuredeploy.json" in den Ordner **Vorlagen** des neuen Projekts.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-2-copyjson.png)
5. Öffnen Sie im Projektmappen-Explorer die kopierte Datei "azuredeploy.json".
6. Nur zur Veranschaulichung fügen wir einige Application Insight-Standardressourcen in der JSON-Datei hinzu, indem wir auf **Ressource hinzufügen**klicken. Wenn Sie nur an der Bereitstellung der JSON-Datei interessiert sind, fahren Sie mit den Schritten zur Bereitstellung fort.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-3-newresource.png)
7. Wählen Sie **Application Insights für Web-Apps** aus, stellen Sie dann sicher, dass ein vorhandener App Service-Plan und eine App ausgewählt sind, und klicken Sie anschließend auf **Hinzufügen**.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-4-newappinsight.png)
   
   Jetzt sehen Sie verschiedene neue Ressourcen, die je nach Ressource und Funktion Abhängigkeiten vom App Service-Plan oder der App aufweisen. Diese Ressourcen werden durch ihre vorhandene Definition nicht aktiviert, und das werden Sie ändern.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-5-appinsightresources.png)
8. Klicken Sie in der JSON-Gliederung auf **appInsights AutoScale** , um den JSON-Code zu markieren. Dies ist die Skalierungseinstellung für Ihren App Service-Plan.
9. Suchen Sie im markierten JSON-Code nach den Eigenschaften `location` und `enabled`, und legen Sie sie wie unten dargestellt fest.
   
   ![](./media/app-service-deploy-complex-application-predictably/deploy-6-autoscalesettings.png)
10. Klicken Sie in der JSON-Gliederung auf **CPUHigh appInsights** , um den JSON-Code zu markieren. Dies ist eine Warnung.
11. Suchen Sie nach den Eigenschaften `location` und `isEnabled`, und legen Sie sie wie unten dargestellt fest. Gehen Sie auf diese Weise für die anderen drei Warnungen vor (lila Glühbirnen).
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-7-alerts.png)
12. Jetzt können Sie mit der Bereitstellung beginnen. Klicken Sie mit der rechten Maustaste auf das Projekt, und wählen Sie **Deploy** > **Neue Bereitstellung**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-8-newdeployment.png)
13. Melden Sie sich bei Ihrem Azure-Konto an, wenn Sie dies nicht bereits getan haben.
14. Wählen Sie eine vorhandene Ressourcengruppe aus Ihrem Abonnement aus, oder erstellen Sie eine neue Ressourcengruppe. Wählen Sie **azuredeploy.json**, und klicken Sie anschließend auf **Parameter bearbeiten**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-9-deployconfig.png)
    
    Jetzt haben Sie die Möglichkeit, alle in der Vorlagendatei definierten Parameter in einer übersichtlichen Tabelle zu bearbeiten. Parameter, die Standardwerte definieren, weisen bereits die Standardwerte auf, und Parameter, die eine Liste zulässiger Werte definieren, werden als Dropdownlisten angezeigt.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-10-parametereditor.png)
15. Geben Sie alle leeren Parameter an, und verwenden Sie die [GitHub-Repositoryadresse für ToDoApp](https://github.com/azure-appservice-samples/ToDoApp.git) in **repoUrl**. Klicken Sie dann auf **Speichern**.
    
    ![](./media/app-service-deploy-complex-application-predictably/deploy-11-parametereditorfilled.png)
    
    > [!NOTE]
    > Die automatische Skalierung ist ein Feature des Tarifs **Standard** oder höher, und Warnungen auf Planebene sind Features des Tarifs **Basic** oder höher. Sie müssen den Parameter **sku** auf **Standard** oder **Premium** festlegen, damit alle neuen App Insights-Ressourcen angezeigt werden.
    > 
    > 
16. Klicken Sie auf **Bereitstellen**. Bei Auswahl von **Kennwörter speichern** wird das Kennwort in der Parameterdatei als **Klartext** gespeichert. Andernfalls werden Sie während des Bereitstellungsprozesses zur Eingabe des Datenbankkennworts aufgefordert.

Fertig! Nun müssen Sie einfach zum [Azure-Portal](https://portal.azure.com/) und zum Tool [Azure-Ressourcen-Explorer](https://resources.azure.com) wechseln, um die neuen Warnungen und die Einstellungen zum automatischen Skalieren zu sehen, die Ihrer mit JSON bereitgestellten Anwendung hinzugefügt wurden.

Mit den Schritten in diesem Abschnitt haben Sie im Wesentlichen Folgendes erreicht:

1. Die Vorlagendatei vorbereitet
2. Eine Parameterdatei für die Vorlagendatei erstellt
3. Die Vorlagendatei mit der Parameterdatei bereitgestellt

Der letzte Schritt kann mühelos durch ein PowerShell-Cmdlet erledigt werden. Um zu sehen, was in Visual Studio bei der Bereitstellung der Anwendung ausgeführt wurde, öffnen Sie "Scripts\Deploy AzureResourceGroup.ps1". Es ist viel Code vorhanden, ich werde aber nur den relevanten Code markieren, den Sie benötigen, um die Vorlagendatei mit der Parameterdatei bereitzustellen.

![](./media/app-service-deploy-complex-application-predictably/deploy-12-powershellsnippet.png)

Das letzte Cmdlet, `New-AzureResourceGroup`, führt die Aktion aus. All dies sollte Ihnen zeigen, dass es mithilfe von Tools relativ einfach ist, die Cloudanwendung vorhersagbar bereitzustellen. Jedes Mal, wenn Sie das Cmdlet für die gleiche Vorlage mit der gleichen Parameterdatei ausführen, erhalten Sie das gleiche Ergebnis.

## <a name="summary"></a>Zusammenfassung
In DevOps sind Wiederholbarkeit und Vorhersagbarkeit der Schlüssel für eine erfolgreiche Bereitstellung komplexer aus Microservices bestehender Anwendungen. In diesem Tutorial haben Sie eine als zwei Microservices bestehende Anwendung in Azure als einzelne Ressourcengruppe mithilfe der Azure-Ressourcen-Manager-Vorlage bereitgestellt. Sie sollten jetzt damit beginnen können, Ihre Anwendung in Azure in eine Vorlage zu konvertieren und sie vorhersagbar in Azure bereitzustellen. 

<a name="resources"></a>

## <a name="more-resources"></a>Weitere Ressourcen
* [Azure Resource Manager-Vorlagensprache](../azure-resource-manager/resource-group-authoring-templates.md)
* [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../azure-resource-manager/resource-group-authoring-templates.md)
* [Vorlagenfunktionen im Azure-Ressourcen-Manager](../azure-resource-manager/resource-group-template-functions.md)
* [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](../azure-resource-manager/resource-group-template-deploy.md)
* [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](../azure-resource-manager/powershell-azure-resource-manager.md)
* [Troubleshooting Resource Group Deployments in Azure (Problembehandlung beim Bereitstellen von Ressourcengruppen in Azure)](../azure-resource-manager/resource-manager-common-deployment-errors.md)

## <a name="next-steps"></a>Nächste Schritte

Informationen zur JSON-Syntax und zu Eigenschaften für Ressourcentypen, die in diesem Artikel bereitgestellt werden, finden Sie unter:

* [Microsoft.Sql/servers](/azure/templates/microsoft.sql/servers)
* [Microsoft.Sql/servers/databases](/azure/templates/microsoft.sql/servers/databases)
* [Microsoft.Sql/servers/firewallRules](/azure/templates/microsoft.sql/servers/firewallrules)
* [Microsoft.Web/serverfarms](/azure/templates/microsoft.web/serverfarms)
* [Microsoft.Web/sites](/azure/templates/microsoft.web/sites)
* [Microsoft.Web/sites/slots](/azure/templates/microsoft.web/sites/slots)
* [Microsoft.Insights/autoscalesettings](/azure/templates/microsoft.insights/autoscalesettings)