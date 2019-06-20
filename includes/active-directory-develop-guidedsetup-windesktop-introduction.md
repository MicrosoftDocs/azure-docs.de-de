---
title: Includedatei
description: Includedatei
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: ae17ef749a353cd60227e31ba4dadf328b1dc935
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66482397"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Aufrufen der Microsoft Graph-API aus einer Windows Desktop-App

In diesem Leitfaden wird gezeigt, wie eine native Windows Desktop .NET-Anwendung (XAML) mit einem Zugriffstoken die Microsoft Graph-API aufruft. Die App kann auch auf andere APIs zugreifen, die Zugriffstoken von einem v2.0-Endpunkt für Microsoft Identity Platform für Entwickler erfordern. Diese Plattform hieß früher Azure AD.

Am Ende dieses Leitfadens kann Ihre Anwendung eine geschützte API aufrufen, die persönliche Konten (outlook.com, live.com und andere) verwendet. Die Anwendung kann auch Geschäfts-, Schul- und Unikonten aus Unternehmen oder Organisationen nutzen, die Azure Active Directory verwenden.  

> [!NOTE]
> Für diesen Leitfaden wird Visual Studio 2015 Update 3 oder Visual Studio 2019 vorausgesetzt. Sie verfügen über keine dieser Versionen? Sie können Visual Studio 2019 [hier](https://www.visualstudio.com/downloads/) kostenlos herunterladen.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Funktionsweise der über diesen Leitfaden generierten Beispiel-App

![Zeigt, wie die in diesem Tutorial generierte Beispiel-App funktioniert](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Die in diesem Leitfaden erstellte Beispielanwendung ermöglicht einer Windows Desktop-Anwendung das Abfragen der Microsoft Graph-API oder einer Web-API, die Token von einem Microsoft Identity Platform-Endpunkt akzeptiert. In diesem Szenario fügen Sie HTTP-Anforderungen ein Token über den Autorisierungsheader hinzu. Tokenabruf und -erneuerung werden von der Microsoft-Authentifizierungsbibliothek (Microsoft Authentication Library, MSAL) gehandhabt.

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Verarbeiten des Beziehens von Token für den Zugriff auf geschützte Web-APIs

Nach der Authentifizierung des Benutzers empfängt die Beispielanwendung ein Token, mit dem eine per Microsoft Identity Platform für Entwickler geschützte Microsoft Graph-API oder Web-API abgefragt werden kann.

APIs wie Microsoft Graph erfordern ein Token, um den Zugriff auf bestimmte Ressourcen zu ermöglichen. Ein Token ist beispielsweise erforderlich, um das Profil eines Benutzers zu lesen, auf den Kalender eines Benutzers zuzugreifen oder eine E-Mail zu senden. Ihre Anwendung kann unter Verwendung von MSAL ein Zugriffstoken anfordern, um auf diese Ressourcen durch Angeben von API-Bereichen zuzugreifen. Dieses Zugriffstoken wird dann dem HTTP-Autorisierungsheader für jeden Aufruf hinzugefügt, der für die geschützte Ressource erfolgt.

MSAL nimmt Ihrer Anwendung die Verwaltung der Zwischenspeicherung und Aktualisierung von Zugriffstoken ab.

## <a name="nuget-packages"></a>NuGet-Pakete

In dieser Anleitung werden die folgenden NuGet-Pakete verwendet:

|Bibliothek|BESCHREIBUNG|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Microsoft Authentication Library (MSAL.NET)|
