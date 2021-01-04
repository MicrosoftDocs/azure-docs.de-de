---
title: Sichere Entwicklung in Microsoft Azure – bewährte Methoden
description: Beschreibung der bewährten Methoden, wie Sie sichereren Code entwickeln und eine sicherere Anwendung in der Cloud bereitstellen können.
author: TerryLanfear
manager: barbkess
ms.author: terrylan
ms.date: 06/11/2019
ms.topic: article
ms.service: security
ms.subservice: security-develop
services: azure
ms.assetid: 521180dc-2cc9-43f1-ae87-2701de7ca6b8
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.openlocfilehash: baaa311620f5c10948aa3494002ce359cc5dab28
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517171"
---
# <a name="secure-development-best-practices-on-azure"></a>Sichere Entwicklung in Azure – bewährte Methoden
In diese Artikelreihe werden Sicherheitsaktivitäten und -kontrollen vorgestellt, die Sie berücksichtigen sollten, wenn Sie Anwendungen für die Cloud entwickeln. Es werden die Phasen des Microsoft Security Development Lifecycle (SDL) sowie Sicherheitsfragen und -konzepte behandelt, die in jeder Phase des Lebenszyklus zu berücksichtigen sind. Das Ziel ist, Ihnen dabei zu helfen, Aktivitäten und Azure-Dienste zu definieren, die Sie in jeder Phase des Lebenszyklus verwenden können, um eine sicherere Anwendung zu entwerfen, zu entwickeln und bereitzustellen.

Die Empfehlungen in den Artikeln lassen sich auf unsere Erfahrungen mit der Sicherheit in Azure und die Erfahrungen unserer Kunden zurückführen. Sie können diese Artikel als Referenz für die Punkte verwenden, die Sie in einer bestimmten Phase Ihres Entwicklungsprojekts berücksichtigen sollten, aber es empfiehlt sich, dass Sie jeden Artikel mindestens einmal vollständig lesen. Beim Lesen aller Artikel werden Sie in Konzepte eingeführt, die Sie in früheren Phasen Ihres Projekts ggf. übersehen haben. Das Implementieren dieser Konzepte vor der Veröffentlichung Ihres Produkts kann Ihnen helfen, sichere Software zu erstellen, Anforderungen an die Sicherheitskonformität zu erfüllen und die Entwicklungskosten zu senken.

Diese Artikel sind als Ressource für Softwaredesigner, -entwickler und -tester auf allen Stufen vorgesehen, die sichere Azure-Lösungen erstellen und bereitstellen.

## <a name="overview"></a>Übersicht

Sicherheit ist einer der wichtigsten Aspekte jeder Anwendung, und es ist nicht einfach, alles richtig zu machen. Glücklicherweise stellt Azure viele Dienste bereit, die Ihnen dabei helfen können, Ihre Anwendung in der Cloud zu schützen. Diese Artikel befassen sich mit Aktivitäten und Azure-Diensten, die Sie in jeder Phase Ihres Softwareentwicklungslebenszyklus implementieren können, damit es Ihen möglich ist, sichereren Code zu entwickeln und eine sicherere Anwendung in der Cloud bereitzustellen.

## <a name="security-development-lifecycle"></a>Security Development Lifecycle

