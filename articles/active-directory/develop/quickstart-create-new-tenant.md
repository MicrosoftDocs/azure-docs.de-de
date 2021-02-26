---
title: 'Schnellstart: Erstellen eines Azure Active Directory-Mandanten'
titleSuffix: Microsoft identity platform
description: In diesem Schnellstart erfahren Sie, wie Sie einen Azure Active Directory-Mandanten für die Entwicklung von Anwendungen erstellen, die Microsoft Identity Platform zur Authentifizierung und Autorisierung verwenden.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: quickstart
ms.date: 02/15/2021
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40, fasttrack-edit
ms.openlocfilehash: cb4995c0c71d0bcebb3cdd8ec471a92b7820cb9d
ms.sourcegitcommit: b513b0becf878eb9a1554c26da53aa48d580bb22
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100535160"
---
# <a name="quickstart-set-up-a-tenant"></a>Schnellstart: Einrichten eines Mandanten

Wen Sie Apps erstellen möchten, die Microsoft Identity Platform für die Identitäts- und Zugriffsverwaltung nutzen, benötigen Sie Zugriff auf einen *Mandanten* von Azure Active Directory (Azure AD). Der Azure AD-Mandanten dient zum Registrieren und Verwalten Ihrer Apps, zum Konfigurieren des App-Zugriffs auf Daten in Microsoft 365 und auf andere Web-APIs sowie zum Aktivieren von Features wie bedingter Zugriff.

Ein Mandant stellt eine Organisation dar. Hierbei handelt es sich um eine dedizierte Instanz von Azure AD, die eine Organisation oder ein App-Entwickler zu Beginn einer Beziehung mit Microsoft erhält. Diese Beziehung kann beispielsweise mit der Registrierung für Azure, Microsoft Intune oder Microsoft 365 beginnen.

Jeder Azure AD-Mandant ist eindeutig und von anderen Azure AD-Mandanten getrennt. Er verfügt über eine eigene Darstellung von Geschäfts-, Schul- oder Uniidentitäten, Consumeridentitäten (wenn es sich um einen Azure AD B2C-Mandanten handelt) und App-Registrierungen. Eine App-Registrierung in Ihrem Mandanten kann Authentifizierungen nur von Konten in Ihrem Mandanten oder aber von allen Mandanten zulassen.

## <a name="prerequisites"></a>Voraussetzungen

Ein Azure-Konto mit einem aktiven Abonnement. Sie können [kostenlos ein Konto erstellen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="determining-the-environment-type"></a>Bestimmen des Umgebungstyps

Sie können zwei Arten von Umgebungen erstellen. Die Umgebung hängt ausschließlich von den Benutzertypen ab, die von Ihrer App authentifiziert werden. 

In dieser Schnellstartanleitung werden zwei Szenarien für die Art von App behandelt, die Sie erstellen möchten:

* Geschäfts-, Schul- oder Unikonten (Azure AD-Konten) oder Microsoft-Konten (etwa „Outlook.com“ oder „Live.com“)
* Social Media-Konten oder lokale Konten (Azure AD B2C)

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Geschäfts-, Schul- und Unikonten oder persönliche Microsoft-Konten

Wenn Sie eine Umgebung für Geschäfts-, Schul- oder Unikonten oder für persönliche Microsoft-Konten erstellen möchten, können Sie einen vorhandenen Azure AD-Mandanten verwenden oder einen neuen Mandanten erstellen.
### <a name="use-an-existing-azure-ad-tenant"></a>Verwenden eines vorhandenen Azure AD-Mandanten

Viele Entwickler verfügen bereits aufgrund von Diensten oder Abonnements, die mit Azure AD-Mandanten verknüpft sind (beispielsweise Microsoft 365- oder Azure-Abonnements), über Mandanten.

So überprüfen Sie den Mandanten:

1. Melden Sie sich beim <a href="https://portal.azure.com/" target="_blank">Azure-Portal</a> an. Verwenden Sie das Konto, mit dem Sie auch Ihre Anwendung verwalten.
1. Sehen Sie sich die rechte obere Ecke an. Wenn Sie über einen Mandanten verfügen, werden Sie automatisch angemeldet. Der Mandantenname wird direkt unter Ihrem Kontonamen angezeigt.
   * Zeigen Sie auf Ihren Kontonamen, um Ihren Namen, Ihre E-Mail-Adresse, Ihre Verzeichnis- oder Mandanten-ID (GUID) und Ihre Domäne anzuzeigen.
   * Falls Ihr Konto mehreren Mandanten zugeordnet ist, können Sie durch Klicken auf Ihren Kontonamen ein Menü öffnen, über das Sie zwischen Mandanten wechseln können. Jeder Mandant besitzt eine eigene Mandanten-ID.

> [!TIP]
> Die Mandanten-ID können Sie wie folgt ermitteln:
> * Zeigen Sie auf den Namen Ihres Kontos, um das Verzeichnis bzw. die Mandanten-ID zu erhalten.
> * Wählen Sie im Azure-Portal **Azure Active Directory** > **Eigenschaften** > **Mandanten-ID** aus.

Sollte Ihrem Konto kein Mandant zugeordnet sein, wird unter Ihrem Kontonamen eine GUID angezeigt. Aktionen wie etwa das Registrieren von Apps können erst nach Erstellung eines Azure AD-Mandanten ausgeführt werden.

### <a name="create-a-new-azure-ad-tenant"></a>Erstellen eines neuen Azure AD-Mandanten

Falls Sie noch nicht über einen Azure AD-Mandanten verfügen oder einen neuen Mandanten für die Entwicklung erstellen möchten, finden Sie weitere Informationen unter [Erstellen eines neuen Mandanten in Azure AD](../fundamentals/active-directory-access-create-new-tenant.md). Alternativ können Sie auch die [Verzeichniserstellung](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) im Azure-Portal verwenden. 

Für die Erstellung Ihres neuen Mandanten sind folgende Angaben erforderlich:

- **Name der Organisation**
- **Anfangsdomäne**: Diese Domäne ist Teil von „*.onmicrosoft.com“. Sie können die Domäne später noch anpassen.
- **Land oder Region**

> [!NOTE]
> Verwenden Sie bei der Benennung Ihres Mandanten alphanumerische Zeichen. Es sind keine Sonderzeichen zulässig. Der Name darf maximal 256 Zeichen lang sein.

## <a name="social-and-local-accounts"></a>Soziale und lokale Konten

Wenn Sie Apps mit Anmeldungen über Social Media-Konten und lokale Konten erstellen möchten, müssen Sie einen Azure AD B2C-Mandanten erstellen. Informationen zu den ersten Schritten finden Sie unter [Tutorial: Erstellen eines Azure Active Directory B2C-Mandanten](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Nächste Schritte

> [!div class="nextstepaction"]
> [Registrieren Sie eine App](quickstart-register-app.md), um diese in Microsoft Identity Platform zu integrieren.
