---
title: include file
description: include file
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 618efc8d2c3784a487c302661f35d5a284c68178
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99475644"
---
[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Das **Hero-Beispiel für Gruppenanrufe für iOS** von Azure Communication Services veranschaulicht, wie die Communication Services-iOS-Clientbibliothek für Anrufe verwendet werden kann, um eine Benutzeroberfläche für Gruppenanrufe mit Sprach- und Videofunktion zu erstellen. In dieser Beispiel-Schnellstartanleitung wird beschrieben, wie Sie das Beispiel einrichten und ausführen. Es ist auch eine Übersicht über das Beispiel vorhanden, um Informationen zum Kontext bereitzustellen.

## <a name="overview"></a>Übersicht

Beim Beispiel handelt es sich um eine native iOS-Anwendung, für die die iOS-Clientbibliotheken von Azure Communication Services zum Entwickeln einer Anrufumgebung mit Sprach- und Videofunktion verwendet werden. In der Anwendung wird eine serverseitige Komponente verwendet, um Zugriffstoken bereitzustellen, die dann zum Initialisieren der Azure Communication Services-Clientbibliothek genutzt werden. Informationen zum Konfigurieren dieser serverseitigen Komponente finden Sie im Tutorial [Erstellen eines vertrauenswürdigen Authentifizierungsdiensts mithilfe von Azure Functions](../../tutorials/trusted-service-tutorial.md).

Das Beispiel sieht wie folgt aus:

:::image type="content" source="../media/calling/landing-page-ios.png" alt-text="Screenshot: Landing Page der Beispielanwendung":::

Wenn Sie die Schaltfläche „Start new call“ (Neuen Anruf starten) auswählen, wird von der iOS-Anwendung ein neuer Anruf erstellt, und Sie werden als Teilnehmer hinzugefügt. Darüber hinaus ermöglicht die Anwendung es Ihnen, einem vorhandenen Azure Communication Services-Anruf beizutreten, indem Sie die ID des vorhandenen Anrufs angeben.

Nachdem Sie einem Anruf beigetreten sind, werden Sie aufgefordert, der Anwendung die Berechtigung zum Zugreifen auf Ihre Kamera und das Mikrofon zu gewähren. Sie werden außerdem aufgefordert, einen Anzeigenamen anzugeben.

:::image type="content" source="../media/calling/pre-call-ios.png" alt-text="Screenshot: Bildschirm zur Vorbereitung von Anrufen in der Beispielanwendung":::

Nachdem Sie Ihren Anzeigenamen und Ihre Geräte konfiguriert haben, können Sie dem Anruf beitreten. Der Hauptbereich für Anrufe, der die zentrale Anrufbenutzeroberfläche enthält, wird angezeigt.

:::image type="content" source="../media/calling/main-app-ios.png" alt-text="Screenshot: Hauptbildschirm der Beispielanwendung":::

Komponenten des Hauptbildschirms für Anrufe:

- **Medienkatalog**: Der Hauptbereich, in dem die Teilnehmer angezeigt werden. Wenn ein Teilnehmer seine Kamera aktiviert hat, wird der Videofeed hier angezeigt. Jeder Teilnehmer verfügt über eine individuelle Kachel, auf der sein Anzeigename und der Videostream (falls vorhanden) angezeigt werden. Für den Medienkatalog wird die Anzeige von mehreren Teilnehmern unterstützt, und die Anzeige wird entsprechend aktualisiert, wenn Teilnehmer für den Anruf hinzugefügt oder entfernt werden.
- **Aktionsleiste**: Diese Leiste enthält die wichtigsten Anrufsteuerelemente. Mit diesen Steuerelementen können Sie Kamera und Mikrofon ein- und ausschalten, Ihren Bildschirm freigeben und den Anruf verlassen.

Unten sind weitere Informationen zu den Voraussetzungen und Schritten zum Einrichten des Beispiels angegeben.

## <a name="prerequisites"></a>Voraussetzungen

- Ein Azure-Konto mit einem aktiven Abonnement. Details finden Sie unter [kostenloses Azure-Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Einen Mac mit [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) zusammen mit einem gültigen in Ihrer Keychain installierten Entwicklerzertifikat.
- Eine Azure Communication Services-Ressource. Ausführlichere Informationen hierzu finden Sie unter [Erstellen einer Azure Communication Services-Ressource](../../quickstarts/create-communication-resource.md).
- Eine Azure-Funktion mit Ausführung von [Logik eines vertrauenswürdigen Diensts](../../tutorials/trusted-service-tutorial.md) zum Abrufen von Zugriffstoken.

## <a name="running-sample-locally"></a>Lokales Ausführen des Beispiels

Das Beispiel für Gruppenanrufe kann lokal mit Xcode ausgeführt werden. Entwickler können entweder ihr physisches Gerät oder einen Emulator verwenden, um die Anwendung zu testen.

### <a name="before-running-the-sample-for-the-first-time"></a>Vor dem erstmaligen Ausführen des Beispiels

1. Installieren Sie Abhängigkeiten, indem Sie `pod install` ausführen.
2. Öffnen Sie `ACSCall.xcworkspace` in Xcode.
3. Aktualisieren Sie `AppSettings.plist`. Legen Sie den Wert für den `acsTokenFetchUrl`-Schlüssel auf die URL für Ihren Authentifizierungsendpunkt fest.

### <a name="run-sample"></a>Ausführen des Beispiels

Erstellen Sie das Beispiel in Xcode, und führen Sie es aus.

## <a name="optional-securing-an-authentication-endpoint"></a>(Optional) Schützen eines Authentifizierungsendpunkts

Zu Demonstrationszwecken wird in diesem Beispiel standardmäßig ein öffentlich zugänglicher Endpunkt verwendet, um ein Azure Communication Services-Token abzurufen. Für Produktionsszenarien empfehlen wir Ihnen, für die Bereitstellung eigener Token Ihren eigenen geschützten Endpunkt zu erstellen.

Wenn weitere Konfigurationsschritte ausgeführt werden, wird für dieses Beispiel auch die Verbindungsherstellung mit einem per **Azure Active Directory** (Azure AD) geschützten Endpunkt unterstützt. So kann erreicht werden, dass die Benutzeranmeldung für die App erzwungen wird, um ein Azure Communication Services-Token abzurufen. Sehen Sie sich die folgenden Schritte an:

1. Aktivieren Sie die Azure Active Directory-Authentifizierung in Ihrer App.  
   - [Registrieren Ihrer App unter Azure Active Directory (über die Einstellungen der iOS-/macOS-Plattform)](https://docs.microsoft.com/azure/active-directory/develop/tutorial-v2-ios) 
    - [Konfigurieren Ihrer App Service- oder Azure Functions-App zur Verwendung der Azure AD-Anmeldung](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)
2. Navigieren Sie in Azure Active Directory unter „App-Registrierungen“ zur Übersichtsseite Ihrer registrierten App. Notieren Sie sich die Werte für `Application (client) ID`, `Directory (tenant) ID` und `Application ID URI`.

:::image type="content" source="../media/calling/aad-overview.png" alt-text="Azure Active Directory-Konfiguration im Azure-Portal":::

3. Öffnen Sie `AppSettings.plist` in Xcode, und fügen Sie die folgenden Schlüsselwerte hinzu:
   - `acsTokenFetchUrl`: URL zum Anfordern des Azure Communication Services-Tokens 
   - `isAADAuthEnabled`: Boolescher Wert, mit dem angegeben wird, ob die Tokenauthentifizierung von Azure Communication Services erforderlich ist
   - `aadClientId`: Ihre Anwendungs-ID (Client)
   - `aadTenantId`: Ihre Verzeichnis-ID (Mandant)
   - `aadRedirectURI`: Der Umleitungs-URI sollte das folgende Format aufweisen: `msauth.<app_bundle_id>://auth`
   - `aadScopes`: Array mit Berechtigungsbereichen, die von Benutzern für die Autorisierung angefordert werden. Fügen Sie dem Array `<Application ID URI>/user_impersonation` hinzu, um Zugriff auf den Authentifizierungsendpunkt zu gewähren.

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn Sie ein Communication Services-Abonnement bereinigen und entfernen möchten, können Sie die Ressource oder die Ressourcengruppe löschen. Wenn Sie die Ressourcengruppe löschen, werden auch alle anderen Ressourcen gelöscht, die ihr zugeordnet sind. Weitere Informationen zum Bereinigen von Ressourcen finden Sie [hier](../../quickstarts/create-communication-resource.md#clean-up-resources).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen finden Sie in den folgenden Artikeln:

- Machen Sie sich mit der [Verwendung der Clientbibliothek für Anrufe](../../quickstarts/voice-video-calling/calling-client-samples.md) vertraut.
- Informieren Sie sich über die [Funktionsweise von Anrufen](../../concepts/voice-video-calling/about-call-types.md).

### <a name="additional-reading"></a>Zusätzliche Lektüre

- [Azure Communication GitHub](https://github.com/Azure/communication): Weitere Beispiele und Informationen finden Sie auf der offiziellen GitHub-Seite
