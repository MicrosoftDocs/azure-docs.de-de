---
title: 'Microsoft Identity Platform: Registrieren von Daemon-Apps, die Web-APIs aufrufen | Azure'
description: Erfahren Sie, wie Sie eine Daemon-App erstellen, die Web-APIs aufruft – App-Registrierung
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/15/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 3b77cc93385efb0bbb8a9b87d29de9bad5cd4ceb
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "95996003"
---
# <a name="daemon-app-that-calls-web-apis---app-registration"></a>Daemon-App, die Web-APIs aufruft – App-Registrierung

In diesem Artikel finden Sie alle wichtigen Informationen zur Registrierung einer Daemon-App.

## <a name="supported-account-types"></a>Unterstützte Kontotypen

Daemonanwendungen können nur in Azure AD-Mandanten sinnvoll eingesetzt werden. Wenn Sie die Anwendung erstellen, müssen Sie daher eine der folgenden Optionen auswählen:

- **Nur Konten in diesem Organisationsverzeichnis**. Diese Option wird am häufigsten ausgewählt, da Daemonanwendungen in der Regel von branchenspezifischen Entwicklern geschrieben werden.
- **Konten in einem beliebigen Organisationsverzeichnis**. Wählen Sie diese Option aus, wenn Sie ein unabhängiger Softwarehersteller sind, der ein Hilfsprogramm für seine Kunden bereitstellt. Sie benötigen hierfür die Genehmigung der Mandantenadministratoren des Kunden.

## <a name="authentication---no-reply-uri-needed"></a>Authentifizierung – kein Antwort-URI erforderlich

Wenn Ihre vertrauliche Clientanwendung *nur* den Clientanmeldeinformations-Flow verwendet, muss der Antwort-URI nicht registriert werden. Er wird weder für die Konfiguration noch für die Erstellung der Anwendung verwendet. Der Fluss mit Clientanmeldeinformationen verwendet diese nicht.

## <a name="api-permissions---app-permissions-and-admin-consent"></a>API-Berechtigungen: App-Berechtigungen und Administratoreinwilligung

Eine Daemonanwendung kann nur Anwendungsberechtigungen (keine delegierten Berechtigungen) für APIs anfordern. Wählen Sie auf der Seite **API-Berechtigungen** der Anwendungsregistrierung **Berechtigung hinzufügen** und dann die API-Familie aus. Wählen Sie anschließend **Anwendungsberechtigungen** und dann Ihre Berechtigungen aus.

![App-Berechtigungen und Administratoreinwilligung](media/scenario-daemon-app/app-permissions-and-admin-consent.png)

> [!NOTE]
> Die Web-API, die Sie aufrufen möchten, muss *Anwendungsberechtigungen (App-Rollen)* definieren – keine delegierten Berechtigungen. Weitere Informationen zum Bereitstellen einer solchen API finden Sie unter [Geschützte Web-API: App-Registrierung: Aufrufen der Web-API durch Daemon-Apps](scenario-protected-web-api-app-registration.md#if-your-web-api-is-called-by-a-daemon-app).

Bei Daemonanwendungen ist vorab eine Einwilligung des Mandantenadministrators für den Aufruf der Web-API durch die Anwendung erforderlich. Diese Einwilligung wird ebenfalls auf der Seite **API-Berechtigungen** durch einen Mandantenadministrator angegeben. Dieser wählt dazu **Administratoreinwilligung erteilen *für unsere Organisation*** aus.

Wenn Sie ein unabhängiger Softwarehersteller sind, der eine mehrinstanzenfähige Anwendung erstellt, lesen Sie den Abschnitt zur [Bereitstellung bei mehrinstanzenfähigen Daemon-Apps](scenario-daemon-production.md#deployment---multitenant-daemon-apps).

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-registration-client-secrets.md)]

## <a name="next-steps"></a>Nächste Schritte

Fahren Sie mit dem nächsten Artikel in diesem Szenario fort: [App-Codekonfiguration](./scenario-daemon-app-configuration.md).
