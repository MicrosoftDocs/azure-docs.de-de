---
title: Entwerfen von Hybrididentitäten – Anforderungen für das Content Management in Azure | Microsoft-Dokumentation
description: Sie erhalten einen Einblick, wie Sie die Content Management-Anforderungen für Ihr Unternehmen ermitteln. Wenn Benutzer ein eigenes Gerät verwenden, verfügen sie möglicherweise auch über verschiedene anwendungsspezifische Anmeldeinformationen. Es ist wichtig zu unterscheiden, welche Inhalte mit persönlichen Anmeldeinformationen und welche Inhalte mit den Anmeldeinformationen des Unternehmens erstellt wurden. Ihre Identitätslösung sollte in der Lage sein, mit Clouddiensten zu interagieren, um für Endbenutzer eine nahtlose Erfahrung zu schaffen, und dabei den Datenschutz sicherzustellen und den Schutz vor Datenlecks zu erhöhen.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: dd1ef776-db4d-4ab8-9761-2adaa5a4f004
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/29/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 57990fda7475b95bd6582fa5a495ac8d24aa55d5
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94408531"
---
# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Ermitteln der Content Management-Anforderungen für Ihre Hybrid-Identitätslösung
Wenn Sie die Content Management-Anforderungen Ihres Unternehmens genau kennen, kann sich dies direkt auf die Entscheidung auswirken, welche Hybrid-Identitätslösung verwendet werden soll. Aufgrund der Nutzung mehrerer Geräte und der Mitnahme eigener Geräte durch Benutzer ([BYOD](/mem/intune/fundamentals/byod-technology-decisions)) muss das Unternehmen seine Daten schützen und gleichzeitig dafür sorgen, dass der Datenschutz für Benutzer gewahrt bleibt. Wenn Benutzer ein eigenes Gerät verwenden, verfügen sie möglicherweise auch über verschiedene anwendungsspezifische Anmeldeinformationen. Es ist wichtig zu unterscheiden, welche Inhalte mit persönlichen Anmeldeinformationen und welche Inhalte mit den Anmeldeinformationen des Unternehmens erstellt wurden. Ihre Identitätslösung sollte in der Lage sein, mit Clouddiensten zu interagieren, um für Endbenutzer eine nahtlose Erfahrung zu schaffen, und dabei den Datenschutz sicherzustellen und den Schutz vor Datenlecks zu erhöhen. 

Ihre Identitätslösung wird von unterschiedlichen technischen Steuerungen genutzt, um Content Management bereitzustellen. Dies ist in der folgenden Abbildung dargestellt:

![Sicherheitskontrollen](./media/plan-hybrid-identity-design-considerations/securitycontrols.png)

**Sicherheitskontrollen, für die Ihr Identitätsverwaltungssystem verwendet wird**

Im Allgemeinen wird Ihr Identitätsverwaltungssystem in den folgenden Bereichen für Content Management-Anforderungen genutzt:

* Datenschutz: Identifizieren des Benutzers, dem eine Ressource gehört, und Anwenden der richtigen Kontrollen, um die Integrität zu wahren.
* Klassifizierung von Daten: Identifizieren des Benutzers bzw. der Gruppe und der Zugriffsebene für ein Objekt nach seiner Klassifizierung. 
* Schutz vor Datenlecks: Sicherheitskontrollen, die als Schutz vor Datenlecks dienen, müssen mit dem Identitätssystem interagieren, um die Identität des Benutzers zu überprüfen. Dies ist auch für Überwachungspfadzwecke wichtig.

> [!NOTE]
> Weitere Informationen zu den bewährten Methoden und Richtlinien für die Datenklassifizierung finden Sie unter [Datenklassifizierung für Cloud Readiness](https://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) .
> 
> 

Stellen Sie beim Planen Ihrer Hybrid-Identitätslösung sicher, dass die folgenden Fragen gemäß den Anforderungen Ihres Unternehmens beantwortet werden:

* Verfügt Ihr Unternehmen über Sicherheitskontrollen zur Durchsetzung des Datenschutzes?
  * Wenn ja: Können die Sicherheitskontrollen in die Hybrididentitätslösung integriert werden, die Sie einführen möchten?
* Wird in Ihrem Unternehmen die Datenklassifizierung verwendet?
  * Wenn ja: Kann die aktuelle Lösung in die Hybrid-Identitätslösung integriert werden, die Sie einführen möchten?
* Verfügt Ihr Unternehmen derzeit über eine Lösung als Schutz vor Datenlecks? 
  * Wenn ja: Kann die aktuelle Lösung in die Hybrid-Identitätslösung integriert werden, die Sie einführen möchten?
* Muss Ihr Unternehmen den Zugriff auf Ressourcen überwachen?
  * Wenn ja: Für welche Art von Ressourcen gilt dies?
  * Wenn ja: Welche Informationsebene ist hierbei erforderlich?
  * Wenn ja: Wo muss sich das Überwachungsprotokoll befinden? Lokal oder in der Cloud?
* Muss Ihr Unternehmen E-Mails verschlüsseln, die vertrauliche Daten enthalten (Sozialversicherungsnummern, Kreditkartennummern usw.)?
* Muss Ihr Unternehmen alle Dokumente/Inhalte verschlüsseln, die mit externen Geschäftspartnern ausgetauscht werden?
* Muss Ihr Unternehmen für bestimmte Arten von E-Mails (Nicht beantworten, Nicht weiterleiten) Unternehmensrichtlinien durchsetzen?

> [!NOTE]
> Notieren Sie sich alle Antworten, und stellen Sie sicher, dass Ihnen die Begründung der Antwort jeweils klar ist. [Definieren der Strategie zum Schutz von Daten](plan-hybrid-identity-design-considerations-data-protection-strategy.md) sind die verfügbaren Optionen und die jeweiligen Vor- und Nachteile beschrieben.  Indem Sie diese Fragen beantworten, wählen Sie aus, welche Option Ihre Geschäftsanforderungen am besten erfüllt.
> 
> 

## <a name="next-steps"></a>Nächste Schritte
[Bestimmen der Zugriffssteuerungsanforderungen](plan-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Weitere Informationen
[Überlegungen zum Entwurf – Übersicht](plan-hybrid-identity-design-considerations-overview.md)