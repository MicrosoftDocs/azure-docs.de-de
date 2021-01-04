---
title: Microsoft Graph-API
description: Die Microsoft Graph-API ist eine RESTful-Web-API, die Ihnen den Zugriff auf Microsoft Cloud-Dienstressourcen ermöglicht.
author: davidmu1
services: active-directory
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 02/13/2020
ms.author: davidmu
ms.custom: aaddev
ms.openlocfilehash: a799d7c32ad632303931adf838ca8e2e715b2adc
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860183"
---
# <a name="microsoft-graph-api"></a>Microsoft Graph-API

Die Microsoft Graph-API ist eine RESTful-Web-API, die Ihnen den Zugriff auf Microsoft Cloud-Dienstressourcen ermöglicht. Nachdem Sie Ihre App registriert und Authentifizierungstoken für einen Benutzer oder Dienst abgerufen haben, können Sie Anforderungen an die Microsoft Graph-API senden. Weitere Informationen finden Sie in der [Übersicht zu Microsoft Graph](/graph/overview).

Microsoft Graph macht Rest-APIs und Clientbibliotheken für den Zugriff auf Daten für die folgenden Microsoft 365-Dienste verfügbar:
- Microsoft 365-Dienste: Delve, Excel, Microsoft Bookings, Microsoft Teams, OneDrive, OneNote, Outlook/Exchange, Planner und SharePoint
- Enterprise Mobility + Security-Dienste: Advanced Threat Analytics, Advanced Threat Protection, Azure Active Directory, Identity Manager und Intune
- Windows 10-Dienste: Aktivitäten, Geräte, Benachrichtigungen
- Dynamics 365 Business Central

## <a name="versions"></a>Versionen

Microsoft Graph unterstützt derzeit zwei Versionen: v1.0 und Beta. Die Version v1.0 enthält allgemein verfügbare APIs. Verwenden Sie die Version v1.0 für alle Produktions-Apps. Die Betaversion enthält APIs, die sich derzeit in der Vorschau befinden. Weil wir möglicherweise wichtige Änderungen (Breaking Changes) an unseren Beta-APIs einführen, empfehlen wir, die Betaversion nur zum Testen von Apps zu verwenden, die sich in der Entwicklung befinden. Verwenden Sie Beta-APIs nicht in Ihren Produktions-Apps. Weitere Informationen finden Sie unter [Versionsverwaltung, Support und Breaking Change-Richtlinien für Microsoft Graph](/graph/versioning-and-support).

Wenn Sie mit der Verwendung der Beta-APIs beginnen möchten, lesen Sie [Microsoft Graph beta endpoint reference](/graph/api/overview?view=graph-rest-beta) (Microsoft Graph – Beta-Endpunkt-Referenz).

Wenn Sie mit der Verwendung der v1.0-APIs beginnen möchten, lesen Sie [Microsoft Graph REST API v1.0 reference](/graph/api/overview) (Microsoft Graph – REST-API v1.0-Referenz).

## <a name="get-started"></a>Erste Schritte

Zum Lesen aus einer oder Schreiben in eine Ressource, z. B. einen Benutzer oder eine E-Mail-Nachricht, erstellen Sie eine Anforderung, die folgendermaßen aussieht:

`{HTTP method} https://graph.microsoft.com/{version}/{resource}?{query-parameters}`

Weitere Informationen zu den Elementen der erstellten Anforderung finden Sie unter [Verwenden der Microsoft Graph-API](/graph/use-the-api).

Schnellstartbeispiele stehen zur Verfügung, die Ihnen zeigen sollen, wie Sie auf die Leistung der Microsoft Graph-API zugreifen können. Die verfügbaren Beispiele greifen auf zwei Dienste mit einer Authentifizierung zu: Microsoft-Konto und Outlook. Jeder Schnellstart greift auf Informationen aus den Benutzerprofilen des Microsoft-Kontos zu und zeigt Ereignisse aus dem Kalender dieser Benutzer an.
Die Schnellstarts umfassen vier Schritte:
- Auswählen Ihrer Plattform
- Abrufen Ihrer App-ID (Client-ID)
- Erstellen des Beispiels
- Anmelden und Anzeigen von Ereignissen in Ihrem Kalender

Nach Abschluss des Schnellstarts verfügen Sie über eine ausführungsbereite App. Weitere Informationen finden Sie in [Microsoft Graph-Schnellstart – Häufig gestellte Fragen (FAQ)](/graph/quick-start-faq). Erste Schritte mit den Beispielen finden Sie unter [Microsoft Graph – Schnellstart](https://developer.microsoft.com/graph/quick-start).

## <a name="tools"></a>Tools

Der Microsoft Graph-Tester ist ein webbasiertes Tool, mit dem Sie Anforderungen über die Microsoft Graph-APIs erstellen und testen können. Sie können auf den Microsoft Graph-Tester zugreifen unter: `https://developer.microsoft.com/graph/graph-explorer`.

Postman ist ein Tool, mit dem Sie Anforderungen über die Microsoft Graph-APIs ebenfalls erstellen und testen können. Sie können Postman herunterladen unter: `https://www.getpostman.com/`. Für die Interaktion mit Microsoft Graph in Postman verwenden Sie die Microsoft Graph-Sammlung in Postman. Weitere Informationen finden Sie unter [Use Postman with the Microsoft Graph API](/graph/use-postman?context=graph%2Fapi%2Fbeta&view=graph-rest-beta) (Verwenden von Postman bei der Microsoft Graph-API).
