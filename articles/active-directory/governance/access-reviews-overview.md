---
title: Worum handelt es sich bei Zugriffsüberprüfungen? – Azure Active Directory | Microsoft-Dokumentation
description: Mithilfe von Azure Active Directory-Zugriffsüberprüfungen können Sie Gruppenmitgliedschaften und den Anwendungszugriff steuern, um die Anforderungen von Governance-, Risikomanagement- und Konformitätsinitiativen in Ihrer Organisation zu erfüllen.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.subservice: compliance
ms.date: 10/29/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.custom: contperf-fy21q1
ms.openlocfilehash: 08399c1b2d930abfdd5832cb102a789844931d61
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027695"
---
# <a name="what-are-azure-ad-access-reviews"></a>Was sind Azure AD-Zugriffsüberprüfungen?

Mithilfe von Azure Active Directory-Zugriffsüberprüfungen (Azure AD-Zugriffsüberprüfungen) können Organisationen Gruppenmitgliedschaften, den Zugriff auf Unternehmensanwendungen und Rollenzuweisungen effizient verwalten. Der Benutzerzugriff kann regelmäßig überprüft werden, um sicherzustellen, dass nur die richtigen Personen weiterhin Zugriff haben.

Das folgende Video bietet einen kurzen Überblick über Zugriffsüberprüfungen:

