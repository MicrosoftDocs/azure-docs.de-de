---
title: Anwendungstypen in v1.0 | Azure
description: Beschreibt die App-Typen und Szenarien, die vom Azure Active Directory v2.0-Endpunkt unterstützt werden.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ryanwi
ms.reviewer: saeeda, jmprieur, andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: efed9e35aed729c9efa39b0772b681d8c53ba7b8
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/11/2019
ms.locfileid: "65540667"
---
# <a name="application-types-in-v10"></a>Anwendungstypen in v1.0

Azure Active Directory (Azure AD) unterstützt die Authentifizierung für eine Vielzahl moderner App-Architekturen, die alle auf den branchenüblichen Standardprotokollen OAuth 2.0 oder OpenID Connect basieren.

Das folgende Diagramm veranschaulicht die Szenarien und Anwendungstypen sowie das Hinzufügen verschiedener Komponenten:

![Anwendungstypen und -szenarien](./media/authentication-scenarios/application_types_and_scenarios.png)

Azure AD unterstützt die folgenden fünf Hauptanwendungsszenarien:

- **[Single-Page-Webanwendung (SPA):](single-page-application.md)** Ein Benutzer muss sich bei einer durch Azure AD geschützten Single-Page-Webanwendung anmelden.
- **[Webbrowser zu Webanwendung:](web-app.md)** Ein Benutzer muss sich bei einer durch Azure AD geschützten Webanwendung anmelden.
- **[Native Anwendung zu Web-API:](native-app.md)** Eine native Anwendung auf einem Smartphone, Tablet oder PC muss einen Benutzer authentifizieren, um Ressourcen von einer durch Azure AD geschützten Web-API abzurufen.
- **[Webanwendung zu Web-API:](web-api.md)** Eine Webanwendung muss Ressourcen von einer durch Azure AD geschützten Web-API abrufen.
- **[Daemon- oder Serveranwendung zu Web-API:](service-to-service.md)** Eine Daemon- oder Serveranwendung ohne Webbenutzeroberfläche muss Ressourcen von einer durch Azure AD geschützten Web-API abrufen.

Folgen Sie den Links, um mehr über die einzelnen App-Typen und allgemeinen Szenarien zu erfahren, bevor Sie mit dem Code arbeiten. Dort finden Sie auch Information zu den Unterschieden, die Sie beim Schreiben einer bestimmten App, die vom v1.0- oder v2.0-Endpunkt unterstützt wird, beachten müssen.

> [!NOTE]
> Der v2.0-Endpunkt unterstützt nicht alle Szenarien und Funktionen von Azure AD. Lesen Sie die Informationen zu den [Einschränkungen des v2.0-Endpunkts](active-directory-v2-limitations.md), um herauszufinden, ob Sie den v2.0-Endpunkt verwenden sollten.

