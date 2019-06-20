---
title: Azure Monitor-Anwendungsänderungsanalyse – Verwenden der Azure Monitor-Anwendungsänderungsanalyse zum Ermitteln von Änderungen, die zu Problemen/Ausfällen der Livewebsite führen können | Microsoft-Dokumentation
description: Behandeln von Anwendungsproblemen der Livewebsite in Azure App Services mit der Azure Monitor-Anwendungsänderungsanalyse
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 5bd3816e65398283de85b4551a137b3f97db4cc7
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226340"
---
# <a name="application-change-analysis-public-preview"></a>Anwendungsänderungsanalyse (Public Preview)

Wenn es zu einem Problem/Ausfall der Livewebsite kommt, ist es wichtig, die Grundursache schnell bestimmen zu können. Mithilfe von Standardüberwachungslösungen können Sie schnell erkennen, dass ein Problem vorliegt, und häufig auch die fehlerhafte Komponente identifizieren. Dies führt aber nicht immer zu einer sofortigen Erklärung, warum der Fehler auftritt. Vor fünf Minuten hat Ihre Website noch funktioniert, jetzt ist sie fehlerhaft. Was hat sich in den letzten fünf Minuten geändert? Für die Beantwortung dieser Frage ist das neue Feature, die Azure Monitor-Anwendungsänderungsanalyse, konzipiert. Basierend auf dem Funktionsumfang von [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) liefert die Anwendungsänderungsanalyse Erkenntnisse über Azure-Anwendungsänderungen, um die Transparenz zu erhöhen und die durchschnittliche Reparaturzeit (Mean Time To Repair, MTTR) zu verkürzen.

> [!IMPORTANT]
> Die Azure Monitor-Anwendungsänderungsanalyse befindet sich derzeit in der öffentlichen Vorschau.
> Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar.
> Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview-of-change-analysis-service"></a>Übersicht über den Änderungsanalysedienst
Der Änderungsanalysedienst erkennt verschiedene Arten von Änderungen von der Infrastrukturebene bis hin zur Anwendungsbereitstellung. Es handelt sich um einen Azure-Ressourcenanbieter auf Abonnementebene, der Ressourcenänderungen im Abonnement untersucht und Daten für verschiedene Diagnosetools bereitstellt, damit die Benutzer verstehen, welche Änderungen möglicherweise Probleme verursacht haben.

Das folgende Diagramm zeigt die Architektur des Änderungsanalysediensts: ![Architekturdiagramm zum Abruf von Änderungsdaten und Bereitstellung der Daten für Clienttools durch den Änderungsanalysedienst](./media/change-analysis/overview.png)

Derzeit ist das Tool in die Diagnose und Problembehandlung für App Services-Web-Apps integriert. Informationen zum Aktivieren und Anzeigen von Änderungen an einer Web-App finden Sie im Abschnitt *Änderungsanalysedienst für App Services-Web-Apps*.

### <a name="azure-resource-manager-deployment-changes"></a>Bereitstellungsänderungen in Azure Resource Manager
Durch Nutzung von [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) stellt das Änderungsanalysetool einen Verlauf dar, wie sich die Azure-Ressourcen, die Ihre Anwendung hosten, im Laufe der Zeit verändert haben. Wenn beispielsweise einer Web-App ein Tag hinzugefügt wurde, ist die Änderung im Änderungsanalysetool enthalten.
Diese Informationen sind immer verfügbar, solange der `Microsoft.ChangeAnalysis`-Ressourcenanbieter in das Azure-Abonnement integriert ist.

### <a name="web-application-deployment-and-configuration-changes"></a>Bereitstellungs- und Konfigurationsänderungen einer Webanwendung
Das Änderungsanalysetool erfasst alle 4 Stunden den Bereitstellungs- und Konfigurationszustand einer Anwendung, um die Unterschiede zu berechnen und anzugeben, was sich geändert hat. Solche Änderungen sind beispielsweise Änderungen an Umgebungsvariablen der Anwendung, Änderungen der IP-Konfigurationsregel, Änderungen der verwalteten Dienstidentität, Änderungen der SSL-Einstellungen usw.
Im Gegensatz zu Resource Manager-Änderungen ist diese Art von Änderungsinformationen möglicherweise nicht sofort im Tool verfügbar. Zum Anzeigen der letzten Änderungen verwenden Sie die Schaltfläche für das sofortige Überprüfen auf Änderungen im Tool.

