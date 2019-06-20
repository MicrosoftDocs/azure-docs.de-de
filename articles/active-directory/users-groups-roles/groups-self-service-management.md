---
title: 'Einrichten der Self-Service-Gruppenverwaltung: Azure Active Directory | Microsoft-Dokumentation'
description: Es wird beschrieben, wie Sie Sicherheitsgruppen oder Office 365-Gruppen in Azure Active Directory erstellen und verwalten und Mitgliedschaften in Sicherheits- oder Office 365-Gruppen anfordern.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: b860257fd1b3f0897152dc3d48bff0c7e1d3d994
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60469860"
---
# <a name="set-up-self-service-group-management-in-azure-active-directory"></a>Einrichten der Self-Service-Gruppenverwaltung in Azure Active Directory 

Sie können Benutzern die Erstellung und Verwaltung ihrer eigenen Sicherheitsgruppen oder Office 365-Gruppen in Azure Active Directory (Azure AD) ermöglichen. Der Besitzer der Gruppe kann Mitgliedschaftsanforderungen genehmigen oder ablehnen sowie die Steuerung der Gruppenmitgliedschaft delegieren. Self-Service-Funktionen zur Gruppenverwaltung sind nicht für E-Mail-aktivierte Sicherheitsgruppen oder Verteilerlisten verfügbar.

## <a name="self-service-group-membership-defaults"></a>Standardwerte für die Self-Service-Gruppenmitgliedschaft

