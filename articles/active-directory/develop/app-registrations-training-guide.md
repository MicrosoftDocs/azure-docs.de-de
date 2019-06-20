---
title: 'App-Registrierungen im Azure-Portal: Trainingshandbuch – Azure'
description: Erstellen von eingebetteten und browserlosen Authentifizierungsflows unter Verwendung der Gerätecodegewährung.
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 118c6ecb16d325a384246a0b3d9e685f6f6f04ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "64871125"
---
# <a name="training-guide-app-registrations-in-the-azure-portal"></a>Trainingshandbuch: App-Registrierungen im Azure-Portal  

In der neuen Umgebung [App-Registrierungen](https://go.microsoft.com/fwlink/?linkid=2083908) im Azure-Portal finden Sie zahlreiche Verbesserungen. Wenn Ihnen die bisherige Benutzeroberfläche eher vertraut ist, können Sie mit diesem Trainingshandbuch erste Schritte in der neuen Umgebung ausführen.

## <a name="key-changes"></a>Die wichtigsten Änderungen

- App-Registrierungen beschränken sich nicht auf **Web-App/API** oder auf eine **native** App. Sie können die gleiche App-Registrierung für all diese Anwendungen verwenden, indem Sie die entsprechenden Umleitungs-URIs registrieren.
- Die bisherige Benutzeroberfläche hat nur Apps mit Anmeldung bei Organisationskonten (Azure AD) unterstützt. Apps wurden als einzelner Mandant registriert (nur Unterstützung von Organisationskonten aus dem Verzeichnis, in dem die App registriert wurde) und konnten so geändert werden, dass sie mehrinstanzenfähig waren (mit Unterstützung von allen Organisationskonten). Die neue Umgebung ermöglicht die Registrierung von Apps, die diese beiden Optionen und sogar eine dritte Option unterstützen können: alle Organisationskonten und persönliche Microsoft-Konten.
- Die bisherige Benutzeroberfläche war nur verfügbar, wenn Sie sich im Azure-Portal mit einem Organisationskonto angemeldet hatten. Bei der neuen Umgebung können Sie persönliche Microsoft-Konten verwenden, die keinem Verzeichnis zugeordnet sind.

## <a name="list-of-applications"></a>Liste der Anwendungen

- Die neue App-Liste enthält die Anwendungen, die über die bisherige App-Registrierungsoberfläche im Azure-Portal registriert wurden (Apps mit Anmeldung bei Azure AD-Konten) sowie die Apps, die über das [App-Registrierungsportal](https://apps.dev.microsoft.com/) registriert wurden (Apps mit Anmeldung bei Azure AD und persönliche Microsoft-Konten).
- Die neue App-Liste enthält nicht mehr die Spalte **Anwendungstyp** (da eine einzelne App-Registrierung mehrere Anwendungstypen beinhalten kann), dafür aber zwei zusätzliche Spalten **Erstellt am** und **Zertifikate & Geheimnisse**, wobei letztere den Status der für die App registrierten Anmeldeinformationen anzeigt („aktuell“, „läuft bald ab“ oder „abgelaufen“).

## <a name="new-app-registration"></a>Neue App-Registrierung

In der bisherigen Umgebung mussten Sie zum Registrieren einer App Folgendes angeben: **Name**, **Anwendungstyp** und **Anmelde-URL/Umleitungs-URI**. Die erstellten Apps waren Azure AD-Anwendungen mit nur einem Mandanten. Dies bedeutete, dass sie nur Organisationskonten aus dem Verzeichnis unterstützten, in dem die App registriert wurde.

In der neuen Umgebung müssen Sie einen **Namen** für die App angeben und die **unterstützten Kontotypen** auswählen. Sie können optional einen **Umleitungs-URI** angeben. Wenn Sie einen Umleitungs-URI angeben, müssen Sie den Typ „Web“ oder „Öffentlicher Client“ definieren (Mobilgerät und Desktop). Weitere Informationen zum Registrieren einer App mithilfe der neuen App-Registrierungsumgebung finden Sie [in dieser Schnellstartanleitung](quickstart-register-app.md).

## <a name="the-legacy-properties-page"></a>Die bisherige Seite „Eigenschaften“

Die bisherige Benutzeroberfläche verfügte über die Seite **Eigenschaften**, die in der neuen Umgebung nicht mehr enthalten ist. Das Blatt **Eigenschaften** enthielt die folgenden Felder: **Name**, **Objekt-ID**, **Anwendungs-ID**, **App-ID-URI**, **Logo**, **URL der Startseite**, **Abmelde-URL**, **URL zu den Vertragsbedingungen**, **URL zur Datenschutzerklärung**, **Anwendungstyp** und **Mehrinstanzenfähig**.

In der neuen Umgebung finden Sie die jeweilige Funktionalität an folgenden Stellen:

- **Name**, **Logo**, **URL der Startseite**, **URL zu den Vertragsbedingungen** und **URL zur Datenschutzerklärung** befinden sich jetzt auf der Seite **Branding** der App.
- **Objekt-ID** und **Anwendungs-ID (Client)** befinden sich auf der Seite **Übersicht**.
- Die Funktionalität, die auf der bisherigen Benutzeroberfläche über den Schalter **Mehrinstanzenfähig** gesteuert wurde, wurde durch die Option **Unterstützte Kontotypen** ersetzt, die sich auf der Seite **Authentifizierung** befindet. Weitere Informationen zur Zuordnung der Mehrinstanzenfähigkeit zu den unterstützten Kontotypoptionen finden Sie [in dieser Schnellstartanleitung](quickstart-modify-supported-accounts.md).
- Die Option **Abmelde-URL** befindet sich jetzt auf der Seite **Authentifizierung**.
- **Anwendungstyp** ist kein gültiges Feld mehr. Stattdessen bestimmen die Umleitungs-URIs (auf der Seite **Authentifizierung**), welche App-Typen unterstützt werden.
- Die Option **App-ID-URI** wurde umbenannt in **Anwendungs-ID-URI** und befindet sich jetzt auf dem Blatt **Eine API verfügbar machen**. Auf der bisherigen Benutzeroberfläche wurde diese Eigenschaft automatisch mit folgendem Format registriert: `https://{tenantdomain}/{appID}` (z. B. `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). Im neuen Format wird sie automatisch als `api://{appID}` generiert, muss aber explizit gespeichert werden.

## <a name="reply-urlsredirect-urls"></a>Antwort-URLs/Umleitungs-URIs

Auf der bisherigen Benutzeroberfläche gab es für eine App die Seite **Antwort-URLs**. In der neuen Umgebung befinden sich die Antwort-URLs im Abschnitt **Authentifizierung** der App. Darüber hinaus werden sie als **Umleitungs-URIs** bezeichnet. Außerdem hat sich das Format der Umleitungs-URIs geändert. Sie müssen einem App-Typ („Web“ oder „Öffentlicher Client“) zugeordnet werden. Zudem werden aus Sicherheitsgründen Platzhalter und http://-Schemas nicht unterstützt (mit Ausnahme von http://localhost) ).

## <a name="keyscertificates--secrets"></a>Schlüssel/Zertifikate und Geheimnisse

Auf der bisherigen Benutzeroberfläche gab es für eine App die Seite **Schlüssel**. In der neuen Umgebung wurde daraus **Zertifikate & Geheimnisse**. Außerdem werden **Öffentliche Schlüssel** als **Zertifikate** und **Kennwörter** als **Geheime Clientschlüssel** bezeichnet.

## <a name="required-permissionsapi-permissions"></a>Erforderliche Berechtigungen/API-Berechtigungen

- Auf der bisherigen Benutzeroberfläche gab es für eine App die Seite **Erforderliche Berechtigungen**. In der neuen Umgebung wurde die Option umbenannt in **API-Berechtigungen**.
- Wenn Sie auf der bisherigen Benutzeroberfläche eine API auswählten, stand eine kleine Liste von Microsoft-APIs zur Auswahl, oder Sie konnten die Dienstprinzipale im Mandanten durchsuchen. In der neuen Umgebung stehen mehrere Registerkarten zur Auswahl: **Microsoft-APIs**, **Von meiner Organisation verwendete APIs** oder **Meine APIs**. Mit der Suchleiste auf der Registerkarte **Von meiner Organisation verwendete APIs** können Sie nach den Dienstprinzipalen im Mandanten suchen. 

   > [!NOTE]
   > Diese Registerkarte wird nicht angezeigt, wenn Ihre Anwendung keinem Mandanten zugeordnet ist. Weitere Informationen zum Anfordern von Berechtigungen in der neuen Umgebung finden Sie [in dieser Schnellstartanleitung](quickstart-configure-app-access-web-apis.md).

- Auf der bisherigen Benutzeroberfläche gab es am oberen Rand der Seite **Angeforderte Berechtigungen** die Schaltfläche **Berechtigungen erteilen**. Die neue Umgebung enthält im Abschnitt **API-Berechtigungen** für eine App den Bereich **Einwilligung erteilen** mit der Schaltfläche **Administratoreinwilligung erteilen**. Außerdem gibt es einige Unterschiede in der Funktionsweise der Schaltflächen:
   - In der bisherigen Umgebung veränderte sich die Logik in Abhängigkeit vom angemeldeten Benutzer und von den angeforderten Berechtigungen. Folgende Logik kam zum Einsatz:
      - Wenn nur benutzerzustimmungsfähige Berechtigungen angefordert wurden und der angemeldete Benutzer kein Administrator war, konnte der Benutzer die Benutzerzustimmung für die angeforderten Berechtigungen erteilen.
      - Wenn mindestens eine angeforderte Berechtigung die Zustimmung eines Administrators erforderte und der angemeldete Benutzer kein Administrator war, wurde beim Versuch, die Benutzerzustimmung zu erteilen, ein Fehler ausgelöst.
      - War der angemeldete Benutzer jedoch ein Administrator, wurde für alle angeforderten Berechtigungen Administratorzustimmung erteilt.
   - In der neuen Umgebung kann nur ein Administrator die Zustimmung erteilen. Wenn ein Administrator die Schaltfläche **Administratorzustimmung erteilen** auswählt, wird für alle angeforderten Berechtigungen Administratorzustimmung erteilt.

## <a name="deleting-an-app-registration"></a>Löschen einer App-Registrierung

In der bisherigen Umgebung konnte nur eine App mit einem Mandanten gelöscht werden. Bei mehrinstanzenfähigen Apps war die Schaltfläche „Löschen“ deaktiviert. In der neuen Umgebung können Apps in jedem Zustand gelöscht werden; die Aktion muss jedoch bestätigt werden. Weitere Informationen zum Löschen von App-Registrierungen finden Sie [in dieser Schnellstartanleitung](quickstart-remove-app.md).

## <a name="application-manifest"></a>Anwendungsmanifest

In der bisherigen und der neuen Umgebung werden im Manifest-Editor unterschiedliche Formatversionen für die JSON-Datei verwendet. Weitere Informationen finden Sie unter [Anwendungsmanifest](reference-app-manifest.md).

## <a name="new-ui"></a>Neue Benutzeroberfläche

Es gibt eine neue Benutzeroberfläche für die Eigenschaften, die bisher nur mit dem Manifest-Editor oder der API festgelegt werden konnten (oder nicht vorhanden waren).

- Die Option **Impliziter Gewährungsflow** (oauth2AllowImplicitFlow) finden Sie auf der Seite **Authentifizierung**. Im Gegensatz zur bisherigen Benutzeroberfläche können Sie **Zugriffstoken** oder **ID-Token** oder auch beide Optionen aktivieren.
- **Durch diese API definierte Bereiche** (oauth2Permissions) und **Autorisierte Clientanwendungen** (preAuthorizedApplications) können auf der Seite **Eine API verfügbar machen** konfiguriert werden. Weitere Informationen zum Konfigurieren einer App als Web-API und zum Verfügbarmachen von Berechtigungen/Bereichen finden Sie [in dieser Schnellstartanleitung](quickstart-configure-app-expose-web-apis.md).
- Die **Herausgeberdomäne** (wird Benutzern nur bei der [Zustimmungsaufforderung der App](application-consent-experience.md) angezeigt) finden Sie auf dem Blatt **Branding**. Weitere Informationen zum Konfigurieren einer Herausgeberdomäne finden Sie [in dieser Anleitung](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Einschränkungen

Für die neue Umgebung gelten die folgenden Einschränkungen:

- Die neue Umgebung ist derzeit nicht in Azure AD B2C-Mandanten verfügbar.
- Das Format des geheimen Clientschlüssels (App-Kennwörter) unterscheidet sich von dem in der bisherigen Umgebung und verursacht Probleme an der Befehlszeilenschnittstelle.
- Das Ändern des Werts für unterstützte Konten wird auf der Benutzeroberfläche nicht unterstützt. Sie müssen dazu das App-Manifest verwenden, sofern Sie nicht zwischen Azure AD mit einem Mandanten und mehreren Mandanten wechseln.
