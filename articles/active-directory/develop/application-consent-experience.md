---
title: Umgebungen für die Azure AD-App-Zustimmung
titleSuffix: Microsoft identity platform
description: Erfahren Sie mehr über die Azure AD-Einwilligungserfahrungen und deren Verwendung beim Verwalten und Entwickeln von Anwendungen in Azure AD
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: ryanwi
ms.reviewer: zachowd
ms.openlocfilehash: c4a4af81c6a216119ae2e1b0221c06ddc349452f
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 10/23/2020
ms.locfileid: "92478132"
---
# <a name="understanding-azure-ad-application-consent-experiences"></a>Grundlegendes zu Einwilligungserfahrungen für Azure AD-Anwendungen

Erfahren Sie mehr über die Einwilligungsbenutzererfahrung für Azure Active Directory-Anwendungen (Azure AD). Sie können also auf intelligente Weise Anwendungen für Ihre Organisation verwalten und/oder Anwendungen mit einer nahtloseren Einwilligungserfahrung entwickeln.

## <a name="consent-and-permissions"></a>Einwilligung und Berechtigungen

Einwilligung ist der Prozess, mit dem ein Benutzer einer Anwendung die Autorisierung für den Zugriff auf geschützte Ressourcen in seinem Auftrag gewährt. Ein Administrator oder Benutzer kann zur Einwilligung aufgefordert werden, um den Zugriff auf seine Organisations-/individuellen Daten zuzulassen.

Die tatsächliche Benutzererfahrung des Erteilens der Einwilligung variiert in Abhängigkeit von den auf dem Mandanten des Benutzers festgelegten Richtlinien, dem Gültigkeitsbereich der Autorität (oder Rolle) des Benutzers und dem Typ der [Berechtigungen](../azuread-dev/v1-permissions-consent.md), die von der Clientanwendung angefordert werden. Dies bedeutet, dass Anwendungsentwickler und Mandantenadministratoren eine gewisse Kontrolle über die Einwilligungserfahrung haben. Administratoren besitzen die Flexibilität, Richtlinien für einen Mandanten oder eine App festzulegen oder zu deaktivieren, um die Einwilligungserfahrung in ihrem Mandanten zu kontrollieren. Anwendungsentwickler können vorgeben, welche Typen von Berechtigungen angefordert werden, und ob sie Benutzer durch den Benutzereinwilligungsflow bzw. den Administratoreinwilligungsflow führen möchten.

- **Benutzereinwilligungsflow** ist, wenn ein Anwendungsentwickler Benutzer mit der Absicht zum Autorisierungsendpunkt weiterleitet, die Einwilligung nur für den aktuellen Benutzer aufzuzeichnen.
- **Administratoreinwilligungsflow** ist, wenn ein Anwendungsentwickler Benutzer mit der Absicht zum Administratoreinwilligungs-Endpunkt weiterleitet, die Einwilligung für den gesamten Mandanten aufzuzeichnen. Um sicherzustellen, dass der Administratoreinwilligungsflow ordnungsgemäß funktioniert, müssen Anwendungsentwickler alle Berechtigungen in der Eigenschaft `RequiredResourceAccess` im Anwendungsmanifest auflisten. Weitere Informationen finden Sie unter [Anwendungsmanifest](./reference-app-manifest.md).

## <a name="building-blocks-of-the-consent-prompt"></a>Bausteine der Einwilligungsaufforderung

Die Einwilligungsaufforderung ist darauf ausgelegt, sicherzustellen, dass Benutzer über ausreichende Informationen verfügen, um zu bestimmen, ob sie der Clientanwendung so vertrauen, dass sie auf geschützte Ressourcen in ihrem Namen zugreifen darf. Das Verständnis der Bausteine hilft Benutzern, die Ihre Einwilligung geben, fundiertere Entscheidungen zu treffen, und hilft Entwicklern, bessere Benutzererfahrungen zu erstellen.

Das Diagramm und die Tabelle im Folgenden bieten Informationen über die Bausteine der Einwilligungsaufforderung.

![Bausteine der Einwilligungsaufforderung](./media/application-consent-experience/consent_prompt.png)

