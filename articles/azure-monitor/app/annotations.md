---
title: Versionsanmerkungen für Application Insights | Microsoft Docs
description: Fügen Sie den Diagrammen im Metrik-Explorer in Application Insights Bereitstellungs- oder Buildmarker hinzu.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 58f6603687838713fafbf4cd5cc3f100e22b7401
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993719"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anmerkungen zu Metrik-Diagrammen in Application Insights

Mit Anmerkungen wird angegeben, wo Sie einen neuen Build bereitgestellt haben, oder es wird auf andere wichtige Ereignisse hingewiesen. Dank der Anmerkungen sehen Sie auf einen Blick, ob Ihre Änderungen Auswirkungen auf die Leistung Ihrer Anwendung hatten. Sie können automatisch durch das Buildsystem von [Azure Pipelines](/azure/devops/pipelines/tasks/) erstellt werden. Es können auch Anmerkungen erstellt werden, die alle gewünschten Ereignisse markieren, indem diese aus PowerShell erstellt werden.

## <a name="release-annotations-with-azure-pipelines-build"></a>Releaseanmerkungen mit Azure Pipelines-Build

Releaseanmerkungen sind ein Feature des cloudbasierten Azure Pipelines-Diensts von Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Installieren der Erweiterung für Anmerkungen (einmalig)

Um Releaseanmerkungen erstellen zu können, müssen Sie eine der zahlreichen Azure DevOps-Erweiterungen installieren, die im Visual Studio Marketplace zur Verfügung stehen.

