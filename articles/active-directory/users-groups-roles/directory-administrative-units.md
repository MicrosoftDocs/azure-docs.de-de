---
title: Verwalten von Verwaltungseinheiten (Vorschau) – Azure Active Directory | Microsoft-Dokumentation
description: Verwenden von Verwaltungseinheiten zum präziseren Delegieren von Berechtigungen in Azure Active Directory
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.subservice: users-groups-roles
ms.workload: identity
ms.date: 01/31/2019
ms.author: curtand
ms.reviewer: elkuzmen
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77f1a6e5b1e8191c1497e437cc26e1caf1255ba7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60472365"
---
# <a name="administrative-units-management-in-azure-active-directory-public-preview"></a>Verwalten von Verwaltungseinheiten in Azure Active Directory (öffentliche Vorschau)

Dieser Artikel beschreibt Verwaltungseinheiten – einen neuen Azure Active Directory (Azure AD)-Container mit Ressourcen, die zum Delegieren administrativer Berechtigungen und Anwenden von Richtlinien für bestimmte Benutzergruppen verwendet werden können. In Azure Active Directory können Administratoren mit Verwaltungseinheiten Berechtigungen zu regionalen Administratoren delegieren oder eine Richtlinie auf präziser Ebene festlegen.

Dies ist hilfreich in Organisationen mit unabhängigen Bereichen, z.B. eine große Universität, die aus vielen autonomen Fakultäten besteht (eine Wirtschaftsfakultät, eine Ingenieursfakultät, usw.), die unabhängig voneinander sind. Solche Bereiche haben ihre eigenen IT-Administratoren, die den Zugriff kontrollieren, Benutzer verwalten und Richtlinien festlegen, die speziell für die jeweilige Abteilung gelten. Zentrale Administratoren wollen in der Lage sein, den Administratoren der einzelnen Bereiche Berechtigungen für die Benutzer in ihren Bereichen zu erteilen. Genauer gesagt kann ausgehend von diesem Beispiel ein zentraler Administrator z. B. eine Verwaltungseinheit für eine bestimmten Fakultät (Wirtschaftsfakultät) erstellen und sie nur mit den Benutzern der Wirtschaftsfakultät befüllen. Dann kann ein zentraler Administrator den IT-Mitarbeitern der Wirtschaftsfakultät eine bereichsbezogene Rolle hinzufügen. Oder anders ausgedrückt, er kann den IT-Mitarbeitern der Wirtschaftsfakultät Berechtigungen nur für die Verwaltungseinheit „Wirtschaftsfakultät“ gewähren.

> [!IMPORTANT]
> Für die Verwendung von Verwaltungseinheiten muss der Administrator, der für eine solche Einheit zuständig ist, über eine Azure Active Directory Premium-Lizenz verfügen. Alle anderen Benutzer in der Verwaltungseinheit müssen über Azure Active Directory Basic-Lizenzen verfügen. Weitere Informationen finden Sie unter [Erste Schritte mit Azure AD Premium](../fundamentals/active-directory-get-started-premium.md).
>


Aus Sicht des zentralen Administrators ist eine Verwaltungseinheit ein Verzeichnisobjekt, das erstellt und mit Ressourcen aufgefüllt werden kann. **In dieser Vorschauversion können diese Ressourcen nur Benutzer sein.** Sobald erstellt und aufgefüllt, kann die Verwaltungseinheit zum Einschränken der erteilten Berechtigung nur für Ressourcen in der Verwaltungseinheit verwendet werden.

## <a name="managing-administrative-units"></a>Verwalten von Verwaltungseinheiten
In dieser Vorschauversion können Sie Verwaltungseinheiten mit dem Azure Active Directory-Modul für Windows PowerShell-Cmdlets erstellen und verwalten. Weitere Informationen hierzu finden Sie unter [Verwenden von Verwaltungseinheiten](https://docs.microsoft.com/powershell/azure/active-directory/working-with-administrative-units?view=azureadps-2.0).

Weitere Informationen zu Softwareanforderungen und der Installation des Azure AD-Moduls sowie Informationen zu den Azure AD-Modul-Cmdlets zur Verwaltung von Verwaltungseinheiten, einschließlich Syntax, Beschreibung der Parameter und Beispielen, finden Sie unter [Working with Administrative Units](https://docs.microsoft.com/powershell/azure/active-directory/overview?view=azureadps-2.0) (Verwenden von Verwaltungseinheiten).

## <a name="next-steps"></a>Nächste Schritte
[Azure Active Directory-Editionen](../fundamentals/active-directory-whatis.md)
