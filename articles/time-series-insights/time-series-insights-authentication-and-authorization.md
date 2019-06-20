---
title: Informationen zum Authentifizieren und Autorisieren mithilfe der API in Azure Time Series Insights | Microsoft-Dokumentation
description: In diesem Artikel wird die Konfiguration der Authentifizierung und Autorisierung für eine benutzerdefinierte Anwendung erläutert, die die Azure Time Series Insights-API aufruft.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 9b6cd993e9f6c6dbf173c161de638c6c4a8b18d3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66237049"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Authentifizierung und Autorisierung für die Azure Time Series Insights-API

In diesem Artikel wird die Konfiguration der in einer benutzerdefinierten Anwendung verwendeten Authentifizierung und Autorisierung erläutert, die die Azure Time Series Insights-API aufruft.

> [!TIP]
> Lesen Sie Informationen zum [Gewähren von Datenzugriff](./time-series-insights-data-access.md) auf Ihre Time Series Insights-Umgebung in Azure Active Directory.

## <a name="service-principal"></a>Dienstprinzipal

In diesem Abschnitt und den folgenden Abschnitten wird erläutert, wie Sie eine Anwendung für den Zugriff auf die Azure Time Series Insights-API für die Anwendung konfigurieren. Die Anwendung kann dann mit Anwendungsanmeldeinformationen statt Benutzeranmeldeinformationen Referenzdaten in der Time Series Insights-Umgebung abfragen oder veröffentlichen.

## <a name="best-practices"></a>Bewährte Methoden

Wenn Sie eine Anwendung haben, die auf Time Series Insights zugreifen muss, ist Folgendes erforderlich:

1. Richten Sie eine Azure Active Directory-App ein.
1. [Weisen Sie Datenzugriffsrichtlinien](./time-series-insights-data-access.md) in der Time Series Insights-Umgebung zu.

Die Verwendung von Anwendungsanmeldeinformationen statt Ihrer Benutzeranmeldeinformationen ist aus folgenden Gründen wünschenswert:

* Sie können der App-Identität Berechtigungen zuweisen, die sich von Ihren eigenen Berechtigungen unterscheiden. In der Regel sind diese Berechtigungen auf diejenigen beschränkt, die für die App erforderlich sind. Beispielsweise können Sie festlegen, dass die App in einer bestimmten Time Series Insights-Umgebung Daten nur lesen darf.
* So müssen Sie die Anmeldeinformationen für die App nicht ändern, wenn sich Ihre Zuständigkeiten ändern.
* Sie können ein Zertifikat oder einen Anwendungsschlüssel verwenden, um die Authentifizierung beim Ausführen eines unbeaufsichtigten Skripts zu automatisieren.

Die folgenden Abschnitte veranschaulichen, wie diese Schritte über das Azure-Portal ausgeführt werden. Der Schwerpunkt des Artikels liegt dabei auf einer Anwendung mit nur einem Mandanten, die zur Ausführung in nur einer einzigen Organisation vorgesehen ist. Sie setzen Anwendungen mit nur einem Mandanten in der Regel für Branchenanwendungen ein, die innerhalb Ihrer Organisation ausgeführt werden.

## <a name="set-up-summary"></a>Setup – Zusammenfassung

Das Setup besteht aus drei Schritten:

1. Erstellen einer Anwendung in Azure Active Directory.
1. Autorisieren dieser Anwendung zum Zugriff auf die Time Series Insights-Umgebung.
1. Verwenden der Anwendungs-ID und des Schlüssels zum Abrufen eines Tokens aus `https://api.timeseries.azure.com/`. Mit dem Token kann dann die Time Series Insights-API aufgerufen werden.

## <a name="detailed-setup"></a>Setup – detailliert

