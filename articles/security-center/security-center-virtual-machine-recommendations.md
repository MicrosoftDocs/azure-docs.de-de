---
title: Empfehlungen für virtuelle Computer in Azure Security Center | Microsoft-Dokumentation
description: In diesem Dokument werden die Azure Security Center-Empfehlungen zum Schützen Ihrer Computer und virtuellen Computer sowie Ihrer Web-Apps und App Service-Umgebungen erläutert.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: f5ce7f62-7b12-4bc0-b418-4a2f9ec49ca1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/12/2018
ms.author: rkarlin
ms.openlocfilehash: a4aaf440856746895a31914aeee2bddec2ce23f6
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60544981"
---
# <a name="understand-azure-security-center-resource-recommendations"></a>Grundlegendes zu Azure Security Center-Empfehlungen für Ressourcen


## <a name="recommendations"></a>Empfehlungen
Den folgenden Tabellen können Sie entnehmen, welche Empfehlungen für Compute Services und App Services verfügbar sind und welche Aktionen jeweils ausgeführt werden, wenn Sie sie anwenden.

### <a name="computers"></a>Computer
| Empfehlung | BESCHREIBUNG |
| --- | --- |
| [Sammlung von Daten für Abonnements aktivieren](security-center-enable-data-collection.md) |Empfiehlt Ihnen, in der Sicherheitsrichtlinie die Datensammlung für alle Abonnements und alle virtuellen Computer in Ihren Abonnements zu aktivieren. |
| [Aktivieren der Verschlüsselung für das Azure Storage-Konten](security-center-enable-encryption-for-storage-account.md) | Empfiehlt die Aktivierung von Azure Storage Service Encryption für ruhende Daten. Storage Service Encryption (SSE) verschlüsselt die Daten, wenn diese in Azure Storage geschrieben werden, und entschlüsselt sie vor dem Abrufen. SSE steht zurzeit nur für den Azure Blob-Dienst zur Verfügung und kann für Blockblobs, Seitenblobs und Anfügeblobs verwendet werden. Weitere Informationen finden Sie unter [Storage Service Encryption für ruhende Daten](../storage/common/storage-service-encryption.md).</br>SSE wird nur für Resource Manager-Speicherkonten unterstützt. Klassische Speicherkonten werden zurzeit nicht unterstützt. Weitere Informationen zum klassischen Bereitstellungsmodell und zum Resource Manager-Bereitstellungsmodell finden Sie unter [Azure-Bereitstellungsmodellen](../azure-classic-rm.md). |
| [Optimieren von Sicherheitskonfigurationen](security-center-remediate-os-vulnerabilities.md) |Empfiehlt Ihnen, Ihre Betriebssystemkonfigurationen mit den empfohlenen Sicherheitskonfigurationsregeln in Einklang zu bringen und beispielsweise nicht zuzulassen, dass Kennwörter gespeichert werden. |
| [Systemupdates anwenden](security-center-apply-system-updates.md) |Empfiehlt Ihnen, fehlende Systemupdates und kritische Updates für virtuelle Computer bereitzustellen. |
| [JIT-Netzwerkzugriffssteuerung anwenden](security-center-just-in-time.md) | Empfiehlt Ihnen, Just-In-Time-VM-Zugriff anzuwenden. Das Just-In-Time-Feature befindet sich in der Vorschauphase und ist im Standard-Tarif von Security Center verfügbar. Weitere Informationen zu den Tarifen von Security Center finden Sie unter [Preise](security-center-pricing.md). |
| [Neustart nach Systemupdates](security-center-apply-system-updates.md#reboot-after-system-updates) |Empfiehlt Ihnen, einen virtuellen Computer neu zu starten, um die Anwendung von Systemupdates abzuschließen. |
| [Endpoint Protection installieren](security-center-install-endpoint-protection.md) |Empfiehlt Ihnen, Antischadsoftware für virtuelle Maschinen bereitzustellen (nur Windows-VMs). |
| [VM-Agent aktivieren](security-center-enable-vm-agent.md) |Ermöglicht Ihnen, anzuzeigen, welche VMs den VM-Agent benötigen. Der VM-Agent muss auf virtuellen Maschinen installiert werden, um das Überprüfen von Patches, Überprüfen von Grundregeln und Antischadsoftware bereitzustellen. Der VM-Agent wird standardmäßig für virtuelle Maschinen installiert, die vom Azure Marketplace bereitgestellt werden. Der Artikel [VM-Agent und Erweiterungen – Teil 2](https://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) enthält Informationen zum Installieren des VM-Agents. |
| [Datenträgerverschlüsselung anwenden](security-center-apply-disk-encryption.md) |Empfiehlt Ihnen, dass Sie Ihre VM-Datenträger per Azure Disk Encryption (Windows- und Linux-VMs) verschlüsseln. Die Verschlüsselung wird sowohl für die Betriebssystem- als auch für die Datenvolumes auf Ihrer VM empfohlen. |
| [Betriebssystemversion aktualisieren](security-center-update-os-version.md) |Die Empfehlung lautet, dass Sie die Betriebssystemversion für den Clouddienst auf die aktuelle Version aktualisieren sollten, die für Ihre Betriebssystemfamilie verfügbar ist.  Weitere Informationen zu Cloud Services finden Sie unter [Übersicht über Cloud Services](../cloud-services/cloud-services-choose-me.md). |
| [Sicherheitsrisikobewertung nicht installiert](security-center-vulnerability-assessment-recommendations.md) |Empfiehlt die Installation einer Lösung zur Sicherheitsrisikobewertung auf dem virtuellen Computer. |
| [Sicherheitsrisiken beheben](security-center-vulnerability-assessment-recommendations.md#review-the-recommendation) |Ermöglicht das Anzeigen von System- und Anwendungssicherheitslücken, die von der auf dem virtuellen Computer installierten Lösung zur Sicherheitsrisikobewertung erkannt werden. |

### App Services <a name="app-services"></a>
| Empfehlung | BESCHREIBUNG |
| --- | --- |
| Zugriff auf App Service nur über HTTPS gestatten | Empfiehlt, den Zugriff auf App Service nur auf HTTPS zu beschränken. |
| WebSockets für Webanwendung deaktivieren| Empfiehlt, die Verwendung von WebSockets innerhalb von Webanwendungen sorgfältig zu prüfen.  Das WebSockets-Protokoll ist für verschiedene Arten von Sicherheitsrisiken anfällig. |
| Benutzerdefinierte Domänen für Webanwendung verwenden | Empfiehlt, benutzerdefinierte Domänen zu verwenden, damit eine Webanwendung vor gängigen Angriffen wie Phishing und anderen DNS-bezogenen Angriffen geschützt wird. |
| IP-Einschränkungen für Webanwendung konfigurieren | Empfiehlt, eine Liste mit IP-Adressen zu definieren, die auf Ihre Anwendung zugreifen dürfen.  Mithilfe von IP-Einschränkungen kann eine Webanwendung vor gängigen Angriffen geschützt werden. |
| Nicht allen („*“) Ressourcen Zugriff auf Ihre Anwendung erteilen | Rät davon ab, den WEBSITE_LOAD_CERTIFICATES-Parameter auf „ *“ festzulegen. Durch die Einstellung des Parameters auf „* “ werden alle Zertifikate in den persönlichen Zertifikatspeicher Ihrer Webanwendungen geladen.  Dies kann zum Missbrauch des Prinzips der „geringsten Rechte“ führen, da es unwahrscheinlich ist, dass die Website Zugriff auf sämtliche Zertifikate zur Laufzeit benötigt. |
| Nicht jeder Ressource den Zugriff auf Ihre Anwendung über CORS gestatten | Empfiehlt, nur erforderlichen Domänen die Interaktion mit Ihrer Webanwendung zu ermöglichen. Bei der Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) sollte nicht allen Domänen gestattet werden, auf Ihre Webanwendung zuzugreifen. |
| Die neueste unterstützte .NET Framework-Version für Webanwendungen verwenden | Empfiehlt, die neueste .NET Framework-Version für die neuesten Sicherheitsklassen zu verwenden. Die Verwendung von älteren Klassen und Typen kann die Anfälligkeit Ihrer Anwendung für Sicherheitsrisiken erhöhen. |
| Die neueste unterstützte Java-Version für Webanwendungen verwenden | Empfiehlt, die neueste Java-Version für die neuesten Sicherheitsklassen zu verwenden. Die Verwendung von älteren Klassen und Typen kann die Anfälligkeit Ihrer Anwendung für Sicherheitsrisiken erhöhen. |
| Die neueste unterstützte PHP-Version für Webanwendungen verwenden | Empfiehlt, die neueste PHP-Version für die neuesten Sicherheitsklassen zu verwenden. Die Verwendung von älteren Klassen und Typen kann die Anfälligkeit Ihrer Anwendung für Sicherheitsrisiken erhöhen. |
| [Add a web application firewall](security-center-add-web-application-firewall.md) |Empfiehlt Ihnen, eine Web Application Firewall (WAF) für Webendpunkte bereitzustellen. Eine WAF-Empfehlung wird für jede öffentlich zugängliche IP-Adresse (sowohl auf Instanzebene als auch mit Lastenausgleich) angezeigt, die über eine zugeordnete Netzwerksicherheitsgruppe mit offenen eingehenden Webports (80, 443) verfügt.</br></br>Security Center empfiehlt die Bereitstellung einer WAF zum Schutz vor Angriffen auf Ihre Webanwendungen auf virtuellen Computern und in der App Service-Umgebung. Eine App Service-Umgebung ist eine Option des [Premium](https://azure.microsoft.com/pricing/details/app-service/)-Tarifs von Azure App Service, die eine vollständig isolierte und dedizierte Umgebung zur sicheren Ausführung von Azure App Service-Apps bereitstellt. Weitere Informationen zu ASE finden Sie unter [Dokumentation zur App Service-Umgebung](../app-service/environment/intro.md).</br></br>Sie können mehrere Webanwendungen in Security Center schützen, indem Sie diese Anwendungen Ihren vorhandenen WAF-Bereitstellungen hinzufügen. |
| [Finalize application protection (Anwendungsschutz abschließen) (Anwendungsschutz abschließen)](security-center-add-web-application-firewall.md#finalize-application-protection) |Um die Konfiguration einer WAF abzuschließen, muss Datenverkehr an das WAF-Gerät umgeleitet werden. Nach dieser Empfehlung werden die erforderlichen Setupänderungen vorgenommen. |
| Die neueste unterstützte Node.js-Version für Webanwendungen verwenden | Empfiehlt, die neueste Node.js-Version für die neuesten Sicherheitsklassen zu verwenden. Die Verwendung von älteren Klassen und Typen kann die Anfälligkeit Ihrer Anwendung für Sicherheitsrisiken erhöhen. |
| Nicht jeder Ressource den Zugriff auf Ihre Funktions-App über CORS gestatten | Empfiehlt, nur erforderlichen Domänen die Interaktion mit Ihrer Webanwendung zu ermöglichen. Bei der Ressourcenfreigabe zwischen verschiedenen Ursprüngen (Cross-Origin Resource Sharing, CORS) sollte nicht allen Domänen gestattet werden, auf Ihre Funktionsanwendung zuzugreifen. |
| Benutzerdefinierte Domänen für Funktions-App verwenden | Empfiehlt, benutzerdefinierte Domänen zu verwenden, damit eine Funktions-App vor gängigen Angriffen wie Phishing und anderen DNS-bezogenen Angriffen geschützt wird. |
| IP-Einschränkungen für Funktions-App konfigurieren | Empfiehlt, eine Liste mit IP-Adressen zu definieren, die auf Ihre Anwendung zugreifen dürfen. Mithilfe von IP-Einschränkungen kann eine Funktions-App vor gängigen Angriffen geschützt werden. |
| Zugriff auf Funktions-App nur über HTTPS gestatten | Empfiehlt, den Zugriff auf Funktions-Apps nur über HTTPS zu beschränken. |
| Remotedebuggen sollte für Funktions-Apps deaktiviert werden | Empfiehlt, das Debuggen für Funktions-Apps zu deaktivieren, wenn Sie sie nicht mehr benötigen. Für das Remotedebuggen müssen die eingehenden Ports für eine Funktions-App geöffnet sein. |
| WebSockets für Funktions-App deaktivieren | Empfiehlt, die Verwendung von Websockets innerhalb von Funktions-Apps sorgfältig zu prüfen. Das WebSockets-Protokoll ist für verschiedene Arten von Sicherheitsrisiken anfällig. |


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen zu Empfehlungen für andere Arten von Azure-Ressourcen finden Sie in den folgenden Themen:

* [Überwachen von Identität und Zugriff in Azure Security Center](security-center-identity-access.md)
* [Schützen Ihres Netzwerks in Azure Security Center](security-center-network-recommendations.md)
* [Schützen Ihres Azure SQL-Diensts in Azure Security Center](security-center-sql-service-recommendations.md)

Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Schützen von Computern und Anwendungen im Azure Security Center](security-center-virtual-machine-protection.md)
* [Festlegen von Sicherheitsrichtlinien in Azure Security Center:](tutorial-security-policy.md) Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
* [Verwalten von und Reagieren auf Sicherheitswarnungen in Azure Security Center:](security-center-managing-and-responding-alerts.md) Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
* [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md) : Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.

<!--Image references-->
[1]: ./media/security-center-virtual-machine-recommendations/overview.png
[2]: ./media/security-center-virtual-machine-recommendations/compute.png
[3]: ./media/security-center-virtual-machine-recommendations/monitoring-agent-health-issues.png
[4]: ./media/security-center-virtual-machine-recommendations/compute-recommendations.png
[5]: ./media/security-center-virtual-machine-recommendations/apply-system-updates.png
[6]: ./media/security-center-virtual-machine-recommendations/missing-update-details.png
[7]: ./media/security-center-virtual-machine-recommendations/vm-computers.png
[8]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon1.png
[9]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon2.png
[10]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon3.png
[11]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-icon4.png
[12]: ./media/security-center-virtual-machine-recommendations/filter.png
[13]: ./media/security-center-virtual-machine-recommendations/vm-detail.png
[14]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig1-new006-2017.png
[15]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new3.png
[16]: ./media/security-center-virtual-machine-recommendations/security-center-monitoring-fig8-new4.png
[17]: ./media/security-center-virtual-machine-recommendations/app-services.png
[18]: ./media/security-center-virtual-machine-recommendations/ase.png
[19]: ./media/security-center-virtual-machine-recommendations/web-app.png
[20]: ./media/security-center-virtual-machine-recommendations/recommendation.png
[21]: ./media/security-center-virtual-machine-recommendations/recommendation-desc.png
[22]: ./media/security-center-virtual-machine-recommendations/passed-assessment.png
[23]: ./media/security-center-virtual-machine-recommendations/healthy-resources.png
[24]: ./media/security-center-virtual-machine-recommendations/function-app.png
