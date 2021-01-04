---
title: Übersicht über Azure Defender und die verfügbaren Pläne
description: Erfahren Sie mehr über die Pläne, Schutzmaßnahmen und Warnungen von Azure Defender. Aktivieren Sie anschließend Azure Defender für zusätzliche Sicherheit für Ihre Abonnements.
author: memildin
ms.author: memildin
ms.date: 9/30/2020
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 8bf3959651048a7a568df1d94aa07aa206663d50
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96750984"
---
# <a name="introduction-to-azure-defender"></a>Einführung in Azure Defender

Die Features von Azure Security Center decken die zwei Säulen der Cloudsicherheit ab:

- **Verwaltung des Cloudsicherheitsstatus (Cloud Security Posture Management, CSPM)** : Security Center ist für alle Azure-Benutzer **kostenlos** verfügbar. Die kostenlose Benutzeroberfläche umfasst CSPM-Features, wie z. B. Sicherheitsbewertung, Erkennung von Sicherheitsfehlkonfigurationen auf Ihren Azure-Computern, Ressourcenbestand und mehr. Verwenden Sie diese CSPM-Features, um den Hybridcloudstatus zu stärken und die Konformität mit den integrierten Richtlinien nachzuverfolgen.

- **Cloudworkloadschutz (Cloud Workload Protection, CWP)** : Die in Security Center integrierte Cloudworkloadschutz-Plattform (CWPP), **Azure Defender**, bietet erweiterten, intelligenten Schutz für Ihre Azure- und Hybridressourcen und -workloads. Durch die Aktivierung von Azure Defender wird eine Reihe zusätzlicher Sicherheitsfeatures bereitgestellt, die auf dieser Seite beschrieben werden. Zusätzlich zu den integrierten Richtlinien können Sie bei aktiviertem Azure Defender-Plan auch benutzerdefinierte Richtlinien und Initiativen hinzufügen. Sie können gesetzliche Standards (z. B. NIST und Azure CIS) sowie den Azure-Sicherheitsvergleichstest für eine echte angepasste Sicht auf Ihre Konformität hinzufügen.

Das Azure Defender-Dashboard in Security Center bietet Transparenz und Kontrolle der CWP-Features für Ihre Umgebung:

:::image type="content" source="./media/azure-defender/sample-defender-dashboard.png" alt-text="Beispiel für das Azure Defender-Dashboard" lightbox="./media/azure-defender/sample-defender-dashboard.png":::

## <a name="what-resource-types-can-azure-defender-secure"></a>Welche Ressourcentypen können von Azure Defender geschützt werden?

Azure Defender bietet Sicherheitswarnungen und erweiterten Bedrohungsschutz für virtuelle Computer, SQL-Datenbanken, Container, Webanwendungen, Ihr Netzwerk u. v. m.

Wenn Sie Azure Defender über den Bereich **Preise und Einstellungen** von Azure Security Center aktivieren, werden alle folgenden Defender-Pläne aktiviert und bieten umfassende Schutzmechanismen für die Compute-, Daten- und Dienstebenen Ihrer Umgebung:

- [Azure Defender für Server](defender-for-servers-introduction.md)
- [Azure Defender für App Service](defender-for-app-service-introduction.md)
- [Azure Defender für Storage](defender-for-storage-introduction.md)
- [Azure Defender für SQL](defender-for-sql-introduction.md)
- [Azure Defender für Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender für Containerregistrierungen](defender-for-container-registries-introduction.md)
- [Azure Defender für Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender für Resource Manager](defender-for-resource-manager-introduction.md)
- [Azure Defender für DNS](defender-for-dns-introduction.md)

Jeder dieser Pläne wird in der Security Center-Dokumentation einzeln erläutert.

> [!TIP]
> Azure Defender für IoT (Vorschau) ist ein separates Produkt. Alle Details dazu finden Sie unter [Einführung in Azure Defender für IoT (Vorschau)](../defender-for-iot/overview.md). 

## <a name="hybrid-cloud-protection"></a>Schutz für Hybrid Clouds

Außerdem können Sie Ihrer Hybrid Cloud-Umgebung Azure Defender-Funktionen hinzufügen, um Ihre Azure-Umgebung zu schützen:

- Schützen von Nicht-Azure-Servern
- Schützen virtueller Computer in anderen Clouds (z. B. AWS und GCP)

Sie erhalten eine angepasste Threat Intelligence und priorisierte Warnungen Ihrer spezifischen Umgebung entsprechend, damit Sie sich auf das konzentrieren können, was für Sie am wichtigsten ist.

