---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit HYPR
titleSuffix: Azure AD B2C
description: Tutorial zum Konfigurieren von Azure Active Directory B2C mit HYPR für eine echte kennwortlose und sichere Kundenauthentifizierung
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/27/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: b80b1a4b3f9bcde6cf01b0e0e59425c6783bd5d9
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953762"
---
# <a name="tutorial-for-configuring-hypr-with-azure-active-directory-b2c"></a>Tutorial zum Konfigurieren von HYPR mit Azure Active Directory B2C

In diesem Beispieltutorial erhalten Sie eine Anleitung zum Konfigurieren von Azure AD B2C mit [HYPR](https://get.hypr.com). Mit Azure AD B2C als Identitätsanbieter können Sie HYPR in jede Ihrer Kundenanwendungen integrieren, um Ihren Benutzern eine echte kennwortlose Authentifizierung zu bieten. Mit HYPR werden Kennwörter durch eine Verschlüsselung mit öffentlichen Schlüsseln ersetzt. Dadurch lassen sich Betrug, Phishing und die Wiederverwendung von Anmeldeinformationen vermeiden.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- Einen [Azure AD B2C-Mandanten](./tutorial-create-tenant.md). Der Mandant ist mit Ihrem Azure-Abonnement verknüpft.

- Einen HYPR Cloud-Mandanten. Registrieren Sie sich für ein kostenloses [Demokonto](https://get.hypr.com/free-trial).

- Ein Mobilgerät des Benutzers, das mithilfe der HYPR-Rest-APIs oder des HYPR-Geräte-Managers in Ihrem HYPR-Mandanten registriert wurde. Für diese Aufgabe können Sie beispielsweise das [HYPR Java SDK](https://docs.hypr.com/integratinghypr/docs/hypr-java-web-sdk) verwenden.

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die HYRP-Integration umfasst die folgenden Komponenten:

- Azure AD B2C: Der Autorisierungsserver, der für die Überprüfung der Anmeldeinformationen des Benutzers verantwortlich ist und auch als Identitätsanbieter bezeichnet wird.

- Web- und mobile Anwendungen: Ihre mobilen oder Webanwendungen, die Sie mit HYPR und Azure AD B2C schützen möchten. HYPR stellt ein robustes mobiles SDK und auch eine mobile App bereit, die Sie auf iOS- und Android-Plattformen für eine echte kennwortlose Authentifizierung verwenden können.

- Mobile HYPR-App: Die mobile HYPR-App kann zum Ausführen dieses Beispiels verwendet werden, falls sie nicht die mobilen SDKs in Ihren eigenen mobilen Anwendungen verwenden möchten.

- HYPR-Rest-APIs: Mit den HYPR-APIs können Sie sowohl die Registrierung von Benutzergeräten als auch die Authentifizierung ausführen. Diese APIs finden Sie [hier](https://apidocs.hypr.com).

Im folgenden Architekturdiagramm ist die Implementierung dargestellt.

![Screenshot des HYPR-Architekturdiagramms](media/partner-hypr/hypr-architecture-diagram.png)

|Schritt | BESCHREIBUNG |
|:-----| :-----------|
| 1. | Der Benutzer gelangt auf eine Anmeldeseite. Der Benutzer wählt Anmeldung oder Registrierung aus und gibt den Benutzernamen auf der Seite ein.
| 2. | Die Anwendung sendet die Benutzerattribute an Azure AD B2C zum Überprüfen der Identität.
| 3. | Azure AD B2C sammelt die Benutzerattribute und sendet die Attribute an HYPR, um den Benutzer über die mobile HYPR-App zu authentifizieren.
| 4. | HYPR sendet eine Pushbenachrichtigung an das registrierte Mobilgerät des Benutzers, um eine nach FIDO (Fast Identity Online) zertifizierte Authentifizierung durchzuführen. Dabei kann es sich um einen Fingerabdruck des Benutzers, ein biometrisches Merkmal oder eine dezentrale Pin handeln.  
| 5. | Nachdem der Benutzer die Pushbenachrichtigung bestätigt hat, wird ihm anhand der Prüfergebnisse entweder der Zugriff auf die Kundenanwendung gewährt oder verweigert.

## <a name="configure-the-azure-ad-b2c-policy"></a>Konfigurieren der Azure AD B2C-Richtlinie

1. Wechseln Sie im Richtlinienordner zur [Azure AD B2C-HYPR-Richtlinie](https://github.com/HYPR-Corp-Public/Azure-AD-B2C-HYPR-Sample/tree/master/policy).

2. Folgen Sie den Anweisungen in diesem [Dokument](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack), um das [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) herunterzuladen.

3. Konfigurieren Sie die Richtlinie für den Azure AD B2C-Mandanten.

>[!NOTE]
>Aktualisieren Sie die bereitgestellten Richtlinien, sodass sie sich auf Ihren genauen Mandanten beziehen.

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Öffnen Sie den Azure AD B2C-Mandanten, und wählen Sie unter „Richtlinien“ die Option **Identity Experience Framework** aus.

2. Wählen Sie den zuvor erstellten Flow **SignUpSignIn** aus.

3. Wählen Sie **Benutzerflow ausführen** und dann die Einstellungen aus:

   a. **Anwendung:** Wählen Sie die registrierte App aus (Beispiel: JWT).

   b. **Antwort-URL:** Wählen Sie die **Umleitungs-URL** aus.

   c. Wählen Sie **Benutzerflow ausführen** aus.

4. Führen Sie den Registrierungsflow aus, und erstellen Sie ein Konto.

5. HYPR wird während des Flows aufgerufen, nachdem das Benutzerattribut erstellt wurde. Wenn der Flow unvollständig ist, stellen Sie sicher, dass der Benutzer nicht im Verzeichnis gespeichert ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)