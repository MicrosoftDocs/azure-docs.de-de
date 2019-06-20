---
title: Versionsanmerkungen für Application Insights | Microsoft Docs
description: Fügen Sie den Diagrammen im Metrik-Explorer in Application Insights Bereitstellungs- oder Buildmarker hinzu.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 23173e33-d4f2-4528-a730-913a8fd5f02e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: mbullwin
ms.openlocfilehash: 6567d7f2ebaab5bd7b5bc8fb7b5a62970f169161
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66476174"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Anmerkungen zu Metrik-Diagrammen in Application Insights

Die Anmerkungen in den Diagrammen des [Metrik-Explorers](../../azure-monitor/app/metrics-explorer.md) geben an, wo Sie einen neuen Build oder ein anderes wichtiges Ereignis bereitgestellt haben. Dank dieser Anmerkungen sehen Sie auf einen Blick, ob Ihre Änderungen Auswirkungen auf die Leistung Ihrer Anwendung hatten. Sie können automatisch vom [Buildsystem von Azure DevOps Services](https://docs.microsoft.com/azure/devops/pipelines/tasks/) erstellt werden. Es können auch Anmerkungen erstellt werden, die alle gewünschten Ereignisse markieren, indem diese aus PowerShell erstellt werden.

> [!NOTE]
> In diesem Artikel wird die veraltete **Umgebung für klassische Metriken** verwendet. Anmerkungen sind derzeit nur in der klassischen Umgebung und in **[Arbeitsmappen](../../azure-monitor/app/usage-workbooks.md)** verfügbar. Weitere Informationen zur aktuellen Metrikumgebung finden Sie in [diesem Artikel](../../azure-monitor/platform/metrics-charts.md).

![Beispiel für Anmerkungen](./media/annotations/0-example.png)

## <a name="release-annotations-with-azure-devops-services-build"></a>Versionsanmerkungen mit Azure DevOps-Services-Build

Versionsanmerkungen sind ein Feature des cloudbasierten Azure Pipelines-Diensts von Azure DevOps Services.

### <a name="install-the-annotations-extension-one-time"></a>Installieren der Erweiterung für Anmerkungen (einmalig)
Um Versionsanmerkungen erstellen zu können, müssen Sie eine der zahlreichen Azure DevOps Services-Erweiterungen installieren, die im Visual Studio Marketplace zur Verfügung stehen.

1. Melden Sie sich bei Ihrem [Azure DevOps Services](https://azure.microsoft.com/services/devops/)-Projekt an.
2. Laden Sie im Visual Studio Marketplace die [Erweiterung Release Annotations](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) herunter, und fügen Sie sie Ihrer Azure DevOps Services-Organisation hinzu.

![Wählen Sie eine Azure DevOps-Organisation aus, und führen Sie dann die Installation aus.](./media/annotations/1-install.png)

Sie müssen dies nur einmal für Ihre Azure DevOps Services-Organisation durchführen. Versionsanmerkungen können nun für jedes Projekt in Ihrer Organisation konfiguriert werden.

### <a name="configure-release-annotations"></a>Konfigurieren von Versionsanmerkungen

Es wird ein separater API-Schlüssel für jede Azure DevOps Services-Releasevorlage benötigt.

1. Melden Sie sich beim [Microsoft Azure-Portal](https://portal.azure.com) an, und öffnen Sie die Application Insights-Ressource, die Ihre Anwendung überwacht. (Oder [erstellen Sie jetzt eine Ressource](../../azure-monitor/app/app-insights-overview.md), sofern noch nicht geschehen.)
2. Öffnen Sie die Registerkarte **API-Zugriff**, und kopieren Sie die **Application Insights-ID**.
   
    ![Öffnen Sie unter „portal.azure.com“ die Application Insights-Ressource, und wählen Sie „Einstellungen“. Öffnen Sie „API-Zugriff“. Kopieren der Anwendungs-ID](./media/annotations/2-app-id.png)

4. Öffnen (oder erstellen) Sie in einem separaten Browserfenster die Releasevorlage, mit der Ihre Bereitstellungen über Azure DevOps Services verwaltet werden.
   
    Fügen Sie eine Aufgabe hinzu, und wählen Sie im Menü die Aufgabe für Application Insights-Versionsanmerkungen aus.

   ![Klicken Sie auf das Pluszeichen, um eine Aufgabe hinzuzufügen, und wählen Sie dann die Aufgabe für Application Insights-Versionsanmerkungen aus. Fügen Sie die Application Insights-ID ein.](./media/annotations/3-add-task.png)

    Fügen Sie die **Anwendungs-ID** ein, die Sie auf der Registerkarte „API-Zugriff“ kopiert haben.
   
    ![Application Insights-ID einfügen](./media/annotations/4-paste-app-id.png)

5. Wechseln Sie zurück ins Azure-Fenster, erstellen Sie einen neuen API-Schlüssel, und erstellen Sie eine Kopie davon.
   
    ![Klicken Sie im Azure-Fenster auf der Registerkarte „API-Zugriff“ auf „API-Schlüssel erstellen“.](./media/annotations/5-create-api-key.png)

    ![Geben Sie auf der Registerkarte „API-Schlüssel erstellen“ einen Kommentar an, aktivieren Sie das Kontrollkästchen „Anmerkungen schreiben“, und klicken Sie dann auf „Schlüssel generieren“. Kopieren Sie den neuen Schlüssel.](./media/annotations/6-create-api-key.png)

6. Öffnen Sie die Registerkarte „Konfiguration“ der Versionsvorlage.
   
    Erstellen Sie eine Variablendefinition für `ApiKey`.
   
    Fügen Sie Ihren API-Schlüssel in die ApiKey-Variablendefinition ein.
   
    ![Wählen Sie im Azure DevOps Services-Fenster die Registerkarte „Variablen“ aus, und klicken Sie auf „Hinzufügen“. Legen Sie den Namen auf „ApiKey“ fest, und fügen Sie unter „Wert“ den von Ihnen generierten Schlüssel ein. Klicken Sie dann auf das Schlosssymbol.](./media/annotations/7-paste-api-key.png)
1. **Speichern** Sie abschließend die Releasepipeline.


## <a name="view-annotations"></a>Anmerkungen anzeigen
Wenn Sie nun diese Versionsvorlage zum Bereitstellen einer neuen Version verwenden, wird jedes Mal eine Anmerkung an Application Insights gesendet. Die Anmerkungen werden in Diagrammen im Metrik-Explorer angezeigt.

Klicken Sie auf einen Anmerkungsmarker (hellgrauen Pfeil), um Details zur Version einschließlich Anfordernder, Quellcodeverwaltungsbranch, Releasepipeline, Umgebung usw. anzuzeigen.

![Klicken Sie auf einen Versionsanmerkungsmarker.](./media/annotations/8-release.png)

## <a name="create-custom-annotations-from-powershell"></a>Erstellen von benutzerdefinierten Anmerkungen in PowerShell
Sie können Anmerkungen auch von jedem beliebigen Prozess erstellen (ohne Azure DevOps Services zu verwenden). 


1. Erstellen Sie eine lokale Kopie des [PowerShell-Skript aus GitHub](https://github.com/Microsoft/ApplicationInsights-Home/blob/master/API/CreateReleaseAnnotation.ps1).

2. Rufen Sie auf der Registerkarte „API-Zugriff“ die Anwendungs-ID ab, und erstellen Sie einen API-Schlüssel.

3. Nennen Sie das Skript folgendermaßen:

```PS

     .\CreateReleaseAnnotation.ps1 `
      -applicationId "<applicationId>" `
      -apiKey "<apiKey>" `
      -releaseName "<myReleaseName>" `
      -releaseProperties @{
          "ReleaseDescription"="a description";
          "TriggerBy"="My Name" }
```

Änderungen am Skript können einfach vorgenommen werden, um beispielsweise frühere Anmerkungen zu erstellen.

## <a name="next-steps"></a>Nächste Schritte

* [Erstellen eines Arbeitselements](../../azure-monitor/app/diagnostic-search.md#create-work-item)
* [Automation mit PowerShell](../../azure-monitor/app/powershell.md)
