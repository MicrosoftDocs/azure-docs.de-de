---
title: Anzeigen und Konfigurieren von DDoS Protection-Warnungen für Azure DDoS Protection Standard
description: Erfahren Sie mehr zum Anzeigen und Konfigurieren von DDoS Protection-Warnungen für Azure DDoS Protection Standard.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 2d72027082ed2b57b28a15a736c35801ba88188c
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/08/2021
ms.locfileid: "99832520"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>Anzeigen und Konfigurieren von DDoS-Schutzwarnungen

Die Standardversion von Azure DDoS Protection liefert per Analyse von DDoS-Angriffen ausführliche Erkenntnisse zu Angriffen und ermöglicht eine Visualisierung. Kunden, die ihre virtuellen Netzwerke vor DDoS-Angriffen schützen, verfügen über detaillierte Einblicke in den Datenverkehr von Angriffen und die Aktionen, die zur Eindämmung des Angriffs durchgeführt werden. Hierfür werden Berichte zur Angriffsentschärfung und Protokolle zum Verlauf der Entschärfung genutzt. Über Azure Monitor werden umfangreiche Telemetriedaten bereitgestellt. Diese umfassen u. a. detaillierte Metriken während der Dauer eines DDoS-Angriffs. Warnungen können für beliebige durch den DDoS-Schutz verfügbar gemachte Azure Monitor-Metriken konfiguriert werden. Die Protokollierung kann zudem mit [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event Hubs), OMS Log Analytics und Azure Storage für die erweiterte Analyse über die Schnittstelle für die Azure Monitor-Diagnose integriert werden.

In diesem Tutorial lernen Sie Folgendes:

> [!div class="checklist"]
> * Konfigurieren von Warnungen in Azure Monitor
> * Konfigurieren von Warnungen im Portal
> * Anzeigen von Warnungen in Azure Security Center
> * Überprüfen und Testen Ihrer Warnungen

## <a name="prerequisites"></a>Voraussetzungen

- Wenn Sie kein Azure-Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.
- Damit Sie die Schritte in diesem Tutorial ausführen können, müssen Sie zunächst einen [Azure DDoS Protection Standard-Schutzplan](manage-ddos-protection.md) erstellen. Außerdem muss DDoS Protection Standard in einem virtuellen Netzwerk aktiviert sein.
- DDoS überwacht öffentliche IP-Adressen, die Ressourcen in einem virtuellen Netzwerk zugewiesen sind. Wenn Sie keine Ressourcen mit öffentlichen IP-Adressen im virtuellen Netzwerk besitzen, müssen Sie zunächst eine Ressource mit einer öffentlichen IP-Adresse erstellen. Sie können die öffentliche IP-Adresse aller Ressourcen (einschließlich Azure Load Balancer-Instanzen, bei denen sich die virtuellen Back-End-Computer im virtuellen Netzwerk befinden) überwachen, die über Resource Manager (nicht klassisch) bereitgestellt werden und unter [virtuelles Netzwerk für Azure-Dienste](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) aufgeführt sind, mit Ausnahme der Ressourcen für Azure App Service-Umgebungen. Um mit diesem Tutorial fortzufahren, können Sie schnell einen virtuellen [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)- oder [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)-Computer erstellen.     

## <a name="configure-alerts-through-azure-monitor"></a>Konfigurieren von Warnungen in Azure Monitor

Mit diesen Vorlagen können Sie Warnungen für alle öffentlichen IP-Adressen konfigurieren, für die Sie die Diagnoseprotokollierung aktiviert haben. Um diese Vorlagen für Warnungen verwenden zu können, benötigen Sie daher zunächst einen Log Analytics-Arbeitsbereich, in dem Diagnoseeinstellungen aktiviert sind. Weitere Informationen finden Sie unter [Anzeigen und Konfigurieren der DDoS-Diagnoseprotokollierung](diagnostic-logging.md).

### <a name="azure-monitor-alert-rule"></a>Azure Monitor-Warnungsregel

