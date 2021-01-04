---
title: Hinzufügen einer mehrinstanzenfähigen App zum Azure AD-Anwendungskatalog
description: In diesem Artikel wird erläutert, wie Sie Ihre benutzerdefiniert entwickelte mehrinstanzenfähige Anwendung im Azure AD-Anwendungskatalog listen können.
services: active-directory
documentationCenter: na
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: ryanwi
ms.reviewer: jeedes
ms.openlocfilehash: 0df811f4e8b5d6a95868dfebe192e96fbfa80ed9
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96169255"
---
# <a name="add-a-multitenant-application-to-the-azure-ad-application-gallery"></a>Hinzufügen einer mehrinstanzenfähigen Anwendung zum Azure AD-Anwendungskatalog

## <a name="what-is-the-azure-ad-application-gallery"></a>Was ist der Azure AD-Anwendungskatalog?

Azure Active Directory (Azure AD) ist ein cloudbasierter Identitätsdienst. Der [Azure AD-Anwendungskatalog](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) ist der Azure Marketplace-App-Store, in dem alle Anwendungsconnectors für einmaliges Anmelden und die Benutzerbereitstellung veröffentlicht werden. Kunden, die Azure AD als Identitätsanbieter verwenden, finden hier die veröffentlichten SaaS-Anwendungsconnectors. IT-Administratoren fügen Connectors aus dem App-Katalog hinzu und konfigurieren und verwenden diese dann für einmaliges Anmelden und die Bereitstellung. Azure AD unterstützt alle wichtigen Verbundprotokolle für einmaliges Anmelden, einschließlich SAML 2.0, OpenID Connect, OAuth und WS-Fed. 

## <a name="if-your-application-supports-saml-or-openidconnect"></a>Wenn Ihre Anwendung SAML oder OpenIDConnect unterstützt
Wenn Sie über eine mehrinstanzenfähige Anwendung verfügen, die im Azure AD-Anwendungskatalog gelistet werden soll, müssen Sie zunächst sicherstellen, dass Ihre Anwendung eine der folgenden Technologien zum einmaligen Anmelden unterstützt:

- **OpenID Connect:** Damit Ihre App gelistet wird, erstellen Sie die mehrinstanzenfähige Anwendung in Azure AD, und implementieren Sie das [Azure AD-Genehmigungsframework](./consent-framework.md) für Ihre Anwendung. Senden Sie die Anmeldeanforderung an einen gemeinsamen Endpunkt, damit jeder Kunde der Anwendung zustimmen kann. Sie können den Benutzerzugriff anhand der Mandanten-ID und des im Token erhaltenen UPN des Benutzers steuern. Senden Sie die Anwendung mithilfe des Prozesses, der unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](./v2-howto-app-gallery-listing.md) beschrieben wird.

- **SAML**: Wenn Ihre Anwendung SAML 2.0 unterstützt, kann die App im Katalog gelistet werden. Befolgen Sie die Anwendungen unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](./v2-howto-app-gallery-listing.md).

## <a name="if-your-application-does-not-support-saml-or-openidconnect"></a>Wenn Ihre Anwendung keine SAML- oder OpenIDConnect-Unterstützung bietet
Anwendungen, die nicht SAML oder OpenIDConnect unterstützen, können weiterhin mittels kennwortbasierter einmaliger Anmeldung in den App-Katalog integriert werden.

Das kennwortbasierte einmalige Anmelden (auch als „Password Vaulting“ oder „Kennworttresor“ bezeichnet) ermöglicht es Ihnen, den Benutzerzugriff und die Kennwörter für Webanwendungen zu verwalten, die keinen Identitätsverbund unterstützen. Es ist auch für Szenarien nützlich, in denen mehrere Benutzer ein Konto gemeinsam verwenden müssen, wie z.B. bei den App-Konten für die sozialen Medien Ihrer Organisation. 

Wenn Sie Ihre Anwendung mit dieser Technologie listen möchten, führen Sie folgende Schritte durch:
1. Erstellen Sie eine Webanwendung mit HTML-Anmeldeseite, um das [kennwortbasierte einmalige Anmelden](../manage-apps/what-is-single-sign-on.md) zu konfigurieren. 
2. Senden Sie die Anfrage, wie unter [Listen Ihrer Anwendung im Azure Active Directory-Anwendungskatalog](./v2-howto-app-gallery-listing.md) beschrieben wird.

## <a name="escalations"></a>Eskalationen

Senden Sie für Eskalationen eine E-Mail an das [Azure AD-SSO-Integrationsteam](<mailto:SaaSApplicationIntegrations@service.microsoft.com>). Wir melden uns so schnell wie möglich bei Ihnen.

## <a name="next-steps"></a>Nächste Schritte
Erfahren Sie, wie Sie [Ihre Anwendung im Azure Active Directory-Anwendungskatalog listen können](./v2-howto-app-gallery-listing.md).