---
title: Problem beim Erstellen einer Anwendungsproxyanwendung | Microsoft-Dokumentation
description: Beheben von Problemen beim Erstellen von Anwendungsproxyanwendungen im Azure AD-Verwaltungsportal
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
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 104b98cba0948ec5d0896877e54eab1e7cd4049f
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825820"
---
# <a name="problem-creating-an-application-proxy-application"></a>Problem beim Erstellen einer Anwendungsproxyanwendung 

Nachfolgend sind einige der häufigen Probleme aufgeführt, die beim Erstellen einer neuen Anwendungsproxyanwendung auftreten können.

## <a name="recommended-documents"></a>Empfohlene Dokumente 

Informationen zum Erstellen einer Anwendungsproxyanwendung über das Verwaltungsportal finden Sie unter [Veröffentlichen von Anwendungen mit Azure AD-Anwendungsproxy](application-proxy-add-on-premises-application.md).

Wenn Sie die Schritte in dieser Dokumentation befolgen und beim Erstellen der Anwendung einen Fehler erhalten, finden Sie weitere Informationen und Vorschläge zur Behebung der Anwendung in den Fehlerdetails. Die meisten Fehlermeldungen enthalten eine empfohlene Problemlösung. 

## <a name="specific-things-to-check"></a>Überprüfen bestimmter Punkte

Stellen Sie Folgendes sicher, um häufige Fehler zu vermeiden:

-   Sie sind ein Administrator mit der Berechtigung zum Erstellen einer Anwendungsproxyanwendung.

-   Die interne URL ist eindeutig.

-   Die externe URL ist eindeutig.

-   Die URLs beginnen mit „http“ oder „https“ und enden mit einem „/“.

-   Die URL muss ein Domänenname, keine IP-Adresse sein.

Die Fehlermeldung sollte beim Erstellen der Anwendung in der oberen rechten Ecke angezeigt werden. Sie können auch das Benachrichtigungssymbol auswählen, um die Fehlermeldungen anzuzeigen.

   ![Benachrichtigungsaufforderung](./media/application-proxy-config-problem/error-message.png)

## <a name="next-steps"></a>Nächste Schritte
[Aktivieren des Anwendungsproxys über das Azure-Portal](application-proxy-add-on-premises-application.md)
