---
title: Aktivieren eines Microsoft AppSource- und Azure Marketplace-Eintrags mithilfe von Azure Active Directory | Azure
description: Aktivieren des Listingtyps mithilfe von Azure Active Directory im Azure Marketplace und in AppSource für Herausgeber von Apps und Diensten.
services: Azure, AppSource, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: qianw211
manager: pabutler
ms.service: marketplace
ms.topic: article
ms.date: 09/12/2018
ms.author: qianw211
ms.openlocfilehash: 981d9427dc8407c7300f01c941e39ba9723d53a8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64938086"
---
# <a name="enable-an-appsource-and-marketplace-listing-by-using-azure-active-directory"></a>Aktivieren eines AppSource- und Marketplace-Eintrags mithilfe von Azure Active Directory

 Azure Active Directory (Azure AD) ist ein Cloudidentitätsdienst, der die Authentifizierung mit einem Microsoft-Konto ermöglicht. Azure AD verwendet branchenübliche Frameworks. [Weitere Informationen zu Azure Active Directory](https://azure.microsoft.com/services/active-directory)

## <a name="azure-ad-benefits"></a>Vorteile von Azure AD

Microsoft AppSource- und Azure Marketplace-Kunden nutzen die produktinternen Umgebungen, um in den Listingkatalogen zu suchen. Diese Aktionen erfordern die Anmeldung der Kunden beim Produkt. Die Integration von Azure AD bietet folgende Vorteile:

- Schnellere Bindung und optimierte Benutzerfreundlichkeit
- Ermöglicht einmaliges Anmelden (Single Sign-On, SSO) für Millionen von Unternehmensbenutzern
- Konsistente Umgebung für die Anmeldung über Anwendungen verschiedener Partner hinweg
- Skalierbare, plattformübergreifende Authentifizierung für mobile und Cloud-Apps

## <a name="offers-that-require-azure-ad"></a>Angebote, die Azure AD erfordern

Die verschiedenen [Listenoptionen und Angebotsarten](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) für AppSource und Azure Marketplace haben unterschiedliche Anforderungen an die Implementierung von Azure AD. Details finden Sie in der folgenden Tabelle:

| **Angebotstyp**    | **Azure AD-SSO erforderlich?**  |  |   |  |
| :------------------- | :-------------------|:-------------------|:-------------------|:-------------------|
|  | Kontaktformular | Testversion | Testversion | Transaktion |
| Virtual Machine | – | Nein | Nein | Nein |
| Azure-Apps (Lösungsvorlage)  | – | – | – | – |
| Verwaltete Apps  | – | – | – | Nein |
| SaaS  | Nein | Ja | Ja | Ja |
| Container  | – | – | – | Nein |
| Beratungsdienste  | Nein | – | – | – |

Weitere Informationen zu den technischen Anforderungen für SaaS finden Sie unter [SaaS-Anwendungen: Leitfaden für die Veröffentlichung von Angeboten](https://docs.microsoft.com/azure/marketplace/marketplace-saas-applications-technical-publishing-guide).

## <a name="azure-ad-integration"></a>Azure AD-Integration

- Informationen zum Aktivieren der einmaligen Anmeldung (Single Sign-On) durch die Integration von Azure AD in Ihr Angebot finden Sie unter [Azure Active Directory für Entwickler]( https://aka.ms/aaddev).
- Weitere Informationen zur einmaligen Anmeldung mit Azure AD finden Sie unter [Was bedeuten Anwendungszugriff und einmaliges Anmelden mit Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="enable-a-trial-listing"></a>Aktivieren einer Testauflistung

Eine automatisierte Kundeneinrichtung kann die Wahrscheinlichkeit einer Konvertierung erhöhen. Wenn Ihr Kunde Ihre Testauflistung auswählt und in Ihre Testumgebung umgeleitet wird, können Sie den Kunden direkt einrichten, ohne dass zusätzliche Anmeldeverfahren erforderlich sind.

Während der Authentifizierung sendet Azure AD ein Token an Ihre App oder Ihr Angebot. Die vom Token bereitgestellten Benutzerinformationen ermöglichen die Erstellung eines Benutzerkontos in Ihrer App oder Ihrem Angebot. Weitere Informationen finden Sie unter [Beispieltoken](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims).

Wenn Sie Azure AD verwenden, um die Authentifizierung mit einem Klick in Ihrer App oder Ihrer Testauflistung zu aktivieren, erreichen Sie Folgendes:

- Sie optimieren die Benutzerfreundlichkeit zwischen Marketplace und Testauflistung.
- Auch wenn der Benutzer vom Marketplace an Ihre Domäne oder Testumgebung umgeleitet wird, hat er weiterhin das Gefühl, sich innerhalb des gleichen Produkts zu befinden.
- Da keine weiteren Anmeldeschritte erforderlich sind, sinkt die Wahrscheinlichkeit, dass Benutzer Ihr Angebot wieder verlassen.
- Senken Sie Bereitstellungshürden für die große Anzahl von Azure AD-Benutzern.

## <a name="verify-azure-ad-integration"></a>Überprüfen der Azure AD-Integration

### <a name="multitenant-solutions"></a>Lösungen für mehrere Mandanten

Mit Azure AD können Sie die folgenden Aktionen unterstützen:

- Registrieren Sie Ihre App in einer der Marketplace-Storefronts. Weitere Informationen finden Sie unter [App-Registrierung](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications) oder [AppSource-Zertifizierung](https://docs.microsoft.com/azure/active-directory/develop/active-directory-devhowto-appsource-certified).
- Aktivieren Sie das Feature für die Unterstützung der Mehrinstanzenfähigkeit in Azure AD, um für die Testversion die Nutzung per einfachem Klick zu ermöglichen.

Wenn Sie das einmalige Anmelden (Single Sign-On, SSO) im Azure AD-Verbund nicht verwenden, führen Sie diese Schritte aus:

1. Registrieren Sie Ihre App im Marketplace.
1. Richten Sie das einmalige Anmelden mit Azure AD mithilfe von [OAuth 2.0](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-oauth-code) oder [OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/active-directory-protocols-openid-connect-code) ein.
1. Aktivieren Sie das Feature für die Unterstützung der Mehrinstanzenfähigkeit in Azure AD, um für Ihre Testversion die Nutzung per einfachem Klick zu ermöglichen.

### <a name="single-tenant-solutions"></a>Lösungen mit nur einem Mandanten

Mit Azure AD können Sie eine der folgenden Aktionen unterstützen:

- Fügen Sie Gastbenutzer mithilfe von [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) zu Ihrem Verzeichnis hinzu.
- Sie können auch manuell Testversionen für Kunden einrichten, indem Sie die Veröffentlichungsoption **Kontaktformular** auswählen.
- Entwickeln einer Testversion pro Kunde
- Erstellen einer mehrinstanzenfähigen Beispieldemo-App, die SSO verwendet.

## <a name="next-steps"></a>Nächste Schritte

- Stellen Sie sicher, dass Sie für den [Azure Marketplace registriert](https://azuremarketplace.microsoft.com/sell) sind.
- Melden Sie sich beim [Cloud-Partnerportal](https://cloudpartner.azure.com/) an, um Ihr Angebot zu erstellen oder zu vervollständigen.
