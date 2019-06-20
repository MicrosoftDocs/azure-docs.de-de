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
ms.openlocfilehash: c5514a43602106cf045b575d289e02b591468359
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60561648"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---resources"></a>Governance der Azure DevTest Labs-Infrastruktur – Ressourcen
Dieser Artikel behandelt die Ausrichtung und die Verwaltung von Ressourcen für DevTest Labs in Ihrer Organisation. 

## <a name="align-within-an-azure-subscription"></a>Ausrichtung an einem Azure-Abonnement 

### <a name="question"></a>Frage
Wie richte ich DevTest Labs-Ressourcen im Rahmen eines Azure-Abonnements aus?

### <a name="answer"></a>Antwort
Bevor eine Organisation damit beginnt, Azure für die allgemeine Anwendungsentwicklung einzusetzen, sollten die IT-Planer zunächst überprüfen, wie die Funktionalität im Rahmen des Gesamtportfolios an Diensten eingeführt werden soll. Die zu überprüfenden Bereiche sollten die folgenden Anliegen berücksichtigen:

- Wie werden die mit dem Lebenszyklus der Anwendungsentwicklung einhergehenden Kosten gemessen?
- Wie bringt die Organisation das vorgeschlagene Dienstangebot mit der Sicherheitsrichtlinie des Unternehmens in Einklang? 
- Ist Segmentierung erforderlich, um Entwicklungs- und Produktionsumgebung zu trennen? 
- Welche Steuerungsmechanismen werden für eine langfristig komfortable Verwaltung, für Stabilität und Wachstum eingeführt?

Als **erste empfohlene Praxis** sollte die Azure-Taxonomie von Organisationen überprüft werden, die einen Hinweis auf die Grenzen zwischen Produktions- und Entwicklungsabonnements gibt. Im folgenden Diagramm ermöglicht die vorgeschlagene Taxonomie eine logische Trennung von Entwicklungs-/Test- und Produktionsumgebung. Bei diesem Ansatz kann eine Organisation Abrechnungscodes einführen, um die für die einzelnen Umgebungen anfallenden Kosten separat nachzuverfolgen. Weitere Informationen finden Sie unter [Prescriptive subscription governance](/azure/architecture/cloud-adoption/appendix/azure-scaffold) (Präskriptive Abonnementgovernance). Darüber hinaus können Sie [Azure-Tags](../azure-resource-manager/resource-group-using-tags.md) verwenden, um Ressourcen zu Nachverfolgungs- und Abrechnungszwecken zu ordnen.

