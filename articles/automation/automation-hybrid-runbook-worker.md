---
title: Azure Automation – Hybrid Runbook Worker
description: In diesem Artikel werden Informationen zum Installieren und Verwenden des Azure Automation-Features „Hybrid Runbook Worker“ bereitgestellt, mit dem Sie Runbooks auf Computern in Ihrem lokalen Datencenter oder Cloudanbieter ausführen können.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/05/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 785cf5159615b4a81740e853f2b513f0e6d74aec
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65198469"
---
# <a name="automate-resources-in-your-datacenter-or-cloud-by-using-hybrid-runbook-worker"></a>Automatisieren von Ressourcen im Datencenter oder in der Cloud mit Hybrid Runbook Worker

Runbooks in Azure Automation haben möglicherweise keinen Zugriff auf Ressourcen in anderen Clouds oder in Ihrer lokalen Umgebung, da sie auf der Azure-Cloudplattform ausgeführt werden. Sie können mit dem Azure Automation-Feature „Hybrid Runbook Worker“ Runbooks direkt auf dem Computer ausführen, der die Rolle hostet, und außerhalb für Ressourcen in der Umgebung zur Verwaltung dieser lokalen Ressourcen. Runbooks werden in Azure Automation gespeichert und verwaltet und an einzelne oder mehrere zugewiesene Computer übermittelt.

Diese Funktionalität wird in der folgenden Abbildung veranschaulicht:

![Übersicht über Hybrid Runbook Worker](media/automation-hybrid-runbook-worker/automation.png)

Jeder Hybrid-Runbook-Worker ist ein Mitglied einer Hybrid-Runbook-Workergruppe, die Sie beim Installieren des Agents angeben. Eine Gruppe kann einen einzelnen Agent umfassen, aber für Hochverfügbarkeit können Sie mehrere Agents installieren.

