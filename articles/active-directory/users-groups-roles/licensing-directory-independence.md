---
title: Merkmale der Interaktion mehrerer Mandanten – Azure Active Directory | Microsoft-Dokumentation
description: Verwalten Ihrer Azure Active Directory-Mandanten, indem Sie Ihre Mandanten als vollständig unabhängige Ressourcen verstehen
services: active-tenant
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.subservice: users-groups-roles
ms.date: 01/31/2019
ms.author: curtand
ms.custom: it-pro
ms.reviewer: sumitp
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45f48b6d8ef29d14606f18d4ccee77bd742a670a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60470071"
---
# <a name="understand-how-multiple-azure-active-directory-tenants-interact"></a>Grundlegendes zum Interagieren mehrerer Azure Active Directory-Mandanten

In Azure Active Directory (Azure AD) ist jeder Mandant eine vollständig unabhängige Ressource: gleichgestellt, logisch unabhängig von anderen Mandanten, die Sie verwalten. Es gibt keine unter- und übergeordneten Beziehungen zwischen den Mandanten. Diese Unabhängigkeit zwischen den Mandanten beinhaltet Ressourcen-, Verwaltungs- und Synchronisierungsunabhängigkeit.

## <a name="resource-independence"></a>Ressourcenunabhängigkeit
* Wenn Sie eine Ressource in einem Mandanten erstellen oder löschen, hat dies keine Auswirkungen auf Ressourcen in einem anderen Mandanten. Eine teilweise geltende Ausnahme bilden externe Benutzer. 
* Wenn Sie einen Ihrer Domänennamen bei einem Mandanten verwenden, kann er bei keinem anderen Mandanten verwendet werden.

## <a name="administrative-independence"></a>Verwaltungsunabhängigkeit
Wenn ein Benutzer ohne Administratorberechtigungen aus dem Mandanten „Contoso“ einen Testmandanten „Test“ erstellt, geschieht Folgendes:

* Standardmäßig wird der Benutzer, der einen Mandanten erstellt, als externer Benutzer in diesem neuen Mandanten hinzugefügt, und ihm wird ihm die globale Administratorrolle in diesem Mandanten zugewiesen.
* Die Administratoren für den Mandanten „Contoso“ haben keine direkten Administratorberechtigungen für den Mandanten „Test“, sofern ihnen nicht ein Administrator dieses Mandanten die Berechtigungen explizit erteilt. Die Administratoren von „Contoso“ können allerdings den Zugriff auf den Mandanten „Test“ steuern, wenn sie das Benutzerkonto steuern, mit dem dieser Mandant erstellt wurde.
* Wenn Sie eine Administratorrolle für einen Benutzer in einem Mandanten hinzufügen oder entfernen, wirkt sich die Änderung nicht auf die Administratorrollen aus, über die der Benutzer in einem anderen Mandanten verfügt.

## <a name="synchronization-independence"></a>Synchronisierungsunabhängigkeit
Sie können jeden Azure AD-Mandanten unabhängig voneinander konfigurieren, damit Sie mit einer der folgenden Möglichkeiten synchronisierte Daten einer einzelnen Instanz erhalten:

* Dem Tool Azure AD Connect, um Daten mit einer AD-Gesamtstruktur zu synchronisieren
* Dem Connector des Azure Active Directory-Mandanten für Forefront Identity Manager, um Daten mit mindestens einer lokalen Gesamtstruktur und/oder anderen Datenquellen als Azure AD zu synchronisieren

## <a name="add-an-azure-ad-tenant"></a>Hinzufügen eines Azure AD-Mandanten
Um einen Azure AD-Mandanten im Azure-Portal hinzuzufügen, melden Sie sich mit einem globalen Azure AD-Administratorkonto beim [Azure-Portal](https://portal.azure.com) an, und wählen Sie auf der linken Seite **Neu** aus.

> [!NOTE]
> Im Gegensatz zu anderen Azure-Ressourcen sind Ihre Mandanten keine untergeordneten Ressourcen eines Azure-Abonnements. Wenn Ihr Azure-Abonnement gekündigt oder abgelaufen ist, können Sie weiterhin mithilfe von Azure PowerShell, Azure Graph-API oder Microsoft 365 Admin Center auf Ihre Mandantendaten zugreifen. Sie können [dem Mandanten auch ein anderes Abonnement zuordnen](../fundamentals/active-directory-how-subscriptions-associated-directory.md).
>

## <a name="next-steps"></a>Nächste Schritte
Eine grobe Übersicht über Lizenzierungsprobleme und Best Practices im Zusammenhang mit Azure AD finden Sie unter [Grundlagen der gruppenbasierten Lizenzierung in Azure Active Directory](../fundamentals/active-directory-licensing-whatis-azure-portal.md).
