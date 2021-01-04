---
title: Von Azure Automation verwendete DNS-Einträge in Azure-Rechenzentren | Microsoft-Dokumentation
description: In diesem Artikel finden Sie die DNS-Einträge, die von Azure Automation-Features benötigt werden, wenn die Kommunikation auf eine bestimmte Azure-Region beschränkt werden soll, in der dieses Automation-Konto gehostet wird.
services: automation
ms.subservice: process-automation
ms.date: 11/25/2020
ms.topic: conceptual
ms.openlocfilehash: b4b8f48afc75c0a96937575bdad5bb884d0cb4d8
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183718"
---
# <a name="dns-records-for-azure-regions-used-by-azure-automation"></a>DNS-Einträge für von Azure Automation genutzte Azure-Regionen

Der Dienst [Azure Automation](../automation-intro.md) verwendet eine Reihe von DNS-Einträgen für Features, um eine Verbindung mit dem Dienst herzustellen. Wenn eines Ihrer Automation-Konten für eine bestimmte Region definiert ist, können Sie die Kommunikation mit diesem regionalen Rechenzentrum einschränken. Möglicherweise müssen Sie diese Einträge kennen, damit die folgenden Azure Automation-Features funktionieren, wenn sie hinter einer Firewall gehostet werden:

* Hybrid Runbook Worker
* Zustandskonfiguration
* webhooks

>[!NOTE]
>Die Registrierung von Linux Hybrid Runbook Worker schlägt mit den neuen Einträgen fehl, es sei denn, die Version ist mindestens 1.6.10.2. Sie müssen auf eine neuere Version des [Log Analytics-Agent für Linux](../../azure-monitor/platform/agent-linux.md) aktualisieren, damit der Computer eine aktualisierte Version der Workerrolle erhalten und diese neuen Einträge verwenden kann. Vorhandene VMs funktionieren ohne Probleme weiter.  

## <a name="dns-records-per-region"></a>DNS-Einträge pro Region

Die folgende Tabelle enthält den DNS-Eintrag für jede Region.

