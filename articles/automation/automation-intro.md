---
title: Einführung in Azure Automation
description: In diesem Artikel wird erläutert, was Azure Automation ist und wie es verwendet wird, um den Lebenszyklus von Infrastruktur und Anwendungen zu automatisieren.
services: automation
ms.subservice: process-automation
keywords: Azure Automation, DSC, PowerShell, State Configuration, Updateverwaltung, Änderungsnachverfolgung, DSC, Bestand, Runbooks, Python, grafisch
ms.date: 10/18/2018
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: e3986b7e8fc70f8662bed40b076897caa6165744
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96182817"
---
# <a name="an-introduction-to-azure-automation"></a>Einführung in Azure Automation

Mit Azure Automation wird ein cloudbasierter Automatisierungs- und Konfigurationsdienst bereitgestellt, der eine einheitliche Verwaltung Ihrer Azure- und Nicht-Azure-Umgebungen unterstützt. Der Dienst umfasst sowohl die Prozessautomatisierung, Konfigurationsverwaltung und Updateverwaltung als auch gemeinsam genutzte Funktionen und Features für heterogene Umgebungen. Mit Azure Automation haben Sie die volle Kontrolle über Bereitstellung, Ausführung und Außerbetriebnahme von Workloads und Ressourcen.

![Funktionen von Automation](media/automation-overview/automation-overview.png)

## <a name="process-automation"></a>Prozessautomatisierung

Die Prozessautomatisierung in Azure Automation ermöglicht es Ihnen, häufig anfallende, zeitaufwendige und fehleranfällige Cloudverwaltungsaufgaben zu automatisieren. Bei Verwendung dieses Diensts können Sie sich auf Aufgaben konzentrieren, die den Geschäftswert erhöhen. Aufgrund der geringeren Fehlerquote und höheren Effizienz können Betriebskosten gesenkt werden. Einzelheiten zur Betriebsumgebung für die Prozessautomatisierung finden Sie unter [Ausführen von Runbooks in Azure Automation](automation-runbook-execution.md).

Die Prozessautomatisierung unterstützt die Integration von Azure-Diensten und anderen öffentlichen Systemen, die für die Bereitstellung, Konfiguration und Verwaltung Ihrer End-to-End-Prozesse erforderlich sind. Der Dienst ermöglicht das grafische Erstellen von [Runbooks](automation-runbook-types.md) mithilfe von PowerShell oder Python. Unter Verwendung eines [Hybrid Runbook Workers](automation-hybrid-runbook-worker.md) können Sie die Verwaltung durch eine übergreifende Orchestrierung Ihrer lokalen Umgebungen vereinheitlichen. Mit [Webhooks](automation-webhooks.md) können Sie Anforderungen erfüllen und sowohl Continuous Delivery-Abläufe als auch eine fortlaufende Ausführung von Vorgängen sicherstellen, indem die Automatisierung über ITSM, DevOps und Überwachungssysteme ausgelöst wird. 

## <a name="configuration-management"></a>Konfigurationsverwaltung

Die Konfigurationsverwaltung in Azure Automation ermöglicht den Zugriff auf zwei Features:

* Änderungsnachverfolgung und Bestand
* Azure Automation State Configuration

### <a name="change-tracking-and-inventory"></a>Änderungsnachverfolgung und Bestand

„Änderungsnachverfolgung und Bestand“ kombiniert Änderungsnachverfolgungs- und Bestandsfunktionen, mit denen Sie Änderungen an virtuellen Computern und Serverinfrastrukturen nachverfolgen können. Da der Dienst auch eine Änderungsnachverfolgung für Dienste, Daemons, Software, Registrierung und Dateien in Ihrer Umgebung unterstützt, können Sie unerwünschte Änderungen ermitteln und entsprechende Warnungen auslösen. Durch die Unterstützung einer Bestandserfassung ist es zudem möglich, Ressourcen auf Gastsystemen abzurufen, um sich über die installierten Anwendungen und andere Konfigurationselemente zu informieren. Weitere Informationen zu diesem Feature finden Sie unter [Änderungsnachverfolgung und Bestand](change-tracking/overview.md).