![Screenshot der Schaltfläche zum sofortigen Überprüfen auf Änderungen im Diagnose- und Problembehandlungstool mit Integration der Änderungsanalyse für App Service-Web-Apps](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Abhängigkeitsänderungen
Abhängigkeiten einer Ressource können ebenfalls die Ursache für Probleme sein. Wenn eine Web-App beispielsweise Aufrufe an einen Redis Cache richtet, kann die Web-App-Leistung durch die Redis Cache-SKU beeinträchtigt werden. Wenn man sich den Änderungsanalysedienst für den DNS-Eintrag der Web-App ansieht, sind auch die Änderungsinformationen für Abhängigkeiten enthalten, um Änderungen bei allen Komponenten einer App anzugeben, die Probleme verursacht haben könnten.


## <a name="change-analysis-service-for-app-services-web-app"></a>Änderungsanalysedienst für App Services-Web-Apps

Die Azure Monitor-Anwendungsänderungsanalyse ist derzeit in die Oberfläche für Self-Service-**Diagnose und Problembehandlung** integriert, auf die Sie in Ihrer Azure App Service-Anwendung über den Abschnitt **Übersicht** zugreifen können:

![Screenshot der Azure App Service-Übersichtsseite mit roter Umrandung der Schaltflächen „Übersicht“ und „Diagnose und Problembehandlung“](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-diagnose-and-solve-problems-tool"></a>Aktivieren der Änderungsanalyse im Diagnose- und Problembehandlungstool

1. Wählen Sie **Verfügbarkeit und Leistung** aus.

    ![Screenshot der Problembehandlungsoptionen von „Verfügbarkeit und Leistung“](./media/change-analysis/availability-and-performance.png)

2. Klicken Sie auf die Kachel **Anwendungsabstürze**.

   ![Screenshot, auf dem die Kachel „Anwendungsabstürze“ hervorgehoben ist](./media/change-analysis/application-crashes-tile.png)

3. Wählen Sie zum Aktivieren der **Änderungsanalyse** die Option **Jetzt aktivieren** aus.

   ![Screenshot der Problembehandlungsoptionen von „Verfügbarkeit und Leistung“](./media/change-analysis/application-crashes.png)

4. Um alle Funktionen der Änderungsanalyse zu nutzen, legen Sie **Änderungsanalyse**, **Nach Codeänderungen suchen** und **Immer aktiviert** auf **Ein** fest, und wählen Sie dann **Speichern** aus.

    ![Screenshot der Aktivierungsoptionen auf der Benutzeroberfläche der Azure App Service-Änderungsanalyse](./media/change-analysis/change-analysis-on.png)

    Wenn die **Änderungsanalyse** aktiviert ist, können Sie Änderungen auf Ressourcenebene erkennen. Wenn **Nach Codeänderungen suchen** aktiviert ist, werden auch Änderungen an Bereitstellungsdateien und der Websitekonfiguration angezeigt. Durch Aktivieren von **Immer aktiviert** wird die Änderungsüberprüfungsleistung optimiert, aber abrechnungstechnisch können dadurch zusätzliche Kosten anfallen.

5.  Nachdem Sie alle Optionen aktiviert haben, können Sie durch Auswählen von **Diagnose und Problembehandlung** > **Verfügbarkeit und Leistung** > **Anwendungsabstürze** auf die Oberfläche der Änderungsanalyse zugreifen. Im Diagramm werden die Arten von Änderungen, die im Laufe der Zeit erfolgt sind, zusammen mit Details zu diesen Änderungen zusammengefasst:

     ![Screenshot der Gegenüberstellung der Änderungen](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-service-at-scale"></a>Bedarfsorientiertes Aktivieren des Änderungsanalysediensts
Wenn Ihr Abonnement viele Web-Apps umfasst, ist ein Aktivieren des Diensts auf Ebene der einzelnen Web-Apps ineffizient. Nachfolgend sind einige alternative Anweisungen für die Integration aufgeführt.

#### <a name="registering-change-analysis-resource-provider-for-your-subscription"></a>Registrieren des Änderungsanalyse-Ressourcenanbieters für Ihr Abonnement

1. Registrieren des Änderungsanalyse-Featureflags (Vorschau)

    Da sich dieses Feature in der Vorschauphase befindet, müssen Sie zuerst das entsprechende Featureflag registrieren, damit es für Ihr Abonnement sichtbar ist.
    - Öffnen Sie [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/).

    ![Screenshot der Azure Cloud Shell-Änderung](./media/change-analysis/cloud-shell.png)

    - Ändern Sie den Shelltyp in „PowerShell“:

    ![Screenshot der Azure Cloud Shell-Änderung](./media/change-analysis/choose-powershell.png)

    - Führen Sie den folgenden PowerShell-Befehl aus:

    ``` PowerShell

    Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to onboard
    Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
    Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag

    ```

2. Registrieren des Änderungsanalyse-Ressourcenanbieters für das Abonnement

    - Navigieren Sie zu „Abonnements“, wählen Sie das Abonnement aus, in das Sie den Änderungsdienst integrieren möchten, und klicken Sie dann auf „Ressourcenanbieter“:

        ![Screenshot zum Registrieren des Änderungsanalyse-Ressourcenanbieters über das Blatt „Abonnements“](./media/change-analysis/register-rp.png)

    - Wählen Sie *Microsoft.ChangeAnalysis* aus, und klicken Sie oben auf der Seite auf *Registrieren*.

    - Sobald der Ressourcenanbieter integriert ist, folgen Sie den Anweisungen im Abschnitt *Die Informationen der Änderungsanalyse können nicht abgerufen werden* weiter unten, um ein ausgeblendetes Tag für die Web-App festzulegen und so die Änderungserkennung auf Bereitstellungsebene für die Web-App zu aktivieren.

3. Alternativ zu Schritt 2 oben können Sie den Ressourcenanbieter über ein PowerShell-Skript registrieren:

    ```PowerShell
    Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

    Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
    ```

4. Zum Festlegen eines ausgeblendeten Tags für eine Web-App mithilfe von PowerShell führen Sie den folgenden Befehl aus:

    ```powershell
    $webapp=Get-AzWebApp -Name <name_of_your_webapp>
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
    ```

> [!NOTE]
> Nachdem das ausgeblendete Tag hinzugefügt wurde, müssen Sie möglicherweise noch bis zu vier Stunden warten, bis die Änderungen angezeigt werden. Der Grund dafür ist das Häufigkeitsintervall von 4 Stunden, das der Änderungsanalysedienst verwendet, um Ihre Web-App zu überprüfen und gleichzeitig die Auswirkungen der Überprüfung auf die Leistung zu beschränken.

## <a name="next-steps"></a>Nächste Schritte

- Verbessern Sie die Überwachung von Azure App Services [durch Aktivieren der Application Insights-Features](azure-web-apps.md) von Azure Monitor.
- Erweitern Sie Ihre Kenntnisse über den [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview)-Dienst, der die Azure Monitor-Anwendungsänderungsanalyse unterstützt.
