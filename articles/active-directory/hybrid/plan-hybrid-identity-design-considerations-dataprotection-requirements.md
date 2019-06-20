---
title: Entwerfen von Hybrididentitäten – Anforderungen für den Datenschutz in Azure | Microsoft-Dokumentation
description: Wenn Sie eine Hybrid-Identitätslösung planen, müssen Sie zuerst die für Ihr Unternehmen geltenden Datenschutzanforderungen und die Möglichkeiten ermitteln, diese bestmöglich zu erfüllen.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 40dc4baa-fe82-4ab6-a3e4-f36fa9dcd0df
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 056d0caf2bfc6e99d5d659a2561bc41844feb79a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64918777"
---
# <a name="plan-for-enhancing-data-security-through-a-strong-identity-solution"></a>Planen einer Verbesserung der Datensicherheit mit einer starken Identitätslösung
Um Daten zu schützen, müssen Sie zuerst in Erfahrung bringen, wer auf diese Daten zugreifen kann. Außerdem benötigen Sie eine Identitätslösung, die in Ihr System integrierbar ist, um Authentifizierung und Autorisierung bereitstellen zu können. Authentifizierung und Autorisierung werden häufig miteinander verwechselt, und ihre jeweilige Rolle wird falsch verstanden. Dabei handelt es sich um unterschiedliche Funktionen, wie in der folgenden Abbildung dargestellt:

![Lebenszyklus von mobilen Geräten](./media/plan-hybrid-identity-design-considerations/mobile-devicemgt-lifecycle.png)

**Lebenszyklusphasen der Mobilgerätverwaltung**

Beim Planen Ihrer Hybrid-Identitätslösung müssen Sie die Anforderungen für den Datenschutz kennen, die für Ihr Unternehmen gelten. Außerdem müssen Sie wissen, wie Sie diese Anforderungen am besten erfüllen.

> [!NOTE]
> Nach Abschluss der Planung für den Schutz der Daten sollten Sie sich die Informationen unter [Ermitteln der Anforderungen an die Multi-Factor Authentication für Ihre Hybrididentitätslösung](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md) durchlesen. So können Sie sicherstellen, dass Ihre Auswahl in Bezug auf Multi-Factor Authentication-Anforderungen nicht negativ von den Entscheidungen beeinflusst wird, die Sie in diesem Abschnitt getroffen haben.
> 
> 

## <a name="determine-data-protection-requirements"></a>Bestimmen der Datenschutzanforderungen
Im Zeitalter der Mobilität haben die meisten Unternehmen das gleiche Ziel: Benutzer sollen mit ihren mobilen Geräten lokal oder per Remoteverbindung an jedem Ort produktiv arbeiten können, um die Produktivität zu steigern. Unternehmen, die diese Anforderung haben, sorgen sich auch um die zahlreichen Bedrohungen, denen begegnet werden muss, um die Daten des Unternehmens zu schützen und den Datenschutz für Benutzer zu wahren. Die jeweiligen Anforderungen unterscheiden sich hierbei von Unternehmen zu Unternehmen. Unterschiedliche Compliance-Regeln, die häufig von der Branche des Unternehmens abhängen, führen zu unterschiedlichen Entwurfsentscheidungen. 

Branchenunabhängig gibt es jedoch einige Sicherheitsaspekte, die immer geprüft werden sollten. Diese werden im nächsten Abschnitt erläutert.

## <a name="data-protection-paths"></a>Wege zum Datenschutz
![Wege zum Datenschutz](./media/plan-hybrid-identity-design-considerations/data-protection-paths.png)

**Wege zum Datenschutz**

Im obigen Diagramm wird zuerst die Identitätskomponente überprüft, bevor auf die Daten zugegriffen wird. Diese Daten können sich im Zeitraum des Zugriffs in verschiedenen Zuständen befinden. Jede Zahl in diesem Diagramm steht für einen Pfad, unter dem sich Daten zu bestimmten Zeitpunkten befinden können. Diese Zahlen werden im Folgenden erläutert:

1. Schutz von Daten auf Geräteebene
2. Schutz von Daten während der Übertragung
3. Schutz von Daten im lokalen Ruhezustand
4. Schutz von Daten im Ruhezustand in der Cloud