### <a name="azure-automation-state-configuration"></a>Azure Automation State Configuration

[Azure Automation State Configuration](automation-dsc-overview.md) ist ein cloudbasiertes PowerShell DSC-Feature (Desired State Configuration), mit dem Dienste für Unternehmensumgebungen bereitgestellt werden. Unter Verwendung dieser Funktion können Sie Ihre DSC-Ressourcen in Azure Automation verwalten und Konfigurationen auf virtuelle oder physische Computer anwenden. Dabei werden die Konfigurationen von einem DSC-Pullserver in der Azure-Cloud abgerufen. 

## <a name="update-management"></a>Updateverwaltung

Azure Automation umfasst ein Feature zur [Updateverwaltung](./update-management/overview.md) für Windows- und Linux-Systeme in Hybridumgebungen. Mithilfe der Updateverwaltung erhalten Sie einen Einblick in die Konformität von Updates für Azure, andere Clouds und die lokale Umgebung. Mit diesem Feature können Sie geplante Bereitstellungen erstellen, um die Installation von Updates in einem definierten Wartungsfenster zu orchestrieren. Wenn ein Update nicht auf einem Computer installiert werden soll, können Sie es mithilfe der Funktionen zur Updateverwaltung von einer Bereitstellung ausschließen.

## <a name="shared-capabilities"></a>Gemeinsam genutzte Funktionen

Azure Automation bietet eine Reihe von gemeinsam genutzten Funktionen. Dazu zählen u. a. gemeinsam genutzte Ressourcen, die rollenbasierte Zugriffssteuerung, eine flexible Zeitplanung, die Integration in die Quellcodeverwaltung, Überwachungsfunktionen und das Tagging.

### <a name="shared-resources"></a><a name="shared-resources"></a>Gemeinsam genutzte Ressourcen

Azure Automation besteht aus einem Satz von gemeinsam genutzten Ressourcen, die Ihnen das bedarfsabhängige Automatisieren und Konfigurieren Ihrer Umgebungen erleichtern.

* **[Zeitpläne](./shared-resources/schedules.md)** : Auslösen von Automation-Vorgängen zu vorgegebenen Zeiten.
* **[Module](./shared-resources/modules.md)** : Verwalten von Azure und anderen Systemen. Sie können Module für Microsoft-, Drittanbieter-, Community- oder benutzerdefinierte Cmdlets und DSC-Ressourcen in das Automation-Konto importieren.
* **[Modulkatalog](automation-runbook-gallery.md)** : Unterstützung einer nativen PowerShell-Katalogintegration, um Runbooks anzuzeigen und in das Automation-Konto zu importieren. Mithilfe des Katalogs können Sie im Handumdrehen mit dem Integrieren und Erstellen Ihrer Prozesse aus dem PowerShell-Katalog und Microsoft Script Center beginnen.
* **[Python 2-Pakete](python-packages.md)** : Unterstützung von Python 2-Runbooks für Ihr Automation-Konto.
* **[Anmeldeinformationen](./shared-resources/credentials.md)** : Sicheres Speichern von vertraulichen Informationen, die zur Laufzeit von Runbooks und Konfigurationen verwendet werden können.
* **[Verbindungen](automation-connections.md)** : Speichern von Name/Wert-Paaren gemeinsamer Informationen für Verbindungen mit Systemen. Verbindungen werden vom Autor eines Moduls für die Nutzung in Runbooks und Konfigurationen zur Laufzeit definiert.
* **[Zertifikate](./shared-resources/certificates.md)** : Definition von Informationen, die zur Authentifizierung und zum Schützen von bereitgestellten Ressourcen verwendet werden, wenn Runbooks oder DSC-Konfigurationen zur Laufzeit darauf zugreifen. 
* **[Variablen](./shared-resources/variables.md)** : Definition von Inhalten, die für verschiedene Runbooks und Konfigurationen verwendet werden können. Sie können Variablenwerte ändern, ohne die Runbooks oder Konfigurationen bearbeiten zu müssen, die darauf verweisen.

