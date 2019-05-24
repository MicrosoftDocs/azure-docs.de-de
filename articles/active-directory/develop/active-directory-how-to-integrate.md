---
title: Integration in Azure Active Directory | Microsoft-Dokumentation
description: Lernen Sie die Vorteile der Integration Ihrer Anwendung in Azure Active Directory kennen, und rufen Sie Ressourcen zu Features wie vereinfachte Anmeldung, Identity Management, Multifactor-Factor Authentication und Zugriffssteuerung ab.
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: d13bba54-96bd-4b81-bee9-c8025ffa1648
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/08/2019
ms.author: ryanwi
ms.reviewer: bryanla
ms.custom: aaddev, seoapril2019
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8804988387ba4a6cdc8dff5d93077de42ea2051
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65546192"
---
# <a name="integrating-with-azure-active-directory"></a>Integration in Azure Active Directory

[!INCLUDE [active-directory-devguide](../../../includes/active-directory-devguide.md)]

In diesem Artikel erfahren Sie mehr über die Vorteile der Integration Ihrer Anwendung in Azure Active Directory (Azure AD), und rufen Ressourcen zur Integration ab. Azure AD bietet Organisationen eine erstklassige Identitätsverwaltung für Cloudanwendungen. Die Azure AD-Integration ermöglicht Ihren Benutzern eine optimierte Anmeldungsumgebung und hilft Ihrer Anwendung bei der Einhaltung von IT-Richtlinien.

## <a name="how-to-integrate"></a>Möglichkeiten der Integration

Es gibt mehrere Möglichkeiten, Ihre Anwendung in Azure AD zu integrieren. Nutzen Sie diese den Anforderungen Ihrer Anwendung entsprechend.

### <a name="support-azure-ad-as-a-way-to-sign-in-to-your-application"></a>Unterstützen von Azure AD als Möglichkeit zur Anmeldung bei Ihrer Anwendung

**Verringern von Anmeldeaufwand und Supportkosten.**  Wenn sich Ihre Benutzer über Azure AD bei Ihrer Anwendung anmelden, müssen sie sich keinen weiteren Benutzernamen und kein weiteres Kennwort merken. Als Entwickler müssen Sie ein Kennwort weniger speichern und schützen. Allein dadurch, dass keine vergessenen Kennwörter zurückgesetzt werden müssen, kann es zu beträchtlichen Kosteneinsparungen kommen. Azure AD unterstützt das Anmelden für einige der weltweit beliebtesten Cloudanwendungen wie Office 365 und Microsoft Azure. Bei Hunderten Millionen Benutzern in Millionen Organisationen ist es wahrscheinlich, dass Ihr Benutzer bereits bei Azure AD angemeldet ist. Erfahren Sie mehr über das [Hinzufügen von Unterstützung für die Azure AD-Anmeldung](authentication-scenarios.md).

**Vereinfachen der Registrierung für Ihre Anwendung.**   Während der Registrierung für Ihre Anwendung kann Azure AD wichtige Informationen über einen Benutzer senden, sodass Sie Ihr Anmeldeformular vorab ausfüllen lassen oder vollständig entfernen können. Benutzer können sich für Ihre Anwendung mit ihrem Azure AD-Konto in einer vertrauten Genehmigungsumgebung registrieren, die derjenigen in sozialen Medien und mobilen Anwendungen ähnelt. Benutzer können sich ohne Beteiligung der IT bei einer Anwendung, die in Azure AD integriert ist, registrieren und anmelden. Erfahren Sie mehr über das [Registrieren Ihrer Anwendung für die Azure AD-Kontoanmeldung](../../app-service/configure-authentication-provider-aad.md).

### <a name="browse-for-users-manage-user-provisioning-and-control-access-to-your-application"></a>Suchen nach Benutzern, Verwalten der Benutzerbereitstellung und Steuern des Zugriffs auf Ihre Anwendung

**Suchen nach Benutzern im Verzeichnis.**   Verwenden Sie die Graph-API, um Benutzern zu helfen, nach anderen andere Personen in ihrer Organisation zu suchen, wenn diese eingeladen werden sollen oder Zugriff gewährt werden soll, anstatt von ihnen zu verlangen, E-Mail-Adressen einzugeben. Benutzer können auf einer vertrauten Adressbuchoberfläche suchen und beispielsweise Details der Organisationshierarchie anzeigen. Erfahren Sie mehr über die [Graph-API](active-directory-graph-api.md).

