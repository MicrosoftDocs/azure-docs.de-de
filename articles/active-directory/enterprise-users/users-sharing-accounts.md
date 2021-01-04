---
title: Freigeben von Konten und Anmeldeinformationen – Azure Active Directory | Microsoft-Dokumentation
description: Beschreibt, wie es Azure Active Directory Organisationen ermöglicht, Konten für lokale Apps und für Verbraucherclouddienste auf sichere Weise gemeinsam zu nutzen.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/03/2020
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fb088d56879ebdf5d439c913ac47a701db5c4a60
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96576246"
---
# <a name="sharing-accounts-with-azure-ad"></a>Freigeben von Konten in Azure AD

## <a name="overview"></a>Übersicht

Es kann vorkommen, dass in Organisationen ein Benutzername und ein Kennwort für mehrere Personen verwendet werden müssen. Dies passiert normalerweise in den folgenden beiden Fällen:

* Beim Zugreifen auf Anwendungen, für die ein eindeutiger Benutzername und ein Kennwort für jeden Benutzer erforderlich sind (hier kann es sich um lokale Apps oder Clouddienste für Verbraucher handeln, z.B. Unternehmenskonten für Social Media).
* Beim Erstellen von Mehrbenutzerumgebungen. Beispielsweise verfügen Sie ggf. über ein einzelnes lokales Konto mit erhöhten Rechten, das für die grundlegende Einrichtung sowie für Verwaltungs- und Wiederherstellungsaktivitäten verwendet wird. Ein Beispiel hierfür ist das lokale Konto „Globaler Administrator“ für Microsoft 365 oder das Stammkonto in Salesforce.

Normalerweise werden diese Konten gemeinsam genutzt, indem die Anmeldeinformationen (Benutzername und Kennwort) an geeignete Personen verteilt oder an gemeinsam genutzten Orten aufbewahrt werden, auf die vertrauenswürdige Personen Zugriff haben.

Das herkömmliche Modell der gemeinsamen Nutzung hat mehrere Nachteile:

* Das Bereitstellen des Zugriffs auf neue Anwendungen erfordert die Verteilung von Anmeldeinformationen an jeden, der Zugriff benötigt.
* Jede freigegebene Anwendung erfordert möglicherweise einen eigenen, eindeutigen Satz gemeinsam genutzter Anmeldeinformationen, sodass die Benutzer mehrere verschiedene Anmeldeinformationen kennen müssen. Wenn Benutzer sich viele Anmeldeinformationen merken müssen, steigt das Risiko, dass auf risikoreiche Methoden zurückgegriffen wird (z.B. das Aufschreiben von Kennwörtern).
* Es ist nicht erkennbar, wer genau Zugriff auf eine Anwendung hat.
* Es ist nicht erkennbar, wer auf eine Anwendung *zugegriffen* hat.
* Wenn Sie den Zugriff auf eine Anwendung entfernen möchten, müssen die Anmeldeinformationen aktualisiert und erneut an alle Benutzer verteilt werden, die Zugriff benötigen.

## <a name="azure-active-directory-account-sharing"></a>Azure Active Directory-Kontofreigabe

Azure AD bietet einen neuen Ansatz für die Verwendung von gemeinsam genutzten Konten, der diese Nachteile beseitigt.

Der Azure AD-Administrator konfiguriert, auf welche Anwendungen ein Benutzer zugreifen kann, indem er den Zugriffsbereich verwendet und die Art von einmaligem Anmelden auswählt, die für diese Anwendung am besten geeignet ist. Einer dieser Typen, die *kennwortbasierte einmalige Anmeldung*, ermöglicht Azure AD, während des Anmeldevorgangs für die Anwendung als eine Art Zwischenhändler (Broker) aufzutreten.

