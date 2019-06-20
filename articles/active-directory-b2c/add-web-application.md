---
title: Hinzufügen einer Webanwendung – Azure Active Directory B2C | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Ihrem Active Directory B2C-Mandanten eine Webanwendung hinzufügen.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: a1d098550c6fb733e088f8ad211d29f48f55d2d6
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66511707"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Hinzufügen einer Web-API-Anwendung zu Ihrem Azure Active Directory B2C-Mandanten

 Web-API-Ressourcen müssen bei Ihrem Mandanten registriert werden, damit sie auf Anforderungen Clientanwendungen reagieren können, die ein Zugriffstoken bereitstellen. In diesem Artikel wird das Registrieren einer Anwendung in Azure Active Directory (Azure AD) B2C veranschaulicht.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Stellen Sie sicher, dass Sie das Verzeichnis verwenden, das Ihren Azure AD B2C-Mandanten enthält. Klicken Sie im oberen Menü auf **Verzeichnis- und Abonnementfilter**, und wählen Sie das Verzeichnis aus, das Ihren Mandanten enthält.
3. Wählen Sie links oben im Azure-Portal die Option **Alle Dienste** aus, suchen Sie nach **Azure AD B2C**, und wählen Sie dann diese Option aus.
4. Wählen Sie **Anwendungen** und dann **Hinzufügen** aus.
5. Geben Sie einen Namen für die Anwendung ein. Beispiel: *webapi1*.
6. Wählen Sie für **Web-App/Web-API einschließen** und n**Impliziten Ablauf zulassen** die Option **Ja**.
7. Geben Sie für **Antwort-URLs** einen Endpunkt ein, an den Azure AD B2C von Ihrer App angeforderte Token zurückgibt. In Ihrer Produktionsanwendung können Sie die Antwort-URL auf einen Wert wie `https://localhost:44332` festlegen. Zu Testzwecken legen Sie die Antwort-URL auf `https://jwt.ms` fest.
8. Geben Sie für **App-ID-URI** den für Ihre Web-API verwendeten Bezeichner ein. Der vollständige Bezeichner-URI (einschließlich der Domäne) wird für Sie generiert. Beispiel: `https://contosotenant.onmicrosoft.com/api`.
9. Klicken Sie auf **Create**.
10. Notieren Sie sich auf der Eigenschaftenseite die Anwendungs-ID, die Sie beim Konfigurieren der Webanwendung verwenden.

## <a name="configure-scopes"></a>Konfigurieren von Bereichen

Bereiche ermöglichen die Steuerung des Zugriffs auf geschützte Ressourcen. Bereiche werden von der Web-API verwendet, um eine bereichsbasierte Zugriffssteuerung zu implementieren. Benutzer der Web-API können beispielsweise über Lese- und Schreibzugriff oder nur über Lesezugriff verfügen. In diesem Tutorial verwenden Sie Bereiche zum Definieren von Lese- und Schreibberechtigungen für die Web-API.

1. Wählen Sie **Anwendungen** und dann *webapi1* aus.
2. Wählen Sie **Veröffentlichte Bereiche** aus.
3. Geben Sie unter **Bereich** den Bereich `Read` und als Beschreibung `Read access to the application` ein.
4. Geben Sie unter **Bereich** den Bereich `Write` und als Beschreibung `Write access to the application` ein.
5. Klicken Sie auf **Speichern**.

Mit den veröffentlichten Bereichen können einer Clientanwendung Berechtigungen für die Web-API gewährt werden.

## <a name="grant-permissions"></a>Erteilen von Berechtigungen

Wenn Sie über eine Anwendung eine geschützte Web-API aufrufen möchten, müssen Sie Ihrer Anwendung Berechtigungen für die API erteilen. Unter [Tutorial: Registrieren einer Anwendung in Azure Active Directory B2C](tutorial-register-applications.md) wird eine Webanwendung in Azure AD B2C mit dem Namen *webapp1* erstellt. Sie können diese Anwendung für den Aufruf der Web-API verwenden.

1. Wählen Sie **Anwendungen** und anschließend Ihre Webanwendung aus.
2. Wählen Sie **API-Zugriff** und dann **Hinzufügen** aus.
3. Wählen Sie in der Dropdownliste **API auswählen** die Web-API *webapi1* aus.
4. Wählen Sie in der Dropdownliste **Bereiche auswählen** die Bereiche **Read** und **Write** aus, die Sie zuvor definiert haben.
5. Klicken Sie auf **OK**.

Ihre Anwendung ist für den Aufruf der geschützten Web-API registriert. Ein Benutzer authentifiziert sich mit Azure AD B2C, um die Anwendung zu verwenden. Die Anwendung bezieht eine Autorisierungsgewährung von Azure AD B2C, um auf die geschützte Web-API zuzugreifen.
