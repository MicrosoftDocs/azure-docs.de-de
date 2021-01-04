---
title: Grundlegendes zum OIDC-basierten einmaligen Anmelden (Single Sign-On, SSO) für Apps bei Azure Active Directory
description: Hier erfahren Sie Grundlegendes zum OIDC-basierten einmaligen Anmelden (Single Sign-On, SSO) für Apps bei Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 10/19/2020
ms.author: kenwith
ms.reviewer: arajpathak7
ms.custom: contperf-fy21q2
ms.openlocfilehash: d1acdc47d5a702faf7d5dbd5f2a4ea6826e97981
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97033237"
---
# <a name="understand-oidc-based-single-sign-on"></a>Grundlegendes zum OIDC-basierten einmaligen Anmelden
In der [Schnellstartserie](view-applications-portal.md) zur Anwendungsverwaltung haben Sie gelernt, wie Sie Azure AD als Identitätsanbieter (Identity Provider, IdP) für eine Anwendung verwenden. In diesem Artikel werden die Apps ausführlicher beschrieben, die den OpenID Connect-Standard zum Implementieren von einmaligem Anmelden verwenden. 

## <a name="before-you-begin"></a>Bevor Sie beginnen
Der Vorgang zum Hinzufügen einer App zu Ihrem Azure Active Directory-Mandanten ist abhängig von der Art der einmaligen Anwendung, die von der Anwendung implementiert wurde. Weitere Informationen zu den Optionen für einmaliges Anmelden, die Apps zur Verfügung stehen, die Azure AD für die Identitätsverwaltung verwenden können, finden Sie unter [Optionen für einmaliges Anmelden](sso-options.md). In diesem Artikel werden OIDC-basierte Apps behandelt.


## <a name="basic-oidc-configuration"></a>Grundlegende OIDC-Konfiguration
In der [Schnellstartserie](add-application-portal-setup-oidc-sso.md) finden Sie einen Artikel zum Konfigurieren des einmaligen Anmeldens. Darin erfahren Sie, wie Sie Ihrem Azure-Mandanten eine OIDC-basierte App hinzufügen.

Das Schöne beim Hinzufügen einer App, die den OIDC-Standard für einmaliges Anmelden verwendet, ist eine minimale Konfiguration. Hier ist ein kurzes Video, das zeigt, wie Sie Ihrem Mandanten eine OIDC-basierte App hinzufügen.

Hinzufügen einer OIDC-basierten App in Azure Active Directory

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4HoNI]

Weitere Informationen zur Benutzer- und Administratoreinwilligung finden Sie unter [Grundlegendes zur Benutzer- und Administratoreinwilligung](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Nächste Schritte

- [Schnellstartserie zur Anwendungsverwaltung](add-application-portal-setup-oidc-sso.md)
- [Optionen für einmaliges Anmelden](sso-options.md)
- [Vorgehensweise: Anmelden von Azure Active Directory-Benutzern mit dem mehrinstanzenfähigen Anwendungsmuster](../develop/howto-convert-app-to-be-multi-tenant.md)
