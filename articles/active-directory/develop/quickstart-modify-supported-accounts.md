---
title: 'Vorgehensweise: Ändern der von einer Anwendung unterstützten Kontotypen | Azure'
titleSuffix: Microsoft identity platform
description: In dieser Anleitung konfigurieren Sie eine bei Microsoft Identity Platform registrierte Anwendung, um zu ändern, welche Benutzer oder Konten auf die Anwendung zugreifen können.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/15/2020
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: marsma, aragra, lenalepa, sureshja
ms.openlocfilehash: 94a7f4d9ce1471aa1dd6aef3165562a2abc02816
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453256"
---
# <a name="how-to-modify-the-accounts-supported-by-an-application"></a>Ändern der von einer Anwendung unterstützten Konten

Wenn Sie Ihre Anwendung bei Microsoft Identity Platform registriert haben, haben Sie festgelegt, wer (d. h. welche Kontotypen) darauf zugreifen kann. Sie haben beispielsweise ausschließlich Konten in Ihrer Organisation angegeben. Dies entspricht dann einer App mit *einem einzelnen Mandanten*. Oder Sie haben möglicherweise Konten in beliebigen Organisationen (einschließlich Ihrer eigenen) angegeben. Dies entspricht dann einer App mit *mehreren Mandanten*.

In den folgenden Abschnitten erfahren Sie, wie Sie die Registrierung Ihrer App im Azure-Portal bearbeiten, um zu ändern, welche Benutzer oder Kontotypen auf die Anwendung zugreifen können.

## <a name="prerequisites"></a>Voraussetzungen

* Eine [Anwendung, die in Ihrem Azure AD-Mandanten registriert ist](quickstart-register-app.md)

## <a name="change-the-application-registration-to-support-different-accounts"></a>Unterstützen anderer Konten durch Ändern der Anwendungsregistrierung

So geben Sie eine andere Einstellung für die Kontotypen an, die von einer vorhandenen App-Registrierung unterstützt werden:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Wenn Sie Zugriff auf mehrere Mandanten haben, verwenden Sie im Menü am oberen Rand den Filter **Verzeichnis + Abonnement** :::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false":::, um den Mandanten auszuwählen, für den Sie eine Anwendung registrieren möchten.
1. Suchen Sie nach **Azure Active Directory**, und wählen Sie diese Option aus.
1. Wählen Sie unter **Verwalten** die Option **App-Registrierungen** und dann Ihre Anwendung aus.
1. Geben Sie nun an, wer die Anwendung verwenden kann (manchmal auch als *Zielgruppe für die Anmeldung* bezeichnet).

    | Unterstützte Kontotypen | BESCHREIBUNG |
    |-------------------------|-------------|
    | **Nur Konten in diesem Organisationsverzeichnis** | Wählen Sie diese Option aus, wenn Sie eine Anwendung nur für Benutzer (oder Gäste) in *Ihrem* Mandanten erstellen.<br><br>Bei dieser Anwendung, die häufig auch als *branchenspezifische Anwendung* bezeichnet wird, handelt es sich um eine **Einzelmandantenanwendung** in Microsoft Identity Platform. |
    | **Konten in einem beliebigen Organisationsverzeichnis** | Wählen Sie diese Option aus, wenn Sie möchten, dass Benutzer in *einem beliebigen* Azure AD-Mandanten Ihre Anwendung verwenden können. Diese Option eignet sich beispielsweise beim Erstellen von SaaS-Anwendungen (Software-as-a-Service), die Sie für mehrere Organisationen bereitstellen möchten.<br><br>Diese Anwendung wird in Microsoft Identity Platform als **mehrinstanzenfähige Anwendung** bezeichnet. |
1. Wählen Sie **Speichern** aus.

### <a name="why-changing-to-multi-tenant-can-fail"></a>Gründe für Fehler bei der Umstellung auf mehrere Mandanten

Aufgrund von Namenskonflikten des Anwendungs-ID-URI (App-ID-URI) kann es bei der Umstellung einer App-Registrierung von einem Mandanten auf mehrere Mandanten manchmal zu Fehlern kommen. Ein Beispiel für einen App-ID-URI ist `https://contoso.onmicrosoft.com/myapp`.

Der App-ID-URI ist eine der Methoden, mit der eine Anwendung in Protokollmeldungen identifiziert wird. Bei einer Anwendung mit einem einzelnen Mandanten muss der App-ID-URI nur in diesem Mandanten eindeutig sein. Bei einer mehrinstanzenfähigen Anwendung muss er global eindeutig sein, damit Azure AD die App in allen Mandanten finden kann. Globale Eindeutigkeit wird durch die Anforderung erzwungen, dass der Hostname des App-ID-URI mit [verifizierten Herausgeberdomänen](howto-configure-publisher-domain.md) des Azure AD-Mandanten übereinstimmt.

Wenn der Name Ihres Mandanten also beispielsweise *contoso.onmicrosoft.com* lautet, ist `https://contoso.onmicrosoft.com/myapp` ein gültiger App-ID-URI. Wenn Ihr Mandant eine verifizierte Domäne von *contoso.com* hat, ist auch `https://contoso.com/myapp` ein gültiger App-ID-URI. Wenn der App-ID-URI nicht das zweite Format (`https://contoso.com/myapp`) aufweist, tritt bei der Umstellung der App-Registrierung auf mehrere Mandanten ein Fehler auf.

Weitere Informationen zum Konfigurieren einer verifizierten Herausgeberdomäne finden Sie unter [Schnellstart: Ändern der von einer Anwendung unterstützten Konten](howto-configure-publisher-domain.md).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu den Anforderung für das [Umstellung einer App-Registrierung von einem Mandanten auf mehrere Mandanten](howto-convert-app-to-be-multi-tenant.md)
