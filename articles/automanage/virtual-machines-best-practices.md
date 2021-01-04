---
title: Bewährte Methoden für die automatische Azure-Verwaltung von virtuellen Computern
description: Erfahren Sie mehr über die bewährten Methoden für die automatische Azure-Verwaltung von virtuellen Computern für Dienste, für die automatisch das Onboarding und die Konfiguration für Sie durchgeführt wird.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 09/04/2020
ms.author: deanwe
ms.openlocfilehash: 208c1ba6a0fb059640b3f8bb19bb4bc90e5eb116
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183650"
---
# <a name="azure-automanage-for-virtual-machines-best-practices"></a>Bewährte Methoden für die automatische Azure-Verwaltung von virtuellen Computern


Für diese Azure-Dienste wird automatisch für Sie das Onboarding durchgeführt, wenn Sie die automatische Verwaltung für virtuelle Computer verwenden. Sie sind für unser Whitepaper über bewährte Methoden unerlässlich, das Sie in unserem [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management) finden.

Bei all diesen Diensten werden wir das Onboarding automatisch durchführen, sie automatisch konfigurieren, auf Datendrift überwachen und schlichten, wenn ein Datendrift festgestellt wird. Weitere Informationen zu diesem Prozess finden Sie unter [Automatische Azure-Verwaltung für virtuelle Computer](automanage-virtual-machines.md).


## <a name="participating-services"></a>Beteiligte Dienste

|Dienst    |Beschreibung    |Unterstützte Profile<sup>1</sup>    |Unterstützte Voreinstellungen<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|VM Insights-Überwachung    |Azure Monitor für VMs überwacht die Leistung und Integrität Ihrer virtuellen Computer, einschließlich ihrer ausgeführten Prozesse und Abhängigkeiten von anderen Ressourcen. [Weitere Informationen](../azure-monitor/insights/vminsights-overview.md).    |Bewährte Methoden für Azure-VMs – Produktion    |Nein    |
|Backup    |Azure Backup bietet unabhängige und isolierte Sicherungen zum Schutz vor dem versehentlichen Löschen der Daten auf Ihren VMs. [Weitere Informationen](../backup/backup-azure-vms-introduction.md). Die Gebühren basieren auf der Anzahl und Größe der VMs, die geschützt werden. [Weitere Informationen](https://azure.microsoft.com/pricing/details/backup/).    |Bewährte Methoden für Azure-VMs – Produktion    |Ja    |
|Azure Security Center    |Azure Security Center ist ein vereinheitlichtes Sicherheitsverwaltungssystem für Infrastrukturen, mit dem der Sicherheitsstatus Ihrer Rechenzentren gestärkt wird und ein erweiterter Schutz vor Bedrohungen für Ihre Hybridworkloads in der Cloud bereitgestellt wird. [Weitere Informationen](../security-center/security-center-introduction.md).  Die automatische Verwaltung konfiguriert das Abonnement, in dem sich Ihre VM befindet, für das Free-Tarifangebot von Azure Security Center. Wenn Ihr Abonnement bereits mit Azure Security Center verbunden ist, wird es von der automatischen Verwaltung nicht neu konfiguriert.    |Bewährte Methoden für Azure-VMs – Produktion, Bewährte Methoden für Azure-VMs – Dev/Test    |Nein    |
|Microsoft Antimalware    |Microsoft Antimalware für Azure ist eine kostenlose Echtzeit-Schutzfunktion zum Erkennen und Entfernen von Viren, Spyware und anderer Schadsoftware. Das Tool generiert Warnungen, wenn bekannte schädliche oder unerwünschte Software versucht, sich selbst auf Ihren Azure-Systemen zu installieren oder dort auszuführen. [Weitere Informationen](../security/fundamentals/antimalware.md). |Bewährte Methoden für Azure-VMs – Produktion, Bewährte Methoden für Azure-VMs – Dev/Test    |Ja    |
|Updateverwaltung    |Sie können die Updateverwaltung in Azure Automation verwenden, um Betriebssystemupdates für Ihre virtuellen Computer zu verwalten. Sie können den Status der verfügbaren Updates auf allen Agent-Computern schnell auswerten und die Installation der für den Server erforderlichen Updates initiieren. [Weitere Informationen](../automation/update-management/overview.md).    |Bewährte Methoden für Azure-VMs – Produktion, Bewährte Methoden für Azure-VMs – Dev/Test    |Nein    |
|Änderungsnachverfolgung und Bestand    |„Änderungsnachverfolgung und Bestand“ kombiniert Änderungsnachverfolgungs- und Bestandsfunktionen, mit denen Sie Änderungen an virtuellen Computern und Serverinfrastrukturen nachverfolgen können. Da der Dienst auch eine Änderungsnachverfolgung für Dienste, Daemons, Software, Registrierung und Dateien in Ihrer Umgebung unterstützt, können Sie unerwünschte Änderungen ermitteln und entsprechende Warnungen auslösen. Durch die Unterstützung einer Bestandserfassung ist es zudem möglich, Ressourcen auf Gastsystemen abzurufen, um sich über die installierten Anwendungen und andere Konfigurationselemente zu informieren.  [Weitere Informationen](../automation/change-tracking/overview.md).    |Bewährte Methoden für Azure-VMs – Produktion, Bewährte Methoden für Azure-VMs – Dev/Test    |Nein    |
|Azure-Gastkonfiguration    | Die Gastkonfigurationsrichtlinie dient zur Überwachung der Konfiguration sowie zur Generierung von Berichten zur Konformität des Computers. Der Automanage-Dienst installiert die [Windows-Sicherheitsbaselines](/windows/security/threat-protection/windows-security-baselines) mithilfe der Gastkonfigurationserweiterung. [Weitere Informationen](../governance/policy/concepts/guest-configuration.md).    |Bewährte Methoden für Azure-VMs – Produktion, Bewährte Methoden für Azure-VMs – Dev/Test    |Nein    |
|Azure Automation-Konto    |Azure Automation unterstützt die Verwaltung des gesamten Lebenszyklus Ihrer Infrastruktur und Anwendungen. [Weitere Informationen](../automation/automation-intro.md).    |Bewährte Methoden für Azure-VMs – Produktion, Bewährte Methoden für Azure-VMs – Dev/Test    |Nein    |
|Log Analytics-Arbeitsbereich    |Azure Monitor speichert Protokolldaten in einem Log Analytics-Arbeitsbereich, bei dem es sich um eine Azure-Ressource und einen Container handelt, in dem Daten gesammelt und aggregiert werden und der als eine administrative Grenze dient. [Weitere Informationen](../azure-monitor/platform/design-logs-deployment.md).    |Bewährte Methoden für Azure-VMs – Produktion, Bewährte Methoden für Azure-VMs – Dev/Test    |Nein    |


<sup>1</sup> Konfigurationsprofile sind verfügbar, wenn Sie die automatische Verwaltung aktivieren. [Weitere Informationen](automanage-virtual-machines.md#configuration-profiles). Sie können auch die Standardeinstellungen des Konfigurationsprofils anpassen und Ihre eigenen Voreinstellungen im Rahmen der bewährten Methoden festlegen.


## <a name="next-steps"></a>Nächste Schritte

Versuchen Sie, die automatische Verwaltung für virtuelle Computer im Azure-Portal zu aktivieren.

> [!div class="nextstepaction"]
> [Aktivieren der automatischen Verwaltung für virtuelle Computer im Azure-Portal](quick-create-virtual-machines-portal.md)