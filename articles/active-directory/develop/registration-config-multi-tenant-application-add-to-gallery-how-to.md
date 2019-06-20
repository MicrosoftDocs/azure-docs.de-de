---
title: Hinzufügen einer mehrinstanzenfähigen Anwendung zum Azure AD-Anwendungskatalog | Microsoft-Dokumentation
description: In diesem Artikel wird erläutert, wie Sie Ihre benutzerdefiniert entwickelte mehrinstanzenfähige Anwendung im Azure AD-Anwendungskatalog listen können.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.assetid: 92c1651a-675d-42c8-b337-f78e7dbcc40d
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0434ec889a791079e2019bcb1d04f5ce0fceb731
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545557"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Hinzufügen einer mehrinstanzenfähigen Anwendung zum Azure AD-Anwendungskatalog

## <a name="what-is-the-azure-ad-application-gallery"></a>Was ist der Azure AD-Anwendungskatalog?

Azure Active Directory (Azure AD) ist ein cloudbasierter Identitätsdienst. Der [Azure AD-Anwendungskatalog](https://azure.microsoft.com/marketplace/active-directory/all/) ist der Azure Marketplace-App-Store, in dem alle Anwendungsconnectors für einmaliges Anmelden und die Benutzerbereitstellung veröffentlicht werden. Kunden, die Azure AD als Identitätsanbieter verwenden, finden hier die veröffentlichten SaaS-Anwendungsconnectors. IT-Administratoren fügen Connectors aus dem App-Katalog hinzu und konfigurieren und verwenden diese dann für einmaliges Anmelden und die Bereitstellung. Azure AD unterstützt alle wichtigen Verbundprotokolle für einmaliges Anmelden, einschließlich SAML 2.0, OpenID Connect, OAuth und WS-Fed. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Wenn Ihre Anwendung SAML oder OpenIDConnect unterstützt
Wenn Sie über eine mehrinstanzenfähige Anwendung verfügen, die im Azure AD-Anwendungskatalog gelistet werden soll, müssen Sie zunächst sicherstellen, dass Ihre Anwendung eine der folgenden Technologien zum einmaligen Anmelden unterstützt:

- **OpenID Connect:** Damit Ihre App gelistet wird, erstellen Sie die mehrinstanzenfähige Anwendung in Azure AD, und implementieren Sie das [Azure AD-Genehmigungsframework](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) für Ihre Anwendung. Senden Sie die Anmeldeanforderung an einen gemeinsamen Endpunkt, damit jeder Kunde der Anwendung zustimmen kann. Sie können den Benutzerzugriff anhand der Mandanten-ID und des im Token erhaltenen UPN des Benutzers steuern. Senden Sie die Anwendung mithilfe des Prozesses, der unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing) beschrieben wird.

- **SAML**: Wenn Ihre Anwendung SAML 2.0 unterstützt, kann die App im Katalog gelistet werden. Befolgen Sie die Anwendungen unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Wenn Ihre Anwendung keine SAML- oder OpenIDConnect-Unterstützung bietet
Anwendungen, die nicht SAML oder OpenIDConnect unterstützen, können weiterhin mittels kennwortbasierter einmaliger Anmeldung in den App-Katalog integriert werden.

Das kennwortbasierte einmalige Anmelden (auch als „Password Vaulting“ oder „Kennworttresor“ bezeichnet) ermöglicht es Ihnen, den Benutzerzugriff und die Kennwörter für Webanwendungen zu verwalten, die keinen Identitätsverbund unterstützen. Es ist auch für Szenarien nützlich, in denen mehrere Benutzer ein Konto gemeinsam verwenden müssen, wie z.B. bei den App-Konten für die sozialen Medien Ihrer Organisation. 

Wenn Sie Ihre Anwendung mit dieser Technologie listen möchten, führen Sie folgende Schritte durch:
1. Erstellen Sie eine Webanwendung mit HTML-Anmeldeseite, um das [kennwortbasierte einmalige Anmelden](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) zu konfigurieren. 
2. Senden Sie die Anfrage, wie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing) beschrieben wird.

## <a name="escalations"></a>Eskalationen

Senden Sie für Eskalationen eine E-Mail an das [Azure AD-SSO-Integrationsteam](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Wir melden uns so schnell wie möglich bei Ihnen.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie [Ihre Anwendung im Azure Active Directory-Anwendungskatalog listen können](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).