>[!VIDEO https://www.youtube.com/embed/kDRjQQ22Wkk]

## <a name="why-are-access-reviews-important"></a>Warum sind Zugriffsüberprüfungen wichtig?

Mit Azure AD können Sie mit internen Benutzern Ihrer Organisation und mit externen Benutzern zusammenarbeiten. Benutzer können Gruppen beitreten, Gäste einladen, Verbindungen mit Cloud-Apps herstellen und mit ihren Firmen- oder persönlichen Geräten remote arbeiten. Die Möglichkeit zur komfortablen Nutzung von Self-Service hat dazu geführt, dass bessere Zugriffsverwaltungsfunktionen benötigt werden.

- Wie stellen Sie sicher, dass neue Mitarbeiter die erforderlichen Zugriffsrechte erhalten, damit sie produktiv arbeiten können?
- Wie stellen Sie sicher, dass alte Zugriffsrechte entzogen werden, wenn Personen das Team wechseln oder das Unternehmen verlassen?
- Zu weit reichende Zugriffsrechte können zu Kompromittierungen führen.
- Darüber hinaus können zu umfangreiche Zugriffsrechte auch zu negativen Ergebnissen bei Überprüfungen führen, da dies auf eine fehlende Kontrolle des Zugriffs hinweist.
- Sie müssen sich proaktiv mit Ressourcenbesitzern austauschen, um sicherzustellen, dass sie regelmäßig überprüfen, wer Zugriff auf ihre Ressourcen hat.

## <a name="when-should-you-use-access-reviews"></a>Wann sollten Sie Zugriffsüberprüfungen verwenden?

- **Zu viele Benutzer in privilegierten Rollen:** Sie sollten überprüfen, wie viele Benutzer über Administratorzugriff verfügen, wie viele dieser Benutzer globale Administratoren sind und ob es eingeladene Gäste oder Partner gibt, die nicht entfernt wurden, nachdem ihnen eine administrative Aufgabe zugewiesen wurde. Sie können die Rollenzuweisung für Benutzer in [Azure AD-Rollen](../privileged-identity-management/pim-how-to-perform-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) wie globale Administratoren oder in [Azure-Ressourcenrollen](../privileged-identity-management/pim-resource-roles-perform-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) wie Benutzerzugriffsadministratoren in [Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-configure.md) erneut bestätigen.
- **Falls die Automatisierung nicht möglich ist:** Sie können Regeln für dynamische Mitgliedschaften für Sicherheits- oder Microsoft 365-Gruppen erstellen. Was geschieht aber, wenn die Personaldaten nicht in Azure AD gespeichert sind oder die Benutzer nach dem Verlassen der Gruppe weiterhin Zugriff benötigen, um ihre Nachfolger einzuarbeiten? Dann können Sie eine Überprüfung für diese Gruppe erstellen, um sicherzustellen, dass die Benutzer, die weiterhin Zugriff benötigen, auch weiterhin Zugriff haben.
- **Wenn eine Gruppe zu einem neuen Zweck verwendet wird:** Wenn Sie über eine Gruppe verfügen, die mit Azure AD synchronisiert wird, oder wenn Sie die Salesforce-Anwendung für alle Benutzer in der Gruppe „Vertriebsteam“ aktivieren möchten, wäre es sinnvoll, den Besitzer der Gruppe zu bitten, die Gruppenmitgliedschaft zu überprüfen, bevor die Gruppe in einem anderen Risikokontext verwendet wird.
- **Zugriff auf unternehmenskritische Daten:** Bei bestimmten Ressourcen kann es zu Überwachungszwecken erforderlich sein, die Mitarbeiter außerhalb der IT-Abteilung zu bitten, sich regelmäßig abzumelden und zu begründen, warum sie Zugriff benötigen.
- **Zum Verwalten einer Richtlinienausnahmeliste:** Im Idealfall halten sich alle Benutzer an die Zugriffsrichtlinien, um den Zugriff auf die Ressourcen Ihrer Organisation zu schützen. Es kann aber auch Geschäftsszenarien geben, in denen Ausnahmen erforderlich sind. Als IT-Administrator können Sie diese Aufgabe verwalten, das Übersehen von Richtlinienausnahmen vermeiden und Prüfern den Nachweis erbringen, dass diese Ausnahmen regelmäßig überprüft werden.
- **Auffordern von Gruppenbesitzern zur Bestätigung, dass sie weiterhin Gäste in ihren Gruppen benötigen:** Der Mitarbeiterzugriff kann per lokalem Identity & Access Management (IAM) automatisiert werden. Dies trifft aber nicht auf die Zugriffsrechte eingeladener Gäste zu. Wenn eine Gruppe Gästen Zugriff auf vertrauliche Unternehmensinhalte gewährt, muss der Besitzer der Gruppe bestätigen, dass für die Gäste immer noch eine berechtigte geschäftliche Notwendigkeit des Zugriffs besteht.
- **Regelmäßige Durchführung von Überprüfungen:** Sie können die Häufigkeit der regelmäßigen Durchführung von Zugriffsüberprüfungen für Benutzer (z.B. wöchentlich, monatlich, vierteljährlich oder jährlich) einrichten. Die Prüfer werden zu Beginn jeder Überprüfung benachrichtigt. Prüfer können den Zugriff über eine benutzerfreundliche Oberfläche und mithilfe intelligenter Empfehlungen genehmigen oder verweigern.

>[!NOTE]
>Wenn Sie die Verwendung von Zugriffsüberprüfungen ausprobieren möchten, helfen Ihnen die Informationen unter [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md) weiter.

## <a name="where-do-you-create-reviews"></a>Wo erstellen Sie Überprüfungen?

Je nachdem, was Sie überprüfen möchten, erstellen Sie Ihre Zugriffsüberprüfung in Azure AD-Zugriffsüberprüfungen, Azure AD-Unternehmens-Apps (in der Vorschauversion) oder Azure AD PIM.

| Zugriffsrechte von Benutzern | Prüfer | Überprüfung erstellt in | Prüferoberfläche |
| --- | --- | --- | --- |
| Mitglieder von Sicherheitsgruppen</br>Mitglieder von Office-Gruppen | Angegebene Prüfer</br>Gruppenbesitzer</br>Selbstüberprüfung | Azure AD-Zugriffsüberprüfungen</br>Azure AD-Gruppen | Zugriffsbereich |
| Einer verbundenen App zugewiesen | Angegebene Prüfer</br>Selbstüberprüfung | Azure AD-Zugriffsüberprüfungen</br>Azure AD-Unternehmens-Apps (in der Vorschauversion) | Zugriffsbereich |
| Azure AD-Rolle | Angegebene Prüfer</br>Selbstüberprüfung | [Azure AD PIM](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure-Portal |
| Azure-Ressourcenrolle | Angegebene Prüfer</br>Selbstüberprüfung | [Azure AD PIM](../privileged-identity-management/pim-resource-roles-start-access-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json) | Azure-Portal |

## <a name="license-requirements"></a>Lizenzanforderungen

[!INCLUDE [Azure AD Premium P2 license](../../../includes/active-directory-p2-license.md)]

### <a name="how-many-licenses-must-you-have"></a>Wie viele Lizenzen benötigen Sie?

Sie benötigen für Ihr Verzeichnis mindestens so viele Azure AD Premium P2-Lizenzen wie Mitarbeiter, die mit den folgenden Aufgaben betraut sind:

-   Mitgliedsbenutzer, die als Prüfer zugewiesen sind
-   Mitgliedsbenutzer, die eine Selbstüberprüfung durchführen
-   Als Gruppenbesitzer fungierende Mitgliedsbenutzer, die eine Zugriffsüberprüfung durchführen
-   Als Anwendungsbesitzer fungierende Mitgliedsbenutzer, die eine Zugriffsüberprüfung durchführen

Bei Gastbenutzern hängen die Lizenzierungsanforderungen vom verwendeten Lizenzierungsmodell ab. Die folgenden Gastbenutzeraktivitäten werden jedoch als Azure AD Premium P2-Nutzung betrachtet:

-   Gastbenutzer, die als Prüfer zugewiesen sind
-   Gastbenutzer, die eine Selbstüberprüfung durchführen
-   Als Gruppenbesitzer fungierende Gastbenutzer, die eine Zugriffsüberprüfung durchführen
-   Als Anwendungsbesitzer fungierende Gastbenutzer, die eine Zugriffsüberprüfung durchführen


Azure AD Premium P2-Lizenzen sind **nicht** für Benutzer mit den Rollen „Globaler Administrator“ oder „Benutzeradministrator“ erforderlich, von denen Zugriffsüberprüfungen eingerichtet, Einstellungen konfiguriert oder die Entscheidungen aus den Überprüfungen umgesetzt werden.

Der Zugriff von Azure AD-Gastbenutzern basiert auf einem Abrechnungsmodell für monatlich aktive Benutzer (Monthly Active Users, MAU). Dieses ersetzt das Abrechnungsmodell für das 1:5-Verhältnis. Weitere Informationen finden Sie unter [Abrechnungsmodell für externe Identitäten in Azure AD](../external-identities/external-identities-pricing.md).

Weitere Informationen zu Lizenzen finden Sie unter [Zuweisen oder Entfernen von Lizenzen im Azure Active Directory-Portal](../fundamentals/license-users-groups.md).

### <a name="example-license-scenarios"></a>Beispielszenarien für Lizenzen

Anhand der folgenden Beispielszenarien für Lizenzen können Sie die Anzahl der benötigten Lizenzen bestimmen.

| Szenario | Berechnung | Anzahl der Lizenzen |
| --- | --- | --- |
| Ein Administrator erstellt eine Zugriffsüberprüfung von Gruppe A mit 75 Benutzern und 1 Gruppenbesitzer und weist den Gruppenbesitzer als Reviewer zu. | 1 Lizenz für den Gruppenbesitzer als Reviewer | 1 |
| Ein Administrator erstellt eine Zugriffsüberprüfung von Gruppe B mit 500 Benutzern und 3 Gruppenbesitzern und weist die 3 Gruppenbesitzer als Reviewer zu. | 3 Lizenzen für jeden Gruppenbesitzer als Reviewer | 3 |
| Ein Administrator erstellt eine Zugriffsüberprüfung für Gruppe B mit 500 Benutzern. Er legt sie als Selbstüberprüfung fest. | 500 Lizenzen für jeden Benutzer als Selbstprüfer | 500 |
| Ein Administrator erstellt eine Zugriffsüberprüfung für Gruppe C mit 50 Mitgliedsbenutzern und 25 Gastbenutzern. Er legt sie als Selbstüberprüfung fest. | 50 Lizenzen für jeden Benutzer als Selbstprüfer.* | 50 |
| Ein Administrator erstellt eine Zugriffsüberprüfung für Gruppe D mit 6 Mitgliedsbenutzern und 108 Gastbenutzern. Er legt sie als Selbstüberprüfung fest. | 6 Lizenzen für jeden Benutzer als Selbstprüfer. Für Gastbenutzer erfolgt die Abrechnung basierend auf den monatlich aktiven Benutzern (Monthly Active Users, MAU). Es sind keine zusätzlichen Lizenzen erforderlich. *  | - |

\* Die Preise für Azure AD External Identities (Gastbenutzer) basieren auf den monatlich aktiven Benutzern (Monthly Active Users, MAU). Dies ist die Anzahl eindeutiger Benutzer mit Authentifizierungsaktivität innerhalb eines Kalendermonats. Dieses Modell ersetzt das Abrechnungsmodell im Verhältnis 1:5, das bis zu fünf Gastbenutzer pro Azure AD Premium-Lizenz in Ihrem Mandanten zuließ. Wenn Ihr Mandant mit einem Abonnement verknüpft ist und Sie External Identities-Features für die Zusammenarbeit mit Gastbenutzern verwenden, erfolgt Ihre Abrechnung automatisch nach dem MAU-basierten Abrechnungsmodell. Weitere Informationen finden Sie unter „Abrechnungsmodell für Azure AD External Identities“.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md)
- [Erstellen einer Zugriffsüberprüfung von Benutzern in der Azure AD-Administratorrolle](../privileged-identity-management/pim-how-to-start-security-review.md?toc=%2fazure%2factive-directory%2fgovernance%2ftoc.json)
- [Überprüfen des Zugriffs auf Gruppen oder Anwendungen](perform-access-review.md)
- [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md)