Benutzer melden sich einmalig mit ihrem Organisationskonto an. Dieses Konto ist dasselbe Konto, das Benutzer regelmäßig zum Zugreifen auf den Desktop oder ihre E-Mails verwenden. Sie können nur diejenigen Anwendungen ermitteln und auf sie zugreifen, die ihnen zugewiesen wurden. Bei gemeinsam genutzten Konten kann diese Liste von Anwendungen beliebig viele gemeinsam genutzte Anmeldeinformationen enthalten. Endbenutzer müssen sich die unterschiedlichen Konten nicht mehr merken oder notieren.

Gemeinsam genutzte Konten verbessern nicht nur den Überblick, sondern auch die Sicherheit. Benutzer mit Berechtigungen zur Verwendung der Anmeldeinformationen sehen das gemeinsame Kennwort nicht, sondern erhalten vielmehr die Berechtigung, es als Teil des orchestrierten Authentifizierungsablaufs nutzen zu dürfen. Bei einigen Kennwort-SSO-Anwendungen haben Sie die Möglichkeit, Azure AD zum Durchführen eines regelmäßigen Rollovers (Aktualisierung) von Kennwörtern zu verwenden. Für das System werden lange, komplexe Kennwörter genutzt, um die Kontosicherheit zu erhöhen. Der Administrator kann Zugriffsrechte für bestimmte Anwendungen mühelos gewähren oder widerrufen. Er weiß, wer Zugriff auf das Konto hat und wer bereits darauf zugegriffen hat.

Azure AD unterstützt freigegebene Konten für alle Lizenzpläne vom Typ Enterprise Mobility Suite (EMS) oder Azure AD Premium für alle Arten des einmaligen Anmeldens per Kennwort. Sie können Konten für Tausende im Katalog befindliche, bereits integrierte Anwendungen gemeinsam nutzen und mithilfe [benutzerdefinierter SSO-Apps](../manage-apps/what-is-single-sign-on.md)eigene Anwendungen mit Kennwortauthentifizierung hinzufügen.

Zu den Azure AD-Features zur gemeinsamen Nutzung von Konten gehören folgende Funktionen:

* [Einmaliges Anmelden per Kennwort](../manage-apps/sso-options.md#password-based-sso)
* Agent für einmaliges Anmelden per Kennwort
* [Gruppenzuweisung](groups-self-service-management.md)
* Apps mit benutzerdefinierten Kennwörtern
* [Dashboard für Anwendungsnutzung/Berichte](../authentication/howto-sspr-reporting.md)
* Zugriffsportale für Endbenutzer
* [Anwendungsproxy](../manage-apps/application-proxy.md)
* [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory)

## <a name="sharing-an-account"></a>Gemeinsames Nutzen eines Kontos

Folgende Schritte sind erforderlich, um Azure AD zum Freigeben eines Kontos zu verwenden:

* Hinzufügen einer Anwendung aus dem [App-Katalog](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory) oder einer [benutzerdefinierten Anwendung](https://cloudblogs.microsoft.com/enterprisemobility/2015/06/17/bring-your-own-app-with-azure-ad-self-service-saml-configuration-now-in-preview/).
* Konfigurieren der Anwendung für einmaliges Anmelden per Kennwort (SSO)
* Verwenden einer [gruppenbasierten Zuweisung](groups-saasapps.md) mit der Option „Gemeinsam genutzte Anmeldeinformationen“

Mit Multi-Factor Authentication (MFA) lassen sich gemeinsam genutzte Konten sicherer gestalten. Lernen Sie, wie Sie [Anwendungen mit Azure AD sichern](../authentication/concept-mfa-howitworks.md) und mit der [Self-Service-Gruppenverwaltung](groups-self-service-management.md) von Azure AD die Verwaltungsrechte für den Zugriff auf Anwendungen delegieren.

## <a name="next-steps"></a>Nächste Schritte

* [Anwendungsverwaltung in Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Schützen von Apps durch bedingten Zugriff](../../active-directory-b2c/overview.md)
* [Self-Service-Gruppenverwaltung/SSAA](groups-self-service-management.md)