Die Einhaltung der bewährten Methoden für die Entwicklung von sicherer Software erfordert die Berücksichtigung von Sicherheit in jeder Phase des Lebenszyklus der Softwareentwicklung, von der Anforderungsanalyse bis zur Wartung, unabhängig von der Projektmethodik ([Wasserfallmodell](https://en.wikipedia.org/wiki/Waterfall_model), [Agile Softwareentwicklung](https://en.wikipedia.org/wiki/Agile_software_development) oder [DevOps](https://en.wikipedia.org/wiki/DevOps)). Im Zuge von schwerwiegenden Verletzungen der Datensicherheit und der Ausnutzung von betrieblichen Sicherheitslücken gelangen immer mehr Entwickler zu der Erkenntnis, dass die Sicherheit während des gesamten Entwicklungsprozesses berücksichtigt werden muss.

Je später Sie ein Problem in Ihrem Entwicklungslebenszyklus beheben, desto teurer wird das Beheben des Problems. Sicherheitsprobleme sind da keine Ausnahme. Wenn Sie Sicherheitsprobleme in den frühen Phasen der Softwareentwicklung ignorieren, kann es sein, dass in jeder späteren Phase die Sicherheitsrisiken der jeweils vorhergehenden Phase übernommen werden. Ihr Endprodukt hat akkumuliert mehrere Sicherheitsprobleme und enthält möglicherweise eine Sicherheitsverletzung. Die Einbeziehung von Sicherheit in jeder Phase des Entwicklungslebenszyklus hilft Ihnen, Probleme frühzeitig zu erkennen und Ihre Entwicklungskosten zu senken.

Wir folgen den Phasen des [Microsoft Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)), um Aktivitäten und Azure-Dienste einzuführen, mit denen Sie in jeder Phase des Lebenszyklus Verfahren zur Entwicklung von sicherer Software erfüllen können.

Die SDL-Phasen sind:

  - Training
  - Requirements (Anforderungen)
  - Entwurf
  - Implementierung
  - Überprüfung
  - Release
  - Antwort

![Security Development Lifecycle](./media/secure-dev-overview/01-sdl-phase.png)

In diesen Artikeln sind die SDL-Phasen in Entwerfen, Entwickeln und Bereitstellen gruppiert.

## <a name="engage-your-organizations-security-team"></a>Einbinden des Sicherheitsteams Ihrer Organisation

Möglicherweise verfügt Ihr Unternehmen über ein formales Anwendungssicherheitsprogramm, das Sie während des gesamten Entwicklungslebenszyklus bei Sicherheitsaktivitäten unterstützt. Wenn Ihre Organisation Sicherheits- und Compliance-Teams hat, sollten Sie diese einbeziehen, bevor Sie mit der Entwicklung Ihrer Anwendung beginnen. Fragen Sie die Teams in jeder Phase des SDL, ob es Aufgaben gibt, die Sie übersehen haben.

Uns ist bewusst, dass viele Leser möglicherweise kein Sicherheits- oder Compliance-Team haben, das einbezogen werden kann. Diese Artikel versetzen Sie in die Lage, sich mit den Sicherheitsfragen und -entscheidungen vertraut zu machen, die Sie in jeder Phase des SDL berücksichtigen müssen.

## <a name="resources"></a>Ressourcen

Verwenden Sie die folgenden Ressourcen, um mehr über die Entwicklung sicherer Anwendungen zu erfahren und Informationen zu erhalten, wie Sie Ihre Anwendungen in Azure schützen können:

[Microsoft Security Development Lifecycle (SDL)](/previous-versions/windows/desktop/cc307891(v=msdn.10)): Der SDL ist ein Softwareentwicklungsprozess von Microsoft, der Entwicklern hilft, sicherere Software zu entwickeln. Es hilft Ihnen, die jeweiligen Sicherheitsanforderungen zu erfüllen und gleichzeitig die Entwicklungskosten zu senken.

[Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page): OWASP ist eine Onlinecommunity, von der frei verfügbare Artikel, Methoden, Dokumentationen, Tools und Technologien im Bereich Sicherheit von Webanwendungen erstellt werden.

[Pushing Left, Like a Boss](https://code.likeagirl.io/pushing-left-like-a-boss-part-1-80f1f007da95?WT.mc_id=docs-blog-tajanca): Eine Reihe von Onlineartikeln, in denen verschiedene Arten von Anwendungssicherheitsaktivitäten beschrieben sind, die Entwickler ausführen sollten, um sichereren Code zu erstellen.

[Microsoft Identity Platform](../../active-directory/develop/index.yml): Die Microsoft Identity Platform ist eine Weiterentwicklung des Azure AD-Identitätsdiensts und der zugehörigen Entwicklerplattform. Es handelt sich um eine Plattform mit vollem Funktionsumfang, die einen Authentifizierungsdienst, Open-Source-Bibliotheken, Anwendungsregistrierung und -konfiguration, eine vollständige Entwicklerdokumentation, Codebeispiele und andere Inhalte für Entwickler umfasst. Microsoft Identity Platform unterstützt die branchenüblichen Protokolle wie OAuth 2.0 und OpenID Connect.

[Security best practices for Azure solutions](https://azure.microsoft.com/resources/security-best-practices-for-azure-solutions/): Eine Sammlung von bewährten Sicherheitsmethoden, die Sie verwenden sollten, wenn Sie Cloudlösungen in Azure entwerfen, bereitstellen und verwalten. Dieser Artikel ist als Ressource für IT-Experten ausgelegt. Dazu gehören beispielsweise Designer, Architekten, Entwickler und Tester, die sichere Azure-Lösungen erstellen und bereitstellen.

[Sicherheits- und Compliance-Blueprints in Azure](https://servicetrust.microsoft.com/ViewPage/BlueprintOverview): Azure-Blaupausen für Sicherheit und Compliance sind Ressourcen, die Ihnen helfen können, cloudbasierte Anwendungen zu entwickeln und einzuführen, die strengen Vorschriften und Standards entsprechen.

## <a name="next-steps"></a>Nächste Schritte
In den folgenden Artikeln empfehlen wir die Sicherheitskontrollen und -aktivitäten, die Ihnen beim Entwerfen, Entwickeln und Bereitstellen von sicheren Anwendungen helfen können.

- [Entwerfen sicherer Anwendungen](secure-design.md)
- [Entwickeln sicherer Anwendungen](secure-develop.md)
- [Bereitstellen sicherer Anwendungen](secure-deploy.md)