Diese [Azure Monitor-Warnregel](https://aka.ms/DDOSmitigationstatus) führt eine einfache Abfrage aus, um festzustellen, wann eine aktive DDoS-Entschärfung auftritt. Dies deutet auf einen potenziellen Angriff hin. Aktionsgruppen dienen zum Aufrufen von Aktionen als Ergebnis der Warnung.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>Azure Monitor-Warnungsregel mit Logik-App

Diese [Vorlage](https://aka.ms/ddosalert) stellt die erforderlichen Komponenten einer angereicherten DDoS-Entschärfungswarnung bereit: Azure Monitor-Warnungsregel, Aktionsgruppe und Logik-App. Das Ergebnis des Vorgangs ist eine E-Mail-Warnung mit Details zur angegriffenen IP-Adresse, einschließlich Informationen zur mit der IP-Adresse verbundene Ressource. Der Besitzer der Ressource wird neben dem Sicherheitsteam als Empfänger der E-Mail hinzugefügt. Es erfolgt auch ein grundlegender Test der Verfügbarkeit der Anwendung, dessen Ergebnisse in die E-Mail-Warnung aufgenommen werden.

[![In Azure bereitstellen](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>Konfigurieren von Warnungen im Portal

Mithilfe der Warnungskonfiguration von Azure Monitor können Sie jede der verfügbaren DDoS -Schutzmetriken zum Warnen auswählen, wenn während eines Angriffs eine aktive Entschärfung vorhanden ist. 

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, und navigieren Sie zu Ihrem DDoS Protection-Plan.
2. Wählen Sie unter **Überwachung** die Option **Metriken** aus.
3. Wählen Sie auf der grauen Navigationsleiste **Neue Warnungsregel** aus. 
4. Geben Sie Ihre eigenen Werte bzw. die folgenden Beispielwerte ein. Übernehmen Sie die restlichen Standardwerte, und wählen Sie dann **Warnungsregel erstellen** aus:

    |Einstellung                  |Wert                                                                                               |
    |---------                |---------                                                                                           |
    | Bereich                   | Klicken Sie auf **Ressource auswählen**. </br> Wählen Sie das **Abonnement** mit der öffentlichen IP-Adresse aus, die Sie protokollieren möchten. Wählen Sie **Öffentliche IP-Adresse** als **Ressourcentyp** aus. Wählen Sie anschließend die öffentliche IP-Adresse aus, für die Sie Metriken protokollieren möchten. </br> Wählen Sie **Fertig** aus. | 
    | Bedingung | Wählen Sie **Bedingung auswählen** aus. </br> Wählen Sie unter „Signalname“ die Option **Unter DDoS-Angriff oder nicht** aus. </br> Wählen Sie **Größer oder gleich** als **Operator** aus. </br> Wählen Sie **Maximum** für **Aggregationstyp** aus. </br> Geben Sie *1* in **Schwellenwert** ein. Bei der Metrik **Unter DDoS-Angriff oder nicht** bedeutet **0**, dass Sie nicht angegriffen werden, während **1** bedeutet, dass Sie angegriffen werden. </br> Wählen Sie **Fertig** aus. | 
    | Aktionen | Wählen Sie **Aktionsgruppen hinzufügen** aus. </br> Wählen Sie **Aktionsgruppe erstellen** aus. </br> Wählen Sie unter **Benachrichtigungen** als **Benachrichtigungstyp** die Option **E-Mail/SMS/Push/Sprachanruf** aus. </br> Geben Sie _MyUnderAttackEmailAlert_ in **Name** ein. </br> Klicken Sie auf die Schaltfläche „Bearbeiten“. Wählen Sie dann **E-Mail** und so viele der folgenden Optionen, wie Sie benötigen, und anschließend **OK** aus. </br> Klicken Sie auf **Überprüfen + erstellen**. | 
    | Warnungsregeldetails | Geben Sie _MyDdosAlert_ unter **Name der Warnungsregel** ein. |

Innerhalb weniger Minuten nach Erkennung eines Angriffs erhalten Sie eine E-Mail von Azure Monitor-Metriken, die der folgenden Abbildung ähnelt:

![Angriffswarnung](./media/manage-ddos-protection/ddos-alert.png)

Sie können auch mehr über das [Konfigurieren von Webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) und über [Logik-Apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) für die Erstellung von Warnungen erfahren.

## <a name="view-alerts-in-azure-security-center"></a>Anzeigen von Warnungen in Azure Security Center

Im Azure Security Center sind zahlreiche [Sicherheitswarnungen](../security-center/security-center-managing-and-responding-alerts.md) vorhanden, die Informationen zum Untersuchen und Beheben von Problemen enthalten. Dieses Feature bietet eine einheitliche Ansicht für die Warnungen, einschließlich Warnungen für DDoS-Angriffe und Aktionen zum zeitnahen Abwehren dieser Angriffe.
Für die Erkennung und Abwehr von DDoS-Angriffen werden insbesondere zwei Warnungen ausgelöst:

- **DDoS Attack detected for Public IP** (DDoS-Angriff für öffentliche IP-Adresse erkannt): Diese Warnung wird generiert, wenn der DDoS-Schutzdienst einen DDoS-Angriff auf eine Ihrer öffentlichen IP-Adressen erkennt.
- **DDoS Attack mitigated for Public IP** (DDoS-Angriff für öffentliche IP-Adresse abgewehrt): Diese Warnung wird generiert, wenn ein Angriff auf eine öffentliche IP-Adresse abgewehrt wurde.
Öffnen Sie **Security Center** im Azure-Portal, um die Warnungen anzuzeigen. Klicken Sie unter **Bedrohungsschutz** auf **Sicherheitswarnungen**. Auf dem folgenden Screenshot sehen Sie ein Beispiel für eine Warnung zu einem DDoS-Angriff.

![DDoS-Warnung im Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

Die Warnungen enthalten allgemeine Informationen zur angegriffenen öffentlichen IP-Adresse, zum geografischen Raum und zur Bedrohung sowie Schritte zur Behebung.

## <a name="validate-and-test"></a>Überprüfen und Testen

Informationen zum Simulieren eines DDoS-Angriffs zum Überprüfen Ihrer Warnungen finden Sie unter [Durchführen von Simulationstests im Abschnitt „Überwachen und Überprüfen“](test-through-simulations.md).

## <a name="next-steps"></a>Nächste Schritte

In diesem Tutorial haben Sie gelernt, wie die folgenden Aufgaben ausgeführt werden:

- Konfigurieren von Warnungen in Azure Monitor
- Konfigurieren von Warnungen im Portal
- Anzeigen von Warnungen in Azure Security Center
- Überprüfen und Testen Ihrer Warnungen

Informationen zum Testen und Simulieren eines DDoS-Angriffs finden Sie in der Anleitung zum Durchführen von Simulationstests:

> [!div class="nextstepaction"]
> [Durchführen von Simulationstests](test-through-simulations.md)
