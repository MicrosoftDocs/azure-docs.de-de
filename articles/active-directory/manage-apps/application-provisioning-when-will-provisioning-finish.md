---
title: Die Benutzerbereitstellung für eine Azure AD-Kataloganwendung dauert Stunden oder länger | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie herausfinden, warum die Bereitstellung für die Anwendung möglicherweise länger als erwartet dauert.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8622a7bbd913710a2173399048baab7067d2fae7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65783793"
---
# <a name="user-provisioning-to-an-azure-ad-gallery-application-is-taking-hours-or-more"></a>Die Benutzerbereitstellung für eine Azure AD-Kataloganwendung dauert Stunden oder länger

Bei der ersten Aktivierung der automatischen Bereitstellung für eine Anwendung kann die anfängliche Synchronisierung zwischen 20 Minuten und mehreren Stunden dauern. Dies hängt von der Größe des Azure AD-Verzeichnisses und der Anzahl der Benutzer im Bereitstellungsbereich ab. 

Nachfolgende Synchronisierungen nach der anfänglichen Synchronisierung werden schneller durchgeführt, da der Bereitstellungsdienst Wasserzeichen speichert, die den Status beider Systeme nach der Erstsynchronisierung darstellen, sodass die Leistung nachfolgender Synchronisierungen verbessert wird.

## <a name="how-to-improve-provisioning-performance"></a>Verbessern der Bereitstellungsleistung

Wenn die erste Synchronisierung länger als einige Stunden dauert, haben Sie eine Möglichkeit, die Leistung zu verbessern:

-   **Benutzerbereichsfilter** Mit Bereichsfiltern können Sie die Daten, die der Bereitstellungsdienst aus Azure AD extrahiert, fein abstimmen, indem Sie Benutzer anhand bestimmter Attributwerte herausfiltern. Weitere Informationen zu Bereichsfiltern finden Sie unter [Attributbasierte Anwendungsbereitstellung mit Bereichsfiltern](https://docs.microsoft.com/azure/active-directory/active-directory-saas-scoping-filters).

## <a name="next-steps"></a>Nächste Schritte
[Automatisieren der Bereitstellung und Bereitstellungsaufhebung von Benutzern für SaaS-Anwendungen mit Azure Active Directory](user-provisioning.md)