Als **zweite empfohlene Praxis** sollte das DevTest-Abonnement innerhalb des Azure Enterprise-Portals aktiviert werden. Dies ermöglicht Organisationen das Ausführen von Clientbetriebssystemen, die normalerweise in einem Azure Enterprise-Abonnement nicht verfügbar sind. Verwenden Sie außerdem Unternehmenssoftware, bei der Sie nur Computeleistungen bezahlen und sich nicht um die Lizenzierung zu kümmern brauchen. Dadurch wird sichergestellt, dass die Abrechnung für vorgesehene Dienste, einschließlich Katalogimages in IaaS wie etwa Microsoft SQL Server, ausschließlich nutzungsbasiert erfolgt. Details zum Azure DevTest-Abonnement finden Sie [hier](https://azure.microsoft.com/offers/ms-azr-0148p/) für EA-Kunden (Enterprise Agreement) und [hier](https://azure.microsoft.com/offers/ms-azr-0023p/) für Kunden mit nutzungsbasierter Bezahlung.

![Ausrichtung der Ressourcen an Abonnements](./media/devtest-lab-guidance-governance/resource-alignment-with-subscriptions.png)

Dieses Modell bietet einer Organisation die Flexibilität, Azure DevTest Labs bedarfsorientiert bereitzustellen. Eine Organisation kann Hunderte Labs für verschiedene Geschäftseinheiten mit 100 bis 1000 parallel ausgeführten virtuellen Computern unterstützen. Es befördert das Konzept einer konzernweit zentralen Lab-Lösung, bei der die stets gleichen Prinzipien von Konfigurationsmanagement und Sicherheitsmechanismen durchgesetzt werden können.

Dieses Modell stellt darüber hinaus sicher, dass die Organisation die Ressourcengrenzen im Rahmen ihres Azure-Abonnements nicht erschöpft. Details zu Grenzwerten für Abonnements und Dienste finden Sie unter [Azure subscription and service limits, quotas, and constraints](../azure-subscription-service-limits.md) (Grenzwerte für Azure-Abonnements und -Dienste, Kontingente und Einschränkungen). Der DevTest Labs-Bereitstellungsprozess kann eine große Anzahl Ressourcengruppen in Anspruch nehmen. Über eine Supportanfrage beim Azure DevTest-Abonnement können Sie eine Heraufsetzung der Grenzwerte anfordern. Die Ressourcen innerhalb des Produktionsabonnements sind nicht betroffen, wenn die Nutzung des Entwicklungsabonnements zunimmt. Weitere Informationen zum Skalieren von DevTest Labs finden Sie unter [Scale quotas and limits in DevTest Labs](devtest-lab-scale-lab.md) (Skalierungskontingente und -beschränkungen in DevTest Labs).

Ein gemeinsamer Grenzwert auf Abonnementebene, dem Rechnung zu tragen ist, besteht in der Zuweisung des Netzwerk-IP-Adressraums, damit sowohl das Produktions- als auch das Entwicklungsabonnement unterstützt werden. Diese Zuweisungen sollten Wachstum in Lauf der Zeit berücksichtigen (ausgehend von lokaler Konnektivität oder einer anderen Netzwerktopologie, die eine Verwaltung des Netzwerkstapels durch das Unternehmen erfordert, statt einfach die Implementierung in Azure zu nutzen). Die empfohlene Praxis besteht in der Verwendung einiger virtueller Netzwerke, denen ein großes IP-Adresspräfix zugewiesen ist und die in viele große Subnetze aufgeteilt sind, gegenüber einer Lösung mit vielen virtuellen Netzwerken mit kleinen Subnetzen. Beispielsweise können Sie mit 10 Abonnements 10 virtuelle Netzwerke definieren (eins für jedes Abonnement). Alle Labs, für die keine Isolierung erforderlich ist, können im VNet des Abonnements gemeinsam das gleiche Subnetz nutzen.

## <a name="maintain-naming-conventions"></a>Aufrechterhalten von Benennungskonventionen

### <a name="question"></a>Frage
Wie halte ich eine übergreifende Benennungskonvention in meiner DevTest Labs-Umgebung aufrecht?

### <a name="answer"></a>Antwort
Es kann sinnvoll sein, aktuelle Benennungskonventionen im Unternehmen auf den Azure-Betrieb auszudehnen und sie über die gesamte DevTest Labs-Umgebung konsistent zu machen.

Wir empfehlen, bei der Bereitstellung von DevTest Labs bestimmte Ausgangsrichtlinien zu implementieren. Sie stellen diese Richtlinien mit einem zentralen Skript und JSON-Vorlagen bereit, um Konsistenz durchzusetzen. Benennungsrichtlinien können mithilfe von Azure-Richtlinien implementiert werden, die auf Abonnementebene angewendet werden. JSON-Beispiele für Azure Policy finden Sie unter [Azure Policy-Beispiele](../governance/policy/samples/index.md).

## <a name="number-of-users-per-lab-and-labs-per-organization"></a>Anzahl der Benutzer pro Lab und Labs pro Organisation

### <a name="question"></a>Frage 
Wir lässt sich das Verhältnis von Benutzern pro Lab zur Gesamtzahl der innerhalb einer gesamten Organisation erforderlichen Labs bestimmen?

### <a name="answer"></a>Antwort
Wir empfehlen, dass Geschäftseinheiten und Entwicklungsgruppen, die dem gleichen Entwicklungsprojekt angehören, dem gleichen Lab zugeordnet werden. Dadurch können für beide Gruppen die gleichen Arten von Richtlinien, Images und Richtlinien zum Herunterfahren angewendet werden. 

Ggf. müssen auch geografische Grenzen berücksichtigt werden. Beispielsweise benutzen Entwickler im Nordosten der USA möglicherweise ein Lab, das in USA Osten 2 bereitgestellt ist. Demgegenüber sind Entwickler in Dallas, Texas, und Denver, Colorado, gehalten, eine Ressource in USA Süden-Mitte zu verwenden. Wenn es zur einer Zusammenarbeit mit einem externen Drittanbieter kommt, werden beide möglicherweise einem Lab zugewiesen, das nicht von internen Entwicklern verwendet wird. 

Sie können auch ein Lab für ein bestimmtes Projekt in Azure DevOps Projects verwenden. Anschließend wenden Sie Sicherheit in Form einer angegebenen Azure Active Directory-Gruppe an, was den Zugriff auf beide Ressourcensätze ermöglicht. Das dem Lab zugewiesene virtuelle Netzwerk kann eine weitere Grenze zur Konsolidierung der Benutzer bilden.

## <a name="deletion-of-resources"></a>Löschen von Ressourcen

### <a name="question"></a>Frage
Wie lässt sich das Löschen von Ressourcen in einem Lab verhindern?

### <a name="answer"></a>Antwort
Es empfiehlt sich, ordnungsgemäße Berechtigungen auf Lab-Ebene festzulegen, so dass nur autorisierte Benutzer Ressourcen löschen oder Lab-Richtlinien ändern können. Entwickler sollten innerhalb der Gruppe **DevTest Labs-Benutzer** platziert werden. Der Entwicklungsleiter oder der Infrastruktur-Lead sollte als **DevTest Labs-Besitzer** festgelegt werden. Wir empfehlen, nur zwei Lab-Besitzer festzulegen. Diese Richtlinie wird auf das Coderepository ausgeweitet, um Beschädigungen zu vermeiden. Lab-Benutzer verfügen über Rechte zur Nutzung von Ressourcen, sie können aber keine Lab-Richtlinien aktualisieren. Der folgende Artikel listet die Rollen und Rechte auf, über die jede integrierte Gruppe innerhalb eines Labs verfügt: [Hinzufügen von Besitzern und Benutzern in Azure DevTest Labs](devtest-lab-add-devtest-user.md).

## <a name="move-lab-to-another-resource-group"></a>Verschieben eines Labs in eine andere Ressourcengruppe 

### <a name="question"></a>Frage
Wird das Verschieben eines Labs in eine andere Ressourcengruppe unterstützt?

### <a name="answer"></a>Antwort
Ja. Navigieren Sie von der Startseite Ihres Labs zur Seite „Ressourcengruppe“. Wählen Sie dann auf der Symbolleiste **Verschieben** aus, und wählen Sie das Lab aus, das Sie in eine andere Ressourcengruppe verschieben möchten. Beim Erstellen eines Labs wird für Sie automatisch eine Ressourcengruppe erstellt. Allerdings kann es sinnvoll sein, das Lab in eine andere Ressourcengruppe zu verschieben, die den Benennungskonventionen des Unternehmens entspricht. 

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen finden Sie unter [Verwalten von Kosten und Besitz](devtest-lab-guidance-governance-cost-ownership.md).
