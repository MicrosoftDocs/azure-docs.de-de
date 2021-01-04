---
title: Grundlagen der Delegierung der Administratorrolle – Azure Active Directory | Microsoft-Dokumentation
description: Delegierungsmodelle, Beispiele und Rollensicherheit in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: conceptual
ms.date: 11/05/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6f4512bea533b6394fcef025ddc19bb7989292a4
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93378362"
---
# <a name="delegate-administration-in-azure-active-directory"></a>Delegieren der Administration in Azure Active Directory (Azure AD)

Mit dem Wachstum einer Organisation steigt auch die Komplexität. Ein häufiger Ansatz besteht in der Reduzierung des Workloads bei der Zugriffsverwaltung mit Azure Active Directory-Administratorrollen. Sie können Benutzern die geringstmögliche Berechtigung zuweisen, damit diese auf ihre Apps zugreifen und ihre Aufgaben durchführen können. Selbst wenn Sie die globale Administratorrolle nicht jedem Besitzer von Anwendungen zuweisen, weisen Sie die Aufgaben für die Anwendungsverwaltung den vorhandenen globalen Administratoren zu. Es gibt viele Gründe dafür, die Organisation auf eine dezentralisierte Verwaltung umzustellen. Durch den Inhalt dieses Artikels können Sie die Delegierung in Ihrer Organisation besser planen.

<!--What about reporting? Who has which role and how do I audit?-->

## <a name="centralized-versus-delegated-permissions"></a>Zentralisierte und delegierte Berechtigungen im Vergleich

Wenn eine Organisation wächst, kann es sich schwierig gestalten, den Überblick darüber zu behalten, welche Benutzer bestimmte Administratorrollen besitzen. Wenn ein Mitarbeiter Administratorrechte besitzt, die er nicht besitzen sollte, kann Ihre Organisation für Sicherheitsverletzungen anfälliger werden. Im Allgemeinen hängt die Anzahl der von Ihnen unterstützten Administratoren und die Granularität ihrer Berechtigungen von der Größe und Komplexität Ihrer Bereitstellung ab.

* In kleinen oder Proof-of-Concept-Bereitstellungen wird die Arbeit von einem oder wenigen Administratoren erledigt, sodass keine Delegierung nötig ist. Erstellen Sie in diesem Fall jeden Administrator mit der globalen Administratorrolle.
* In größeren Bereitstellung mit mehr Computern, Anwendungen und Desktops ist eine Delegierung erforderlicher. In diesem Fall haben möglicherweise mehrere Administratoren bestimmte funktionale Aufgaben (Rollen). Einige können beispielsweise Privileged Identity-Administratoren und andere Anwendungsadministratoren sein. Ein Administrator kann zusätzlich auch nur bestimmte Objektgruppen (z.B. Geräte) verwalten.
* Noch größere Bereitstellungen erfordern möglicherweise noch präzisere Berechtigungen und Administratoren mit ungewöhnlichen oder hybriden Rollen.

Im Azure AD-Portal können Sie [alle Mitglieder einer Rolle anzeigen](manage-roles-portal.md) und dadurch die Bereitstellung schnell überprüfen und Berechtigungen delegieren.

Wenn Sie den Zugriff auf Azure-Ressourcen anstelle des Administratorzugriffs in Azure AD delegieren möchten, finden Sie weitere Informationen unter [Zuweisen einer Azure-Rolle](../../role-based-access-control/role-assignments-portal.md).

## <a name="delegation-planning"></a>Planen der Delegierung

Das Entwickeln eines Delegierungsmodells, das Ihren Anforderungen entspricht, ist arbeitsaufwändig. Das Entwickeln eines Delegierungsmodells ist ein iterativer Entwurfsprozess. Es wird empfohlen, folgende Schritte durchzuführen:

* Definieren Sie die benötigten Rollen.
* Delegieren Sie die Verwaltung der App.
* Erteilen Sie die Berechtigung, Anwendungen zu registrieren.
* Delegieren Sie den Besitz der App.
* Entwickeln Sie einen Sicherheitsplan.
* Erstellen Sie Notfallkonten.
* Schützen Sie Ihre Administratorrollen.
* Legen Sie die privilegierte Erhöhung der Rechte auf temporär fest.