Stellen Sie [Azure Arc](https://azure.microsoft.com/services/azure-arc/) bereit, und aktivieren Sie Azure Defender, um den Schutz auf virtuelle Computer und SQL-Datenbanken mit mehreren Clouds oder lokal auszuweiten. Azure Arc für Server ist ein kostenloser Dienst. Dienste, die auf Arc-fähigen Servern verwendet werden, wie etwa Azure Defender, werden jedoch zu den Preisen für den jeweiligen Dienst abgerechnet. Weitere Informationen finden Sie unter [Hinzufügen eines Azure-fremden Computers mit Azure Arc](quickstart-onboard-machines.md#add-non-azure-machines-with-azure-arc).

> [!TIP]
> Der native Connector für AWS verarbeitet die Azure Arc-Bereitstellung transparent für Sie. Weitere Informationen finden Sie unter [Verbinden Ihrer AWS-Konten mit Azure Security Center](quickstart-onboard-aws.md).



## <a name="azure-defender-security-alerts"></a>Azure Defender-Sicherheitswarnungen 

Wenn von Azure Defender in einem der Bereiche Ihrer Umgebung eine Bedrohung erkannt wird, wird eine Sicherheitswarnung generiert. Diese Warnungen beschreiben Details zu den betroffenen Ressourcen, empfohlene Problembehandlungsschritte sowie in einigen Fällen eine Option, mit der eine Logik-App als Reaktion ausgelöst werden kann.

Sie können Warnungen exportieren, und zwar unabhängig davon, ob sie von Security Center generiert oder von Security Center über ein integriertes Sicherheitsprodukt empfangen wurden. Befolgen Sie die Anweisungen unter [Streamen von Warnungen an eine SIEM-, SOAR- oder IT-Dienstverwaltungslösung](export-to-siem.md), um die Warnungen zu Azure Sentinel (oder ein Drittanbieter-SIEM) bzw. ein beliebiges anderes externes Tool zu exportieren.

> [!NOTE]
> Warnungen aus unterschiedlichen Quellen erfordern möglicherweise unterschiedlich lange Zeit, bis sie angezeigt werden. Beispielsweise kann es länger dauern, bis Warnungen, die eine Analyse des Netzwerkdatenverkehrs erfordern, angezeigt werden, als das Anzeigen von Warnungen im Zusammenhang mit verdächtigen Prozessen auf virtuellen Computern.


## <a name="azure-defender-advanced-protection-capabilities"></a>Erweiterte Schutzfunktionen von Azure Defender

Azure Defender verwendet erweiterte Analysen für maßgeschneiderte Empfehlungen in Bezug auf Ihre Ressourcen. 

Schutzmaßnahmen umfassen das Sichern der Verwaltungsports Ihrer virtuellen Computer mit Just-In-Time-Zugriff und adaptiven Anwendungssteuerungen zum Erstellen von Positivlisten, die festlegen, welche Apps auf Ihren Computern ausgeführt werden dürfen und welche nicht. 

Verwenden Sie die Kacheln für den erweiterten Schutz im Azure Defender-Dashboard, um die einzelnen Schutzmaßnahmen zu überwachen und zu konfigurieren. 

## <a name="vulnerability-assessment-and-management"></a>Sicherheitsrisikobewertung und -management

Azure Defender beinhaltet auch das Überprüfen von Sicherheitsrisiken für Ihre virtuellen Computer und Containerregistrierungen, ohne dass dafür zusätzliche Kosten entstehen. Die Überprüfungen werden von Qualys unterstützt, Sie benötigen jedoch keine Qualys-Lizenz und auch kein Qualys-Konto – alles erfolgt nahtlos innerhalb von Security Center. 

Überprüfen Sie die Ergebnisse dieser Sicherheitsrisikoüberprüfungen, und reagieren Sie in Security Center darauf. Dadurch wird Security Center zur zentralen Benutzeroberfläche für alle Ihre Bemühungen im Bereich Cloudsicherheit.

Weitere Informationen finden Sie auf den folgenden Seiten:

- [Integrierte Security Center-Lösung zur Sicherheitsrisikobewertung für virtuelle Computer](deploy-vulnerability-assessment-vm.md)
- [Identifizieren von Sicherheitsrisiken in Images in Azure-Containerregistrierungen](defender-for-container-registries-usage.md#identify-vulnerabilities-in-images-in-other-container-registries)



## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie mehr über die Vorteile von Azure Defender erfahren. 

> [!div class="nextstepaction"]
> [Aktivieren von Azure Defender](security-center-pricing.md)