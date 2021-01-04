---
title: Registrieren der Azure Active Directory-Apps für Azure API for FHIR
description: In diesem Tutorial erfahren Sie, welche Anwendungen für Azure API for FHIR und für den FHIR Server für Azure registriert werden müssen.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 10/13/2019
ms.openlocfilehash: aa95dc5cc052fbff6c553de50f4f52dc5df850a5
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398112"
---
# <a name="register-the-azure-active-directory-apps-for-azure-api-for-fhir"></a>Registrieren der Azure Active Directory-Apps für Azure API for FHIR

Ihnen stehen verschiedene Konfigurationsoptionen zur Verfügung, wenn Sie Azure API for FHIR oder den FHIR-Server für Azure (OSS) einrichten. Für Open Source müssen Sie Ihre eigene Ressourcenanwendungsregistrierung erstellen. Für Azure API for FHIR wird diese Ressourcenanwendung automatisch erstellt.

## <a name="application-registrations"></a>Anwendungsregistrierungen

Damit eine Anwendung mit Azure AD interagieren kann, muss sie registriert werden. Im Kontext des FHIR-Servers gibt es zwei relevante Arten von Anwendungsregistrierungen:

1. Ressourcenanwendungsregistrierungen
1. Clientanwendungsregistrierungen

Bei **Ressourcenanwendungen** handelt es sich um Azure AD-Darstellungen einer durch Azure AD geschützten API oder Ressource. (In diesem Fall ist das Azure API for FHIR.) Eine Ressourcenanwendung für Azure API for FHIR wird automatisch erstellt, wenn Sie den Dienst bereitstellen. Bei Verwendung des Open-Source-Servers müssen Sie allerdings [eine Ressourcenanwendung in Azure AD registrieren](register-resource-azure-ad-client-app.md). Diese Ressourcenanwendung verfügt über einen Bezeichner-URI. Dieser URI sollte dem URI des FHIR-Servers entsprechen. Der URI muss als Zielgruppe (`Audience`) für den FHIR-Server verwendet werden. Von einer Clientanwendung kann Zugriff auf diesen FHIR-Server angefordert werden, wenn von ihr ein Token angefordert wird.

Bei *Clientanwendungen* handelt es sich um Registrierungen der Clients, von denen Token angefordert werden. Im Kontext von OAuth 2.0 wird häufig zwischen mindestens drei verschiedenen Anwendungsarten unterschieden:

1. **Vertrauliche Clients** , in Azure AD auch bekannt als Web-Apps: Vertrauliche Clients sind Anwendungen, von denen der [Autorisierungscodeflow](../active-directory/azuread-dev/v1-protocols-oauth-code.md) verwendet wird, um im Namen eines angemeldeten Benutzers mit gültigen Anmeldeinformationen ein Token zu beziehen. Sie werden als vertrauliche Clients bezeichnet, da sie ein Geheimnis speichern und dieses Geheimnis Azure AD gegenüber angeben können, wenn der Authentifizierungscode gegen ein Token getauscht wird. Da sich vertrauliche Clients mithilfe des geheimen Clientschlüssels authentifizieren können, sind sie vertrauenswürdiger als öffentliche Clients und können daher über langlebigere Token verfügen und ein Aktualisierungstoken erhalten. Ausführliche Informationen zur Registrierung eines vertraulichen Clients finden Sie [hier](register-confidential-azure-ad-client-app.md). Beachten Sie, dass die Antwort-URL registriert werden muss, unter der der Client den Autorisierungscode empfängt.
1. **Öffentliche Clients:** Diese Clients eignen sich nicht zum Speichern von Geheimnissen. In der Regel handelt es sich hierbei um Anwendungen für mobile Geräte oder um Single-Page-JavaScript-Webanwendungen, bei denen ein Geheimnis im Client von einem Benutzer entdeckt werden kann. Von öffentlichen Clients wird zwar ebenfalls der Autorisierungscodeflow verwendet, sie dürfen beim Tokenbezug jedoch kein Geheimnis angeben und verfügen ggf. über kurzlebigere Token und über kein Aktualisierungstoken. Ausführliche Informationen zur Registrierung eines öffentlichen Clients finden Sie [hier](register-public-azure-ad-client-app.md).
1. Dienstclients: Diese Clients beziehen Token mithilfe des [Clientanmeldeinformations-Flows](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md) für sich selbst (nicht im Namen eines Benutzers). In der Regel handelt es sich hierbei um Anwendungen, von denen auf nicht interaktive Weise auf den FHIR-Server zugegriffen wird. Ein Beispiel wäre etwa ein Erfassungsprozess. Bei Verwendung eines Dienstclients muss der Prozess zum Abrufen eines Tokens nicht mit einem Aufruf des Endpunkts `/authorize` gestartet werden. Ein Dienstclient kann direkt den Endpunkt `/token` kontaktieren und die Client-ID sowie den geheimen Clientschlüssel angeben, um ein Token zu beziehen. Ausführliche Informationen zur Registrierung eines Dienstclients finden Sie [hier](register-service-azure-ad-client-app.md).

## <a name="next-steps"></a>Nächste Schritte

In dieser Übersicht wurden die Arten von Anwendungsregistrierungen vorgestellt, die ggf. für die Verwendung einer FHIR-API benötigt werden.

Lesen Sie abhängig von Ihrem Setup die entsprechende Registrierungsanleitung für Ihre Anwendungen:

* [Registrieren einer Ressourcenanwendung in Azure Active Directory](register-resource-azure-ad-client-app.md)
* [Registrieren einer vertraulichen Clientanwendung in Azure Active Directory](register-confidential-azure-ad-client-app.md)
* [Registrieren einer öffentlichen Clientanwendung in Azure Active Directory](register-public-azure-ad-client-app.md)
* [Registrieren einer Dienstclientanwendung in Azure Active Directory](register-service-azure-ad-client-app.md)

Nachdem Sie Ihre Anwendungen registriert haben, können Sie Azure API for FHIR bereitstellen.

>[!div class="nextstepaction"]
>[Schnellstart: Bereitstellen von Azure API for FHIR mithilfe von PowerShell](fhir-paas-powershell-quickstart.md)