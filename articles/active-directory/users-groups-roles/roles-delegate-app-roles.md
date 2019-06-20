---
title: Delegieren von Anwendungsadministratorrollen – Azure Active Directory | Microsoft-Dokumentation
description: Delegieren von Rollen durch die Anwendungszugriffsverwaltung, um Berechtigungen in Azure Active Directory zu gewähren
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 58ca814551d8c7d309328f236052e1d07ac6f035
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60469127"
---
# <a name="delegate-app-administrator-roles-in-azure-active-directory"></a>Delegieren von App-Administratorrollen in Azure Active Directory

 In Azure AD können Sie die Verwaltung des Anwendungszugriffs an einen Satz integrierter Administratorrollen delegieren. Außer den globalen Administratormehraufwand zu reduzieren, kann das Delegieren spezieller Berechtigungen zum Verwalten von Anwendungszugriffsaufgaben Ihren Sicherheitsstatus verbessern und das Risiko nicht autorisierten Zugriffs reduzieren. Delegierungsprobleme und allgemeine Richtlinien werden unter [Delegieren der Administration in Azure Active Directory (Azure AD)](roles-concept-delegation.md) erörtert.

## <a name="delegate-app-administration"></a>Delegieren Sie die Verwaltung der App.

Die folgenden Rollen gewähren Berechtigungen zum Verwalten von Anwendungsregistrierungen, Einstellungen für einmaliges Anmelden, Benutzer- und Gruppenzuweisungen sowie zum Zustimmen zu delegierten Berechtigungen und Anwendungsberechtigungen (mit Ausnahme von Microsoft Graph und Azure AD Graph). Der einzige Unterschied besteht darin, dass die Anwendungsadministratorrolle außerdem Berechtigungen zum Verwalten von Einstellungen des Anwendungsproxys gewährt. Keine dieser Rollen gewährt die Fähigkeit zum Verwalten der Einstellungen des bedingten Zugriffs.
> [!IMPORTANT]
> Benutzer, die dieser Rolle zugewiesen sind, können einer Anwendung Anmeldeinformationen hinzufügen und diese Anmeldeinformationen verwenden, um die Anwendung zu imitieren. Dieses Annehmen der Identität der Anwendung bedeutet ggf. eine Rechteerweiterung im Vergleich zu dem, was der Benutzer unter seinen Rollenzuweisungen in Azure AD tun kann. Ein Benutzer, der dieser Rolle zugewiesen ist, könnte potenziell Benutzer oder andere Objekte erstellen oder aktualisieren, während er die Identität der Anwendung angenommen hat.

So gewähren Sie die Fähigkeit zur Verwaltung des Anwendungszugriffs im Azure-Portal:

1. Melden Sie sich bei Ihrem [Azure AD-Mandanten](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) mit einem Konto an, das für die globale Administratorrolle im Mandanten geeignet ist.
2. Wenn Sie über ausreichende Berechtigungen verfügen, öffnen Sie die [Seite „Rollen und Administratoren“](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators).
3. Öffnen Sie eine der folgenden Rollen, um ihre Mitgliedszuweisungen anzuzeigen:
   * **Anwendungsadministrator**
   * **Cloudanwendungsadministrator**
4. Wählen Sie auf der Seite **Mitglieder** für die Rolle **Mitglied hinzufügen** aus.
5. Wählen Sie mindestens ein Mitglied aus, das der Rolle hinzugefügt werden soll. <!--Members can be users or groups.-->

