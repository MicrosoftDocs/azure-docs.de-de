---
title: Verwenden der Anwendungsänderungsanalyse in Azure Monitor für die Suche nach Web-App-Problemen | Microsoft-Dokumentation
description: Verwenden der Anwendungsänderungsanalyse in Azure Monitor zum Behandeln von Anwendungsproblemen auf Live-Websites in Azure App Service.
ms.topic: conceptual
author: cawams
ms.author: cawa
ms.date: 05/04/2020
ms.openlocfilehash: 133a7d9b3fa04797648fa253825505d29e37ca98
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99576392"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Verwenden der Anwendungsänderungsanalyse (Vorschau) in Azure Monitor

Wenn es zu einem Problem oder Ausfall einer Live-Website kommt, ist es wichtig, die Grundursache schnell bestimmen zu können. Die Standardüberwachungslösungen machen Sie möglicherweise auf ein Problem aufmerksam. Sie geben unter Umständen sogar an, bei welcher Komponente der Fehler aufgetreten ist. In den meisten Fällen geht aus dieser Warnung jedoch die Fehlerursache nicht direkt hervor. Sie wissen, dass Ihre Website vor fünf Minuten noch funktioniert hat, und jetzt nicht mehr. Was hat sich in den letzten fünf Minuten geändert? Für die Beantwortung dieser Frage ist die Anwendungsänderungsanalyse in Azure Monitor konzipiert.

Basierend auf dem Funktionsumfang von [Azure Resource Graph](../../governance/resource-graph/overview.md) liefert die Änderungsanalyse Erkenntnisse über Azure-Anwendungsänderungen, um die Transparenz zu erhöhen und die durchschnittliche Reparaturzeit (Mean Time To Repair, MTTR) zu verkürzen.

> [!IMPORTANT]
> Die Änderungsanalyse befindet sich derzeit in der Vorschauphase. Diese Vorschauversion wird ohne Vereinbarung zum Servicelevel bereitgestellt. Diese Version wird nicht für die Produktion empfohlen. Manche Funktionen werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="overview"></a>Übersicht

Die Änderungsanalyse erkennt von der Infrastrukturebene bis hin zur Anwendungsbereitstellung verschiedene Arten von Änderungen. Es handelt sich um einen Azure-Ressourcenanbieter auf Abonnementebene, der Ressourcenänderungen im Abonnement überprüft. Die Änderungsanalyse stellt Daten für verschiedene Diagnosetools bereit, damit der Benutzer besser verstehen kann, welche Änderungen möglicherweise zu Problemen geführt haben.

Das folgende Diagramm zeigt die Architektur der Änderungsanalyse:

![Architekturdiagramm zum Abrufen von Änderungsdaten und Bereitstellung dieser Daten für Clienttools durch die Änderungsanalyse](./media/change-analysis/overview.png)

## <a name="supported-resource-types"></a>Unterstützte Ressourcentypen

Der Dienst für die Anwendungsänderungsanalyse unterstützt Änderungen auf der Ressourceneigenschaftsebene für alle Azure-Ressourcentypen einschließlich der folgenden allgemeinen Ressourcen:
- Virtual Machine
- VM-Skalierungsgruppe
- App Service
- Azure Kubernetes Service
- Azure Function
- Netzwerkressourcen (z. B. Netzwerksicherheitsgruppe, Virtual Network und Application Gateway)
- Datendienste (z. B. Azure Storage, SQL, Redis Cache, Cosmos DB)

## <a name="data-sources"></a>Datenquellen

Anwendungsänderungsanalyse-Abfragen für von Azure Resource Manager nachverfolgte Eigenschaften, per Proxy übermittelte Konfigurationen und Web-App-Änderungen des Gastsystems. Außerdem verfolgt der Dienst Änderungen an Ressourcenabhängigkeiten, um Anwendungen End-to-End zu diagnostizieren und zu überwachen.

### <a name="azure-resource-manager-tracked-properties-changes"></a>Verfolgte Eigenschaftsänderungen in Azure Resource Manager