**Wiederverwenden von Active Directory-Gruppen und Verteilerlisten, die Ihr Kunde bereits verwaltet.**   Azure AD enthält die Gruppen, die Ihr Kunde bereits für die E-Mail-Verteilung und das Verwalten des Zugriffs verwendet. Verwenden Sie diese Gruppen mithilfe der Graph-API wieder, anstatt vom Kunden zu verlangen, eine getrennte Zusammenstellung von Gruppen in Ihrer Anwendung zu erstellen und zu nutzen. Informationen zu Gruppen können auch in Anmeldetoken an Ihre Anwendung gesendet werden. Erfahren Sie mehr über die [Graph-API](active-directory-graph-api.md).

**Verwenden von Azure AD zum Steuern des Zugriffs auf Ihre Anwendung.**  Administratoren und Besitzer von Anwendungen in Azure AD können bestimmten Benutzern und Gruppen Zugriff auf Anwendungen zuweisen. Mithilfe der Graph-API können Sie diese Liste lesen und zum Steuern der Bereitstellung bzw. der Aufhebung der Bereitstellung von Ressourcen und des Zugriffs innerhalb Ihrer Anwendung verwenden.

**Verwenden von Azure AD für die auf Rollen basierende Zugriffskontrolle.**   Administratoren und Besitzer von Anwendungen können Benutzer und Gruppen Rollen zuweisen, die Sie definieren, wenn Sie Ihre Anwendung in Azure AD registrieren. Rolleninformationen werden in Anmeldetoken an die Anwendung gesendet und können auch mithilfe der Graph-API gelesen werden. Erfahren Sie mehr über das [Verwenden von Azure AD für die Autorisierung](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles/).

### <a name="get-access-to-users-profile-calendar-email-contacts-files-and-more"></a>Anfordern des Zugriffs auf das Profil, den Kalender, E-Mails, Kontakte, Dateien und andere Informationen des Benutzers