1. Wählen Sie im Azure-Portal **Azure Active Directory** > **App-Registrierungen** > **Registrierung einer neuen Anwendung** aus.

   [![Registrierung einer neuen Anwendung in Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)](media/authentication-and-authorization/active-directory-new-application-registration.png#lightbox)

1. Benennen Sie die Anwendung, wählen Sie als Typ **Web-App/API** aus, wählen Sie einen beliebigen gültigen URI unter **URL für Anmeldung** aus, und klicken Sie auf **Erstellen**.

   [![Erstellen der Anwendung in Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)](media/authentication-and-authorization/active-directory-create-web-api-application.png#lightbox)

1. Wählen Sie die neu erstellte Anwendung aus, und kopieren Sie die Anwendungs-ID in einen Text-Editor.

   [![Kopieren der Anwendungs-ID](media/authentication-and-authorization/active-directory-copy-application-id.png)](media/authentication-and-authorization/active-directory-copy-application-id.png#lightbox)

1. Wählen Sie **Schlüssel** aus, geben Sie den Namen des Schlüssels ein, wählen Sie den Ablauf aus, und klicken Sie auf **Speichern**.

   [![Auswählen von Anwendungsschlüsseln](media/authentication-and-authorization/active-directory-application-keys.png)](media/authentication-and-authorization/active-directory-application-keys.png#lightbox)

   [![Eingeben des Schlüsselnamens und -ablaufs und Klicken auf „Speichern“](media/authentication-and-authorization/active-directory-application-keys-save.png)](media/authentication-and-authorization/active-directory-application-keys-save.png#lightbox)

1. Kopieren Sie den Schlüssel in einen Text-Editor.

   [![Kopieren des Anwendungsschlüssels](media/authentication-and-authorization/active-directory-copy-application-key.png)](media/authentication-and-authorization/active-directory-copy-application-key.png#lightbox)

1. Wählen Sie für die Time Series Insights-Umgebung **Datenzugriffsrichtlinien** aus, und klicken Sie auf **Hinzufügen**.

   [![Hinzufügen einer neuen Datenzugriffsrichtlinie zur Time Series Insights-Umgebung](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png#lightbox)

1. Fügen Sie im Dialogfeld **Benutzer auswählen** den Anwendungsnamen (aus **Schritt 2**) oder die Anwendungs-ID (aus **Schritt 3**) ein.

   [![Suchen einer Anwendung im Dialogfeld „Benutzer auswählen“](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png#lightbox)

1. Wählen Sie die Rolle aus (**Leser** zum Abfragen von Daten, **Mitwirkender** zum Abfragen von Daten und Ändern von Referenzdaten), und klicken Sie auf **OK**.

   [![Auswählen von „Leser“ oder „Mitwirkender“ im Dialogfeld „Rolle auswählen“](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png#lightbox)

1. Speichern Sie die Richtlinie durch Klicken auf **OK**.

1. Verwenden Sie die Anwendungs-ID (aus **Schritt 3**) und den Anwendungsschlüssel (aus **Schritt 5**), um das Token für die Anwendung abzurufen. Das Token kann dann im `Authorization`-Header übergeben werden, wenn die Anwendung die Time Series Insights-API aufruft.

    Wenn Sie C# verwenden, können Sie mit dem folgenden Code das Token für die Anwendung abrufen. Ein vollständiges Beispiel finden Sie unter [Abfragen von Daten aus einer Azure Time Series Insights-Umgebung mit C#](time-series-insights-query-data-csharp.md).

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/",
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "YOUR_APPLICATION_ID",
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "YOUR_CLIENT_APPLICATION_KEY"));

    string accessToken = token.AccessToken;
    ```

Verwenden Sie die **Anwendungs-ID** und den **Schlüssel** in Ihrer Anwendung für die Authentifizierung bei Azure Time Series Insight.

## <a name="next-steps"></a>Nächste Schritte

- Beispielcode, in dem die Time Series Insights-API aufgerufen wird, finden Sie unter [Abfragen von Daten aus einer Azure Time Series Insights-Umgebung mit C#](time-series-insights-query-data-csharp.md).

- API-Referenzinformationen finden Sie in der Referenz zur [Abfrage-API](/rest/api/time-series-insights/ga-query-api).

- Informieren Sie sich, wie Sie [einen Dienstprinzipal erstellen](../active-directory/develop/howto-create-service-principal-portal.md).