### <a name="role-based-access-control"></a>Rollenbasierte Zugriffssteuerung

Azure Automation unterstützt die rollenbasierte Zugriffssteuerung von Azure (Azure RBAC), um den Zugriff auf das Automation-Konto und die zugehörigen Ressourcen zu steuern. Weitere Informationen zur Konfiguration von Azure RBAC für Ihr Automation-Konto, Ihre Runbooks und Ihre Aufträge finden Sie unter [Rollenbasierte Zugriffssteuerung in Azure Automation](automation-role-based-access-control.md).

### <a name="source-control-integration"></a>Integration der Quellcodeverwaltung

Azure Automation unterstützt eine [Integration in die Quellcodeverwaltung](source-control-integration.md). Diese Funktion ermöglicht die Konfiguration in Form von Code, sodass Runbooks oder Konfigurationen in ein Quellcodeverwaltungssystem eingecheckt werden können.

## <a name="heterogeneous-support-windows-and-linux"></a>Unterstützung heterogener Systeme (Windows und Linux)

Azure Automation ist für die Nutzung in Ihrer gesamten Hybridcloudumgebung und in Ihren Windows- und Linux-Systemen ausgelegt. Sie können bereitgestellte Workloads und das Betriebssystem, auf dem diese ausgeführt werden, auf einheitliche Weise automatisieren und konfigurieren.

## <a name="common-scenarios-for-automation"></a>Häufige Automation-Szenarien

Azure Automation unterstützt die Verwaltung des gesamten Lebenszyklus Ihrer Infrastruktur und Anwendungen. Zu den häufigen Szenarios gehören:

* **Schreiben von Runbooks**: Erstellen Sie PowerShell-, PowerShell-Workflow-, Python 2- und DSC-Runbooks sowie grafische Runbooks mithilfe einer gemeinsamen Sprache. 
* **Erstellen und Bereitstellen von Ressourcen**: Verwenden Sie Runbooks und Azure Resource Manager-Vorlagen, um virtuelle Computer in einer Hybridumgebung bereitzustellen. Führen Sie eine Integration in Entwicklungstools wie Jenkins und Azure DevOps durch.
* **Konfigurieren von VMs**: Bewerten und konfigurieren Sie Windows- und Linux-Computer mit Konfigurationen für die Infrastruktur und Anwendung.
* **Teilen von Informationen**: Übertragen Sie Informationen zur Bereitstellung und Verwaltung von Workloads in Ihrer Organisation in das System. 
* **Abrufen von Bestandsinformationen**: Verschaffen Sie sich einen Überblick über den gesamten Bestand an bereitgestellten Ressourcen, um Informationen zu Zielgruppenadressierung, Berichterstellung und Konformität zu erhalten. 
* **Ermitteln von Änderungen**: Identifizieren Sie Änderungen, die zu Fehlkonfigurationen führen können, und verbessern Sie die Konformität Ihrer Abläufe.
* **Überwachen**: Ermitteln Sie Änderungen auf Computern, die Probleme verursachen, und beheben Sie diese, oder nutzen Sie die Eskalation an Verwaltungssysteme.
* **Schützen**: Versetzen Sie Computer in Quarantäne, für die Sicherheitswarnungen ausgelöst werden. Legen Sie Anforderungen auf Gastsystemen fest.
* **Steuern**: Richten Sie Azure RBAC für Teams ein. Stellen Sie ungenutzte Ressourcen wieder her.

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="pricing-for-azure-automation"></a>Preise für Azure Automation

Informationen zur Preisgestaltung bei Azure Automation finden Sie auf der entsprechenden Seite mit den [Preisen](https://azure.microsoft.com/pricing/details/automation/).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Erstellen eines Automation-Kontos](automation-quickstart-create-account.md)