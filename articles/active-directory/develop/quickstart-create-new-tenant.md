---
title: Erstellen eines Azure Active Directory-Mandanten | Microsoft Docs
description: Erfahren Sie, wie Sie einen Azure AD-Mandanten erstellen, der für die Registrierung und Erstellung von Anwendungen verwendet wird.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
ms.assetid: 1f4b24eb-ab4d-4baa-a717-2a0e5b8d27cd
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 02/05/2019
ms.author: ryanwi
ms.reviewer: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d7ff28d65c68c25613d1f61c00a6132ae93fdf6
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65962504"
---
# <a name="quickstart-set-up-a-dev-environment"></a>Schnellstart: Einrichten einer Entwicklungsumgebung

Die Microsoft Identity Platform ermöglicht es Entwicklern, Anwendungen für eine Vielzahl von benutzerdefinierten Microsoft 365-Umgebungen und -Identitäten zu erstellen. Um mit der Verwendung von Microsoft Identity Platform zu beginnen, müssen Sie auf eine Umgebung zugreifen, die auch als Azure AD-Mandant bezeichnet wird und die Apps registrieren und verwalten kann, Zugriff auf Microsoft 365-Daten besitzt und benutzerdefinierten bedingten Zugriff und Mandanteneinschränkungen bereitstellt.

Ein Mandant ist eine Darstellung einer Organisation. Es handelt sich um eine dedizierte Instanz von Azure AD, die ein Unternehmen oder ein App-Entwickler erhält, wenn das Unternehmen oder der App-Entwickler eine Beziehung zu Microsoft eingeht (z.B. die Registrierung für Azure, Microsoft Intune oder Microsoft 365).

Jeder Azure AD-Mandant ist getrennt und separat von anderen Azure AD-Mandanten und verfügt über eine eigene Darstellung von Geschäfts-, Schul- oder Uniidentitäten, Consumeridentitäten (wenn es sich um einen Azure AD B2C-Mieter handelt) und App-Registrierungen. Eine App-Registrierung in Ihrem Mandanten kann Authentifizierungen von Konten nur in Ihrem Mandanten oder von allen Mandanten zulassen.

## <a name="determining-environment-type"></a>Bestimmen des Umgebungstyps

Zwei Arten von Umgebungen können erstellt werden. Die Entscheidung, welche Umgebung Sie benötigen, basiert ausschließlich auf den Benutzertypen, die Ihre App authentifiziert.

* Geschäfts-, Schul- oder Unikonten (Azure AD-Konten) oder Microsoft-Konten (z.B. outlook.com und live.com)
* Soziale und lokale Konten (Azure AD B2C)

Der Schnellstart ist in zwei Szenarien unterteilt, je nachdem, welche Art von App Sie erstellen möchten. Wenn Sie weitere Hilfe bei der Auswahl eines Identitätstyps benötigen, werfen Sie einen Blick auf die [Informationen zu Microsoft Identity Platform](about-microsoft-identity-platform.md).

## <a name="work-and-school-accounts-or-personal-microsoft-accounts"></a>Geschäfts-, Schul- und Unikonten oder persönliche Microsoft-Konten

### <a name="use-an-existing-tenant"></a>Verwenden eines vorhandenen Mandanten

Viele Entwickler verfügen bereits aufgrund von Diensten oder Abonnements, die mit Azure AD-Mandanten verknüpft sind (beispielsweise Microsoft 365- oder Azure-Abonnements), über Mandanten.

1. Um den Mandanten zu überprüfen, melden Sie sich am [Azure-Portal](https://portal.azure.com) mit dem Konto an, das Sie für die Verwaltung Ihrer Anwendung verwenden möchten.
1. Überprüfen Sie die obere rechte Ecke. Falls Sie einen Mandanten besitzen, werden Sie automatisch angemeldet, und der Mandantenname wird direkt unter dem Kontonamen angezeigt.
   * Zeigen Sie rechts oben im Azure-Portal auf Ihren Kontonamen, um Ihren Namen, Ihre E-Mail-Adresse, Ihr Verzeichnis und Ihre Mandanten-ID (GUID) sowie Ihre Domäne anzuzeigen.
   * Falls Ihr Konto mehreren Mandanten zugeordnet ist, können Sie durch Klicken auf Ihren Kontonamen ein Menü öffnen, über das Sie zwischen Mandanten wechseln können. Jeder Mandant besitzt eine eigene Mandanten-ID.

> [!TIP]
> Wenn Sie die Mandanten-ID ermitteln müssen, gehen Sie folgendermaßen vor:
> * Zeigen Sie mit der Maus auf den Namen Ihres Kontos, um das Verzeichnis bzw. die Mandanten-ID abzurufen, oder
> * Wählen Sie **Azure Active Directory > Eigenschaften > Verzeichnis-ID** im Azure-Portal aus.

Sollte Ihrem Konto kein vorhandener Mandant zugeordnet sein, wird unter Ihrem Kontonamen eine GUID angezeigt. In diesem Fall sind Aktionen wie das Registrieren von Apps erst möglich, nachdem Sie die im nächsten Abschnitt beschriebenen Schritte ausgeführt haben.

### <a name="create-a-new-azure-ad-tenant"></a>Erstellen eines neuen Azure AD-Mandanten

Falls Sie nicht bereits über einen Azure AD-Mandanten verfügen oder einen neuen Mandanten für die Entwicklung erstellen möchten, können Sie hierfür die [Benutzeroberfläche für die Verzeichniserstellung](https://portal.azure.com/#create/Microsoft.AzureActiveDirectory) verwenden. Sie müssen die folgende Informationen zum Erstellen Ihres neuen Mandanten angeben:

- **Name der Organisation**
- **Anfangsdomäne**: Diese ist Bestandteil von *.onmicrosoft.com. Sie können die Domäne später weiter anpassen.
- **Land oder Region**

> [!NOTE]
> Verwenden Sie bei der Benennung Ihres Mandanten alphanumerische Zeichen. Sonderzeichen sind nicht zulässig. Der Name darf maximal 256 Zeichen lang sein.

## <a name="social-and-local-accounts"></a>Soziale und lokale Konten

Um mit dem Erstellen von Apps zu beginnen, die sich bei sozialen und lokalen Konten anmelden, müssen Sie einen Azure AD B2C-Mandanten erstellen. Befolgen Sie dazu die Anweisungen unter [Erstellen eines Azure AD B2C-Mandanten](../../active-directory-b2c/tutorial-create-tenant.md).

## <a name="next-steps"></a>Nächste Schritte

* Probieren Sie einen Codierungsschnellstart aus, und beginnen Sie mit der Authentifizierung von Benutzern. 
* Detailliertere Codebeispiele finden Sie im Abschnitt **Tutorials** der Dokumentation.
* Möchten Sie Ihre App in der Cloud bereitstellen? Lesen Sie [Bereitstellen von Containern in Azure](https://docs.microsoft.com/azure/index#pivot=products&panel=containers). 
