---
title: Governance der Azure DevTest Labs-Infrastruktur
description: Dieser Artikel enthält Anweisungen zur Governance der Azure DevTest Labs-Infrastruktur.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: e02400ef940efdf42370fbdc1da75bdc7062a8ef
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62127360"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Governance der Azure DevTest Labs-Infrastruktur – Unternehmensrichtlinie und Compliance
Dieser Artikel enthält Hinweise zur Einhaltung der Unternehmensrichtlinie und der Compliance für die Azure DevTest Labs-Infrastruktur. 

## <a name="public-vs-private-artifact-repository"></a>Vergleich: Öffentliches vs. privates Artefaktrepository

### <a name="question"></a>Frage
Wann sollte eine Organisation ein öffentliches und wann ein privates Artefaktrepository in DevTest Labs verwenden?

### <a name="answer"></a>Antwort
Das [öffentliche Artefaktrepository](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) stellt einen Anfangssatz von Softwarepaketen bereit, die am häufigsten verwendet werden. Dies hilft bei der schnellen Bereitstellung – ohne Zeitaufwand zum Reproduzieren gängiger Entwicklertools und Add-Ins. Sie können auch Ihr eigenes privates Repository bereitstellen. Sie haben die Möglichkeit, ein öffentliches und ein privates Repository parallel zu nutzen. Das öffentliche Repository lässt sich auch deaktivieren. Beachten Sie die folgenden Fragen und Überlegungen, bevor Sie ein privates Repository bereitstellen:

- Muss die Organisation im Rahmen ihres DevTest Labs-Angebots lizenzierte Unternehmenssoftware verwenden? Falls ja, sollte ein privates Repository erstellt werden.
- Entwickelt die Organisation benutzerdefinierte Software, die einen bestimmten Vorgang bereitstellt, der im Rahmen des gesamten Bereitstellungsprozesses erforderlich ist? Falls ja, sollte ein privates Repository bereitgestellt werden.
- Wenn die Governancerichtlinie der Organisation eine Isolierung erfordert und externe Repositorys nicht direkt der Konfigurationsverwaltung durch die Organisation unterliegen, sollte ein privates Artefaktrepository bereitgestellt werden. Im Rahmen dieses Prozesses kann eine Erstkopie des öffentlichen Repositorys kopiert und in das private Repository integriert werden. Dann kann das öffentliche Repository deaktiviert werden, sodass niemand mehr innerhalb der Organisation darauf zugreifen kann. Dieser Ansatz bewirkt, dass alle Benutzer innerhalb der Organisation nur über ein einziges Repository verfügen, das von der Organisation genehmigt wird, und Konfigurationsabweichungen minimiert werden.

### <a name="single-repository-or-multiple-repositories"></a>Vergleich: Einzelnes Repository vs. mehrere Repositorys 

### <a name="question"></a>Frage
Sollte eine Organisation ein einzelnes Repository planen oder mehrere Repositorys zulassen?

### <a name="answer"></a>Antwort
Im Rahmen der allgemeinen Governance- und Konfigurationsverwaltungsstrategie Ihrer Organisation wird empfohlen, ein zentrales Repository zu verwenden. Wenn Sie mehrere Repositorys verwenden, können diese im Laufe der Zeit zu Silos nicht verwalteter Software werden. Mit einem zentralen Repository können mehrere Teams Artefakte aus diesem Repository für ihre Projekte verwenden. Es bewirkt Standardisierung, Sicherheit sowie einfache Verwaltung und vermeidet doppelten Aufwand. Im Rahmen der Zentralisierung werden die folgenden Aktionen zur langfristigen Verwaltung und Nachhaltigkeit empfohlen:

- Ordnen Sie die Azure Repos demselben Azure Active Directory-Mandanten zu, den das Azure-Abonnement zur Authentifizierung und Autorisierung verwendet.
- Erstellen Sie in Azure Active Directory eine Gruppe mit dem Namen **Alle DevTest Labs-Entwickler**, die zentral verwaltet wird. Jeder Entwickler, der zur Entwicklung von Artefakten beiträgt, sollte in diese Gruppe aufgenommen werden.
- Die gleiche Azure Active Directory-Gruppe kann verwendet werden, um Zugriff auf das Azure Repos-Repository und -Lab zu ermöglichen.
- In den Azure Repos sollten Verzweigungen oder Gabelungen verwendet werden, um ein Entwicklungsrepository vom primären Produktionsrepository zu trennen. Inhalte werden erst nach einem ordnungsgemäßen Code Review mit einem Pull Request dem Masterbranch hinzugefügt. Sobald der Codereviewer die Änderung genehmigt hat, führt ein leitender Entwickler, der für die Wartung des Masterbranchs verantwortlich ist, den aktualisierten Code zusammen. 

