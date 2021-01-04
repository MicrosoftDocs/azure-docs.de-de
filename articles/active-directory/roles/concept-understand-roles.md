---
title: Grundlegendes zu Konzepten im Zusammenhang mit Azure Active Directory-Rollen
description: Hier finden Sie grundlegende Informationen zu integrierten und benutzerdefinierten Azure Active Directory-Rollen mit Ressourcenbereich in Azure Active Directory.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/20/2020
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 968ca6fa293e7bcc01bae6f48164c48c19b8267b
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501675"
---
# <a name="understand-roles-in-azure-active-directory"></a>Grundlegendes zu Rollen in Azure Active Directory

Es gibt etwa 60 integrierte Azure AD-Rollen (Azure Active Directory). Hierbei handelt es sich um Rollen mit einer Reihe fester Rollenberechtigungen. Zur Ergänzung der integrierten Rollen werden von Azure AD auch benutzerdefinierte Rollen unterstützt. Mit benutzerdefinierten Rollen können Sie die gewünschten Rollenberechtigungen auswählen. So können Sie beispielsweise eine Rolle für die Verwaltung bestimmter Azure AD-Ressourcen (etwa Anwendungen oder Dienstprinzipale) erstellen.

In diesem Artikel erfahren Sie, was Azure AD-Rollen sind und wie sie verwendet werden können.

## <a name="how-azure-ad-roles-are-different-from-other-microsoft-365-roles"></a>Unterschiede zwischen Azure AD-Rollen und anderen Microsoft 365-Rollen

Microsoft 365 umfasst zahlreiche verschiedene Dienste – beispielsweise Azure AD und Intune. Einige dieser Dienste verfügen über eigene rollenbasierte Zugriffssteuerungssysteme. Das gilt insbesondere für folgende Dienste:

- Azure AD
- Exchange
- Intune
- Security Center
- Compliance Center
- Microsoft Cloud App Security
- Commerce

Andere Dienste wie Teams, SharePoint und Managed Desktop verfügen dagegen über keine separaten rollenbasierten Zugriffssteuerungssysteme. Sie verwenden Azure AD-Rollen für den Administratorzugriff. Azure verfügt über ein eigenes rollenbasiertes Zugriffssteuerungssystem für Azure-Ressourcen wie etwa virtuelle Computer, und dieses System unterscheidet sich von Azure AD-Rollen.

Ein separates rollenbasiertes Zugriffssteuerungssystem bedeutet, dass Rollendefinitionen und Rollenzuweisungen in einem anderen Datenspeicher gespeichert werden. Ebenso gibt es einen anderen Richtlinienentscheidungspunkt, an dem Zugriffsüberprüfungen durchgeführt werden. Weitere Informationen finden Sie unter [Rollen für Microsoft 365-Dienste in Azure Active Directory](m365-workload-docs.md) sowie unter [Administratorrollen für klassische Abonnements, Azure-Rollen und Azure AD-Rollen](../../role-based-access-control/rbac-and-directory-admin-roles.md).

## <a name="why-some-azure-ad-roles-are-for-other-services"></a>Warum gibt es einige Azure AD-Rollen für andere Dienste?

Microsoft 365 verfügt über eine Reihe rollenbasierter Zugriffssteuerungssysteme, die im Laufe der Zeit unabhängig voneinander entwickelt wurden und jeweils über ein eigenes Dienstportal verfügen. Um die Identitätsverwaltung innerhalb von Microsoft 365 über das Azure AD-Portal zu vereinfachen, haben wir einige dienstspezifische integrierte Rollen hinzugefügt, die jeweils Administratorzugriff auf einen Microsoft 365-Dienst gewähren. Ein Beispiel hierfür ist die Rolle „Exchange-Administrator“ in Azure AD. Diese Rolle entspricht der [Rollengruppe „Organisationsverwaltung“](/exchange/organization-management-exchange-2013-help) im rollenbasierten Zugriffssteuerungssystem von Exchange und kann alle Aspekte von Exchange verwalten. Analog dazu haben wir die Rollen „Intune-Administrator“, „Teams-Administrator“, „SharePoint-Administrator“ usw. hinzugefügt. Serverspezifische Rollen sind eine Kategorie integrierter Azure AD-Rollen im folgenden Abschnitt.

## <a name="categories-of-azure-ad-roles"></a>Kategorien von Azure AD-Rollen

Integrierte Azure AD-Rollen unterscheiden sich hinsichtlich ihres Verwendungsorts und lassen sich in drei allgemeine Kategorien unterteilen:

