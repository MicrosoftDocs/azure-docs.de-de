---
title: Tutorial zum Konfigurieren von Azure Active Directory B2C mit WhoIAM
titleSuffix: Azure AD B2C
description: In diesem Tutorial erfahren Sie, wie Sie die Azure AD B2C-Authentifizierung mit WhoIAM für die Benutzerüberprüfung integrieren.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: d1b2c7513562e951e1098cf327780387ddf6a495
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94953541"
---
# <a name="tutorial-for-configuring-whoiam-with-azure-active-directory-b2c"></a>Tutorial zum Konfigurieren von WhoIAM mit Azure Active Directory B2C

In diesem Beispieltutorial erhalten Sie Anleitungen dazu, wie Sie BRIMS (Branded Identity Management System) von [WhoIAM](https://www.whoiam.ai/brims/) in Ihrer Umgebung konfigurieren und in Azure Active Directory B2C (Azure AD B2C) integrieren.

Bei BRIMS handelt es sich um eine Reihe von Apps und Diensten, die in Ihrer Umgebung bereitgestellt werden. Es ermöglicht eine Sprach-, SMS- und E-Mail-Überprüfung Ihrer Benutzerbasis. BRIMS erbeitet in Verbindung mit Ihrer vorhandenen Identitäts- und Zugriffsverwaltungslösung und ist plattformunabhängig.

## <a name="prerequisites"></a>Voraussetzungen

Zunächst benötigen Sie Folgendes:

- Ein Azure AD-Abonnement Falls Sie über kein Abonnement verfügen, können Sie ein [kostenloses Azure-Konto](https://azure.microsoft.com/free/) verwenden.

- [Einen Azure AD B2C-Mandanten](./tutorial-create-tenant.md), der mit Ihrem Azure-Abonnement verknüpft ist.

- Ein WhoIAM-[Testkonto](https://www.whoiam.ai/contact-us/).

## <a name="scenario-description"></a>Beschreibung des Szenarios

Die WhoIAM-Integration umfasst die folgenden Komponenten:

- Einen Azure AD B2C-Mandanten. Es ist Aufgabe des Autorisierungsservers, die Anmeldeinformationen des Benutzers anhand der auf ihm definierten benutzerdefinierten Richtlinien zu überprüfen. Er wird auch als Identitätsanbieter bezeichnet.

- Ein Verwaltungsportal zum Verwalten von Clients und deren Konfigurationen.

- Einen API-Dienst, der verschiedene Funktionen über Endpunkte verfügbar macht.  

- Azure Cosmos DB, das als Back-End für das BRIMS-Verwaltungsportal und den API-Dienst fungiert.

Im folgenden Architekturdiagramm ist die Implementierung dargestellt.

![Diagramm der Architektur der Azure AD B2C-Integration mit WhoIAM.](media/partner-whoiam/whoiam-architecture-diagram.png)

|Schritt | BESCHREIBUNG |
|:-----| :-----------|
| 1. | Der Benutzer gelangt auf eine Seite, auf der er die Anforderung zum Registrieren einer App oder zum Anmelden bei einer App starten kann, die Azure AD B2C als Identitätsanbieter verwendet.
| 2. | Im Rahmen der Authentifizierung fordert der Benutzer entweder eine Überprüfung des Besitzes seiner E-Mail-Adresse oder seines Telefons oder die Verwendung seiner Stimme als biometrischen Überprüfungsfaktor an.  
| 3. | Azure AD B2C ruft den BRIMS-API-Dienst auf und übergibt die E-Mail-Adresse, Telefonnummer und Sprachaufzeichnung des Benutzers.
| 4. | BRIMS verwendet vordefinierte Konfigurationen wie vollständig anpassbare E-Mail- und SMS-Vorlagen, um mit dem Benutzer in der jeweiligen Sprache und in Übereinstimmung mit dem Format der Anwendung zu interagieren.
| 5. | Nachdem die Identitätsüberprüfung eines Benutzers abgeschlossen ist, gibt BRIMS ein Token mit dem Ergebnis der Überprüfung an Azure AD B2C zurück. Azure AD B2C gewährt dem Benutzer dann entweder Zugriff auf die App oder lässt den Authentifizierungsversuch scheitern.  

## <a name="sign-up-with-whoiam"></a>Registrieren mit WhoIAM

1. Nehmen Sie Kontakt mit [WhoIAM](https://www.whoiam.ai/contact-us/) auf, und erstellen Sie ein BRIMS-Konto.

2. Verwenden Sie die verfügbaren Anweisungen für die Registrierung, und konfigurieren Sie die folgenden Azure-Dienste:

    - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/): Dient zum sicheren Speichern von Kennwörtern (z. B Kennwörter des E-Mail-Diensts).

    - [Azure App Service](https://azure.microsoft.com/services/app-service/): Dient zum Hosten der BRIMS-API und der Verwaltungsportaldienste.

    - [Azure Active Directory:](https://azure.microsoft.com/services/active-directory/) Wird zum Authentifizieren von Administratoren für das Verwaltungsportal verwendet.

    - [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/): Dient zum Speichern und Abrufen von Einstellungen.

    - [Application Insights](../azure-monitor/app/app-insights-overview.md) (optional): Wird verwendet, um sich sowohl bei der API als auch im Verwaltungsportal anzumelden.

3. Stellen Sie die BRIMS-API und das BRIMS-Verwaltungsportal in Ihrer Azure-Umgebung bereit.

4. Beispiele benutzerdefinierter Richtlinien für Azure AD B2C sind in der Dokumentation zur BRIMS-Registrierung verfügbar. Folgen Sie den Anweisungen in der Dokumentation, um Ihre App zu konfigurieren und die BRIMS-Plattform zur Überprüfung der Benutzeridentität zu nutzen.  

Weitere Informationen zu BRIMS von WhoIAM finden Sie in der [Produktdokumentation](https://www.whoiam.ai/brims/).

## <a name="test-the-user-flow"></a>Testen des Benutzerflows

1. Öffnen Sie den Azure AD B2C-Mandanten. Wählen Sie unter **Richtlinien** die Option **Identity Experience Framework** aus.

2. Wählen Sie den zuvor erstellten Flow **SignUpSignIn** aus.

3. Wählen Sie **Benutzerflow ausführen** aus, und gehen Sie dann wie folgt vor:

   a. Als **Anwendung** wählen Sie die registrierte App aus (im Beispiel JWT).

   b. Als **Antwort-URL** wählen Sie die **Umleitungs-URL** aus.

   c. Wählen Sie **Benutzerflow ausführen** aus.

4. Führen Sie den Registrierungsablauf aus, und erstellen Sie ein Konto.

5. Der BRIMS-Dienst wird während des Flows aufgerufen, nachdem das Benutzerattribut erstellt wurde. Wenn der Flow unvollständig ist, stellen Sie sicher, dass der Benutzer nicht im Verzeichnis gespeichert ist.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Richtlinien in Azure AD B2C](./custom-policy-overview.md)

- [Erste Schritte mit benutzerdefinierten Richtlinien in Azure AD B2C](./custom-policy-get-started.md?tabs=applications)