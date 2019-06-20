---
title: Probleme bei der Anmeldung bei benutzerdefiniert entwickelten Anwendungen | Microsoft-Dokumentation
description: Allgemeine Fehler, die dazu führen können, dass Sie sich nicht bei einer mit Azure AD entwickelten Anwendung anmelden können
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
ms.openlocfilehash: b8ad2499aea8bf4e41ca00d6c78d76e112f0493e
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/17/2019
ms.locfileid: "65825235"
---
# <a name="problems-signing-in-to-a-custom-developed-application"></a>Probleme bei der Anmeldung bei benutzerdefiniert entwickelten Anwendungen

Es gibt mehrere Fehler, die dazu führen können, dass Sie sich nicht einer App anmelden können. Der Hauptgrund für das Auftreten dieses Problems sind falsch konfigurierte Apps.

## <a name="errors-related-to--misconfigured-apps"></a>Fehler im Zusammenhang mit falsch konfigurierten Apps

* Stellen Sie sicher, dass die Konfigurationen im Portal mit der App übereinstimmen. Vergleichen Sie insbesondere Client-/Anwendungs-ID, Antwort-URLs, Clientgeheimnisse/-schlüssel und App-ID-URI.

* Vergleichen Sie die Ressource, auf die Sie zugreifen möchten, im Code mit den konfigurierten Berechtigungen auf der Registerkarte **Erforderliche Ressourcen**, um sicherzustellen, dass Sie nur Ressourcen anfordern, die Sie konfiguriert haben.

* Weitere Informationen zu ähnlichen Fehlern oder Problemen finden Sie unter [Azure AD bei Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory).

## <a name="next-steps"></a>Nächste Schritte

[Entwicklerhandbuch zu Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)<br>

[Genehmigen und Integrieren von Apps in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications)<br>

[Genehmigen und Zuweisen von Berechtigungen für konvergierte Azure AD V2.0-Apps](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-scopes)<br>

[Azure AD bei Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory>)