1. Melden Sie sich bei Ihrem [Azure DevOps](https://azure.microsoft.com/services/devops/)-Projekt an.
   
1. Wählen Sie im Visual Studio Marketplace auf der Seite [Release Annotations for Azure Application Insights](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) (Releaseanmerkungen für Azure Application Insights) Ihre Azure DevOps-Organisation und anschließend **Install** (Installieren) aus, um die Erweiterung Ihrer Azure DevOps-Organisation hinzuzufügen.
   
   ![Wählen Sie eine Azure DevOps-Organisation und anschließend „Install“ (Installieren) aus.](./media/annotations/1-install.png)
   
Die Erweiterung muss nur einmal für Ihre Azure DevOps-Organisation installiert werden. Nun können Sie Releaseanmerkungen für ein beliebiges Projekt in Ihrer Organisation konfigurieren.

### <a name="configure-release-annotations"></a>Konfigurieren von Versionsanmerkungen

Erstellen Sie für Ihre Azure Pipelines-Versionsvorlagen jeweils einen separaten API-Schlüssel.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und öffnen Sie die Application Insights-Ressource, die Ihre Anwendung überwacht. Sollten Sie über keine solche Ressource verfügen, [erstellen Sie eine neue Application Insights-Ressource](./app-insights-overview.md).
   
1. Öffnen Sie die Registerkarte **API-Zugriff**, und kopieren Sie die **Application Insights-ID**.
   
   ![Kopieren Sie die Anwendungs-ID unter „API-Zugriff“.](./media/annotations/2-app-id.png)

1. Öffnen oder erstellen Sie in einem separaten Browserfenster die Versionsvorlage, mit der Ihre Azure Pipelines-Bereitstellungen verwaltet werden.
   
1. Wählen Sie **Aufgabe hinzufügen** und anschließend im Menü die Aufgabe **Application Insights Release Annotation** (Application Insights-Releaseanmerkung) aus.
   
   ![Wählen Sie „Aufgabe hinzufügen“ und anschließend „Application Insights Release Annotation“ (Application Insights-Releaseanmerkung) aus.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > Die Aufgabe „Releaseanmerkung“ unterstützt zurzeit nur Windows-basierte Agents. Sie kann nicht unter Linux, macOS oder anderen Agent-Typen ausgeführt werden.
   
1. Fügen Sie unter **Anwendungs-ID** die Application Insights-ID ein, die Sie auf der Registerkarte **API-Zugriff** kopiert haben.
   
   ![Application Insights-ID einfügen](./media/annotations/4-paste-app-id.png)
   
1. Wählen Sie im Application Insights-Fenster **API-Zugriff** die Option **API-Schlüssel erstellen** aus. 
   
   ![Wählen Sie auf der Registerkarte „API-Zugriff“ die Option „API-Schlüssel erstellen“ aus.](./media/annotations/5-create-api-key.png)
   
1. Geben Sie im Fenster **API-Schlüssel erstellen** eine Beschreibung ein, wählen Sie **Anmerkungen schreiben** aus, und wählen Sie anschließend **Schlüssel generieren** aus. Kopieren Sie den neuen Schlüssel.
   
   ![Geben Sie im Fenster „API-Schlüssel erstellen“ eine Beschreibung ein, wählen Sie „Anmerkungen schreiben“ aus, und wählen Sie anschließend „Schlüssel generieren“ aus.](./media/annotations/6-create-api-key.png)
   
1. Wählen Sie im Fenster für die Versionsvorlage auf der Registerkarte **Variablen** die Option **Hinzufügen** aus, um eine Variablendefinition für den neuen API-Schlüssel zu erstellen.

1. Geben Sie unter **Name** den Namen `ApiKey` ein, und fügen Sie unter **Wert** den API-Schlüssel ein, den Sie auf der Registerkarte **API-Zugriff** kopiert haben.
   
   ![Wählen Sie auf der Azure DevOps-Registerkarte „Variablen“ die Option „Hinzufügen“ aus, nennen Sie die Variable „ApiKey“, und fügen Sie unter „Wert“ den API-Schlüssel ein.](./media/annotations/7-paste-api-key.png)
   
1. Wählen Sie im Hauptfenster der Versionsvorlage die Option **Speichern** aus, um die Vorlage zu speichern.


   > [!NOTE]
   > Einschränkungen für API-Schlüssel werden in der [Dokumentation zur REST-API-Ratenbegrenzungen](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits) beschrieben.

## <a name="view-annotations"></a>Anmerkungen anzeigen


   > [!NOTE]
   > Versionsanmerkungen sind im Bereich „Metriken“ von Application Insights derzeit nicht verfügbar.

Wenn Sie nun diese Versionsvorlage zum Bereitstellen einer neuen Version verwenden, wird jedes Mal eine Anmerkung an Application Insights gesendet. Die Anmerkungen können an den folgenden Orten angezeigt werden:

Bereich „Nutzung“, in dem Sie auch manuell Versionsanmerkungen erstellen können:

![Screenshot: Balkendiagramm mit der Anzahl von Benutzerbesuchen für eine bestimmte Anzahl von Stunden Versionsanmerkungen werden als grüne Häkchen oberhalb des Diagramms angezeigt und geben den Zeitpunkt einer Versionsveröffentlichung an.](./media/annotations/usage-pane.png)

Alle protokollbasierten Arbeitsmappenabfragen, bei denen bei der Visualisierung die Uhrzeit auf der X-Achse angezeigt wird.

![Screenshot: Arbeitsmappenbereich mit protokollbasierter Zeitreihenabfrage mit Anzeige von Anmerkungen](./media/annotations/workbooks-annotations.png)

Navigieren Sie zum Aktivieren der Anmerkungen in Ihrer Arbeitsmappe zu **Erweiterte Einstellungen**, und wählen Sie **Anmerkungen anzeigen** aus.

![Screenshot: Menü „Erweiterte Einstellungen“ mit Hervorhebung von „Anmerkungen anzeigen“ und einem Häkchen zum Aktivieren neben der Einstellung](./media/annotations/workbook-show-annotations.png)

Wählen Sie einen Anmerkungsmarker aus, um Details zur Version anzuzeigen, z. B. Anforderer, Quellcodeverwaltungsbranch, Releasepipeline und Umgebung.

## <a name="create-custom-annotations-from-powershell"></a>Erstellen von benutzerdefinierten Anmerkungen in PowerShell
Mit dem PowerShell-Skript [CreateReleaseAnnotation](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1) von GitHub können Sie Anmerkungen auf der Grundlage eines beliebigen Prozesses erstellen, ohne Azure DevOps zu verwenden. 

1. Erstellen Sie eine lokale Kopie von [CreateReleaseAnnotation.ps1](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).
   
1. Gehen Sie wie oben beschrieben vor, um Ihre Application Insights-ID abzurufen und einen API-Schlüssel über die Application Insights-Registerkarte **API-Zugriff** zu erstellen.
   
1. Rufen Sie das PowerShell-Skript mithilfe des folgenden Codes auf, und ersetzen Sie dabei die in spitzen Klammern angegebenen Platzhalter durch Ihre eigenen Werte. Die Releaseeigenschaften (`-releaseProperties`) sind optional. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Sie können das Skript auch anpassen, um beispielsweise Anmerkungen für die Vergangenheit zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Arbeitselements](./diagnostic-search.md#create-work-item)
* [Automation mit PowerShell](./powershell.md)

