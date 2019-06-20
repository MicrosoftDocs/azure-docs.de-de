---
title: 'Azure AD Connect-Synchronisierung: Aktivieren des Active Directory-Papierkorbs | Microsoft-Dokumentation'
description: In diesem Thema wird die Verwendung des AD-Papierkorbfeatures für Azure AD Connect empfohlen.
services: active-directory
keywords: AD-Papierkorb, versehentliches Löschen, Quellanker
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: afec4207-74f7-4cdd-b13a-574af5223a90
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/17/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fe7d3ea7d4f6d648438efc1a484d5909ade2f23
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60382894"
---
# <a name="azure-ad-connect-sync-enable-ad-recycle-bin"></a>Azure AD Connect-Synchronisierung: Aktivieren des AD-Papierkorbs
Es wird empfohlen, den Active Directory-Papierkorb für Ihre lokalen Active Directory-Instanzen, die mit Azure AD synchronisiert werden, zu aktivieren. 

Wenn Sie versehentlich ein lokales AD-Benutzerobjekt löschen und mit dem Feature wiederherstellen, stellt Azure AD das entsprechende Azure AD-Benutzerobjekt wieder her.  Informationen zum AD-Papierkorbfeature finden Sie im Artikel [Scenario Overview for Restoring Deleted Active Directory Objects](https://technet.microsoft.com/library/dd379542.aspx) (Szenarioübersicht über das Wiederherstellen gelöschter Active Directory-Objekte).

## <a name="benefits-of-enabling-the-ad-recycle-bin"></a>Vorteile der Aktivierung des AD-Papierkorbs
Dieses Feature unterstützt Sie folgendermaßen beim Wiederherstellen von Azure AD-Benutzerobjekten:

* Wenn Sie versehentlich ein lokales AD-Benutzerobjekt löschen, wird das entsprechende Azure AD-Benutzerobjekt im nächsten Synchronisierungszyklus gelöscht. Standardmäßig bewahrt Azure AD das gelöschte Azure AD-Benutzerobjekt für 30 Tage als „vorläufig gelöscht“ auf.

* Wenn Sie den lokalen AD-Papierkorb aktiviert haben, können Sie das gelöschte lokale AD-Benutzerobjekt wiederherstellen, ohne den Quellanker-Wert ändern zu müssen. Wenn das wiederhergestellte lokale AD-Benutzerobjekt mit Azure AD synchronisiert wird, stellt Azure AD das zugehörige, vorläufig gelöschte Azure AD-Benutzerobjekt wieder her. Informationen zum Quellankerattribut finden Sie im Artikel [Azure AD Connect: Designkonzepte](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-design-concepts#sourceanchor).

* Wenn Sie das Feature für den lokalen AD-Papierkorb nicht aktiviert haben, müssen Sie möglicherweise ein AD-Benutzerobjekt erstellen, um das gelöschte Objekt zu ersetzen. Wenn der Azure AD Connect-Synchronisierungsdienst für die Verwendung von vom System generierten AD-Attributen (z.B. „ObjectGuid“) für das Quellanker-Attribut konfiguriert wurde, hat das neu erstellte AD-Benutzerobjekt nicht denselben Quellanker-Wert wie das gelöschte AD-Benutzerobjekt. Wenn das neu erstellte AD-Benutzerobjekt mit Azure AD synchronisiert wurde, erstellt Azure AD ein neues Azure AD-Benutzerobjekt, anstatt das vorläufig gelöschte Azure AD-Benutzerobjekt wiederherzustellen.

> [!NOTE]
> Standardmäßig behält Azure AD gelöschte Azure AD-Benutzerobjekte im vorläufig gelöschten Zustand für 30 Tage bei, bevor sie dauerhaft gelöscht werden. Allerdings können Administratoren das Löschen dieser Objekte beschleunigen. Sobald die Objekte dauerhaft gelöscht wurden, können sie nicht mehr wiederhergestellt werden, selbst wenn das Feature für den lokalen AD-Papierkorb aktiviert ist.

## <a name="next-steps"></a>Nächste Schritte
**Übersichtsthemen**

* [Azure AD Connect-Synchronisierung: Grundlagen und Anpassung der Synchronisierung](how-to-connect-sync-whatis.md)

* [Integrieren lokaler Identitäten in Azure Active Directory](whatis-hybrid-identity.md)