>[!NOTE]
>Obwohl die hier bereitgestellte Liste von DNS-Einträgen für Azure Automation ausgemustert wurde, bleiben sie weiterhin funktionsfähig, um Ihnen Zeit für die Migration zu den neuen Einträgen zu geben, die unter [Unterstützung für Private Link](#support-for-private-link) aufgeführt sind, und um Fehler bei Ihren Automatisierungsprozessen zu vermeiden.

| **Region** | **DNS-Eintrag** |
| --- | --- |
| Australien, Mitte |ac-jobruntimedata-prod-su1.azure-automation.net</br>ac-agentservice-prod-1.azure-automation.net |
| Australien (Osten) |ae-jobruntimedata-prod-su1.azure-automation.net</br>ae-agentservice-prod-1.azure-automation.net |
| Australien, Südosten |ase-jobruntimedata-prod-su1.azure-automation.net</br>ase-agentservice-prod-1.azure-automation.net |
| Kanada, Mitte |cc-jobruntimedata-prod-su1.azure-automation.net</br>cc-agentservice-prod-1.azure-automation.net |
| Indien, Mitte |cid-jobruntimedata-prod-su1.azure-automation.net</br>cid-agentservice-prod-1.azure-automation.net |
| USA (Ost) 2 |eus2-jobruntimedata-prod-su1.azure-automation.net</br>eus2-agentservice-prod-1.azure-automation.net |
| Japan, Osten |jpe-jobruntimedata-prod-su1.azure-automation.net</br>jpe-agentservice-prod-1.azure-automation.net |
| Nordeuropa |ne-jobruntimedata-prod-su1.azure-automation.net</br>ne-agentservice-prod-1.azure-automation.net |
| USA Süd Mitte |scus-jobruntimedata-prod-su1.azure-automation.net</br>scus-agentservice-prod-1.azure-automation.net |
| Südostasien |sea-jobruntimedata-prod-su1.azure-automation.net</br>sea-agentservice-prod-1.azure-automation.net|
| UK, Süden | uks-jobruntimedata-prod-su1.azure-automation.net</br>uks-agentservice-prod-1.azure-automation.net |
| US Government, Virginia | usge-jobruntimedata-prod-su1.azure-automation.us<br>usge-agentservice-prod-1.azure-automation.us |
| USA, Westen-Mitte | wcus-jobruntimedata-prod-su1.azure-automation.net</br>wcus-agentservice-prod-1.azure-automation.net |
| Europa, Westen |we-jobruntimedata-prod-su1.azure-automation.net</br>we-agentservice-prod-1.azure-automation.net |
| USA, Westen 2 |wus2-jobruntimedata-prod-su1.azure-automation.net</br>wus2-agentservice-prod-1.azure-automation.net |

### <a name="support-for-private-link"></a>Unterstützung für Private Link

Um [Private Link](../../private-link/private-link-overview.md) in Azure Automation zu unterstützen, wurden die DNS-Einträge für jedes unterstützte Rechenzentrum aktualisiert. Anstelle regionsspezifischer URLs sind die URLs spezifisch für das Automation-Konto.

| **Region** | **DNS-Eintrag** |
| --- | --- |
| USA, Westen-Mitte |`https://<accountId>.webhook.wcus.azure-automation.net`<br>`https://<accountId>.agentsvc.wcus.azure-automation.net`<br>`https://<accountId>.jrds.wcus.azure-automation.net` |
| USA (Westen) |`https://<accountId>.webhook.wus.azure-automation.net`<br>`https://<accountId>.agentsvc.wus.azure-automation.net`<br>`https://<accountId>.jrds.wus.azure-automation.net` |
| USA, Westen 2 |`https://<accountId>.webhook.wus2.azure-automation.net`<br>`https://<accountId>.agentsvc.wus2.azure-automation.net`<br>`https://<accountId>.jrds.wus2.azure-automation.net` |
| USA (Mitte) |`https://<accountId>.webhook.cus.azure-automation.net`<br>`https://<accountId>.agentsvc.cus.azure-automation.net`<br>`https://<accountId>.jrds.cus.azure-automation.net` |
| USA Süd Mitte |`https://<accountId>.webhook.scus.azure-automation.net`<br>`https://<accountId>.agentsvc.scus.azure-automation.net`<br>`https://<accountId>.jrds.scus.azure-automation.net` |
| USA Nord Mitte |`https://<accountId>.webhook.ncus.azure-automation.net`<br>`https://<accountId>.agentsvc.ncus.azure-automation.net`<br>`https://<accountId>.jrds.ncus.azure-automation.net` |
| East US |`https://<accountId>.webhook.eus.azure-automation.net`<br>`https://<accountId>.agentsvc.eus.azure-automation.net`<br>`https://<accountId>.jrds.eus.azure-automation.net` |
| USA (Ost) 2 |`https://<accountId>.webhook.eus2.azure-automation.net`<br>`https://<accountId>.agentsvc.eus2.azure-automation.net`<br>`https://<accountId>.jrds.eus2.azure-automation.net` |
| Kanada, Mitte |`https://<accountId>.webhook.cc.azure-automation.net`<br>`https://<accountId>.agentsvc.cc.azure-automation.net`<br>`https://<accountId>.jrds.cc.azure-automation.net` |
| Europa, Westen |`https://<accountId>.webhook.we.azure-automation.net`<br>`https://<accountId>.agentsvc.we.azure-automation.net`<br>`https://<accountId>.jrds.we.azure-automation.net` |
| Nordeuropa |`https://<accountId>.webhook.ne.azure-automation.net`<br>`https://<accountId>.agentsvc.ne.azure-automation.net`<br>`https://<accountId>.jrds.ne.azure-automation.net` |
| Südostasien |`https://<accountId>.webhook.sea.azure-automation.net`<br>`https://<accountId>.agentsvc.sea.azure-automation.net`<br>`https://<accountId>.jrds.sea.azure-automation.net` |
| Asien, Osten |`https://<accountId>.webhook.ea.azure-automation.net`<br>`https://<accountId>.agentsvc.ea.azure-automation.net`<br>`https://<accountId>.jrds.ea.azure-automation.net` |
| Indien, Mitte |`https://<accountId>.webhook.cid.azure-automation.net`<br>`https://<accountId>.agentsvc.cid.azure-automation.net`<br>`https://<accountId>.jrds.cid.azure-automation.net` |
| Japan, Osten |`https://<accountId>.webhook.jpe.azure-automation.net`<br>`https://<accountId>.agentsvc.jpe.azure-automation.net`<br>`https://<accountId>.jrds.jpe.azure-automation.net` |
| Korea, Mitte |`https://<accountId>.webhook.kc.azure-automation.net`<br>`https://<accountId>.agentsvc.kc.azure-automation.net`<br>`https://<accountId>.jrds.kc.azure-automation.net` |
| Australien, Südosten |`https://<accountId>.webhook.ase.azure-automation.net`<br>`https://<accountId>.agentsvc.ase.azure-automation.net`<br>`https://<accountId>.jrds.ase.azure-automation.net` |
| Australien (Osten) |`https://<accountId>.webhook.ae.azure-automation.net`<br>`https://<accountId>.agentsvc.ae.azure-automation.net`<br>`https://<accountId>.jrds.ae.azure-automation.net` |
| Australien, Mitte |`https://<accountId>.webhook.ac.azure-automation.net`<br>`https://<accountId>.agentsvc.ac.azure-automation.net`<br>`https://<accountId>.jrds.ac.azure-automation.net` |
| UK, Süden |`https://<accountId>.webhook.uks.azure-automation.net`<br>`https://<accountId>.agentsvc.uks.azure-automation.net`<br>`https://<accountId>.jrds.uks.azure-automation.net` |
| Frankreich, Mitte |`https://<accountId>.webhook.fc.azure-automation.net`<br>`https://<accountId>.agentsvc.fc.azure-automation.net`<br>`https://<accountId>.jrds.fc.azure-automation.net` |
| Südafrika, Norden |`https://<accountId>.webhook.san.azure-automation.net`<br>`https://<accountId>.agentsvc.san.azure-automation.net`<br>`https://<accountId>.jrds.san.azure-automation.net` |
| Brasilien Süd |`https://<accountId>.webhook.brs.azure-automation.net`<br>`https://<accountId>.agentsvc.brs.azure-automation.net`<br>`https://<accountId>.jrds.brs.azure-automation.net` |
| China, Norden |`https://<accountId>.webhook.bjb.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjb.azure-automation.cn`<br>`https://<accountId>.jrds.bjb.azure-automation.cn` |
| China, Norden 2 |`https://<accountId>.webhook.bjs2.azure-automation.cn`<br>`https://<accountId>.agentsvc.bjs2.azure-automation.cn`<br>`https://<accountId>.jrds.bjs2.azure-automation.cn` |
| China, Osten 2 |`https://<accountId>.webhook.sha2.azure-automation.cn`<br>`https://<accountId>.agentsvc.sha2.azure-automation.cn`<br>`https://<accountId>.jrds.sha2.azure-automation.cn` |
| US Government, Virginia |`https://<accountId>.webhook.usge.azure-automation.us`<br>`https://<accountId>.agentsvc.usge.azure-automation.us`<br>`https://<accountId>.jrds.usge.azure-automation.us` |
| US Gov Texas |`https://<accountId>.webhook.ussc.azure-automation.us`<br>`https://<accountId>.agentsvc.ussc.azure-automation.us`<br>`https://<accountId>.jrds.ussc.azure-automation.us` |
| US Gov Arizona |`https://<accountId>.webhook.phx.azure-automation.us`<br>`https://<accountId>.agentsvc.phx.azure-automation.us`<br>`https://<accountId>.jrds.phx.azure-automation.us` |

Ersetzen Sie `<accountId>` im DNS-Datensatz durch die GUID für Ihre Automation-Konto-ID aus dem Wert **URL**. Sie finden die erforderliche ID im Azure-Portal unter **Kontoeinstellungen** in **Schlüssel**.

![Seite für den Primärschlüssel des Automation-Kontos](./media/automation-region-dns-records/automation-account-keys.png)

Kopieren Sie den Wert hinter *accounts/* aus dem Feld **URL** – `https://<GUID>.agentsvc.<region>.azure-automation.net/accounts/<GUID>`.

> [!NOTE]
> Alle Webhook- und Agentservice-DNS-Einträge wurden auf die DNS-Einträge im neuen Format aktualisiert, um Private Link zu unterstützen. Für JRDS-DNS-Einträge werden sowohl DNS-Einträge im alten als auch im neuen Format unterstützt. Wenn Sie Private Link nicht verwenden, werden die DNS-Einträge im alten Format angezeigt, während diejenigen, die Private Link verwenden, DNS-Einträge im neuen Format anzeigen.

Es wird empfohlen, beim Definieren von [Ausnahmen](../automation-runbook-execution.md#exceptions) die aufgeführten Adressen zu verwenden. Eine Liste der IP-Adressen von Regionen anstelle von Regionsnamen erhalten Sie, wenn Sie die JSON-Datei aus dem Microsoft Download Center für die folgenden Cloudumgebungen herunterladen:

* [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519) (Azure-IP-Bereiche und -Diensttags – öffentliches Azure)
* [Azure IP Ranges and Service Tags- Azure Government](https://www.microsoft.com/download/details.aspx?id=57063) (Azure-IP-Bereiche und -Diensttags – Azure Government)
* [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=57064) (Azure-IP-Bereiche und -Diensttags – Azure Deutschland)
* [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=57062) (Azure-IP-Bereiche und -Diensttags – Azure China 21Vianet)

Die Datei mit den IP-Adressen enthält die IP-Adressbereiche, die in den Microsoft Azure-Rechenzentren verwendet werden. Sie umfasst die jeweils aktuellen bereitgestellten Compute-, SQL- und Speicherbereiche sowie alle anstehenden Änderungen an den IP-Adressbereichen. In der Datei enthaltene neue Bereiche werden frühestens nach einer Woche in den Rechenzentren verwendet.

Sie sollten die neue Datei mit den IP-Adressen jede Woche herunterladen. Führen Sie damit dann eine Aktualisierung an Ihrem Standort durch, um in Azure ausgeführte Dienste ordnungsgemäß zu identifizieren.

> [!NOTE]
> Wenn Sie Azure ExpressRoute verwenden, denken Sie daran, dass diese Datei mit den IP-Adressen zum Aktualisieren der BGP-Ankündigung (Border Gateway Protocol) von Azure-Bereichen jeweils in der ersten Woche des Monats verwendet wird.

## <a name="next-steps"></a>Nächste Schritte

* Informationen zum Behandeln von Problemen mit Ihren Hybrid Runbook Workern finden Sie unter [Problembehandlung von Hybrid Runbook Workern](../troubleshoot/hybrid-runbook-worker.md#general).

* Informationen zum Beheben von State Configuration-Problemen finden Sie unter [Beheben von State Configuration-Problemen](../troubleshoot/desired-state-configuration.md).