## <a name="define-roles"></a>Definieren von Rollen

Bestimmen Sie die Active Directory-Aufgaben, die von Administratoren durchgeführt werden und wie diese Rollen zugeordnet sind. Sie können [detaillierte Rollenbeschreibungen](manage-roles-portal.md) im Azure-Portal anzeigen.

Jede Aufgabe sollte nach Häufigkeit, Wichtigkeit und Schwierigkeit bewertet werden. Diese Kriterien sind bei der Aufgabendefinition wichtig, weil sie bestimmen, ob eine Berechtigung delegiert werden soll.

* Aufgaben, die routinemäßig und einfach durchgeführt werden können und begrenzte Risiken aufweisen, sind gut für die Delegierung geeignet.
* Bei Aufgaben, die selten durchgeführt werden, aber einen großen Einfluss auf die Organisation haben und ausgeprägte Fähigkeiten erfordern, sollte sorgfältig überlegt werden, ob diese delegiert werden können. Stattdessen können Sie [ein Konto vorübergehend auf die erforderliche Rolle erhöhen](../privileged-identity-management/pim-configure.md) oder die Aufgabe neu zuweisen.

## <a name="delegate-app-administration"></a>Delegieren Sie die Verwaltung der App.

Die Verbreitung von Apps in Ihrer Organisation kann Ihr Delegierungsmodell komplizierter gestalten. Wenn der Aufwand der Zugriffsverwaltung für die Anwendung ausschließlich dem globalen Administrator zufällt, erhöht sich die Verwaltung des Modells wahrscheinlich im Lauf der Zeit. Wenn Sie Benutzern die globale Administratorrolle für Aufgaben wie das Konfigurieren von Geschäftsanwendungen zugeteilt haben, können Sie diese nun an folgende weniger privilegierte Rollen auslagern. So können Sie den Sicherheitsstatus verbessern und das Fehlerpotenzial verringern. Folgende Rollen für Anwendungsadministratoren besitzen die meisten Privilegien:

* Die Rolle **Anwendungsadministrator** , durch die alle Anwendungen im Verzeichnis verwaltet werden können, einschließlich Registrierungen, Einstellungen für einmaliges Anmelden, Benutzer- und Gruppenzuweisungen und -lizenzierung, Anwendungsproxyeinstellungen und Zustimmungen. Durch diese Rolle kann der bedingte Zugriff nicht verwaltet werden.
* Die Rolle **Cloudanwendungsadministrator** , die alle Berechtigungen des Anwendungsadministrators außer dem Zugriff auf die Anwendungsproxyeinstellungen gewährt, da sie nicht über lokale Berechtigungen verfügt.

## <a name="delegate-app-registration"></a>Delegieren der App-Registrierung

Standardmäßig können alle Benutzer App-Registrierungen erstellen. So gewähren Sie selektiv die Fähigkeit zum Erstellen von Anwendungsregistrierungen

* Legen Sie **Benutzer können Anwendungen registrieren** in den **Benutzereinstellungen** auf „Nein“ fest.
* Weisen Sie den Benutzer der Rolle „Anwendungsentwickler“ zu.

So gewähren Sie selektiv die Fähigkeit zur Einwilligung, einer Anwendung den Zugriff auf Daten zu gewähren:

* Legen **Benutzer können Anwendungen den Zugriff auf Unternehmensdaten in ihrem Namen gestatten** in den **Benutzereinstellungen** auf „Nein“ fest.
* Weisen Sie den Benutzer der Rolle „Anwendungsentwickler“ zu.

Wenn ein Anwendungsentwickler eine neue Anwendungsregistrierung erstellt, wird dieser automatisch als erster Besitzer hinzugefügt.

## <a name="delegate-app-ownership"></a>Delegieren Sie den Besitz der App.