**Azure AD ist der Autorisierungsserver für Office 365 und andere Microsoft-Unternehmensdienste.**   Wenn Sie Azure AD für die Anmeldung bei Ihrer Anwendung oder das Verknüpfen Ihrer aktuellen Benutzerkonten mit Azure AD-Benutzerkonten mithilfe von OAuth 2.0 unterstützen, können Sie einen Lese- und Schreibzugriff auf das Profil, den Kalender, Kontakte, E-Mails und Dateien und andere Informationen des Benutzers anfordern. Sie können problemlos Ereignisse in den Kalender der Benutzer schreiben und Dateien auf ihrem OneDrive lesen oder schreiben. Erfahren Sie mehr über [den Zugriff auf die Office 365-APIs](https://msdn.microsoft.com/office/office365/howto/platform-development-overview).

### <a name="promote-your-application-in-the-azure-and-office-365-marketplaces"></a>Werben für Ihre Anwendung im Azure Marketplace und Office 365 Marketplace

**Werben Sie für Ihre Anwendung bei Millionen von Organisationen, die Azure AD bereits verwenden.**   Benutzer, die diese Marketplaces durchsuchen bzw durchstöbern, nutzen bereits einen oder mehrere Clouddienste, sodass sie als potenzielle Kunden von Cloudlösungen in Frage kommen. Erfahren Sie mehr über das Werben für Ihre Anwendung in [Azure Marketplace](https://azure.microsoft.com/marketplace/partner-program/).

**Wenn sich Benutzer für Ihre Anwendung registrieren, wird sie in ihrem Azure AD-Zugriffsbereich und Office 365-App-Startfeld angezeigt.**   Benutzer können später schnell und einfach zu Ihrer Anwendung zurückkehren, was die Benutzerbindung verbessert. Erfahren Sie mehr über den [Azure AD-Zugriffsbereich](../user-help/active-directory-saas-access-panel-introduction.md).

### <a name="secure-device-to-service-and-service-to-service-communication"></a>Sichere Kommunikation zwischen Gerät und Dienst und zwischen Diensten

**Durch Verwenden von Azure AD für die Identitätsverwaltung von Diensten und Geräten verringert sich der Code, den Sie schreiben müssen, und der IT wird das Verwalten des Zugriffs ermöglicht.**   Dienste und Geräte können Token von Azure AD mithilfe von OAuth abrufen und diese Token für den Zugriff auf Web-APIs verwenden. Mithilfe von Azure AD können Sie das Schreiben von komplexem Authentifizierungscode vermeiden. Da die Identitäten der Dienste und Geräte in Azure AD gespeichert sind, kann die IT das Verwalten und Sperren von Schlüsseln zentral verwalten, sodass diese Aufgabe in Ihrer Anwendung nicht separat erfolgen muss.

## <a name="benefits-of-integration"></a>Vorteile der Integration

Die Integration in Azure AD bietet den Vorteil, dass Sie keinen zusätzlichen Code schreiben müssen.

### <a name="integration-with-enterprise-identity-management"></a>Integration der Identitätsverwaltung des Unternehmens

**Optimieren Ihrer Anwendung für die Einhaltung von IT-Richtlinien.**  Organisationen integrieren Identitätsverwaltungssysteme in Azure AD, sodass wenn eine Person eine Organisation verlässt, sie automatisch den Zugriff auf Ihre Anwendung verliert, ohne dass die IT eingreifen muss. Die IT kann verwalten, wer Zugriff auf Ihre Anwendung hat und bestimmen, welche Zugriffsrichtlinien erfüllt werden müssen, z. B. Multi-Factor Authentication. Dadurch müssen Sie weniger Code schreiben, um komplexe Unternehmensrichtlinien einzuhalten. Azure AD bietet Administratoren ein detailliertes Überwachungsprotokoll dazu, wer sich bei Ihrer Anwendung angemeldet hat, damit die IT die Nutzung nachverfolgen kann.

**Azure AD erweitert Active Directory in die Cloud, damit Ihre Anwendung in Active Directory integriert werden kann.**   Viele Organisationen auf der ganzen Welt nutzen Active Directory als primäres Anmeldungs- und Identitätsverwaltungssystem und verlangen, dass ihre Anwendungen mit AD zusammenarbeiten. Durch die Integration in Azure AD wird Ihre App in Active Directory integriert.

### <a name="advanced-security-features"></a>Erweiterte Sicherheitsfunktionen

**Multi-Factor Authentication.**   Azure AD bietet eine systemeigene Multi-Factor Authentication. IT-Administratoren können für den Zugriff auf Ihre Anwendung die Multi-Factor Authentication anfordern, damit Sie diese Unterstützung nicht selbst programmieren müssen. Erfahren Sie mehr über die [Multi-Factor Authentication](https://azure.microsoft.com/documentation/services/multi-factor-authentication/).

**Erkennung anomaler Anmeldungen.**  Azure AD verarbeitet mehr als eine Milliarde Anmeldungen pro Tag. Dabei kommen Algorithmen für maschinelles Lernen zum Erkennen verdächtiger Aktivitäten zum Einsatz, die IT-Administratoren über mögliche Probleme informieren. Durch die Unterstützung der Azure AD-Anmeldung kommt Ihre Anwendung in den Genuss dieses Schutzes. Erfahren Sie mehr über das [Anzeigen des Azure Active Directory-Zugriffsberichts](../active-directory-view-access-usage-reports.md).

**Bedingter Zugriff.**  Zusätzlich zur Multi-Factor Authentication können Administratoren anfordern, dass bestimmte Bedingungen erfüllt sein müssen, ehe sich Benutzer bei Ihrer Anwendung anmelden können. Bedingungen, die festgelegt werden können, sind u. a. der IP-Adressbereich von Clientgeräten, Mitgliedschaft in angegebenen Gruppen und den Status des Geräts, das für den Zugriff verwendet wird. Erfahren Sie mehr über den [bedingten Azure Active Directory-Zugriff](../active-directory-conditional-access-azure-portal.md).

### <a name="easy-development"></a>Einfache Entwicklung

**Protokolle gemäß Branchenstandard.**   Microsoft ist bestrebt, Branchenstandards zu unterstützen. Microsoft Identity Platform unterstützt die Branchenstandardprotokolle OAuth 2.0 und OpenID Connect 1.0. Erfahren Sie mehr über [Azure Active Directory-Authentifizierungsprotokolle](active-directory-v2-protocols.md).

**Open-Source-Bibliotheken.**   Microsoft bietet zur Beschleunigung der Entwicklung für gängige Sprachen und Plattformen vollständig unterstützte Open-Source-Bibliotheken. Der Quellcode wird unter Apache 2.0 lizenziert, und Sie sind eingeladen, sich an den Projekten zu beteiligen. Erfahren Sie mehr über [Microsoft Authentication Library (MSAL)](reference-v2-libraries.md).

### <a name="worldwide-presence-and-high-availability"></a>Weltweite Präsenz und Hochverfügbarkeit

**Azure AD ist in Datencentern auf der ganzen Welt in Betrieb bereitgestellt und wird rund um die Uhr verwaltet und überwacht.**   Azure AD ist das Identitätsverwaltungssystem für Microsoft Azure und Office 365 und in 28 Datencentern auf der ganzen Welt im Einsatz. Verzeichnisdaten werden garantiert in mindestens drei Datencenter repliziert. Globale Lastenausgleichsfunktionen stellen sicher, dass Benutzer auf die nächstgelegene Kopie von Azure AD mit ihren Daten zugreifen, und leiten Anforderungen automatisch an andere Datencenter weiter, sobald ein Problem erkannt wird.

## <a name="next-steps"></a>Nächste Schritte

[Erste Schritte mit dem Schreiben von Code](v2-overview.md#getting-started).

[Anmelden von Benutzern mithilfe von Microsoft Identity Platform](authentication-scenarios.md)