| # | Komponente | Zweck |
| ----- | ----- | ----- |
| 1 | Benutzer-ID | Dieser Bezeichner stellt den Benutzer dar, von dem die Clientanwendung den Zugriff auf geschützte Ressourcen in seinem Auftrag anfordert. |
| 2 | Titel | Der Titel ändert sich basierend darauf, ob die Benutzer den Benutzer- oder Administratoreinwilligungsflow durchlaufen. Im Benutzereinwilligungsflow lautet der Titel „Berechtigungen“, während er im Administratoreinwilligungsflow eine zusätzliche Zeile namens „Für Ihre Organisation zustimmen“ besitzt. |
| 3 | App-Logo | Dieses Bild sollte Benutzern einen visuellen Hinweis geben, ob diese App die App ist, auf die sie zugreifen wollten. Dieses Bild wird von den Anwendungsentwicklern bereitgestellt, und der Besitz für dieses Bild wird nicht überprüft. |
| 4 | App-Name | Dieser Wert sollte Benutzer darüber informieren, welche Anwendung Zugriff auf ihre Daten anfordert. Beachten Sie, dass dieser Name von den Entwicklern bereitgestellt wird, und dass der Besitz an diesem App-Namen nicht überprüft wird. |
| 5 | Herausgeberdomäne | Dieser Wert sollte Benutzern eine Domäne bereitstellen, deren Vertrauenswürdigkeit sie möglicherweise bewerten können. Diese Domäne wird von den Entwicklern bereitgestellt, und der Besitz an dieser Herausgeberdomäne wird überprüft. |
| 6 | Berechtigungen | Diese Liste enthält die Berechtigungen, die von der Clientanwendung angefordert werden. Benutzer sollten die Typen der angeforderten Berechtigungen immer bewerten, um zu verstehen, auf welche Daten der Zugriff der Clientanwendung in ihrem Namen autorisiert wird, wenn sie zustimmen. Als Anwendungsentwickler ist es am besten, den Zugriff auf die Berechtigungen mit den geringsten Rechten anzufordern. |
| 7 | Berechtigungsbeschreibung | Dieser Wert wird vom Dienst bereitgestellt, der die Berechtigungen verfügbar macht. Um die Berechtigungsbeschreibung anzuzeigen, müssen Sie das Chevron neben den Berechtigungen umschalten. |
| 8 | App-Nutzungsbedingungen | Diese Nutzungsbedingungen enthalten Links zu den Nutzungsbedingungen und Datenschutzbestimmungen der Anwendung. Der Herausgeber ist verantwortlich für die Darlegung ihrer Regeln in ihren Nutzungsbedingungen. Darüber hinaus ist der Herausgeber dafür verantwortlich, in den Datenschutzbestimmungen die Art zu veröffentlichen, in der sie Benutzerdaten verwenden und teilen. Stellt der Verleger Herausgeber keine Links zu diesen Werten für mehrinstanzenfähige Anwendungen bereit, wird eine fett formatierte Warnung in der Einwilligungsaufforderung angezeigt. |
| 9 | https://myapps.microsoft.com | Dies ist der Link, unter dem Benutzer alle Nicht-Microsoft-Anwendungen überprüfen und entfernen können, die zurzeit Zugriff auf ihre Daten haben. |

## <a name="common-consent-scenarios"></a>Gängige Einwilligungsszenarios

Hier finden Sie die Einwilligungserfahrungen, denen ein Benutzer in den gängigsten Einwilligungsszenarien begegnen kann:

1. Einzelne Personen, die auf eine App zugreifen, die sie zum Benutzereinwilligungsflow weiterleitet und gleichzeitig einen Satz Berechtigungen anfordert, der innerhalb ihres Gültigkeitsbereichs der Autorität liegt.
    
    1. Administratoren wird in der herkömmlichen Einwilligungsaufforderung ein zusätzliches Steuerelement angezeigt, das es ihnen gestattet, die Einwilligung im Auftrag des gesamten Mandanten zu geben. Das Steuerelement ist standardmäßig deaktiviert, weshalb auch nur, wenn Administratoren das Kontrollkästchen ausdrücklich aktivieren, die Einwilligung im Auftrag des gesamten Mandanten erklärt wird. Ab sofort wird dieses Kontrollkästchen nur für die Rolle „Globaler Administrator“ angezeigt, sodass dieses Kontrollkästchen den Rollen „Cloudadministrator“ und „App-Administrator“ nicht angezeigt werden.

        ![Einwilligungsaufforderung für Szenario 1a](./media/application-consent-experience/consent_prompt_1a.png)
    
    2. Benutzern wird die herkömmliche Einwilligungsaufforderung angezeigt.

        ![Screenshot der herkömmlichen Einwilligungsaufforderung.](./media/application-consent-experience/consent_prompt_1b.png)

2. Einzelne Personen, die auf eine App zugreifen, die mindestens eine Berechtigung erfordert, die außerhalb ihres Gültigkeitsbereichs der Autorität liegt.
    1. Administratoren wird dieselbe Aufforderung wie 1.i, wie oben abgebildet, angezeigt.
    2. Benutzer werden am Erteilen der Einwilligung für die Anwendung gehindert und darauf hingewiesen, ihren Administrator um Zugriff auf die App zu bitten. 
                
        ![Screenshot der Einwilligungsaufforderung, die den Benutzer auffordert, einen Administrator um Zugriff auf die App zu bitten.](./media/application-consent-experience/consent_prompt_2b.png)

3. Einzelne Personen, die zum Administratoreinwilligungsflow navigieren oder dorthin weitergeleitet werden.
    1. Administratorbenutzern wird die Administratoreinwilligungsaufforderung angezeigt. Der Titel und die Beschreibung der Berechtigungen hat sich in dieser Eingabeaufforderung geändert, und die Änderungen unterstreichen die Tatsache, dass das Annehmen dieser Aufforderung der App Zugriff auf die angeforderten Daten im Auftrag des gesamten Mandanten erteilt.
        
        ![Einwilligungsaufforderung für Szenario 1b](./media/application-consent-experience/consent_prompt_3a.png)
        
    1. Nicht-Administratorbenutzern wird derselbe Bildschirm wie 2.ii, wie oben abgebildet, angezeigt.

## <a name="next-steps"></a>Nächste Schritte
- Eine ausführliche Übersicht über die [Zustimmungsimplementierung mit dem Azure AD-Zustimmungsframework](./quickstart-register-app.md)
- Ausführlichere Informationen zur [Verwendung des Zustimmungsframeworks durch mehrinstanzenfähige Anwendungen](./howto-convert-app-to-be-multi-tenant.md) zur Implementierung von Benutzer- und Administratorzustimmung mit Unterstützung für erweiterte Anwendungsmuster mit mehreren Ebenen
- Erfahren Sie, [wie Sie die Herausgeberdomäne der App konfigurieren](howto-configure-publisher-domain.md).