Wenn Sicherheitsgruppen im Azure-Portal oder über Azure AD PowerShell erstellt werden, können nur die Besitzer der Gruppen die Mitgliedschaft aktualisieren. Ein Beitritt zu im [Zugriffsbereich](https://account.activedirectory.windowsazure.com/r#/joinGroups) erstellten Sicherheitsgruppen und allen Office 365-Gruppen ist für alle vom Besitzer genehmigten sowie automatisch genehmigten Benutzer möglich. Im Zugriffsbereich können Sie beim Erstellen einer Gruppe die Mitgliedschaftsoptionen ändern.

Erstellte Gruppen in | Standardverhalten von Sicherheitsgruppen | Standardverhalten von Office 365-Gruppen
------------------ | ------------------------------- | ---------------------------------
[Azure AD PowerShell](groups-settings-cmdlets.md) | Nur Besitzer können Mitglieder hinzufügen.<br>Sichtbar, jedoch kein Beitritt im Zugriffsbereich möglich. | Beitritt aller Benutzer ist möglich.
[Azure-Portal](https://portal.azure.com) | Nur Besitzer können Mitglieder hinzufügen.<br>Sichtbar, jedoch kein Beitritt im Zugriffsbereich möglich.<br>Besitzer wird bei der Gruppenerstellung nicht automatisch zugewiesen. | Beitritt aller Benutzer ist möglich.
[Zugriffsbereich](https://account.activedirectory.windowsazure.com/r#/joinGroups) | Beitritt aller Benutzer ist möglich.<br>Mitgliedschaftsoptionen können bei der Erstellung der Gruppe geändert werden. | Beitritt aller Benutzer ist möglich.<br>Mitgliedschaftsoptionen können bei der Erstellung der Gruppe geändert werden.

## <a name="self-service-group-management-scenarios"></a>Szenarien der Self-Service-Gruppenverwaltung

* **Delegierte Gruppenverwaltung** – Beispielsweise ein Administrator, der den Zugriff auf eine SaaS-Anwendung verwaltet, die im Unternehmen verwendet wird. Die Verwaltung dieser Zugriffsrechte wird immer mühsamer, und der Administrator bittet den Geschäftsinhaber, eine neue Gruppe zu erstellen. Der Administrator weist den Zugriff auf die Anwendung der neuen Gruppe zu und fügt der Gruppe alle Personen zu, die bereits auf die Anwendung zugreifen. Der Geschäftsinhaber kann dann weitere Benutzer hinzufügen, und die Benutzer erhalten automatisch Zugriff auf die Anwendung. Der Geschäftsinhaber muss nicht warten, bis der Administrator die Verwaltungsschritte für den Zugriff der Benutzer durchgeführt hat. Wenn der Administrator einem Manager in einer anderen Geschäftseinheit die gleiche Berechtigung erteilt, kann diese Person ebenfalls den Zugriff für ihre eigenen Gruppenmitglieder verwalten. Weder der Geschäftsinhaber noch der Manager kann die Gruppenmitgliedschaften des jeweils anderen anzeigen oder verwalten. Der Administrator kann weiterhin alle Benutzer anzeigen, die auf die Anwendung zugreifen können, und kann die Zugriffsrechte bei Bedarf blockieren.
* **Self-Service-Gruppenverwaltung** Ein Beispiel für dieses Szenario sind zwei Benutzer, die beide über unabhängig voneinander eingerichtete SharePoint Online-Websites verfügen. Sie möchten dem anderen Team jeweils Zugriff auf ihre Websites gewähren. Hierzu können sie eine Gruppe in Azure AD erstellen, und in SharePoint Online wählt jeder der beiden Benutzer diese Gruppe dann aus, um Zugriff auf seine Websites bereitzustellen. Wenn ein weiterer Benutzer Zugriff benötigt, fordert er diesen einfach über den Zugriffsbereich an und erhält nach Genehmigung automatisch Zugriff auf beide SharePoint Online-Websites. Später entscheidet einer der Hauptbenutzer, dass alle Personen, die auf die Website zugreifen, auch Zugriff auf eine bestimmte SaaS-Anwendung erhalten sollen. Der Administrator der SaaS-Anwendung kann der SharePoint Online-Website Zugriffsrechte für die Anwendung hinzufügen. Anschließend können alle Personen, deren Zugriff genehmigt wird, auf die beiden SharePoint Online-Websites und diese SaaS-Anwendung zugreifen.

## <a name="make-a-group-available-for-user-self-service"></a>Einrichten einer Gruppe für Self-Service durch Benutzer

1. Melden Sie sich beim [Azure AD Admin Center](https://aad.portal.azure.com) mit dem Konto eines globalen Administrators für das Verzeichnis an.
2. Wählen Sie **Benutzer und Gruppen** und dann **Gruppeneinstellungen**.
3. Legen Sie **Self-Service-Gruppenverwaltung aktiviert** auf **Ja** fest.
4. Legen Sie **Benutzer können Sicherheitsgruppen erstellen** oder **Benutzer können Office 365-Gruppen erstellen** auf **Ja** fest.
   * Wenn diese Einstellungen aktiviert sind, können alle Benutzer in Ihrem Verzeichnis neue Sicherheitsgruppen erstellen und diesen Gruppen Mitglieder hinzufügen. Diese neuen Gruppen werden auch für alle Benutzer im Zugriffsbereich angezeigt. Wenn die Richtlinieneinstellung der Gruppe dies zulässt, können andere Benutzer Anforderungen in Bezug auf den Beitritt zu diesen Gruppen erstellen. 
   * Falls diese Einstellungen deaktiviert sind, können Benutzer keine Gruppen erstellen und keine vorhandenen Gruppen ändern, deren Besitzer sie sind. Sie können aber trotzdem die Mitgliedschaften dieser Gruppen verwalten und Anforderungen anderer Benutzer zum Beitreten zu diesen Gruppen genehmigen.

Sie können auch die Optionen **Benutzer, die Sicherheitsgruppen verwalten können** und **Benutzer, die Office 365-Gruppen verwalten können** verwenden, um für die Self-Service-Gruppenverwaltung Ihrer Benutzer eine feinere Zugriffssteuerung zu erzielen. Wenn die Option **Benutzer können Gruppen erstellen** aktiviert ist, können alle Benutzer in Ihrem Mandanten neue Gruppen erstellen und diesen Mitglieder hinzufügen. Sie können keine Personen angeben, die ihre eigenen Gruppen erstellen können. Sie können lediglich Personen angeben, die ein anderes Gruppenmitglied als Gruppenbesitzer festlegen.

Wenn Sie die Option **Benutzer, die Self-Service für Sicherheitsgruppen verwenden** und **Benutzer, die Office 365-Gruppen verwalten können** auf **Ja** festlegen, können alle Benutzer in Ihrem Mandanten neue Gruppen erstellen.

Sie können auch die Option **Gruppe, die Sicherheitsgruppen verwalten kann** oder **Gruppe, die Office 365-Gruppen verwalten kann** verwenden, um eine einzelne Gruppe anzugeben, deren Mitglieder den Self-Service nutzen können.

## <a name="next-steps"></a>Nächste Schritte

Diese Artikel enthalten zusätzliche Informationen zu Azure Active Directory.

* [Verwalten des Zugriffs auf Ressourcen mit Azure Active Directory-Gruppen](../fundamentals/active-directory-manage-groups.md)
* [Azure Active Directory-Cmdlets zum Konfigurieren von Gruppeneinstellungen](groups-settings-cmdlets.md)
* [Anwendungsverwaltung in Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Was ist Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integrieren lokaler Identitäten in Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