Sie können die Beschreibung für diese Rollen unter [Verfügbare Rollen](directory-assign-admin-roles.md#available-roles) anzeigen.

## <a name="delegate-app-registration"></a>Delegieren der App-Registrierung

Standardmäßig können alle Benutzer Anwendungsregistrierungen erstellen, aber Sie können selektiv die Berechtigung zum Erstellen von Anwendungsregistrierungen oder die Berechtigung zum Zustimmen zum Autorisieren einer App gewähren.

1. Melden Sie sich bei Ihrem [Azure AD-Mandanten](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) mit einem Konto an, das für die globale Administratorrolle im Mandanten geeignet ist.
2. Wenn Sie ausreichende Berechtigungen erhalten haben, legen Sie eine oder beide der folgenden fest:
   * Legen Sie auf der [Seite „Benutzereinstellungen“ für Ihren Mandanten](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) für **Benutzer können Anwendungen registrieren** „Nein“ fest.
   * Legen Sie unter [Unternehmensanwendungen – Benutzereinstellungen](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) für **Benutzer können Anwendungen den Zugriff auf Unternehmensdaten in ihrem Namen gestatten** „Nein“ fest.
3. Weisen Sie anschließend nach Bedarf Benutzer zu, die diese Berechtigung benötigen, um Mitglieder der Anwendungsentwicklerrolle zu sein.

Wenn ein Benutzer eine Anwendung registriert, wird er automatisch als erster Besitzer für die Anwendung hinzugefügt.

## <a name="delegate-app-ownership"></a>Delegieren Sie den Besitz der App.

App- und App-Registrierungsbesitzer können jeweils nur die Anwendungen oder App-Registrierungen verwalten, die sie besitzen. Wenn Sie beispielsweise einen Besitzer für die Salesforce-Anwendung hinzufügen, kann dieser Besitzer den Zugriff auf Salesforce sowie die Konfiguration von Salesforce verwalten, jedoch keine anderen Anwendungen. Eine App kann viele Besitzer haben, und ein Benutzer kann Besitzer vieler Apps sein.

Ein Anwendungsbesitzer kann:

* Ändern der Anwendungseigenschaften, z.B. des Namens und der Berechtigungen, die die App anfordert
* Verwalten von Anmeldeinformationen
* Einmaliges Anmelden konfigurieren
* Zuweisen des Benutzerzugriffs
* Hinzufügen oder Entfernen anderer Besitzer
* Bearbeiten des App-Manifests
* Veröffentlichen der App im App-Katalog

> [!IMPORTANT]
> Benutzer, die dieser Rolle zugewiesen sind, können einer Anwendung Anmeldeinformationen hinzufügen und diese Anmeldeinformationen verwenden, um die Anwendung zu imitieren. Dieses Annehmen der Identität der Anwendung bedeutet ggf. eine Rechteerweiterung im Vergleich zu dem, was der Benutzer unter seinen Rollenzuweisungen in Azure AD tun kann. Ein Benutzer, der dieser Rolle zugewiesen ist, könnte potenziell Benutzer oder andere Objekte erstellen oder aktualisieren, während er die Identität der Anwendung angenommen hat.

Der Besitzer einer App-Registrierung kann die App-Registrierung anzeigen und bearbeiten.

<!-- ### To assign an enterprise app ownership role to a user

1. Sign in to your [Azure AD tenant](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) with an account that is the Global Administrator for the tenant.
2. On the [Roles and administrators page](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RolesAndAdministrators), open one of the following roles to see its member assignments:
  * **Enterprise Application Owner**
  * **Application Registration Owner**
3. On the **Members** page for the role, select **Add member**.
4. Select one or more members to add to the role. -->

### <a name="to-assign-an-owner-to-an-application"></a>So weisen Sie einen Besitzer einer Anwendung zu

1. Melden Sie sich bei Ihrem [Azure AD-Mandanten](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) mit einem Konto an, das für den Anwendungsadministrator oder Cloudanwendungsadministrator für den Mandanten geeignet ist.
2. Wählen Sie auf der [Seite „App-Registrierungen“](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) für den Mandanten eine App zum Öffnen der Seite **Übersicht** für die App aus.
3. Wählen Sie **Besitzer** aus, um die Liste der Besitzer für die App anzuzeigen.
4. Wählen Sie **Hinzufügen** aus, um einen oder mehrere Besitzer der App hinzuzufügen.

### <a name="to-assign-an-owner-to-an-application-registration"></a>So weisen Sie einen Besitzer einer Anwendungsregistrierung zu

1. Melden Sie sich bei Ihrem [Azure AD-Mandanten](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) mit einem Konto an, das für die Anwendungsadministrator- oder die Cloudanwendungsadministratorrolle im Mandanten geeignet ist.
2. Wenn Sie über ausreichende Berechtigungen verfügen, wählen Sie auf der [Seite „Unternehmensanwendungen“](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) für den Mandanten eine App-Registrierung aus, um sie zu öffnen.
3. Wählen Sie **Settings**aus.
4. Wählen Sie **Besitzer** auf der Seite **Einstellungen** aus, um die Liste der Besitzer für die App anzuzeigen.
5. Wählen Sie **Besitzer hinzufügen** aus, um einen oder mehrere zur App hinzuzufügende Besitzer auszuwählen.

## <a name="next-steps"></a>Nächste Schritte

* [Berechtigungen der Administratorrolle in Azure Active Directory](directory-assign-admin-roles.md)