Mithilfe von [Azure Resource Graph](../../governance/resource-graph/overview.md) stellt die Änderungsanalyse in Form von Verlaufsdaten dar, wie sich die Azure-Ressourcen, die Ihre Anwendung hosten, im Laufe der Zeit verändert haben. Nachverfolgte Einstellungen wie verwaltete Identitäten, Plattformbetriebssystems-Upgrades und Hostnamen können erkannt werden.

### <a name="azure-resource-manager-proxied-setting-changes"></a>Per Proxy übermittelte Einstellungsänderungen in Azure Resource Manager

Einstellungen wie die IP-Konfigurationsregel, TLS-Einstellungen und Erweiterungsversionen sind in Azure Resource Graph noch nicht verfügbar. Daher fragt die Änderungsanalyse diese Änderungen ab und berechnet sie sicher, um weitere Details darüber bereitzustellen, was sich in der App geändert hat.

### <a name="changes-in-web-app-deployment-and-configuration-in-guest-changes"></a>Änderungen bei Bereitstellung und Konfiguration einer Web-App (Änderungen auf Gastsystemen)

Die Änderungsanalyse erfasst alle vier Stunden den Bereitstellungs- und Konfigurationsstatus der Anwendung. Sie kann z. B. Änderungen in den Umgebungsvariablen der Anwendung erkennen. Das Tool berechnet die Unterschiede und zeigt die Änderungen an. Im Gegensatz zu Resource Manager-Änderungen sind die Informationen zu Änderungen an der Codebereitstellung möglicherweise nicht sofort im Tool verfügbar. Wählen Sie zum Anzeigen der jüngsten Änderungen in der Änderungsanalyse **Aktualisieren** aus.

