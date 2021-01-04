---
title: 'Einrichten der Self-Service-Gruppenverwaltung: Azure Active Directory | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie Sicherheitsgruppen oder Microsoft 365-Gruppen in Azure Active Directory erstellen und verwalten sowie Mitgliedschaften in Sicherheitsgruppen oder Microsoft 365-Gruppen anfordern.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: c6b2b8e3374c362f937aa5cfe106e8da9f9aa39f
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96548000"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Einrichten der Self-Service-Gruppenverwaltung in Azure Active Directory 

Sie können Benutzern die Erstellung und Verwaltung ihrer eigenen Sicherheitsgruppen oder Microsoft 365-Gruppen in Azure Active Directory (Azure AD) ermöglichen. Der Besitzer der Gruppe kann Mitgliedschaftsanforderungen genehmigen oder ablehnen sowie die Steuerung der Gruppenmitgliedschaft delegieren. Self-Service-Funktionen zur Gruppenverwaltung sind nicht für E-Mail-aktivierte Sicherheitsgruppen oder Verteilerlisten verfügbar.

## <a name="self-service-group-membership-defaults"></a>Standardwerte für die Self-Service-Gruppenmitgliedschaft

Wenn Sicherheitsgruppen im Azure-Portal oder über Azure AD PowerShell erstellt werden, können nur die Besitzer der Gruppen die Mitgliedschaft aktualisieren. Ein Beitritt zu im [Zugriffsbereich](https://account.activedirectory.windowsazure.com/r#/joinGroups) nach dem Self-Service-Prinzip erstellten Sicherheitsgruppen sowie zu allen Microsoft 365-Gruppen ist für alle vom Besitzer genehmigten oder automatisch genehmigten Benutzer möglich. Im Zugriffsbereich können Sie beim Erstellen einer Gruppe die Mitgliedschaftsoptionen ändern.

Erstellte Gruppen in | Standardverhalten von Sicherheitsgruppen | Standardverhalten von Microsoft 365-Gruppen
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](../enterprise-users/groups-settings-cmdlets.md) | Nur Besitzer können Mitglieder hinzufügen.<br>Sichtbar, jedoch kein Beitritt im Zugriffsbereich möglich. | Beitritt aller Benutzer ist möglich.
[Azure portal](https://portal.azure.com) | Nur Besitzer können Mitglieder hinzufügen.<br>Sichtbar, jedoch kein Beitritt im Zugriffsbereich möglich.<br>Besitzer wird bei der Gruppenerstellung nicht automatisch zugewiesen. | Beitritt aller Benutzer ist möglich.
[Zugriffsbereich](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Beitritt aller Benutzer ist möglich.<br>Mitgliedschaftsoptionen können bei der Erstellung der Gruppe geändert werden. | Beitritt aller Benutzer ist möglich.<br>Mitgliedschaftsoptionen können bei der Erstellung der Gruppe geändert werden.

## <a name="self-service-group-management-scenarios"></a>Szenarien der Self-Service-Gruppenverwaltung

* **Delegierte Gruppenverwaltung** – Beispielsweise ein Administrator, der den Zugriff auf eine SaaS-Anwendung verwaltet, die im Unternehmen verwendet wird. Die Verwaltung dieser Zugriffsrechte wird immer mühsamer, und der Administrator bittet den Geschäftsinhaber, eine neue Gruppe zu erstellen. Der Administrator weist den Zugriff auf die Anwendung der neuen Gruppe zu und fügt der Gruppe alle Personen zu, die bereits auf die Anwendung zugreifen. Der Geschäftsinhaber kann dann weitere Benutzer hinzufügen, und die Benutzer erhalten automatisch Zugriff auf die Anwendung. Der Geschäftsinhaber muss nicht warten, bis der Administrator die Verwaltungsschritte für den Zugriff der Benutzer durchgeführt hat. Wenn der Administrator einem Manager in einer anderen Geschäftseinheit die gleiche Berechtigung erteilt, kann diese Person ebenfalls den Zugriff für ihre eigenen Gruppenmitglieder verwalten. Weder der Geschäftsinhaber noch der Manager kann die Gruppenmitgliedschaften des jeweils anderen anzeigen oder verwalten. Der Administrator kann weiterhin alle Benutzer anzeigen, die auf die Anwendung zugreifen können, und kann die Zugriffsrechte bei Bedarf blockieren.
* **Self-Service-Gruppenverwaltung** Ein Beispiel für dieses Szenario sind zwei Benutzer, die beide über unabhängig voneinander eingerichtete SharePoint Online-Websites verfügen. Sie möchten dem anderen Team jeweils Zugriff auf ihre Websites gewähren. Hierzu können sie eine Gruppe in Azure AD erstellen, und in SharePoint Online wählt jeder der beiden Benutzer diese Gruppe dann aus, um Zugriff auf seine Websites bereitzustellen. Wenn ein weiterer Benutzer Zugriff benötigt, fordert er diesen einfach über den Zugriffsbereich an und erhält nach Genehmigung automatisch Zugriff auf beide SharePoint Online-Websites. Später entscheidet einer der Hauptbenutzer, dass alle Personen, die auf die Website zugreifen, auch Zugriff auf eine bestimmte SaaS-Anwendung erhalten sollen. Der Administrator der SaaS-Anwendung kann der SharePoint Online-Website Zugriffsrechte für die Anwendung hinzufügen. Anschließend können alle Personen, deren Zugriff genehmigt wird, auf die beiden SharePoint Online-Websites und diese SaaS-Anwendung zugreifen.

## <a name="make-a-group-available-for-user-self-service"></a>Einrichten einer Gruppe für Self-Service durch Benutzer

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com) mit dem Konto eines globalen Administrators für das Verzeichnis an.
1. Klicken Sie auf **Gruppen** und anschließend auf **Allgemeine Einstellungen**.
1. Legen Sie **Besitzer können Anforderungen für eine Gruppenmitgliedschaft im Zugriffspanel verwalten** auf **Ja** fest.
1. Legen Sie **Zugriff auf Gruppen im Zugriffspanel beschränken** auf **Nein** fest.
1. Folgendes geschieht, wenn Sie **Benutzer können Microsoft 365-Gruppen in Azure-Portalen erstellen** oder **Benutzer können Microsoft 365-Gruppen in Azure-Portalen erstellen** festlegen auf

    - **Ja**: Alle Benutzer in Ihrer Azure AD-Organisation können neue Sicherheitsgruppen erstellen und Mitglieder zu diesen Gruppen hinzufügen. Diese neuen Gruppen werden auch für alle Benutzer im Zugriffsbereich angezeigt. Wenn die Richtlinieneinstellung der Gruppe dies zulässt, können andere Benutzer Anforderungen in Bezug auf den Beitritt zu diesen Gruppen erstellen.
    - **Nein**: Benutzer können keine Gruppen erstellen und keine vorhandenen Gruppen ändern, deren Besitzer sie sind. Sie können aber trotzdem die Mitgliedschaften dieser Gruppen verwalten und Anforderungen anderer Benutzer zum Beitreten zu diesen Gruppen genehmigen.

Sie können auch **Besitzer, die im Azure-Portal Mitglieder als Gruppenbesitzer zuweisen können**, verwenden, um eine detailliertere Zugriffssteuerung über die Gruppenverwaltung nach dem Self-Service-Prinzip für Ihre Benutzer zu erreichen.

Wenn Benutzer Gruppen erstellen können, dürfen alle Benutzer in Ihrer Organisation neue Gruppen erstellen und können dann als Standardbesitzer Mitglieder diesen Gruppen hinzufügen. Sie können keine Personen angeben, die ihre eigenen Gruppen erstellen können. Sie können lediglich Personen angeben, die ein anderes Gruppenmitglied als Gruppenbesitzer festlegen.

> [!NOTE]
> Eine Azure Active Directory Premium-Lizenz (P1 oder P2) ist erforderlich, damit Benutzer den Beitritt zu einer Sicherheitsgruppe oder Microsoft 365-Gruppe anfordern und Besitzer Mitgliedsanforderungen genehmigen oder ablehnen können. Ohne eine Azure Active Directory Premium-Lizenz können Benutzer ihre Gruppen weiterhin im Zugriffsbereich verwalten, sie können jedoch keine Gruppe erstellen, die im Zugriffsbereich eine Genehmigung durch den Besitzer erfordert, und sie können keinen Gruppenbeitritt anfordern.

## <a name="next-steps"></a>Nächste Schritte

Diese Artikel enthalten zusätzliche Informationen zu Azure Active Directory.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory-Cmdlets zum Konfigurieren von Gruppeneinstellungen](../enterprise-users/groups-settings-cmdlets.md)
* [Anwendungsverwaltung in Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Was ist Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