Für eine noch präzisere Delegierung des Zugriffs auf Apps können Sie den Besitz für einzelne Unternehmensanwendungen zuweisen. Dadurch wird die vorhandene Unterstützung für das Zuweisen von Besitzern für Anwendungsregistrierungen ergänzt. Der Besitz wird pro Unternehmensanwendung auf dem Blatt „Unternehmens-Apps“ zugewiesen. Der Vorteil besteht darin, dass Besitzer nur die Unternehmensanwendungen verwalten können, die Sie besitzen. Sie können beispielsweise einen Besitzer für die Salesforce-Anwendung zuweisen. Dieser Besitzer kann dann den Zugriff auf Salesforce sowie die Konfiguration verwalten, jedoch keine anderen Anwendungen. Eine Unternehmensanwendung kann viele Besitzer aufweisen, und ein Benutzer kann mehrere Unternehmensanwendungen besitzen. Es gibt zwei Rollen für App-Besitzer:

* Die Rolle **Unternehmens-App-Besitzer** erteilt die Berechtigung zum Verwalten der Unternehmens-Apps im Besitz, einschließlich Einstellungen für das einmalige Anmelden, Benutzer- und Gruppenzuweisungen und das Hinzufügen zusätzlicher Besitzer. Die Berechtigung zum Verwalten von Anwendungsproxyeinstellungen oder bedingtem Zugriff wird nicht gewährt.
* Die Rolle **Anwendungsregistrierungsbesitzer** erteilt die Berechtigung zum Verwalten von Anwendungsregistrierungen für die Apps, die der Benutzer besitzt, einschließlich des Anwendungsmanifests und des Hinzufügens zusätzlicher Besitzer.

## <a name="develop-a-security-plan"></a>Entwickeln Sie einen Sicherheitsplan.

Azure AD bietet eine umfassende Anleitung zum Planen und Ausführen eines Sicherheitsplans für Ihre Azure AD-Administratorrollen, diese finden Sie unter [Schützen des privilegierten Zugriffs für hybride und Cloudbereitstellungen in Azure AD](security-planning.md).

## <a name="establish-emergency-accounts"></a>Erstellen Sie Notfallkonten.

Bereiten Sie gemäß [Verwalten von Administratorkonten für den Notfallzugriff in Azure AD](security-emergency-access.md) Konten für den Notfallzugriff vor, um den Zugriff auf Identity Management-Speicher nicht zu verlieren, wenn Probleme auftreten.

## <a name="secure-your-administrator-roles"></a>Schützen Sie Ihre Administratorrollen.

Wenn Angreifer den Zugriff auf privilegierte Konten erlangen können, können diese enormen Schaden ausrichten. Deshalb müssen diese Konten zunächst mithilfe einer [Baselinezugriffsrichtlinie](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/22/baseline-security-policy-for-azure-ad-admin-accounts-in-public-preview/) geschützt werden, die standardmäßig für alle Azure AD-Organisationen (in der öffentlichen Vorschauversion) verfügbar ist. Die Richtlinie erzwingt die mehrstufige Authentifizierung für privilegierte Azure AD-Konten. Folgende Azure AD-Rollen werden von der Azure AD-Baselinerichtlinie abgedeckt:

* Globaler Administrator
* SharePoint-Administrator
* Exchange-Administrator
* Administrator für den bedingten Zugriff
* Sicherheitsadministrator

## <a name="elevate-privilege-temporarily"></a>Temporäres Erhöhen von Berechtigungen

Für die meisten alltäglichen Aktivitäten benötigen nicht alle Benutzer globale Administratorrechte, und nicht alle von ihnen sollten dauerhaft der Rolle „Globaler Administrator“ zugewiesen werden. Wenn Benutzer die Berechtigungen eines globalen Administrators benötigen, sollte die Rollenzuweisung in Azure AD [Privileged Identity Management](../privileged-identity-management/pim-configure.md) aktiviert werden – entweder für ihr eigenes Konto oder ein alternatives Administratorkonto.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen und Beschreibungen zu den Azure AD-Rollen finden Sie unter [Zuweisen von Administratorrollen in Azure Active Directory](permissions-reference.md).