![Screenshot der Schaltfläche „Jetzt auf Änderungen prüfen“](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>Abhängigkeitsänderungen

Änderungen an Ressourcenabhängigkeiten können ebenfalls zu Problemen in einer Ressource führen. Wenn eine Web-App beispielsweise Aufrufe an einen Redis Cache richtet, kann die Web-App-Leistung durch die Redis Cache-SKU beeinträchtigt werden. Weitere Beispiel: Wenn Port 22 in der Netzwerksicherheitsgruppe eines virtuellen Computers geschlossen wurde, führt dies zu Verbindungsfehlern. 

#### <a name="web-app-diagnose-and-solve-problems-navigator-preview"></a>Web-App-Navigator für die Diagnose und Problembehandlung (Vorschauversion)
Zum Erkennen von Änderungen bei Abhängigkeiten überprüft die Änderungsanalyse dem DNS-Eintrag der Web-App. Auf diese Weise identifiziert sie Änderungen in allen App-Komponenten, die Probleme verursachen könnten.
Derzeit werden die folgenden Abhängigkeiten im **Web-App-Navigator für die Diagnose und Problembehandlung (Vorschauversion)** unterstützt:
- Web-Apps
- Azure Storage
- Azure SQL

#### <a name="related-resources"></a>Zugehörige Ressourcen
Die Anwendungsänderungsanalyse erkennt zugehörige Ressourcen. Gängige Beispiele sind Netzwerksicherheitsgruppen, das Virtual Network, Application Gateway und Load Balancer im Zusammenhang mit einem virtuellen Computer. Die Netzwerkressourcen werden in der Regel automatisch in derselben Ressourcengruppe wie die Ressourcen bereitgestellt, die sie verwenden. Wenn Sie die Änderungen also nach Ressourcengruppe filtern, werden alle Änderungen für den virtuellen Computer und die zugehörigen Netzwerkressourcen angezeigt.

![Screenshot: Netzwerkänderungen](./media/change-analysis/network-changes.png)

## <a name="application-change-analysis-service-enablement"></a>Aktivieren des Anwendungsänderungsanalyse-Diensts

Der Anwendungsänderungsanalyse-Dienst berechnet und aggregiert Änderungsdaten aus den oben erwähnten Datenquellen. Er bietet eine Reihe von Analysen für Benutzer, um bequem durch alle Ressourcenänderungen zu navigieren und zu ermitteln, welche Änderung im Kontext der Problembehandlung oder Überwachung relevant ist.
Der Ressourcenanbieter „Microsoft.ChangeAnalysis“ muss bei einem Abonnement registriert werden, damit die Daten zu nachverfolgten Änderungen und per Proxy übermittelten Einstellungsänderungen von Azure Resource Manager verfügbar sind. Wenn Sie das Diagnose- und Problembehandlungstool in der Web-App öffnen oder die eigenständige Registerkarte „Änderungsanalyse“ aufrufen, wird dieser Ressourcenanbieter automatisch registriert. Bei Web-App-Änderungen, die das Gastbetriebssystem betreffen, ist eine separate Aktivierung erforderlich, um Codedateien in einer Web-App zu überprüfen. Weitere Informationen finden Sie unter [Änderungsanalyse im Tool „Diagnose und Problembehandlung“](#application-change-analysis-in-the-diagnose-and-solve-problems-tool) weiter unten in diesem Artikel.

## <a name="cost"></a>Cost
Bei der Anwendungsänderungsanalyse handelt es sich um einen kostenlosen Dienst, der keine Kosten für Abonnements verursacht, in denen er aktiviert ist. Der Dienst hat auch keine Auswirkungen auf die Leistung beim Scannen von Änderungen der Azure-Ressourceneigenschaften. Wenn Sie die Änderungsanalyse für Gastdateiänderungen in Web-Apps aktivieren (oder die Aktivierung im Tool für die Diagnose und Problembehandlung vornehmen), hat dies nur geringfügige Auswirkungen auf die Leistung der Web-App und verursacht keinerlei Abrechnungskosten.

## <a name="visualizations-for-application-change-analysis"></a>Visualisierungen für die Anwendungsänderungsanalyse

### <a name="standalone-ui"></a>Eigenständige Benutzeroberfläche

In Azure Monitor gibt es einen eigenständigen Bereich für die Änderungsanalyse, in dem alle Änderungen mit Erkenntnissen zu Anwendungsabhängigkeiten und Ressourcen angezeigt werden können.

Suchen Sie im Azure-Portal in der Suchleiste nach „Änderungsanalyse“, um die Erfahrung zu starten.

![Screenshot der Suche nach „Änderungsanalyse“ im Azure-Portal](./media/change-analysis/search-change-analysis.png)

Alle Ressourcen unter einem ausgewählten Abonnement werden mit Änderungen der letzten 24 Stunden angezeigt. Zur Optimierung der Seitenladeleistung zeigt der Dienst jeweils 10 Ressourcen gleichzeitig an. Klicken Sie auf die nächsten Seiten, um weitere Ressourcen anzuzeigen. Wir arbeiten derzeit daran, diese Einschränkung zu beseitigen.

![Screenshot des Blatts „Änderungsanalyse“ im Azure-Portal](./media/change-analysis/change-analysis-standalone-blade.png)

Klicken Sie auf eine Ressource, um alle ihre Änderungen anzuzeigen. Führen Sie bei Bedarf einen Drilldown in eine Änderung durch, um Änderungsdetails und Erkenntnisse im JSON-Format anzuzeigen.

![Screenshot von Änderungsdetails](./media/change-analysis/change-details.png)

Zum Senden von Feedback verwenden Sie die entsprechende Schaltfläche auf dem Blatt, oder senden Sie eine E-Mail an changeanalysisteam@microsoft.com.

![Screenshot der Feedbackschaltfläche auf dem Blatt „Änderungsanalyse“](./media/change-analysis/change-analysis-feedback.png)

#### <a name="multiple-subscription-support"></a>Unterstützung mehrerer Abonnements
Die Benutzeroberfläche unterstützt das Auswählen mehrerer Abonnements zum Anzeigen von Ressourcenänderungen. Verwenden Sie den Abonnementfilter:

![Screenshot: Abonnementfilter, der das Auswählen mehrerer Abonnements unterstützt](./media/change-analysis/multiple-subscriptions-support.png)

### <a name="web-app-diagnose-and-solve-problems"></a>Diagnose und Behandlung von Problemen bei Web-Apps

In Azure Monitor ist die Änderungsanalyse auch in die Self-Service-Umgebung **Diagnose und Problembehandlung** integriert. Der Zugriff auf diese Umgebung erfolgt von der Seite **Übersicht** Ihrer App Service-Anwendung.

![Screenshot der Schaltflächen „Übersicht“ und „Diagnose und Problembehandlung“](./media/change-analysis/change-analysis.png)

### <a name="application-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>Anwendungsänderungsanalyse im Tool „Diagnose und Problembehandlung“

Bei der Anwendungsänderungsanalyse handelt es sich um ein eigenständiges Erkennungsmodul in den Web-App-Tools für Diagnose und Problembehandlung. Es ist auch in den Erkennungsmodulen für **Anwendungsabstürze** und  **Web-App-Ausfälle** aggregiert. Wenn Sie das Diagnose- und Problembehandlungstool öffnen, wird der Ressourcenanbieter **Microsoft.ChangeAnalysis** automatisch registriert. Befolgen Sie diese Anweisungen, um die Nachverfolgung von Web-App-Änderungen des Gastsystems zu aktivieren.

1. Wählen Sie **Verfügbarkeit und Leistung** aus.

    ![Screenshot der Problembehandlungsoptionen von „Verfügbarkeit und Leistung“](./media/change-analysis/availability-and-performance.png)

2. Wählen Sie **Anwendungsänderungen** aus. Diese Funktion ist ebenfalls unter **Anwendungsabstürze** verfügbar.

   ![Screenshot der Schaltfläche „Anwendungsabstürze“](./media/change-analysis/application-changes.png)

3. Der Link führt zur Benutzeroberfläche der Anwendungsänderungsanalyse bezogen auf die Web-App. Wenn die Nachverfolgung von Web-App-Änderungen des Gastsystems nicht aktiviert ist, befolgen Sie die Anweisung im Banner, um Änderungen an Datei- und App-Einstellungen abzurufen.

   ![Screenshot der Optionen von „Anwendungsabstürze“](./media/change-analysis/enable-changeanalysis.png)

4. Aktivieren Sie **Änderungsanalyse**, und wählen Sie **Speichern** aus. Das Tool zeigt alle Web-Apps unter einem App Service-Plan an. Sie können den Schalter auf Planebene verwenden, um die Änderungsanalyse für alle Web-Apps in einem Plan zu aktivieren.

    ![Screenshot der Benutzeroberfläche zum Aktivieren der Änderungsanalyse](./media/change-analysis/change-analysis-on.png)

5. Änderungsdaten sind auch in ausgewählten Erkennungsmodulen für **Web-App-Ausfälle** und **Anwendungsabstürze** verfügbar. Im angezeigten Diagramm sind die Arten der Änderungen im Laufe der Zeit und Details zu diesen Änderungen zusammengefasst. Standardmäßig werden Änderungen der letzten 24 Stunden angezeigt, um bei unmittelbaren Problemen sofort reagieren zu können.

     ![Screenshot der Gegenüberstellung der Änderungen](./media/change-analysis/change-view.png)



### <a name="virtual-machine-diagnose-and-solve-problems"></a>Diagnose und Problembehandlung bei virtuellen Computern

Wechseln Sie zum Diagnose- und Problembehandlungstool für einen virtuellen Computer.  Wechseln Sie zu **Problembehandlungstools**, scrollen Sie auf der Seite nach unten, und wählen Sie **Aktuelle Änderungen analysieren** aus, um Änderungen an dem virtuellen Computer anzuzeigen.

![Screenshot von „Diagnose und Problembehandlung bei virtuellen Computern“](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Änderungsanalyse in Problembehandlungstools](./media/change-analysis/analyze-recent-changes.png)

### <a name="activity-log-change-history"></a>Änderungsverlauf im Aktivitätsprotokoll
Die Funktion zum [Anzeigen des Änderungsverlaufs](../platform/activity-log.md#view-change-history) im Aktivitätsprotokoll ruft das Back-End des Anwendungsänderungsanalyse-Diensts auf, um Änderungen im Zusammenhang mit einem Vorgang abzurufen. Mit dem **Änderungsverlauf** wurde sonst [Azure Resource Graph](../../governance/resource-graph/overview.md) direkt aufgerufen, doch wurde das Back-End gegen einen Aufruf der Anwendungsänderungsanalyse getauscht, sodass in den zurückgegebenen Änderungen auch Änderungen auf Ressourcenebene aus [Azure Resource Graph](../../governance/resource-graph/overview.md), Ressourceneigenschaften aus [Azure Resource Manager](../../azure-resource-manager/management/overview.md) und Änderungen auf Gastsystemen aus PaaS-Diensten wie der App Services-Web-App enthalten sind. Damit der Anwendungsänderungsanalyse-Dienst nach Änderungen in den Abonnements der Benutzer suchen kann, muss ein Ressourcenanbieter registriert sein. Wenn Sie die Registerkarte **Änderungsverlauf** zum ersten Mal öffnen, beginnt das Tool automatisch mit der Registrierung des **Microsoft.ChangeAnalysis**-Ressourcenanbieters. Nach der Registrierung sind Änderungen aus **Azure Resource Graph** sofort verfügbar und decken die letzten 14 Tage ab. Änderungen aus anderen Quellen stehen ca. vier Stunden nach dem Onboarding des Abonnements zur Verfügung.

![Integration des Änderungsverlaufs im Aktivitätsprotokoll](./media/change-analysis/activity-log-change-history.png)

### <a name="vm-insights-integration"></a>VM Insights-Integration
Benutzer, die [VM Insights](../insights/vminsights-overview.md) aktiviert haben, können Änderungen bei ihren virtuellen Computern anzeigen, die möglicherweise zu Spitzen in einem Metrikdiagramm, z. B. CPU oder Arbeitsspeicher, geführt haben. Änderungsdaten werden in die Seitennavigationsleiste von VM Insights eingefügt. Der Benutzer kann sehen, ob Änderungen bei der VM aufgetreten sind, und auf **Änderungen untersuchen** klicken, um Änderungsdetails auf der eigenständigen Benutzeroberfläche der Anwendungsänderungsanalyse anzuzeigen.

[![VM Insights-Integration](./media/change-analysis/vm-insights.png)](./media/change-analysis/vm-insights.png#lightbox)



## <a name="enable-change-analysis-at-scale"></a>Aktivieren der Änderungsanalyse in größerem Umfang

Wenn Ihr Abonnement zahlreiche Web-Apps enthält, wäre das Aktivieren des Diensts auf Web-App-Ebene nicht sehr effizient. Führen Sie das folgende Skript aus, um alle Web-Apps in Ihrem Abonnement zu aktivieren.

Voraussetzungen:

- PowerShell Az-Modul. Folgen Sie den Anweisungen unter [Installieren des Azure PowerShell-Moduls](/powershell/azure/install-az-ps).

Führen Sie folgendes Skript aus:

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"

# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```

## <a name="troubleshoot"></a>Problembehandlung

### <a name="having-trouble-registering-microsoftchange-analysis-resource-provider-from-change-history-tab"></a>Probleme beim Registrieren des Microsoft.ChangeAnalysis-Ressourcenanbieters auf der Registerkarte „Änderungsverlauf“
Wenn Sie den Änderungsverlauf nach der Integration in die Anwendungsänderungsanalyse zum ersten Mal anzeigen, werden Sie feststellen, dass automatisch ein **Microsoft.ChangeAnalysis**-Ressourcenanbieter registriert wird. In seltenen Fällen kann dabei aus folgenden Gründen ein Fehler auftreten:

- **Sie verfügen nicht über ausreichende Berechtigungen zum Registrieren des Microsoft.ChangeAnalysis-Ressourcenanbieters**. Diese Fehlermeldung bedeutet, dass Ihrer Rolle im aktuellen Abonnement der Bereich **Microsoft.Support/register/action** nicht zugeordnet ist. Dies kann vorkommen, wenn Sie nicht der Besitzer eines Abonnements sind und über einen Kollegen Berechtigungen für den gemeinsamen Zugriff erhalten haben, d. h. Anzeigezugriff für eine Ressourcengruppe. Um dieses Problem zu beheben, können Sie sich an den Besitzer Ihres Abonnements wenden, um den **Microsoft.ChangeAnalysis**-Ressourcenanbieter registrieren zu lassen. Dies kann im Azure-Portal erfolgen, indem **Abonnements | Ressourcenanbieter** aufgerufen, nach ```Microsoft.ChangeAnalysis``` gesucht und der Ressourcenanbieter auf der Benutzeroberfläche registriert wird. Dies ist auch über Azure PowerShell oder die Azure CLI möglich.

    Registrieren des Ressourcenanbieters über PowerShell: 
    ```PowerShell
    # Register resource provider
    Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
    ```

- **Fehler beim Registrieren des Microsoft.ChangeAnalysis-Ressourcenanbieters**. Diese Meldung bedeutet, dass beim Senden der Registrierungsanforderung durch die Benutzeroberfläche sofort ein Fehler aufgetreten ist und dieser nicht mit einem Berechtigungsproblem im Zusammenhang steht. Wahrscheinlich handelt es sich um ein vorübergehendes Problem mit der Internetverbindung. Aktualisieren Sie die Seite, und überprüfen Sie Ihre Internetverbindung. Wenn der Fehler weiterhin auftritt, wenden Sie sich an changeanalysishelp@microsoft.com.

- **Der Vorgang dauert länger als erwartet**. Diese Meldung bedeutet, dass die Registrierung länger als zwei Minuten dauert. Dies ist ungewöhnlich, bedeutet aber nicht unbedingt, dass ein Fehler aufgetreten ist. Sie können zu **Abonnements | Ressourcenanbieter** navigieren, um den Registrierungsstatus für den **Microsoft.ChangeAnalysis**-Ressourcenanbieter zu prüfen. Sie können über die Benutzeroberfläche eine Aufhebung der Registrierung, Neuregistrierung oder Aktualisierung durchführen, um zu sehen, ob dadurch Abhilfe geschaffen wird. Wenn das Problem weiterhin besteht, wenden Sie sich an changeanalysishelp@microsoft.com, um Unterstützung zu erhalten.
    ![Problembehandlung bei zu lange dauernder Registrierung des Ressourcenanbieters](./media/change-analysis/troubleshoot-registration-taking-too-long.png)

![Screenshot des Diagnose- und Problembehandlungstools für einen virtuellen Computer mit ausgewählten Problembehandlungstools](./media/change-analysis/vm-dnsp-troubleshootingtools.png)

![Screenshot der Kachel für das Problembehandlungstool zum Analysieren von aktuellen Änderungen für einen virtuellen Computer](./media/change-analysis/analyze-recent-changes.png)

### <a name="azure-lighthouse-subscription-is-not-supported"></a>Azure Lighthouse-Abonnements werden nicht unterstützt.

- **Fehler beim Abfragen des Microsoft.ChangeAnalysis-Ressourcenanbieters** mit der Meldung, dass das *Azure Lighthouse-Abonnement nicht unterstützt wird und die Änderungen nur im Basismandanten des Abonnements verfügbar sind*. Derzeit besteht eine Einschränkung für die Registrierung des Änderungsanalyse-Ressourcenanbieters über das Azure Lighthouse-Abonnement für Benutzer, die nicht dem Basismandanten angehören. Wir erwarten, dass diese Einschränkung in naher Zukunft behoben wird. Wenn Sie durch dieses Problem blockiert werden, gibt es eine Problemumgehung, die das Erstellen eines Dienstprinzipals und das explizite Zuweisen der Rolle zum Zulassen des Zugriffs umfasst.  Wenden Sie sich an changeanalysishelp@microsoft.com, um mehr darüber zu erfahren.

### <a name="an-error-occurred-while-getting-changes-please-refresh-this-page-or-come-back-later-to-view-changes"></a>Fehler beim Abrufen von Änderungen. Aktualisieren Sie diese Seite, oder kehren Sie später zurück, um Änderungen anzuzeigen.

Dies ist die allgemeine Fehlermeldung, die vom Anwendungsänderungsanalyse-Dienst angezeigt wird, wenn Änderungen nicht geladen werden konnten. Einige bekannte Ursachen sind:
- Internetverbindungsfehler vom Clientgerät
- Anwendungsänderungsanalyse-Dienst ist vorübergehend nicht verfügbar. Durch Aktualisieren der Seite nach einigen Minuten wird dieses Problem normalerweise behoben. Wenn der Fehler weiterhin auftritt, wenden Sie sich an changeanalysishelp@micorosoft.com.

### <a name="you-dont-have-enough-permissions-to-view-some-changes-contact-your-azure-subscription-administrator"></a>Sie verfügen nicht über ausreichende Berechtigungen zum Anzeigen einiger Änderungen. Wenden Sie sich an Ihren Azure-Abonnementadministrator.

Dies ist die allgemeine Fehlermeldung für fehlende Autorisierung, in der erläutert wird, dass der aktuelle Benutzer nicht über ausreichende Berechtigungen zum Anzeigen der Änderung verfügt. Es ist mindestens Lesezugriff für die Ressource erforderlich, um Infrastrukturänderungen anzuzeigen, die von Azure Resource Graph und Azure Resource Manager zurückgegeben werden. Bei Web-App-Dateiänderungen des Gastsystems und Konfigurationsänderungen ist mindestens die Rolle „Mitwirkender“ erforderlich.

### <a name="failed-to-register-microsoftchangeanalysis-resource-provider"></a>Fehler beim Registrieren des Microsoft.ChangeAnalysis-Ressourcenanbieters
Diese Meldung bedeutet, dass beim Senden der Registrierungsanforderung durch die Benutzeroberfläche sofort ein Fehler aufgetreten ist und dieser nicht mit einem Berechtigungsproblem im Zusammenhang steht. Wahrscheinlich handelt es sich um ein vorübergehendes Problem mit der Internetverbindung. Aktualisieren Sie die Seite, und überprüfen Sie Ihre Internetverbindung. Wenn der Fehler weiterhin auftritt, wenden Sie sich an changeanalysishelp@microsoft.com.
 
### <a name="you-dont-have-enough-permissions-to-register-microsoftchangeanalysis-resource-provider-contact-your-azure-subscription-administrator"></a>Sie verfügen nicht über ausreichende Berechtigungen zum Registrieren des Microsoft.ChangeAnalysis-Ressourcenanbieters. Wenden Sie sich an Ihren Azure-Abonnementadministrator.
Diese Fehlermeldung bedeutet, dass Ihrer Rolle im aktuellen Abonnement der Bereich **Microsoft.Support/register/action** nicht zugeordnet ist. Dies kann vorkommen, wenn Sie nicht der Besitzer eines Abonnements sind und über einen Kollegen Berechtigungen für den gemeinsamen Zugriff erhalten haben, d. h. Anzeigezugriff für eine Ressourcengruppe. Um dieses Problem zu beheben, können Sie sich an den Besitzer Ihres Abonnements wenden, um den **Microsoft.ChangeAnalysis**-Ressourcenanbieter registrieren zu lassen. Dies kann im Azure-Portal erfolgen, indem **Abonnements | Ressourcenanbieter** aufgerufen, nach ```Microsoft.ChangeAnalysis``` gesucht und der Ressourcenanbieter auf der Benutzeroberfläche registriert wird. Dies ist auch über Azure PowerShell oder die Azure CLI möglich.

Registrieren des Ressourcenanbieters über PowerShell: 

```PowerShell
# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"
```

## <a name="next-steps"></a>Nächste Schritte

- Aktivieren von Application Insights für [Azure App Services-Apps](azure-web-apps.md)
- Aktivieren von Application Insights für [in IIS gehostete virtuelle Azure-Computer und Azure-VM-Skalierungsgruppen](azure-vm-vmss-apps.md)
- Erfahren Sie mehr über [Azure Resource Graph](../../governance/resource-graph/overview.md), der die Änderungsanalyse unterstützt.