Wenn Sie ein Runbook auf einen Hybrid Runbook Worker starten, geben Sie die Gruppe an, in der das Runbook ausgeführt werden soll. Jeder Worker in der Gruppe ruft Azure Automation ab, um festzustellen, ob Aufträge verfügbar sind. Wenn ein Auftrag verfügbar ist, übernimmt ihn der erste verfügbare Worker. Die Verarbeitungszeit der Auftragswarteschlange hängt vom Hybrid Worker-Hardwareprofil und der Auslastung ab. Sie können keinen bestimmten Worker angeben. Für Hybrid Runbook Worker gelten viele der Grenzwerte von Azure-Sandboxes nicht. Sie unterliegen nicht den gleichen Beschränkungen für Speicherplatz, Arbeitsspeicher oder Netzwerksockets. Hybrid Runbook Worker werden nur durch die eigenen Ressourcen beschränkt. Auch das 180-minütige Zeitlimit für die [gleichmäßige Verteilung](automation-runbook-execution.md#fair-share), dem Azure-Sandboxes unterliegen, gilt bei Hybrid Runbook Workern nicht. Weitere Informationen zu den Diensteinschränkungen für Azure-Sandboxes und Hybrid Runbook Worker finden Sie auf der [Seite mit auftragsspezifischen Grenzwerten](../azure-subscription-service-limits.md#automation-limits).

## <a name="install-a-hybrid-runbook-worker"></a>Installieren eines Hybrid Runbook Workers

Der Vorgang zum Installieren eines Hybrid Runbook Workers ist vom Betriebssystem abhängig. Die folgende Tabelle enthält Links zu den Methoden, die für die Installation verwendet werden können.

Zum Installieren und Konfigurieren eines Windows Hybrid Runbook Workers können Sie zwei verschiedene Methoden verwenden. Die empfohlene Methode besteht darin, ein Automation-Runbook zu verwenden, um den Prozess zum Konfigurieren eines Windows-Computers vollständig zu automatisieren. Bei der zweiten Methode wird die Rolle Schritt für Schritt manuell installiert und konfiguriert. Bei Linux-Computern führen Sie zur Installation des Agents auf dem Computer ein Python-Skript aus.

|Betriebssystem  |Bereitstellungstypen  |
|---------|---------|
|Windows     | [PowerShell](automation-windows-hrw-install.md#automated-deployment)<br>[Manuell](automation-windows-hrw-install.md#manual-deployment)        |
|Linux     | [Python](automation-linux-hrw-install.md#installing-a-linux-hybrid-runbook-worker)        |

> [!NOTE]
> Um die Konfiguration der Server zu verwalten, die die Hybrid Runbook Worker-Rolle mit DSC (Desired State Configuration) unterstützen, müssen Sie die Server als DSC-Knoten hinzufügen. Weitere Informationen zu ihrer Integration für die Verwaltung durch DSC finden Sie unter [Integrieren von Computern für die Verwaltung durch Azure Automation DSC](automation-dsc-onboarding.md).
>
>Wenn Sie die [Lösung für die Updateverwaltung](automation-update-management.md) aktivieren, werden alle mit dem Azure Log Analytics-Arbeitsbereich verbundenen Computer automatisch als Hybrid Runbook Worker konfiguriert, um Runbooks zu unterstützen, die in dieser Lösung enthalten sind. Es wird aber keine Registrierung des Computers für Hybrid Worker-Gruppen ausgeführt, die in Ihrem Automation-Konto bereits definiert wurden. Der Computer kann einer Hybrid Runbook Worker-Gruppe in Ihrem Automation-Konto hinzugefügt werden, um Automation-Runbooks zu unterstützen, solange Sie sowohl für die Lösung als auch die Mitgliedschaft in der Hybrid Runbook Worker-Gruppe dasselbe Konto verwenden. Diese Funktionalität wurde in Version 7.2.12024.0 von Hybrid Runbook Worker hinzugefügt.

Bevor Sie mit der Bereitstellung eines Hybrid Runbook Worker beginnen, lesen Sie die [Informationen zum Planen Ihres Netzwerks](#network-planning). Lesen Sie nach dem erfolgreichen Bereitstellen eines Workers [Running runbooks on a Hybrid Runbook Worker (Ausführen von Runbooks auf einem Hybrid Runbook Worker)](automation-hrw-run-runbooks.md), um zu erfahren, wie Sie Ihre Runbooks für die Automatisierung von Prozessen in Ihrem lokalen Datencenter oder in einer anderen Cloudumgebung konfigurieren.

## <a name="remove-a-hybrid-runbook-worker"></a>Entfernen eines Hybrid Runbook Workers

Abhängig von Ihren Anforderungen können Sie mindestens einen Hybrid Runbook Worker aus einer Gruppe entfernen, oder Sie können die Gruppe entfernen. Wenn Sie einen Hybrid Runbook Worker von einem lokalen Computer entfernen möchten, führen Sie die folgenden Schritte aus:

1. Navigieren Sie im Azure-Portal zu Ihrem Automation-Konto.
2. Wählen Sie unter **Kontoeinstellungen** die Option **Schlüssel** aus, und notieren Sie sich die Werte für **URL** und **Primärer Zugriffsschlüssel**. Sie benötigen diese Informationen im nächsten Schritt.

### <a name="windows"></a>Windows

Öffnen Sie eine PowerShell-Sitzung im Administratormodus, und führen Sie den folgenden Befehl aus: Verwenden Sie den Schalter **-Verbose** , um ein ausführliches Protokoll zum Entfernungsvorgang zu erhalten.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey>
```

Verwenden Sie zum Entfernen veralteter Computer aus Ihrer Hybrid Worker-Gruppe den optionalen Parameter `machineName`.

```powershell-interactive
Remove-HybridRunbookWorker -url <URL> -key <PrimaryAccessKey> -machineName <ComputerName>
```

### <a name="linux"></a>Linux

Sie können den Befehl `ls /var/opt/microsoft/omsagent` auf dem Hybrid Runbook Worker verwenden, um die Arbeitsbereichs-ID abzurufen. Es gibt einen Ordner in dem Verzeichnis, in dem der Name des Ordners die Arbeitsbereichs-ID ist.

```bash
sudo python onboarding.py --deregister --endpoint="<URL>" --key="<PrimaryAccessKey>" --groupname="Example" --workspaceid="<workspaceId>"
```

> [!NOTE]
> Dieser Code entfernt nicht Microsoft Monitoring Agent von diesem Computer, sondern nur die Funktionen und die Konfiguration der Hybrid Runbook Worker-Rolle.

## <a name="remove-a-hybrid-worker-group"></a>Entfernen einer Hybrid Worker-Gruppe

Um eine Gruppe zu entfernen, müssen Sie zunächst den Hybrid Runbook Worker von allen Computern entfernen, die Mitglied der Gruppe sind. Verwenden Sie zu diesem Zweck das weiter oben beschriebene Verfahren. Führen Sie dann die folgenden Schritte aus, um die Gruppe zu entfernen:

1. Wählen Sie im Azure-Portal das Automation-Konto aus.
2. Wählen Sie unter **Prozessautomatisierung** die Option **Hybrid Worker-Gruppen** aus. Wählen Sie die Gruppe aus, die Sie löschen möchten. Die Seite „Eigenschaften“ für diese Gruppe wird angezeigt.

   ![Eigenschaftenseite](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-group-properties.png)

3. Wählen Sie auf der Seite „Eigenschaften“ der ausgewählten Gruppe die Option **Löschen** aus. Eine Meldung fordert Sie zur Bestätigung dieser Aktion auf. Klicken Sie auf **Ja**, wenn Sie den Vorgang wirklich fortsetzen möchten.

   ![Bestätigungsmeldung](media/automation-hybrid-runbook-worker/automation-hybrid-runbook-worker-confirm-delete.png)

   Der Abschluss dieses Vorgangs kann mehrere Sekunden in Anspruch nehmen. Sie können den Fortschritt im Menü unter **Benachrichtigungen** nachverfolgen.

## <a name="network-planning"></a>Konfigurieren des Netzwerks

### <a name="hybrid-worker-role"></a>Hybrid Worker-Rolle

Der Hybrid Runbook Worker benötigt Zugriff auf die in diesem Abschnitt angegebene Portnummer und die URLs, damit er eine Verbindung mit Azure Automation herstellen und sich bei dem Dienst registrieren kann. Dieser Zugriff wird zusätzlich zu den [für Microsoft Monitoring Agent erforderlichen Ports und URLs](../azure-monitor/platform/agent-windows.md) für das Herstellen einer Verbindung mit Azure Monitor-Protokolle benötigt.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Wenn Sie einen Proxyserver für die Kommunikation zwischen dem Agent und dem Azure Automation-Dienst verwenden, müssen Sie sicherstellen, dass auf die entsprechenden Ressourcen zugegriffen werden kann. Das Zeitlimit für Anforderungen vom Hybrid Runbook Worker und von den Automation-Diensten beträgt 30 Sekunden. Nach drei Versuchen schlägt die Anforderung fehl. Wenn Sie eine Firewall verwenden, um den Zugriff auf das Internet einzuschränken, müssen Sie die Firewall so konfigurieren, dass der Zugriff möglich ist. Wenn Sie das Log Analytics-Gateway als Proxy verwenden, achten Sie darauf, dass es für Hybrid Worker konfiguriert ist. Eine entsprechende Anleitung finden Sie unter [Konfigurieren des Log Analytics-Gateways für Automation Hybrid Worker](https://docs.microsoft.com/azure/log-analytics/log-analytics-oms-gateway).

Nachfolgend sind der Port und die URLs aufgeführt, die für die Kommunikation zwischen der Hybrid Runbook Worker-Rolle und Automation erforderlich sind:

* Port: Für ausgehenden Zugriff auf das Internet ist nur TCP 443 erforderlich.
* Globale URL: *.azure-automation.net
* Globale URL von „US Gov Virginia“: *.azure-automation.us
* Agent-Dienst: https://\<Arbeitsbereichs-ID\>.agentsvc.azure-automation.net

Es wird empfohlen, beim Definieren von Ausnahmen die aufgeführten Adressen zu verwenden. Für IP-Adressen können Sie die [IP-Bereiche des Microsoft Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653) herunterladen. Diese Datei mit den jeweils aktuellen bereitgestellten Bereichen und allen anstehenden Änderungen an den IP-Adressbereichen wird wöchentlich veröffentlicht.

Wenn eines Ihrer Automation-Konten für eine bestimmte Region definiert ist, können Sie die Kommunikation mit diesem regionalen Rechenzentrum einschränken. Die folgende Tabelle enthält den DNS-Eintrag für jede Region:

| **Region** | **DNS-Eintrag** |
| --- | --- |
| USA, Westen-Mitte | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| USA Süd Mitte |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| USA (Ost) 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| USA, Westen 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |
| Kanada, Mitte |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Europa, Westen |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| Nordeuropa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| Südostasien |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| Indien, Mitte |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| Japan, Osten |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Australien (Osten) |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Australien, Südosten |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| UK, Süden | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Government, Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |

Wenn Sie eine Liste mit IP-Adressen der Regionen anstelle ihrer Namen benötigen, können Sie die XML-Datei mit den [IP-Adressen der Azure-Rechenzentren](https://www.microsoft.com/download/details.aspx?id=41653) aus dem Microsoft Download Center herunterladen.

> [!NOTE]
> Die XML-Datei mit den IP-Adressen der Azure-Rechenzentren enthält die IP-Adressbereiche, die in den Microsoft Azure-Rechenzentren verwendet werden. Die Datei enthält die Bereiche für Compute, SQL und Storage.
>
>Eine aktualisierte Datei wird wöchentlich veröffentlicht. Die Datei enthält die derzeit bereitgestellten Bereichen und alle anstehenden Änderungen an den IP-Adressbereichen. In der Datei enthaltene neue Bereiche werden frühestens nach einer Woche in den Rechenzentren verwendet.
>
> Sie sollten die neue XML-Datei jede Woche herunterladen. Führen Sie damit dann eine Aktualisierung an Ihrem Standort durch, um in Azure ausgeführte Dienste ordnungsgemäß zu identifizieren. Benutzer von Azure ExpressRoute sollten beachten, dass diese Datei zum Aktualisieren der BGP-Ankündigung (Border Gateway Protocol) von Azure-Bereichen jeweils in der ersten Woche des Monats verwendet wird.

### <a name="update-management"></a>Updateverwaltung

Neben den Standardadressen und -ports, die der Hybrid Runbook Worker erfordert, sind folgende Adressen speziell für die Updateverwaltung erforderlich. Die Kommunikation mit diesen Adressen erfolgt über Port 443.

|Azure – Öffentlich  |Azure Government  |
|---------|---------|
|*.ods.opinsights.azure.com     |*.ods.opinsights.azure.us         |
|*.oms.opinsights.azure.com     | *.oms.opinsights.azure.us        |
|*.blob.core.windows.net|*.blob.core.usgovcloudapi.net|

## <a name="next-steps"></a>Nächste Schritte

* Um zu erfahren, wie Sie Ihre Runbooks für die Automatisierung von Prozessen in Ihrem lokalen Rechenzentrum oder in einer anderen Cloudumgebung konfigurieren, lesen Sie sich [Ausführen von Runbooks auf einem Hybrid Runbook Worker](automation-hrw-run-runbooks.md) durch.
* Informationen zur Problembehandlung Ihrer Hybrid Runbook Worker finden Sie unter [Problembehandlung von Hybrid Runbook Workers](troubleshoot/hybrid-runbook-worker.md#general).