Alle der hier beschriebenen Apps und Szenarien können mit verschiedenen Programmiersprachen und Plattformen entwickelt werden. Zur Unterstützung stehen jeweils vollständige Codebeispiele in den zugehörigen Anleitungen zur Verfügung: [v1.0-Codebeispiele nach Szenario](sample-v1-code.md) und [v2.0-Codebeispiele nach Szenario](sample-v2-code.md). Sie können die Codebeispiele auch direkt aus den entsprechenden [GitHub-Beispielrepositorys](https://github.com/Azure-Samples?q=active-directory) herunterladen.

Sollte Ihre Anwendung einen bestimmten Teil oder ein Segment eines End-to-End-Szenarios benötigen, lässt sich diese Funktion in den meisten Fällen unabhängig hinzufügen. Wenn also beispielsweise Ihre systemeigene Anwendung eine Web-API aufruft, können Sie problemlos eine Webanwendung hinzufügen, die die Web-API ebenfalls aufruft.

## <a name="app-registration"></a>App-Registrierung

### <a name="registering-an-app-that-uses-the-azure-ad-v10-endpoint"></a>Registrieren einer App, die den Azure AD v1.0-Endpunkt verwendet

Jede Anwendung, deren Authentifizierung an Azure AD ausgelagert wird, muss in einem Verzeichnis registriert werden. Hierzu benötigt Azure AD Informationen zu Ihrer Anwendung – unter anderem die URL, an der sie sich befindet, die URL, an die nach der Authentifizierung Antworten gesendet werden sollen, sowie den URI zur Identifizierung Ihrer Anwendung. Diese Informationen sind aus folgenden Gründen erforderlich:

* Bei der Abwicklung von Anmeldungen oder beim Austausch von Token muss Azure AD mit der Anwendung kommunizieren. Dabei werden zwischen Azure AD und der Anwendung folgende Informationen übergeben:
  
  * **Anwendungs-ID-URI**: Der Bezeichner für eine Anwendung. Dieser Wert wird bei der Authentifizierung an Azure AD gesendet und gibt an, für welche Anwendung der Aufrufer ein Token benötigt. Der Wert ist auch im Token enthalten, um der Anwendung mitzuteilen, dass es sich bei ihr um die gewünschte Zielanwendung handelt.
  * **Antwort-URL** und **Umleitungs-URI**: Bei einer Web-API oder Webanwendung ist die Antwort-URL der Ort, an den Azure AD die Authentifizierungsantwort sendet (einschließlich eines Tokens, sofern die Authentifizierung erfolgreich war). Bei einer nativen Anwendung ist der Umleitungs-URI ein eindeutiger Bezeichner, an den Azure AD den Benutzer-Agent in einer OAuth 2.0-Anforderung umleitet.
  * **Anwendungs-ID**: Die ID für eine Anwendung. Diese ID wird bei der Registrierung der Anwendung von Azure AD generiert. Bei der Anforderung eines Autorisierungscodes oder -tokens werden die Anwendungs-ID und der Schlüssel im Rahmen der Authentifizierung an Azure AD gesendet.
  * **Schlüssel**: Der Schlüssel, der bei der Authentifizierung zusammen mit einer Anwendungs-ID an Azure AD gesendet wird, um eine Web-API aufzurufen.
* Azure AD muss überprüfen, ob die Anwendung unter anderem die erforderlichen Berechtigungen für den Zugriff auf Verzeichnisdaten und auf andere Anwendungen in Ihrer Organisation besitzt.

Nähere Informationen finden Sie im Artikel zum [Registrieren einer App](quickstart-register-app.md).

## <a name="single-tenant-and-multi-tenant-apps"></a>Apps mit nur einem Mandanten und mit mehreren Mandanten

Zum besseren Verständnis der Bereitstellung ist es hilfreich zu wissen, dass es zwei Kategorien von Anwendungen gibt, die entwickelt und in Azure AD integriert werden können:

* **Einzelinstanzanwendung**: Eine Einzelinstanzanwendung ist für die Verwendung in einer einzelnen Organisation vorgesehen. Dies sind in der Regel von einem Unternehmensentwickler erstellte Branchenanwendungen. Auf eine Einzelinstanzanwendung greifen nur Benutzer aus einem einzelnen Verzeichnis zu. Daher muss sie auch nur in einem Verzeichnis bereitgestellt werden. Diese Anwendungen werden üblicherweise von einem Entwickler in der Organisation registriert.
* **Mehrinstanzenfähige Anwendung**: Eine mehrinstanzenfähige Anwendung ist für die Verwendung in mehreren Organisationen vorgesehen. Hierbei handelt es sich in der Regel um SaaS-Anwendungen (Software as a Service), die von einem unabhängigen Softwarehersteller (Independent Software Vendor, ISV) geschrieben wurden. Mehrinstanzenfähige Anwendungen müssen in jedem Verzeichnis bereitgestellt werden, in dem sie verwendet werden. Für die Registrierung ist also jeweils die Zustimmung des Benutzers oder Administrators erforderlich. Dieser Zustimmungsprozess beginnt, wenn eine Anwendung im Verzeichnis registriert wurde und Zugriff auf die Graph-API (oder ggf. eine andere Web-API) erhält. Wenn sich ein Benutzer oder Administrator aus einer anderen Organisation für die Verwendung der Anwendung registriert, erscheint ein Dialogfeld mit den für die Anwendung erforderlichen Berechtigungen. Stimmt der Benutzer oder Administrator der Anwendung zu, erhält die Anwendung Zugriff auf die angegebenen Daten und wird schließlich im Verzeichnis des Benutzers oder Administrators registriert. Weitere Informationen finden Sie in der [Übersicht über das Consent Framework](consent-framework.md).

### <a name="additional-considerations-when-developing-single-tenant-or-multi-tenant-apps"></a>Weitere Überlegungen bei der Entwicklung von Einzelinstanz-Apps oder mehrinstanzenfähigen Apps

Bei der Entwicklung einer mehrinstanzenfähigen Anwendung müssen im Gegensatz zur Einzelinstanzanwendung einige zusätzliche Aspekte berücksichtigt werden. Ein Beispiel: Wenn Sie die Anwendung für Benutzer in mehreren Verzeichnissen verfügbar machen, müssen Sie ermitteln, in welchem Mandaten sich diese befinden. Eine Einzelinstanzanwendung muss nur im eigenen Verzeichnis nach einem Benutzer suchen. Eine mehrinstanzenfähige Anwendung muss dagegen einen bestimmten Benutzer in sämtlichen Verzeichnissen in Azure AD identifizieren. Für diese Aufgabe bietet Azure AD anstelle eines mandantenspezifischen Endpunkts einen gemeinsamen Authentifizierungsendpunkt, an den jede mehrinstanzenfähige Anwendung Anmeldeanforderungen richten kann. Dieser Endpunkt lautet für alle Verzeichnisse in Azure AD wie folgt: https://login.microsoftonline.com/common. Im Gegensatz dazu sieht ein mandantenspezifischer Endpunkt beispielsweise wie folgt aus: https://login.microsoftonline.com/contoso.onmicrosoft.com. Der gemeinsame Endpunkt muss insbesondere bei der Anwendungsentwicklung berücksichtigt werden, da Sie die Logik implementieren müssen, die die Verarbeitung mehrerer Mandanten bei der Anmeldung, Abmeldung und Tokenüberprüfung ermöglicht.

Wenn Sie gerade eine Einzelinstanzanwendung entwickeln, diese aber für mehrere Organisationen verfügbar machen möchten, können Sie die Anwendung und deren Konfiguration problemlos in Azure AD ändern, um eine mehrinstanzenfähige Anwendung zu erhalten. Darüber hinaus verwendet Azure AD für alle Token in allen Verzeichnissen den gleichen Anmeldeschlüssel. Es spielt also keine Rolle, ob Sie die Authentifizierung in einer Einzelinstanzanwendung oder in einer mehrinstanzenfähigen Anwendung bereitstellen.

Jedes Szenario aus diesem Dokument enthält einen Unterabschnitt mit den entsprechenden Bereitstellungsanforderungen. Ausführlichere Informationen zur Bereitstellung einer Anwendung in Azure AD sowie zu den Unterschieden zwischen Einzelinstanzanwendungen und mehrinstanzenfähigen Anwendungen finden Sie unter [Integrieren von Anwendungen in Azure Active Directory](quickstart-v1-integrate-apps-with-azure-ad.md). Der nächste Abschnitt erläutert gängige Anwendungsszenarien in Azure AD.

## <a name="next-steps"></a>Nächste Schritte

- Erfahren Sie mehr über weitere [Authentifizierungsszenarien](authentication-scenarios.md) für Azure AD.
