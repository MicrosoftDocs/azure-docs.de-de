---
title: App-Zustimmungsberechtigungen für benutzerdefinierte Rollen in Azure Active Directory | Microsoft-Dokumentation
description: Vorschau der App-Zustimmungsberechtigungen für benutzerdefinierte Azure AD-Rollen im Azure-Portal, in PowerShell oder in der Graph-API.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: roles
ms.topic: overview
ms.date: 11/04/2020
ms.author: curtand
ms.reviewer: psignoret
ms.custom: it-pro
ms.openlocfilehash: 358a458698ec1fd8443e15f71a84e057f33af527
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376730"
---
# <a name="app-consent-permissions-for-custom-roles-in-azure-active-directory"></a>App-Zustimmungsberechtigungen für benutzerdefinierte Rollen in Azure Active Directory

Dieser Artikel enthält die derzeit verfügbaren Berechtigungen zur App-Zustimmung für benutzerdefinierte Rollendefinitionen in Azure Active Directory (Azure AD). In diesem Artikel finden Sie die Berechtigungen, die bei einigen häufigen Szenarien im Zusammenhang mit der Zustimmung zu Apps und den entsprechenden Berechtigungen erforderlich sind.

## <a name="required-license-plan"></a>Erforderlicher Lizenzplan

Für dieses Feature benötigen Sie eine Azure AD Premium P1-Lizenz für Ihre Azure AD-Organisation. Um die richtige Lizenz für Ihre Anforderungen zu ermitteln, lesen Sie [Vergleich: Allgemein verfügbare Features der Editionen Free, Basic und Premium](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="app-consent-permissions"></a>App-Zustimmungsberechtigungen

Verwenden Sie die in diesem Artikel aufgeführten Berechtigungen, um App-Zustimmungsrichtlinien sowie die Berechtigung zum Erteilen der Zustimmung für Apps zu verwalten.

> [!NOTE]
> Im Admin-Portal von Azure AD wird das Hinzufügen der in diesem Artikel aufgeführten Berechtigungen zu einer benutzerdefinierten Verzeichnisrolle noch nicht unterstützt. Sie müssen [Azure AD PowerShell](custom-create.md#create-a-role-using-powershell) verwenden, um eine benutzerdefinierte Verzeichnisrolle mit den in diesem Artikel aufgeführten Berechtigungen zu erstellen.

### <a name="granting-delegated-permissions-to-apps-on-behalf-of-self-user-consent"></a>Erteilen von delegierten Berechtigungen für Apps im eigenen Namen (Benutzerzustimmung)

Ermöglicht Benutzern das Erteilen einer Zustimmung für Anwendungen im eigenen Namen (Benutzerzustimmung) gemäß einer App-Zustimmungsrichtlinie.

- microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id}

Dabei wird `{id}` durch die ID einer [App-Zustimmungsrichtlinie](../manage-apps/manage-app-consent-policies.md) ersetzt, in der die Bedingungen festgelegt sind, die erfüllt sein müssen, damit diese Berechtigung aktiv ist.

Wenn Sie den Benutzern beispielsweise erlauben möchten, Zustimmung im eigenen Namen gemäß der integrierten App-Zustimmungsrichtlinie mit der ID `microsoft-user-default-low` zu erteilen, würden Sie die Berechtigung `...managePermissionGrantsForSelf.microsoft-user-default-low` verwenden.

### <a name="granting-permissions-to-apps-on-behalf-of-all-admin-consent"></a>Erteilen von Berechtigungen für Apps in aller Namen (Administratorzustimmung)

Delegiert sowohl für delegierte Berechtigungen als auch für Anwendungsberechtigungen (App-Rollen) die mandantenweite Administratorzustimmung für Apps:

- microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id}

Dabei wird `{id}` durch die ID einer [App-Zustimmungsrichtlinie](../manage-apps/manage-app-consent-policies.md) ersetzt, in der die Bedingungen festgelegt sind, die erfüllt sein müssen, damit diese Berechtigung verwendbar ist.

Wenn Sie beispielsweise Rolleninhabern erlauben möchten, eine mandantenweite Administratorzustimmung für Apps gemäß der benutzerdefinierten [App-Zustimmungsrichtlinie](../manage-apps/manage-app-consent-policies.md) mit der ID `low-risk-any-app` zu erteilen, würden Sie die Berechtigung `microsoft.directory/servicePrincipals/managePermissionGrantsForAll.low-risk-any-app` verwenden.

### <a name="managing-app-consent-policies"></a>Verwalten von App-Zustimmungsrichtlinien

Delegieren von Erstellung, Aktualisierung und Löschung von [App-Zustimmungsrichtlinien](../manage-apps/manage-app-consent-policies.md).

- microsoft.directory/permissionGrantPolicies/create
- microsoft.directory/permissionGrantPolicies/standard/read
- microsoft.directory/permissionGrantPolicies/basic/update
- microsoft.directory/permissionGrantPolicies/delete

## <a name="full-list-of-permissions"></a>Vollständige Liste der Berechtigungen

Berechtigung | BESCHREIBUNG
---------- | -----------
microsoft.directory/servicePrincipals/managePermissionGrantsForSelf.{id} | Gewährt die Möglichkeit der Zustimmung zu Apps im eigenen Namen (Benutzerzustimmung) gemäß der App-Zustimmungsrichtlinie `{id}`.
microsoft.directory/servicePrincipals/managePermissionGrantsForAll.{id} | Gewährt die Möglichkeit der Zustimmung zu Apps in aller Namen (mandantenweite Administratorzustimmung) gemäß der App-Zustimmungsrichtlinie `{id}`.
microsoft.directory/permissionGrantPolicies/standard/read | Gewährt die Möglichkeit zum Lesen von App-Zustimmungsrichtlinien.
microsoft.directory/permissionGrantPolicies/basic/update | Gewährt die Möglichkeit zum Aktualisieren der Basiseigenschaften vorhandener App-Zustimmungsrichtlinien.
microsoft.directory/permissionGrantPolicies/create | Gewährt die Möglichkeit zum Erstellen von App-Zustimmungsrichtlinien.
microsoft.directory/permissionGrantPolicies/delete | Gewährt die Möglichkeit zum Löschen von App-Zustimmungsrichtlinien.

## <a name="next-steps"></a>Nächste Schritte

- [Erstellen und Zuweisen einer benutzerdefinierten Rolle in Azure Active Directory](custom-create.md)
- [Anzeigen der Zuweisungen für eine benutzerdefinierte Rolle](../roles/view-assignments.md)