## <a name="corporate-security-policies"></a>Unternehmenssicherheitsrichtlinien

### <a name="question"></a>Frage
Wie kann eine Organisation sicherstellen, dass die Sicherheitsrichtlinien des Unternehmens eingehalten werden?

### <a name="answer"></a>Antwort
Eine Organisation kann dies mithilfe der folgenden Aktionen erreichen:

1. Entwickeln und Veröffentlichen einer umfassenden Sicherheitsrichtlinie. Die Richtlinie definiert die Regeln der zulässigen Nutzung von Software und Cloudressourcen. Sie regelt auch, was eindeutig gegen die Richtlinie verstößt. 
2. Entwickeln eines benutzerdefinierten Images, benutzerdefinierten Artefakts und Bereitstellungsprozesses, der die Orchestrierung innerhalb des Sicherheitsbereichs ermöglicht, der mit Active Directory definiert ist. Dieser Ansatz setzt die Unternehmensgrenze durch und legt einen gemeinsamen Steuerelementsatz für die Umgebung fest. Entwickler können diese Umgebungssteuerelemente beim Entwickeln berücksichtigen und im Rahmen ihres Gesamtprozesses einen sicheren Entwicklungslebenszyklus verfolgen. Ziel ist es auch, eine Umgebung zu schaffen, die nicht zu restriktiv ist, wodurch die Entwicklung behindern wäre, sondern einen angemessenen Satz von Steuerelementen. Die Gruppenrichtlinien der Organisationseinheit (OE), die Lab-VMs enthält, können eine Teilmenge der gesamten Gruppenrichtlinien sein, die sich in der Produktion befinden. Alternativ können sie auch eine zusätzliche Menge sein, um identifizierte Risiken ordnungsgemäß zu minimieren.

## <a name="data-integrity"></a>Datenintegrität

### <a name="question"></a>Frage
Wie kann eine Organisation die Datenintegrität gewährleisten, damit Remotingentwickler weder Code entfernen noch Malware oder nicht genehmigte Software integrieren können?

### <a name="answer"></a>Antwort
Es gibt mehrere Kontrollebenen, um die Bedrohung durch externe Berater, Auftragnehmer und Mitarbeiter zu minimieren, die in DevTest Labs remote zusammenarbeiten. 

Wie bereits erwähnt, muss im ersten Schritt eine Acceptable Use Policy ausgearbeitet und definiert werden, die die Folgen eines Verstoßes gegen die Richtlinie klar umreißt. 

Die erste Kontrollebene für den Remotezugriff besteht darin, eine Remotezugriffsrichtlinie über eine VPN-Verbindung anzuwenden, die nicht direkt mit dem Lab verbunden ist. 

Im Rahmen der zweiten Kontrollebene wird ein Satz von Gruppenrichtlinienobjekten angewendet, die das Kopieren und Einfügen über Remotedesktop verhindern. Sie können auch eine Netzwerkrichtlinie implementieren, um keine aus der Umgebung ausgehenden Dienste wie FTP- und RDP-Dienste zuzulassen. Benutzerdefiniertes Routing kann erzwingen, dass der gesamte Azure-Datenverkehr zurück an lokale Umgebungen übermittelt wird. Allerdings kann das Routing nicht alle URLs erfassen, die das Hochladen von Daten ermöglichen, es sei denn, die Steuerung erfolgt über einen Proxy zum Überprüfen von Inhalten und Sitzungen. Öffentliche IP-Adressen können im virtuellen Netzwerk eingeschränkt werden, das DevTest Labs unterstützt, um den Transfer einer externen Netzwerkressource zu untersagen.

Letztendlich muss für die gesamte Organisation der gleiche Einschränkungstyp gelten, der auch alle Methoden von Wechselmedien oder externen URLs berücksichtigen, die einen Beitrag von Inhalten akzeptieren können. Wenden Sie sich an Ihren Sicherheitsexperten, um eine Sicherheitsrichtlinie zu überprüfen und zu implementieren. Weitere Empfehlungen finden Sie unter [Microsoft-Cybersicherheit](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Anwendungsmigration und -integration](devtest-lab-guidance-governance-application-migration-integration.md).