Die Hybrid-Identitätslösung muss sowohl lokale als auch Cloudressourcen zur Identitätsverwaltung nutzen können, um den Benutzer zu identifizieren, bevor ihm Zugriff auf die Daten gewährt wird. Beim Planen der Hybrid-Identitätslösung sollten Sie auch sicherstellen, dass Sie die folgenden Fragen den Anforderungen Ihrer Organisation entsprechend beantworten können:

## <a name="data-protection-at-rest"></a>Schutz von Daten im Ruhezustand
Unabhängig davon, an welchem Ort sich die Daten im Ruhezustand befinden (Gerät, Cloud oder lokal), ist die Durchführung einer Bewertung wichtig, um die jeweiligen Unternehmensanforderungen zu verstehen. Stellen Sie für diesen Bereich sicher, dass die folgenden Fragen gestellt werden:

* Müssen in Ihrem Unternehmen ruhende Daten geschützt werden?
  * Wenn ja: Kann die Hybrid-Identitätslösung in Ihre derzeitige lokale Infrastruktur integriert werden?
  * Wenn ja: Kann die Hybrid-Identitätslösung in die Workloads der Cloud integriert werden?
* Ist die Cloudidentitätsverwaltung in der Lage, die Anmeldeinformationen der Benutzer und andere Daten zu schützen, die in der Cloud gespeichert sind?

## <a name="data-protection-in-transit"></a>Schutz von Daten während der Übertragung
Daten, die zwischen dem Gerät und dem Rechenzentrum oder dem Gerät und der Cloud übertragen werden, müssen geschützt werden. Mit der Übertragung ist aber nicht unbedingt immer ein Kommunikationsprozess mit einer Komponente außerhalb Ihres Clouddiensts gemeint. Es geht auch um interne Übertragungen, z. B. zwischen zwei virtuellen Netzwerken. Stellen Sie für diesen Bereich sicher, dass die folgenden Fragen gestellt werden:

* Müssen Daten während der Übertragung in Ihrem Unternehmen geschützt werden?
  * Wenn ja: Kann die Hybrid-Identitätslösung in sichere Kontrollmechanismen integriert werden, z. B. SSL/TLS?
* Sorgt die Cloud-Identitätsverwaltung dafür, dass der Datenverkehr an den Verzeichnisspeicher bzw. im Verzeichnisspeicher (innerhalb und zwischen Rechenzentren) immer signiert ist?

## <a name="compliance"></a>Compliance
Vorschriften, Gesetze und regulierende Compliance-Anforderungen hängen von der Branche ab, in der Ihr Unternehmen tätig ist. Unternehmen aus Branchen mit starker Regulierung müssen sich um die Aspekte der Identitätsverwaltung kümmern, die mit Compliance-Problemen verbunden sind. Vorschriften wie „Sarbanes-Oxley“ (SOX), „Health Insurance Portability and Accountability Act“ (HIPAA), „Gramm-Leach-Bliley Act“ (GLBA) und der „Payment Card Industry Data Security Standard“ (PCI DSS) sind sehr streng, was die Identität und den Zugriff betrifft. Die Hybrid-Identitätslösung, die von Ihrem Unternehmen eingeführt wird, muss über die Kernfunktionen verfügen, mit denen eine oder mehrere dieser Vorschriften erfüllt werden. Stellen Sie für diesen Bereich sicher, dass die folgenden Fragen gestellt werden:

* Ist die Hybrid-Identitätslösung mit den gesetzlichen Vorschriften für Ihr Unternehmen kompatibel?
* Verfügt die Hybrid-Identitätslösung über integrierte 
* Funktionen, die Ihr Unternehmen in die Lage versetzen, die gesetzlichen Vorschriften zu erfüllen? 

> [!NOTE]
> Notieren Sie sich alle Antworten, und stellen Sie sicher, dass Ihnen die Begründung der Antwort jeweils klar ist. [Definieren der Strategie zum Schutz von Daten](plan-hybrid-identity-design-considerations-data-protection-strategy.md) sind die verfügbaren Optionen und die jeweiligen Vor- und Nachteile beschrieben.  Indem Sie diese Fragen beantworten, wählen Sie aus, welche Option Ihre Geschäftsanforderungen am besten erfüllt.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
 [Bestimmen der Content Management-Anforderungen](plan-hybrid-identity-design-considerations-contentmgt-requirements.md)

## <a name="see-also"></a>Siehe auch
[Überlegungen zum Entwurf – Übersicht](plan-hybrid-identity-design-considerations-overview.md)

