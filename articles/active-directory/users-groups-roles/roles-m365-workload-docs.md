---
title: Administratorrolleninhalte für Microsoft 365-Workloads – Azure AD | Microsoft-Dokumentation
description: Suchen von Inhalten und API-Referenzen für Administratorrollen für Microsoft 365-Workloads in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.subservice: users-groups-roles
ms.topic: article
ms.date: 01/24/2019
ms.author: curtand
ms.reviewer: vincesm
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7460c3a7d957634d14186d313994c65d89fa4a32
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60468180"
---
# <a name="administrator-roles-for-microsoft-365-workloads"></a>Administratorrollen für Microsoft 365-Workloads

Alle Produkte in Microsoft 365 können mit Administratorrollen in Azure AD verwaltet werden. Einige Produkte bieten darüber hinaus zusätzliche Rollen, die für dieses Produkt spezifisch sind. Informationen zu den von den einzelnen Produkten unterstützten Rollen finden Sie in der folgenden Tabelle. Allgemeine Informationen zu Delegierungsproblemen finden Sie unter [Planen der Rollendelegierung in Azure Active Directory](roles-concept-delegation.md).

## <a name="where-to-find-content"></a>Ort

Microsoft 365-Workload | Rolleninhalt | API-Inhalt
---------------------- | ------------------ | -----------------
Administratorrollen in Office 365- und Microsoft 365-Businessplänen | [Office 365-Administratorrollen](https://docs.microsoft.com/office365/admin/add-users/about-admin-roles?view=o365-worldwide) | Nicht verfügbar
Azure Active Directory (Azure AD) und Azure AD Identity Protection| [Azure AD-Administratorrollen](directory-assign-admin-roles.md) | [Graph-API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Abrufen von Rollenzuweisungen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Exchange Online| [Rollenbasierte Zugriffssteuerung in Exchange](https://docs.microsoft.com/exchange/understanding-role-based-access-control-exchange-2013-help) |  [PowerShell für Exchange](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Abrufen von Rollenzuweisungen](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
SharePoint Online | [Azure AD-Administratorrollen](directory-assign-admin-roles.md)<br>Auch [Informationen zur SharePoint-Administratorrolle in Office 365](https://docs.microsoft.com/sharepoint/sharepoint-admin-role) | [Graph-API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Abrufen von Rollenzuweisungen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Teams/Skype for Business | [Azure AD-Administratorrollen](directory-assign-admin-roles.md) | [Graph-API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Abrufen von Rollenzuweisungen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Security & Compliance Center (Office 365 Advanced Threat Protection, Schutz für Exchange Online, Information Protection) | [Office 365-Administratorrollen](https://docs.microsoft.com/office365/SecurityCompliance/permissions-in-the-security-and-compliance-center) | [Exchange PowerShell](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/add-managementroleentry?view=exchange-ps)<br>[Abrufen von Rollenzuweisungen](https://docs.microsoft.com/powershell/module/exchange/role-based-access-control/get-rolegroup?view=exchange-ps)
Sicherheitsbewertung | [Azure AD-Administratorrollen](directory-assign-admin-roles.md) | [Graph-API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Abrufen von Rollenzuweisungen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Compliance-Manager | [Compliance-Manager-Rollen](https://docs.microsoft.com/office365/securitycompliance/meet-data-protection-and-regulatory-reqs-using-microsoft-cloud#permissions-and-role-based-access-control) | Nicht verfügbar
Azure Information Protection | [Azure AD-Administratorrollen](directory-assign-admin-roles.md) | [Graph-API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Abrufen von Rollenzuweisungen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Microsoft Cloud App Security | [Rollenbasierte Zugriffssteuerung](https://docs.microsoft.com/cloud-app-security/manage-admins) | [API-Referenz](https://docs.microsoft.com/cloud-app-security/api-tokens) 
Azure Advanced Threat Protection | [Azure ATP-Rollengruppen](https://docs.microsoft.com/azure-advanced-threat-protection/atp-role-groups) | Nicht verfügbar
Windows Defender Advanced Threat Protection | [Rollenbasierte Zugriffssteuerung von Windows Defender ATP](https://docs.microsoft.com/windows/security/threat-protection/windows-defender-atp/rbac-windows-defender-advanced-threat-protection) | Nicht verfügbar
Privileged Identity Management | [Azure AD-Administratorrollen](directory-assign-admin-roles.md) | [Graph-API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Abrufen von Rollenzuweisungen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)
Intune | [Rollenbasierte Zugriffssteuerung von Intune](https://docs.microsoft.com/intune/role-based-access-control) | [Graph-API](https://docs.microsoft.com/graph/api/resources/intune-rbac-conceptual?view=graph-rest-beta)<br>[Abrufen von Rollenzuweisungen](https://docs.microsoft.com/graph/api/intune-rbac-roledefinition-list?view=graph-rest-beta)
Managed Desktop | [Azure AD-Administratorrollen](directory-assign-admin-roles.md) | [Graph-API](https://docs.microsoft.com/graph/api/overview?view=graph-rest-1.0)<br>[Abrufen von Rollenzuweisungen](https://docs.microsoft.com/graph/api/directoryrole-list?view=graph-rest-1.0)

## <a name="next-steps"></a>Nächste Schritte

* [Anzeigen und Zuweisen von Azure AD-Administratorrollen](directory-manage-roles-portal.md)
* [Berechtigungen von Administratorrollen in Azure Active Directory](directory-assign-admin-roles.md)
