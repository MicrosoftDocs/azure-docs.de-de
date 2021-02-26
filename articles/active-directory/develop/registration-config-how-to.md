---
title: Abrufen der Endpunkte für eine App-Registrierung bei Azure AD
titleSuffix: Microsoft identity platform
description: Hier wird beschrieben, wie Sie die Authentifizierungsendpunkte für eine benutzerdefinierte Anwendung ermitteln, die Sie mit Azure AD entwickeln oder bei Azure AD registrieren.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: ryanwi
ROBOTS: NOINDEX
ms.openlocfilehash: 778523869715916bf1e4c32af59ea7a0081df91b
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/10/2021
ms.locfileid: "100103310"
---
# <a name="how-to-discover-endpoints"></a>Ermitteln von Endpunkten

Sie können die Authentifizierungsendpunkte für Ihre Anwendung im [Azure-Portal](https://portal.azure.com) ermitteln.

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an.
1. Wählen Sie **Azure Active Directory** aus.
1. Wählen Sie unter **Verwalten** die Option **App-Registrierungen** und dann **Endpunkte** im oberen Menü aus.

    Die Seite **Endpunkte** wird angezeigt, auf der die Authentifizierungsendpunkte für Ihren Mandanten aufgelistet sind.
    
    Verwenden Sie den Endpunkt, der dem Authentifizierungsprotokoll entspricht, das Sie zusammen mit der **Anwendungs-ID (Client-ID)** zum Erstellen der spezifischen Authentifizierungsanforderung für Ihre Anwendung verwenden.

**Nationale Clouds** (z. B. Azure AD China, Deutschland und US Government) verfügen über ein eigenes App-Registrierungsportal und eigene Azure AD-Authentifizierungsendpunkte. Weitere Informationen finden Sie in der [Übersicht über nationale Clouds](authentication-national-cloud.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Endpunkten in den verschiedenen Azure-Umgebungen finden Sie in der [Übersicht über nationale Clouds](authentication-national-cloud.md).
