---
title: 'Azure Container Instances: Sicherheitsüberlegungen'
description: Empfehlungen zum Schutz von Images und Secrets für Azure Container Instances und allgemeine Sicherheitsüberlegungen für jede Containerplattform
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 04/29/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: dc516277d79e37500e73e1aee6b88c908acb9b1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64943963"
---
# <a name="security-considerations-for-azure-container-instances"></a>Sicherheitsüberlegungen für Azure Container Instances

Dieser Artikel stellt Sicherheitsüberlegungen für die Verwendung von Azure Container Instances zum Ausführen von Container-Apps vor. Dabei werden folgende Themen behandelt:

> [!div class="checklist"]
> * **Sicherheitsempfehlungen** für die Verwaltung von Images und Geheimnissen für Azure Container Instances
> * **Überlegungen zum Containerökosystem** während des gesamten Lebenszyklus des Containers für alle Containerplattformen

## <a name="security-recommendations-for-azure-container-instances"></a>Sicherheitsempfehlungen für Azure Container Instances

### <a name="use-a-private-registry"></a>Verwenden einer privaten Registrierung

Container werden aus Images erstellt, die in einem oder mehreren Repositorys gespeichert sind. Diese Repositorys können zu einer öffentlichen Registrierung (etwa [Docker Hub](https://hub.docker.com)) oder zu einer privaten Registrierung gehören. Ein Beispiel für eine private Registrierung ist die [Docker Trusted Registry](https://docs.docker.com/datacenter/dtr/2.0/), die lokal oder in einer virtuellen privaten Cloud installiert werden kann. Sie können auch cloudbasierte private Containerregistrierungsdienste verwenden, z.B. [Azure Container Registry](../container-registry/container-registry-intro.md). 

Ein öffentlich verfügbares Containerimage garantiert keine Sicherheit. Containerimages bestehen aus mehreren Softwareschichten, und in jeder Softwareschicht können Sicherheitsrisiken vorliegen. Um die Bedrohung durch Angriffe zu verringern, sollten Sie Images in einer privaten Registry (z.B. Azure Container Registry oder Docker Trusted Registry) speichern und daraus abrufen. Azure Container Registry stellt nicht nur eine verwaltete private Registrierung bereit, sondern unterstützt auch die [dienstprinzipalbasierte Authentifizierung](../container-registry/container-registry-authentication.md) über Azure Active Directory für grundlegende Authentifizierungsabläufe. Diese Authentifizierung umfasst rollenbasierten Zugriff für Lese- (pull), Schreib- (push) und Besitzerberechtigungen.

### <a name="monitor-and-scan-container-images"></a>Überwachen und Überprüfen von Containerimages

Sicherheitsüberwachungs- und -überprüfungslösungen wie [Twistlock](https://azuremarketplace.microsoft.com/marketplace/apps/twistlock.twistlock?tab=Overview) und [Aqua Security](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) sind über Azure Marketplace verfügbar. Mit ihnen können Sie Containerimages in einer privaten Registrierung überprüfen und potenzielle Sicherheitslücken identifizieren. Es ist wichtig, die Überprüfungstiefe zu verstehen, die die verschiedenen Lösungen bieten. 

### <a name="protect-credentials"></a>Schützen von Anmeldeinformationen

Container können sich über mehrere Cluster und Azure-Regionen erstrecken. Daher müssen Sie die für Anmeldungen oder API-Zugriffe erforderlichen Anmeldeinformationen wie Kennwörter oder Token schützen. Stellen Sie sicher, dass nur berechtigte Benutzer auf diese Container während der Übertragung und im Ruhezustand zugreifen können. Inventarisieren Sie alle Anmeldegeheimnisse, und verlangen Sie dann von den Entwicklern, dass sie neue Tools für die Verwaltung von Geheimnissen verwenden, die für Containerplattformen konzipiert sind.  Stellen Sie sicher, dass Ihre Lösung verschlüsselte Datenbanken, TLS-Verschlüsselung für Geheimnisdaten während der Übertragung und [rollenbasierte Zugriffssteuerung](../role-based-access-control/overview.md) mit der geringsten Berechtigung beinhaltet. [Azure Key Vault](../key-vault/key-vault-secure-your-key-vault.md) ist ein Clouddienst und schützt Verschlüsselungsschlüssel und Geheimnisse (wie Zertifikate, Verbindungszeichenfolgen und Kennwörter) für Ihre containerisierten Anwendungen. Da es sich hierbei um vertrauliche und geschäftskritische Daten handelt, sichern Sie den Zugriff auf Key Vault-Instanzen so, dass nur autorisierte Anwendungen und Benutzer darauf zugreifen können.

## <a name="considerations-for-the-container-ecosystem"></a>Überlegungen zum Containerökosystem

Die folgenden Sicherheitsmaßnahmen können Ihnen helfen, Ihr Containerökosystem zu sichern und zu schützen, wenn sie gut umgesetzt und effektiv verwaltet werden. Diese Maßnahmen gelten für den gesamten Lebenszyklus des Containers, von der Entwicklung bis hin zur Bereitstellung in der Produktion, sowie für eine Reihe von Containerorchestratoren, Hosts und Plattformen. 

### <a name="use-vulnerability-management-as-part-of-your-container-development-lifecycle"></a>Verwenden von Sicherheitsrisikoverwaltung als Teil des Containerentwicklungs-Lebenszyklus 

Durch den Einsatz einer effektiven Sicherheitsrisikoverwaltung während des gesamten Lebenszyklus der Containerentwicklung verbessern Sie die Chancen, dass Sie Sicherheitsprobleme identifizieren und lösen können, bevor sie zu einem ernsteren Problem werden. 

### <a name="scan-for-vulnerabilities"></a>Überprüfen auf Sicherheitsrisiken 

Neue Sicherheitsrisiken werden ständig entdeckt, daher ist das Überprüfen auf und Identifizieren von Sicherheitsrisiken ein kontinuierlicher Prozess. Integrieren Sie Sicherheitsrisikoüberprüfungen während des gesamten Containerlebenszyklus:

* Als letzte Überprüfung in Ihrer Entwicklungspipeline sollten Sie eine Sicherheitsrisikoüberprüfung von Containern durchführen, bevor Sie die Images in eine öffentliche oder private Registrierung pushen. 
* Überprüfen Sie die Containerimages in der Registrierung weiterhin, um sowohl Fehler zu identifizieren, die während der Entwicklung übersehen wurden, als auch um neu entdeckte Sicherheitsrisiken zu beheben, die in dem in den Containerimages verwendeten Code vorhanden sein können.  

### <a name="map-image-vulnerabilities-to-running-containers"></a>Zuordnen von Imagesicherheitsrisiken zu ausgeführten Containern 

Sie benötigen eine Möglichkeit, Sicherheitsrisiken, die in Containerimages identifiziert wurden, ausgeführten Containern zuzuordnen, damit Sicherheitsprobleme gemildert oder gelöst werden können.  

### <a name="ensure-that-only-approved-images-are-used-in-your-environment"></a>Sicherstellen, dass nur genehmigte Images in Ihrer Umgebung verwendet werden 

Es gibt genügend Veränderungen und Schwankungen in einem Containerökosystem, auch wenn unbekannte Container nicht zugelassen werden. Lassen Sie nur genehmigte Containerimages zu. Stellen Sie Tools und Prozesse zur Verfügung, um die Verwendung von nicht genehmigten Containerimages zu überwachen und zu verhindern. 

Eine effektive Möglichkeit, die Angriffsfläche zu verringern und Entwickler daran zu hindern, kritische Sicherheitsfehler zu machen, ist die Steuerung des Flusses von Containerimages in Ihre Entwicklungsumgebung. Beispielsweise könnten Sie eine einzelne Linux-Distribution als Basisimage genehmigen (vorzugsweise eine, die schlank ist: Alpine oder CoreOS statt Ubuntu), um die Oberfläche für potenzielle Angriffe zu minimieren. 

Imagesignaturen oder Fingerabdrücke können eine Überwachungskette darstellen, die es Ihnen ermöglicht, die Integrität der Container zu überprüfen. So unterstützt beispielsweise Azure Container Registry das Docker-Modell [content trust](https://docs.docker.com/engine/security/trust/content_trust), das es den Herausgebern von Images ermöglicht, Images zu signieren, die in eine Registry gepusht werden, und den Consumern von Images, nur signierte Images zu pullen.

### <a name="permit-only-approved-registries"></a>Zulassen nur genehmigter Registrierungen 

Eine Erweiterung, um sicherzustellen, dass Ihre Umgebung nur genehmigte Images verwendet, besteht darin, nur die Verwendung von genehmigten Containerregistrierungen zu erlauben. Die Anforderung, zugelassene Containerregistrierungen zu verwenden, reduziert Ihre Risikoexposition, indem sie das Potenzial für die Einführung unbekannter Sicherheitsrisiken oder -probleme begrenzt. 

### <a name="ensure-the-integrity-of-images-throughout-the-lifecycle"></a>Sicherstellen der Integrität des Images während des gesamten Lebenszyklus 

Ein Teil des Sicherheitsmanagements während des gesamten Containerlebenszyklus besteht darin, die Integrität der Containerimages in der Registrierung und bei deren Änderung oder Bereitstellung in der Produktion sicherzustellen. 

* Images mit Sicherheitsrisiken, auch wenn sie noch so klein sind, sollten in Produktionsumgebungen nicht ausgeführt werden dürfen. Idealerweise sollten alle in der Produktion bereitgestellten Images in einer privaten Registrierung gespeichert werden, auf die nur wenige ausgewählte Personen Zugriff besitzen. Halten Sie die Anzahl von Produktionsimages gering, um sicherzustellen, dass die Images effektiv verwaltet werden können.

* Da es schwierig ist, die Herkunft einer Software aus einem öffentlich verfügbaren Containerimage festzustellen, erstellen Sie Images aus der Quelle, um sicherzustellen, dass Sie den Ursprung der Schicht kennen. Wenn in einem selbst erstellten Containerimage eine Schwachstelle zutage tritt, können Kunden schneller eine Lösung finden. Bei einem öffentlichen Image müssen Kunden herausfinden, woher das Image stammt, um das Problem zu beheben, oder ein anderes, sicheres Image vom Herausgeber abrufen. 

* Auch bei einem sorgfältig überprüften Image, das in der Produktion bereitgestellt wurde, gibt es keine Garantie, dass es während der gesamten Lebensdauer der Anwendung aktuell ist. Möglicherweise werden Sicherheitslücken für Imageschichten gemeldet, die zuvor nicht bekannt waren oder erst nach Produktionsbereitstellung entstanden sind. 

  Überwachen Sie in der Produktion bereitgestellte Images regelmäßig, um Images zu ermitteln, die veraltet sind oder längere Zeit nicht aktualisiert wurden. Sie können Methoden für eine so genannte Blaugrünbereitstellung sowie parallele Bereitstellungsmechanismen verwenden, um Containerimages ohne Ausfallzeiten zu aktualisieren. Sie können Images mithilfe der im vorherigen Abschnitt beschriebenen Tools überprüfen. 

* Verwenden Sie eine CI-Pipeline (Continuous Integration) mit integrierter Sicherheitsüberprüfung, um sichere Images zu erstellen und diese in Ihre private Registrierung zu pushen. Die in die CI-Lösung integrierte Schwachstellenprüfung stellt sicher, dass Images, die alle Tests bestehen, per Push in die private Registrierung übertragen werden, aus der Produktionsworkloads bereitgestellt werden. 

  Wenn in der CI-Pipeline ein Fehler festgestellt wird, bedeutet dies, dass anfällige Images nicht in die private Registrierung gepusht werden, die für die Bereitstellung von Produktionsworkloads verwendet wird. Auf diese Weise lässt sich die Sicherheitsüberprüfung von Images auch automatisieren, wenn sehr viele Images vorhanden sind. Andernfalls kann die manuelle Überprüfung von Images auf Sicherheitsrisiken sehr langwierig und zudem fehleranfällig sein. 

### <a name="enforce-least-privileges-in-runtime"></a>Erzwingen der geringsten Berechtigungen in der Laufzeit 

Das Konzept der geringsten Berechtigungen ist eine grundlegende bewährte Sicherheitsmaßnahme, die auch für Container gilt. Wenn ein Sicherheitsrisiko ausgenutzt wird, erhält der Angreifer im Allgemeinen Zugriff und Berechtigungen, die denen der gefährdeten Anwendung oder des gefährdeten Prozesses entsprechen. Die Sicherstellung, dass Container mit den geringsten Berechtigungen und Zugriffsrechten arbeiten, die erforderlich sind, um die Aufgabe zu erledigen, verringert Ihre Gefährdung. 

### <a name="reduce-the-container-attack-surface-by-removing-unneeded-privileges"></a>Verringern der Containerangriffsfläche durch Entfernen nicht benötigter Berechtigungen 

Sie können die potenzielle Angriffsfläche auch minimieren, indem Sie ungenutzte oder unnötige Prozesse oder Berechtigungen aus der Containerlaufzeit entfernen. Privilegierte Container werden als root-Benutzer ausgeführt. Wenn ein böswilliger Benutzer oder eine Workload in einem privilegierten Container entweicht, wird der Container dann als root auf diesem System ausgeführt.

### <a name="whitelist-files-and-executables-that-the-container-is-allowed-to-access-or-run"></a>Erstellen einer Whitelist der Dateien und ausführbaren Dateien, auf die der Container zuzugreifen oder die er ausführen darf 

Die Verringerung der Anzahl der Variablen oder Unbekannten hilft Ihnen, eine stabile, zuverlässige Umgebung zu verwalten. Die Einschränkung von Containern, sodass sie nur auf vorab genehmigte oder in der Whitelist enthaltene Dateien und ausführbare Dateien zugreifen oder diese ausführen können, ist eine bewährte Methode zur Begrenzung der Risikoexposition.  

Es ist viel einfacher, eine Whitelist zu verwalten, wenn diese von Anfang an implementiert ist. Eine Whitelist bietet ein gewisses Maß an Kontrolle und Verwaltbarkeit, während Sie herausfinden, welche Dateien und ausführbaren Dateien für die ordnungsgemäße Funktion der Anwendung erforderlich sind. 

Eine Whitelist verringert nicht nur die Angriffsfläche, sondern kann auch als Grundlage für Anomalien dienen und die Anwendungsfälle der Szenarien „Noisy Neighbor“ und Containerausbruch verhindern. 

### <a name="enforce-network-segmentation-on-running-containers"></a>Erzwingen von Netzwerksegmentierung für ausgeführte Container  

Um Container in einem Subnetz vor Sicherheitsrisiken in einem anderen Subnetz zu schützen, sollten Sie Netzwerksegmentierung (oder Nanosegmentierung) oder Trennung zwischen ausgeführten Containern aufrechterhalten. Die Aufrechterhaltung von Netzwerksegmentierung kann auch für den Einsatz von Containern in Branchen erforderlich sein, die zur Erfüllung von Complianceanforderungen verpflichtet sind.  

So bietet beispielsweise das Partnertool [Aqua](https://azuremarketplace.microsoft.com/marketplace/apps/aqua-security.aqua-security?tab=Overview) einen automatisierten Ansatz für Nanosegmentierung. Aqua überwacht Containernetzwerkaktivitäten in der Laufzeit. Das Tool identifiziert alle eingehenden und ausgehenden Netzwerkverbindungen zu/von anderen Containern, Diensten, IP-Adressen und in das öffentliche sowie aus dem öffentlichen Internet. Nanosegmentierung wird automatisch basierend auf dem überwachten Datenverkehr erstellt. 

### <a name="monitor-container-activity-and-user-access"></a>Überwachen der Containeraktivität und des Benutzerzugriffs 

Wie in jeder IT-Umgebung sollten Sie die Aktivitäten und den Benutzerzugriff auf Ihr Containerökosystem konsequent überwachen, um verdächtige oder böswillige Aktivitäten schnell zu identifizieren. Azure bietet Containerüberwachungslösungen:

* [Azure Monitor für Container](../azure-monitor/insights/container-insights-overview.md) zur Überwachung der Leistung Ihrer Workloads, die in Kubernetes-Umgebungen bereitgestellt werden, die in Azure Kubernetes Service (AKS) gehostet werden. Azure Monitor für Container visualisiert die Leistung, indem anhand der Metrik-API die in Kubernetes verfügbaren Speicher- und Prozessormetriken von Controllern, Knoten und Containern erfasst werden. 

* Mit der [Azure-Containerüberwachungslösung](../azure-monitor/insights/containers.md) können Sie andere Docker- und Windows-Containerhosts an einem zentralen Ort anzeigen und verwalten. Beispiel:

  * Zeigen Sie ausführliche Überwachungsinformationen an, die auch die mit Containern verwendeten Befehle enthalten. 
  * Beheben Sie die Probleme mit Containern, indem Sie zentralisierte Protokolle anzeigen und durchsuchen, ohne eine Remoteanzeige der Docker- und Windows-Hosts zu benötigen.  
  * Suchen Sie nach Containern, die Störungen verursachen und übermäßig viele Ressourcen auf einem Host verbrauchen.
  * Zeigen Sie an einem zentralen Ort Informationen zur Leistung und Auslastung von CPU, Arbeitsspeicher, Speicher und Netzwerk für Container an.  

  Die Lösung unterstützt Containerorchestratoren wie Docker Swarm, DC/OS, nicht verwaltete Kubernetes Services, Service Fabric und Red Hat OpenShift. 

### <a name="monitor-container-resource-activity"></a>Überwachen der Containerressourcenaktivität 

Überwachen Sie Ihre Ressourcenaktivität, z.B. Dateien, Netzwerk und andere Ressourcen, auf die Ihre Container zugreifen. Die Überwachung der Ressourcenaktivität und des -verbrauchs ist für die Leistungsüberwachung und auch als Sicherheitsmaßnahme sinnvoll. 

[Azure Monitor](../azure-monitor/overview.md) ermöglicht Azure-Diensten die Kernüberwachung durch die Sammlung von Metriken, Aktivitätsprotokollen und Diagnoseprotokollen. Beispielsweise können Sie dem Aktivitätsprotokoll entnehmen, wann neue Ressourcen erstellt oder geändert werden. 

Metriken stehen zur Verfügung und liefern Leistungsstatistiken für verschiedene Ressourcen und sogar das Betriebssystem der virtuellen Computer. Sie können diese Daten mit einem der Explorer im Azure-Portal anzeigen und basierend auf diesen Metriken Benachrichtigungen erstellen. Azure Monitor bietet die schnellste Metrikpipeline (nur zwischen einer und fünf Minuten) und sollte daher für zeitkritische Warnungen und Benachrichtigungen verwendet werden. 

### <a name="log-all-container-administrative-user-access-for-auditing"></a>Protokollieren des gesamten administrativen Benutzerzugriffs auf Container für die Überwachung 

Führen Sie ein genaues Überwachungsprotokoll für den administrativen Zugriff auf Ihr Containerökosystem, die Containerregistrierung und die Containerimages. Diese Protokolle können für Überprüfungszwecke erforderlich sein und sind als forensische Beweise nach einem Sicherheitsvorfall nützlich. Sie können die [Azure-Containerüberwachungslösung](../azure-monitor/insights/containers.md) zu diesem Zweck verwenden. 

## <a name="next-steps"></a>Nächste Schritte

* Weitere Informationen zum Verwalten von Containersicherheitsrisiken mit Lösungen von [Twistlock](https://www.twistlock.com/solutions/microsoft-azure-container-security/) und [Aqua Security](https://www.aquasec.com/solutions/azure-container-security/).

* Weitere Informationen zur [Containersicherheit in Azure](https://azure.microsoft.com/resources/container-security-in-microsoft-azure/).