---
title: Leitfaden zur Lizenzierung von B2B-Kollaboration – Azure Active Directory | Microsoft-Dokumentation
description: Für die Azure Active Directory B2B-Kollaboration sind keine kostenpflichtigen Azure AD-Lizenzen erforderlich, aber es sind auch kostenpflichtige Features für B2B-Gastbenutzer erhältlich.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d208e981f46bd5cc156dd7f764de64638c91efb
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/16/2019
ms.locfileid: "65768176"
---
# <a name="azure-active-directory-b2b-collaboration-licensing-guidance"></a>Leitfaden zur Lizenzierung von Azure Active Directory B2B-Kollaboration

Mit der Azure Active Directory Business-to-Business-Kollaboration (Azure AD B2B-Kollaboration) können Sie externe Benutzer (oder „Gastbenutzer“) zur Verwendung Ihrer kostenpflichtigen Azure AD-Dienste einladen. Für jede kostenpflichtige Azure AD-Lizenz, die Sie einem Benutzer zuweisen, können Sie unter dem Kontingent für externe Benutzer bis zu fünf Gastbenutzer einladen.

Die Lizenzierung von B2B-Gastbenutzern wird basierend auf dem Verhältnis von 1:5 automatisch berechnet und gemeldet. Derzeit ist es nicht möglich, Lizenzen für B2B-Gastbenutzer direkt Gastbenutzern zuzuweisen.

Darüber hinaus können Gastbenutzer kostenlose Azure AD-Features ohne zusätzliche Lizenzanforderungen nutzen. Gastbenutzer haben Zugriff auf kostenlose Azure AD-Features, auch wenn sie über keine kostenpflichtigen Azure AD-Lizenzen verfügen. 

## <a name="examples-calculating-guest-user-licenses"></a>Beispiele: Berechnen der Lizenzen für Gastbenutzer
Nachdem Sie bestimmt haben, wie viele Gastbenutzer Zugriff auf Ihre kostenpflichtigen Azure AD-Dienste benötigen, müssen Sie sicherstellen, dass Sie über genügend kostenpflichtige Azure AD-Lizenzen verfügen, um Gastbenutzer im erforderlichen Verhältnis von 1:5 abzudecken. Hier einige Beispiele:

- Sie möchten 100 Gastbenutzer für Ihre Azure AD-Anwendungen oder Azure AD-Dienste einladen, und Sie möchten allen Gastbenutzern die Zugriffsverwaltung und Bereitstellung zuweisen. Sie möchten zudem für 50 dieser Gastbenutzer die mehrstufige Authentifizierung (MFA) und bedingten Zugriff festlegen. Für diese Kombination benötigen Sie 10 Azure AD Basic-Lizenzen und 10 Azure AD Premium P1-Lizenzen. Wenn Sie Identity Protection-Features für Ihre Gastbenutzer verwenden möchten, benötigen Sie Azure AD Premium P2-Lizenzen im gleichen Verhältnis von 1:5 für die Gastbenutzer.
- Sie möchten 60 Gastbenutzer einladen, für die alle MFA erforderlich ist. Daher müssen Sie über mindestens 12 Azure AD Premium P1-Lizenzen verfügen. Sie haben 10 Mitarbeiter mit Azure AD Premium P1-Lizenzen, sodass entsprechend dem Lizenzierungsverhältnis von 1:5 bis zu 50 Gastbenutzer zulässig sind. Sie müssen zwei zusätzliche Premium P1-Lizenzen erwerben, um 10 zusätzliche Gastbenutzer abzudecken.

## <a name="next-steps"></a>Nächste Schritte

Weitere Artikel zur Azure AD B2B-Zusammenarbeit:

* [Azure Active Directory-Preise](https://azure.microsoft.com/pricing/details/active-directory/)
* [Was ist die Azure AD B2B-Zusammenarbeit?](what-is-b2b.md)
* [Häufig gestellte Fragen zur Azure Active Directory B2B-Zusammenarbeit](faq.md)