- **Azure AD-spezifische Rollen:** Durch diese Rollen werden nur Berechtigungen für die Verwaltung von Ressourcen innerhalb von Azure AD gewährt. „Benutzeradministrator“, „Anwendungsadministrator“ und „Gruppenadministrator“ gewähren beispielsweise jeweils Berechtigungen für die Verwaltung von Ressourcen in Azure AD.
- **Dienstspezifische Rollen:** Für wichtige Microsoft 365-Dienste (nicht Azure AD) wurden dienstspezifische Rollen erstellt, die Berechtigungen für die Verwaltung aller Features innerhalb des Diensts gewähren.  Von den Rollen „Exchange-Administrator“, „Intune-Administrator“, „SharePoint-Administrator“ und „Teams-Administrator“ können Features mit den jeweiligen Diensten verwaltet werden. Ein Exchange-Administrator kann Postfächer verwalten, ein Intune-Administrator kann Geräterichtlinien verwalten, ein SharePoint-Administrator kann Websitesammlungen verwalten, ein Teams-Administrator kann Aspekte der Anrufqualität verwalten usw.
- **Dienstübergreifende Rollen:** Einige Rollen umfassen mehrere Dienste. Es gibt zwei globale Rollen: „Globaler Administrator“ und „Globaler Leser“. Diese beiden Rollen werden von allen Microsoft 365-Diensten anerkannt. Darüber hinaus gibt es einige sicherheitsbezogene Rollen wie „Sicherheitsadministrator“ und „Sicherheitsleseberechtigter“, die Zugriff auf mehrere Sicherheitsdienste in Microsoft 365 gewähren. Mit Sicherheitsadministratorrollen in Azure AD können Sie beispielsweise Microsoft 365 Security Center, Microsoft Defender Advanced Threat Protection und Microsoft Cloud App Security verwalten. Analog dazu können Sie mit der Rolle „Complianceadministrator“ compliancebezogene Einstellungen in Microsoft 365 Compliance Center, Exchange usw. verwalten.

![Die drei Kategorien der integrierten Azure AD-Rollen](./media/concept-understand-roles/role-overlap-diagram.png)

Die folgende Tabelle dient zum besseren Verständnis dieser Rollenkategorien. Die Kategorienamen wurden willkürlich gewählt und sollen keine Funktionen implizieren, die über die [dokumentierten Rollenberechtigungen](permissions-reference.md) hinausgehen.

Kategorie | Role
---- | ----
Azure AD-spezifische Rollen | Anwendungsadministrator<br>Anwendungsentwickler<br>Authentifizierungsadministrator<br>B2C-IEF-Schlüsselsatzadministrator<br>B2C-IEF-Richtlinienadministrator<br>Cloudanwendungsadministrator<br>Cloudgeräteadministrator<br>Administrator für den bedingten Zugriff<br>Geräteadministratoren<br>Rolle „Verzeichnis lesen“<br>Konten zur Verzeichnissynchronisierung<br>Verzeichnis schreiben<br>Administrator für Benutzerflows mit externer ID<br>Administrator für Benutzerflowattribute mit externer ID<br>Externer Identitätsanbieteradministrator<br>Gruppenadministrator<br>Gasteinladender<br>Helpdeskadministrator<br>Hybrididentitätsadministrator<br>Lizenzadministrator<br>Partnersupport der Ebene 1<br>Partnersupport der Ebene 2<br>Kennwortadministrator<br>Privilegierter Authentifizierungsadministrator<br>Administrator für privilegierte Rollen<br>Meldet Reader<br>Benutzerkontoadministrator
Dienstübergreifende Rollen | Unternehmensadministrator<br>Complianceadministrator<br>Administrator für Konformitätsdaten<br>Globaler Leser<br>Sicherheitsadministrator<br>Sicherheitsoperator<br>Sicherheitsleseberechtigter<br>Dienstunterstützungsadministrator
Dienstspezifische Rollen | Azure DevOps-Administrator<br>Azure Information Protection-Administrator<br>Abrechnungsadministrator<br>CRM-Dienstadministrator<br>Genehmigende Person für den LockBox-Kundenzugriff<br>Desktop Analytics-Administrator<br>Exchange-Dienstadministrator<br>Insights Administrator<br>Insights Business Leader<br>Intune-Dienstadministrator<br>Kaizala-Administrator<br>Lync-Dienstadministrator<br>Nachrichtencenter-Datenschutzleseberechtigter<br>Nachrichtencenter-Leser<br>Modern Commerce User<br>Netzwerkadministrator<br>Office-Apps-Administrator<br>Power BI-Dienstadministrator<br>Power Platform-Administrator<br>Druckeradministrator<br>Druckertechniker<br>Suchadministrator<br>Such-Editor<br>SharePoint-Dienstadministrator<br>Teams-Kommunikationsadministrator<br>Teams-Kommunikationssupporttechniker<br>Teams-Kommunikationssupportspezialist<br>Teams-Geräteadministrator<br>Teams-Dienstadministrator

## <a name="next-steps"></a>Nächste Schritte

- [Übersicht über die rollenbasierte Zugriffssteuerung in Azure Active Directory](custom-overview.md)
- Erstellen von Rollenzuweisungen per [Azure-Portal, Azure AD PowerShell und Graph-API](custom-create.md)
- [Anzeigen der Zuweisungen benutzerdefinierter Rollen in Azure Active Directory](custom-view-assignments.md)
