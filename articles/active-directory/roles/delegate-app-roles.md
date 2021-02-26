---
title: Delegieren von Administratorberechtigungen für die Anwendungsverwaltung – Azure AD | Microsoft-Dokumentation
description: Erteilen von Berechtigungen für die Anwendungszugriffsverwaltung in Azure Active Directory
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: how-to
ms.date: 11/04/2020
ms.author: rolyon
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b5cb7e1521c649be4abc155d9f28a49b43a11e6d
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99071261"
---
# <a name="delegate-app-registration-permissions-in-azure-active-directory"></a>Delegieren von App-Registrierungsberechtigungen in Azure Active Directory

In diesem Artikel wird beschrieben, wie Sie über Berechtigungen, die von benutzerdefinierten Rollen in Azure Active Directory (Azure AD) gewährt werden, Ihre Anforderungen an die Anwendungsverwaltung erfüllen können. In Azure AD können Sie die Anwendungserstellung und Verwaltungsberechtigungen wie folgt delegieren:

- [Einschränken, welche Benutzer Anwendungen erstellen können](#restrict-who-can-create-applications), und Verwalten der erstellten Anwendungen. Standardmäßig können in Azure AD alle Benutzer Anwendungen registrieren und alle Aspekte der Anwendungen verwalten, die sie erstellen. Dies kann so eingeschränkt werden, dass diese Berechtigung nur für ausgewählte Personen gilt.
- [Zuweisen eines oder mehrerer Besitzer zu einer Anwendung](#assign-application-owners). Dies ist eine einfache Möglichkeit, einer Person die Berechtigung zu erteilen, alle Aspekte der Azure AD-Konfiguration für eine bestimmte Anwendung zu verwalten.
- [Zuweisen einer integrierten Administratorrolle](#assign-built-in-application-admin-roles), mit der Zugriff zum Verwalten der Konfiguration in Azure AD für alle Anwendungen gewährt wird. Dies ist die empfohlene Vorgehensweise, um IT-Experten Zugriff zum Verwalten von umfassenden Berechtigungen für die Anwendungskonfiguration zu gewähren, ohne Zugriff auf die Verwaltung anderer Bereiche von Azure AD zu gewähren, die nicht mit der Anwendungskonfiguration zusammenhängen.
- [Erstellen einer benutzerdefinierten Rolle](#create-and-assign-a-custom-role-preview), die sehr spezifische Berechtigungen definiert, und Zuweisen dieser Rolle zu einem Benutzer im Bereich einer einzelnen Anwendung als eingeschränkter Besitzer oder im Verzeichnisbereich (alle Anwendungen) als eingeschränkter Administrator.

Es ist aus zwei Gründen wichtig, den Zugriff mithilfe einer der oben genannten Methoden zu gewähren. Erstens wird durch die Delegierung der Möglichkeit zum Ausführen administrativer Aufgaben der Aufwand für globale Administratoren reduziert. Zweitens verbessert die Verwendung eingeschränkter Berechtigungen den Sicherheitsstatus und reduziert das Potenzial für nicht autorisierten Zugriff. Delegierungsprobleme und allgemeine Richtlinien werden unter [Delegieren der Administration in Azure Active Directory (Azure AD)](concept-delegation.md) erörtert.

## <a name="restrict-who-can-create-applications"></a>Einschränken, welche Benutzer Anwendungen erstellen können

Standardmäßig können in Azure AD alle Benutzer Anwendungen registrieren und alle Aspekte der Anwendungen verwalten, die sie erstellen. Alle Benutzer haben außerdem die Möglichkeit einzuwilligen, dass Apps in ihrem Namen auf Unternehmensdaten zugreifen. Sie können diese Berechtigungen selektiv erteilen, indem Sie die globalen Optionen auf „Nein“ festlegen und die ausgewählten Benutzer der Rolle „Anwendungsentwickler“ hinzufügen.

### <a name="to-disable-the-default-ability-to-create-application-registrations-or-consent-to-applications"></a>So deaktivieren Sie die Standardoption zum Erstellen von Anwendungsregistrierungen oder Erteilen der Einwilligung für Anwendungen

1. Melden Sie sich bei Ihrer Azure AD-Organisation mit einem Konto an, das für die Rolle „Globaler Administrator“ in der Azure AD-Organisation berechtigt ist.
1. Legen Sie eine der folgenden Einstellungen oder beide fest:

    - Legen Sie auf der Seite [„Benutzereinstellungen“ für Ihre Organisation](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) die Option **Benutzer können Anwendungen registrieren** auf „Nein“ fest. Dadurch wird die Standardoption für Benutzer zum Erstellen von Anwendungsregistrierungen deaktiviert.
    - Legen Sie unter [Benutzereinstellungen – Unternehmensanwendungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) die Einstellung **Benutzer können Apps den Zugriff auf Unternehmensdaten in ihrem Namen gestatten** auf „Nein“ fest. Dadurch wird die Standardfunktion für Benutzer deaktiviert, Anwendungen den Zugriff auf Unternehmensdaten in ihrem Namen zu gestatten.

### <a name="grant-individual-permissions-to-create-and-consent-to-applications-when-the-default-ability-is-disabled"></a>Erteilen einzelner Berechtigungen zum Erstellen von Anwendungen und Erteilen der Einwilligung für Anwendungen bei deaktivierter Standardfunktion

Weisen Sie die Rolle „Anwendungsentwickler“ zu, um die Berechtigung zum Erstellen von Anwendungsregistrierungen zu erteilen, wenn die Einstellung **Benutzer können Anwendungen registrieren** auf „Nein“ festgelegt ist. Diese Rolle erteilt auch die Berechtigung zur Zustimmung im eigenen Namen, wenn die Einstellung **Benutzer können Apps den Zugriff auf Unternehmensdaten in ihrem Namen gestatten** auf „Nein“ festgelegt ist. Wenn ein Benutzer eine neue Anwendungsregistrierung erstellt, wird er als Systemverhalten automatisch als erster Besitzer hinzugefügt. Mit Besitzerberechtigungen können Benutzer alle Aspekte einer Anwendungsregistrierung oder Unternehmensanwendung verwalten, deren Besitzer sie sind.

## <a name="assign-application-owners"></a>Zuweisen von Anwendungsbesitzern

Die Zuweisung von Besitzern ist eine einfache Möglichkeit, die Berechtigung zu erteilen, alle Aspekte der Azure AD-Konfiguration für eine bestimmte Anwendungsregistrierung oder Unternehmensanwendung zu verwalten. Wenn ein Benutzer eine neue Anwendungsregistrierung erstellt, wird er als Systemverhalten automatisch als erster Besitzer hinzugefügt. Mit Besitzerberechtigungen können Benutzer alle Aspekte einer Anwendungsregistrierung oder Unternehmensanwendung verwalten, deren Besitzer sie sind. Der ursprüngliche Besitzer kann entfernt werden, und weitere Besitzer können hinzugefügt werden.

### <a name="enterprise-application-owners"></a>Besitzer von Unternehmensanwendungen

Als Besitzer kann ein Benutzer die organisationsspezifische Konfiguration der Unternehmensanwendung verwalten, z. B. die Konfiguration für einmaliges Anmelden, die Bereitstellung und Benutzerzuweisungen. Ein Besitzer kann außerdem andere Besitzer hinzufügen oder entfernen. Im Gegensatz zu globalen Administratoren können Besitzer nur die Unternehmensanwendungen verwalten, deren Besitzer sie sind.

In einigen Fällen enthalten Unternehmensanwendungen, die über den Anwendungskatalog erstellt werden, sowohl eine Unternehmensanwendung als auch eine Anwendungsregistrierung. Wenn dies der Fall ist, wird beim Hinzufügen eines Besitzers zur Unternehmensanwendung der Besitzer automatisch der entsprechenden Anwendungsregistrierung als Besitzer hinzugefügt.

### <a name="to-assign-an-owner-to-an-enterprise-application"></a>So weisen Sie einer Unternehmensanwendung einen Besitzer zu

1. Melden Sie sich bei Ihrer [Azure AD-Organisation](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) mit einem Konto an, das für den Anwendungsadministrator oder Cloudanwendungsadministrator für die Organisation berechtigt ist.
1. Wählen Sie auf der Seite [App-Registrierungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) für die Organisation eine App aus, um die Seite „Übersicht“ für die App zu öffnen.
1. Wählen Sie **Besitzer** aus, um die Liste der Besitzer für die App anzuzeigen.
1. Wählen Sie **Hinzufügen** aus, um einen oder mehrere Besitzer der App hinzuzufügen.

> [!IMPORTANT]
> Benutzer und Dienstprinzipale können Besitzer von Anwendungsregistrierungen sein. Nur Benutzer können Besitzer von Unternehmensanwendungen sein. Gruppen können beiden nicht als Besitzer zugewiesen werden.
>
> Besitzer können einer Anwendung Anmeldeinformationen hinzufügen und diese Anmeldeinformationen verwenden, um die Identität der Anwendung zu wechseln. Die Anwendung umfasst möglicherweise mehr Berechtigungen als der Besitzer, sodass dies Rechteerweiterungen im Hinblick auf den Zugriff des Besitzers als Benutzer oder Dienstprinzipal zur Folge hat. Beim Wechseln der Identität der Anwendung kann ein Anwendungsbesitzer abhängig von den Berechtigungen der Anwendung potenziell Benutzer oder andere Objekte erstellen oder aktualisieren.

## <a name="assign-built-in-application-admin-roles"></a>Zuweisen von integrierten Anwendungsadministratorrollen

Azure AD umfasst eine Reihe integrierter Administratorrollen zum Gewähren des Zugriffs zum Verwalten der Konfiguration in Azure AD für alle Anwendungen. Die Verwendung dieser Rollen wird empfohlen, um IT-Experten Zugriff zum Verwalten von umfassenden Berechtigungen für die Anwendungskonfiguration zu gewähren, ohne Zugriff auf die Verwaltung anderer Bereiche von Azure AD zu gewähren, die nicht mit der Anwendungskonfiguration zusammenhängen.

- Anwendungsadministrator: Benutzer mit dieser Rolle können alle Aspekte von Unternehmensanwendungen, Anwendungsregistrierungen und Anwendungsproxyeinstellungen erstellen und verwalten. Diese Rolle ermöglicht auch die Zustimmung zu delegierten Berechtigungen und Anwendungsberechtigungen, mit Ausnahme von Berechtigungen für Microsoft Graph. Benutzer, denen diese Rolle zugewiesen wurde, werden bei der Erstellung neuer Anwendungsregistrierungen oder Unternehmensanwendungen nicht als Besitzer hinzugefügt.
- Cloudanwendungsadministrator: Benutzer mit dieser Rolle haben die gleichen Berechtigungen wie die Rolle des Anwendungsadministrators, mit Ausnahme der Möglichkeit, den Anwendungsproxy zu verwalten. Benutzer, denen diese Rolle zugewiesen wurde, werden bei der Erstellung neuer Anwendungsregistrierungen oder Unternehmensanwendungen nicht als Besitzer hinzugefügt.

Weitere Informationen und eine Beschreibung dieser Rollen finden Sie unter [Verfügbare Rollen](permissions-reference.md#available-roles).

Befolgen Sie die Anweisungen in der Anleitung zum [Zuweisen von Administrator- und anderen Rollen zu Benutzern mithilfe von Azure Active Directory](../fundamentals/active-directory-users-assign-role-azure-portal.md), um die Rollen „Anwendungsadministrator“ oder „Cloudanwendungsadministrator“ zuzuweisen.

> [!IMPORTANT]
> Anwendungsadministratoren und Cloudanwendungsadministratoren können einer Anwendung Anmeldeinformationen hinzufügen und diese Anmeldeinformationen verwenden, um die Identität der Anwendung zu wechseln. Die Anwendung umfasst möglicherweise Berechtigungen, die in Bezug auf die Berechtigungen der Administratorrolle Rechteerweiterungen darstellen. Beim Wechseln der Identität der Anwendung kann ein Administrator in dieser Rolle abhängig von den Berechtigungen der Anwendung potenziell Benutzer oder andere Objekte erstellen oder aktualisieren.
> Keine dieser Rollen gewährt die Fähigkeit zum Verwalten der Einstellungen des bedingten Zugriffs.

## <a name="create-and-assign-a-custom-role-preview"></a>Erstellen und Zuweisen einer benutzerdefinierten Rolle (Vorschau)

Die Erstellung von benutzerdefinierten Rollen und deren Zuweisung erfolgen in zwei separaten Schritten:

- [Erstellen Sie eine benutzerdefinierte *Rollendefinition*](custom-create.md), und [fügen Sie ihr Berechtigungen aus einer vordefinierten Liste hinzu](custom-available-permissions.md). Dabei handelt es sich um die gleichen Berechtigungen, die in den integrierten Rollen verwendet werden.
- [Erstellen Sie eine *Rollenzuweisung*](custom-assign-powershell.md), um die benutzerdefinierte Rolle zuzuweisen.

Durch diese separaten Schritte können Sie eine einzelne Rollendefinition erstellen und dann für verschiedene *Bereiche* mehrmals zuzuweisen. Eine benutzerdefinierte Rolle kann im organisationsweiten Bereich oder im Bereich eines einzelnen Azure AD-Objekts zugewiesen werden. Ein Beispiel für einen Objektbereich ist eine einzelne Anwendungsregistrierung. Durch Verwendung verschiedener Bereiche kann die gleiche Rollendefinition Sally für alle Anwendungsregistrierungen in der Organisation und dann Naveen nur für die Anwendungsregistrierung der Contoso-Spesenabrechnungen zugewiesen werden.

Tipps für die Erstellung und Verwendung von benutzerdefinierten Rollen für die Delegierung der Anwendungsverwaltung:
- Benutzerdefinierte Rollen gewähren nur Zugriff auf den Blättern für die aktuellen Anwendungsregistrierungen im Azure AD-Portal. Sie gewähren keinen Zugriff auf den Blättern für Legacyanwendungsregistrierungen.
- Benutzerdefinierte Rollen gewähren keinen Zugriff auf das Azure AD-Portal, wenn die Benutzereinstellung „Zugriff auf Azure AD-Verwaltungsportal einschränken“ auf „Ja“ festgelegt ist.
- Anwendungsregistrierungen, auf die der Benutzer unter Verwendung von Rollenzuweisungen Zugriff hat, werden nur auf der Registerkarte „Alle Anwendungen“ auf der Seite „App-Registrierungen“ angezeigt. Auf der Registerkarte „Anwendungen mit Besitzer“ werden sie dagegen nicht angezeigt.

Weitere Informationen zu den Grundlagen von benutzerdefinierten Rollen finden Sie in der [Übersicht über benutzerdefinierte Rollen](custom-overview.md) sowie unter [Erstellen einer benutzerdefinierten Rolle](custom-create.md) und unter [Zuweisen einer Rolle](custom-assign-powershell.md).

## <a name="next-steps"></a>Nächste Schritte

- [Anwendungsregistrierungsuntertypen und -berechtigungen](custom-available-permissions.md)
- [Berechtigungen der Administratorrolle in Azure Active Directory](permissions-reference.md)
