---
title: Hilfethemen zum App-Registrierungsportal | Microsoft Docs
description: Eine Beschreibung der verschiedenen Funktionen im App-Registrierungsportal von Microsoft.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: f0507c28-9464-4d3e-bd53-de9053fd5278
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2018
ms.author: ryanwi
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ec615e1c6229539958f66d0dca15cf7eb788e597
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65546092"
---
# <a name="app-registration-reference"></a>Referenz zur App-Registrierung
Dieses Dokument enthält Kontextinformationen und Beschreibungen zu verschiedenen Features im [App-Registrierungsportal](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/).

> [!NOTE]
> Die Registrierung und Verwaltung von konvergenten und Azure AD-Anwendungen im [Anwendungsregistrierungsportal](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/) wird ab Mai 2019 nicht mehr unterstützt. Es wird empfohlen, die neue Oberfläche [App-Registrierungen](https://aka.ms/appregistrations) im Azure-Portal zu verwenden, um Ihre vorhandenen Anwendungen zu verwalten und neue Anwendungen zu registrieren.

## <a name="my-applications-or-converged-applications"></a>Eigene Anwendungen oder konvergente Anwendungen
Diese Liste enthält all Ihre Anwendungen, die für die Verwendung mit dem Azure AD v2.0-Endpunkt registriert sind. Diese Anwendungen können Benutzer sowohl über persönliche Microsoft-Konten als auch über Geschäfts-, Schul- oder Unikonten von Azure Active Directory anmelden. Weitere Informationen zum Azure AD v2.0-Endpunkt finden Sie in der [v2.0-Übersicht](active-directory-appmodel-v2-overview.md). Diese Anwendungen können auch für die Integration in den Microsoft-Konto-Authentifizierungsendpunkt, `https://login.live.com`, verwendet werden.

## <a name="azure-ad-only-applications"></a>Nur Azure AD-Anwendungen
Diese Liste enthält all Ihre Anwendungen, die für die Verwendung mit dem Azure AD v1.0-Endpunkt registriert sind. Bei diesen Anwendungen kann die Benutzeranmeldung nur mithilfe von Geschäfts-, Schul- oder Unikonten aus Azure Active Directory erfolgen. Diese Liste enthält Anwendungen, die über den Abschnitt **App-Registrierungen** im [Azure-Portal](https://portal.azure.com) registriert wurden.

## <a name="live-sdk-applications"></a>Live SDK-Anwendungen
Diese Liste enthält all Ihre Anwendungen, die für die ausschließliche Verwendung mit dem Microsoft-Konto registriert sind. Sie können nicht mit Azure Active Directory verwendet werden. Hier finden Sie alle Anwendungen, die zuvor beim MSA-Entwicklerportal unter `https://account.live.com/developers/applications` registriert wurden. Alle Funktionen, die Sie zuvor unter `https://account.live.com/developers/applications` ausgeführt haben, können jetzt in diesem neuen Portal unter `https://apps.dev.microsoft.com` ausgeführt werden.

## <a name="application-secrets"></a>Geheime Schlüssel für Anwendungen
Geheime Schlüssel für Anwendungen sind Anmeldeinformationen, mit denen Ihre Anwendung eine zuverlässige [Clientauthentifizierung](https://tools.ietf.org/html/rfc6749#section-2.3) bei Azure AD durchführen kann. In OAuth und OpenID Connect werden Anwendungsgeheimnisse gemeinhin als `client_secret` bezeichnet. Im v2.0-Protokoll muss jede Anwendung, die ein Sicherheitstoken an einem adressierbaren Webspeicherort (nach `https` -Schema) empfängt, einen geheimen Schlüssel für Anwendungen verwenden, um sich beim Einlösen dieses Sicherheitstokens bei Azure AD zu identifizieren. Darüber hinaus wird jedem nativen Client, der auf einem Gerät Token empfängt, die Verwendung eines Anwendungsgeheimnisses zur Clientauthentifizierung untersagt. Dadurch wird die Speicherung von Geheimnissen in unsicheren Umgebungen verhindert.

Jede App kann immer nur zwei gültige Anwendungsgeheimnisse enthalten. Durch das Verwalten von zwei Geheimnissen haben Sie die Möglichkeit, in der gesamten Umgebung Ihrer Anwendung regelmäßige Schlüsselrollover durchzuführen. Nachdem Sie Ihre Anwendung vollständig zu einem neuen Geheimnis migriert haben, können Sie das alte Geheimnis löschen und ein neues bereitstellen.

Zurzeit sind nur zwei Arten von Anwendungsgeheimnissen im App-Registrierungsportal zulässig. Wenn Sie **Neues Kennwort generieren** auswählen, wird ein gemeinsames Geheimnis im entsprechenden Datenspeicher generiert und gespeichert, das Sie in Ihrer Anwendung verwenden können. Wenn Sie **Neues Schlüsselpaar generieren** auswählen, wird ein neues Paar aus öffentlichem und privatem Schlüssel erstellt, das heruntergeladen und für die Clientauthentifizierung bei Azure AD verwendet werden kann. Durch die Auswahl von **Öffentlichen Schlüssel hochladen** können Sie ein eigenes Paar aus öffentlichem und privatem Schlüssel verwenden.
Sie müssen ein Zertifikat hochladen, das einen öffentlichen Schlüssel enthält.

## <a name="profile"></a>Profil
Im Profilabschnitt des App-Registrierungsportals können Sie die Anmeldeseite für Ihre Anwendung anpassen. Zurzeit können Sie auf der Anmeldeseite das Anwendungslogo, die URL zu den Nutzungsbedingungen und die URL zur Datenschutzerklärung ändern. Das Logo muss ein transparentes Bild im Format 48 x 48 oder 50 x 50 Pixel in einer GIF-, PNG- oder JPEG-Datei von höchstens 15 KB sein. Ändern Sie die Werte nach Belieben, und sehen Sie sich das Ergebnis auf der Anmeldeseite an.

## <a name="live-sdk-support"></a>Live SDK-Unterstützung
Wenn Sie „Live SDK-Unterstützung“ aktivieren, werden alle von Ihnen erstellten geheimen Schlüssel für Anwendungen sowohl im Azure AD- als auch im Microsoft-Konto-Datenspeicher bereitgestellt. Dadurch kann Ihre Anwendung direkt in den Microsoft-Kontodienst (login.live.com) integriert werden. Wenn Sie eine App direkt über das Microsoft-Konto (und nicht über den Azure AD v2.0-Endpunkt) erstellen möchten, sollten Sie sicherstellen, dass die Live SDK-Unterstützung aktiviert ist.

Wenn Sie die Live SDK-Unterstützung deaktivieren, wird das Anwendungsgeheimnis nur in den Azure AD-Datenspeicher geschrieben. Der Azure AD-Datenspeicher umfasst Vorschriften auf Unternehmensebene, die das Einhalten bestimmter Standards wie z. B. FISMA ermöglichen. Wenn Sie die Live SDK-Unterstützung aktivieren, kann Ihre Anwendung eventuell keine Kompatibilität mit einigen dieser Standards erzielen.

Wenn Sie ausschließlich den v2.0-Endpunkt von Azure AD verwenden möchten, können Sie die Live SDK-Unterstützung problemlos deaktivieren.

