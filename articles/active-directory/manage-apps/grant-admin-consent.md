---
title: 'Azure AD: Erteilen einer mandantenweiten Administratoreinwilligung für eine Anwendung'
description: Erfahren Sie, wie Sie eine mandantenweite Einwilligung für eine Anwendung erteilen, sodass die Endbenutzer beim Anmelden bei der Anwendung keine Einwilligung erteilen müssen.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 11/04/2019
ms.author: kenwith
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66a6317b0cb59d656cdb2e402c5ade1b78ed60aa
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/02/2021
ms.locfileid: "99258319"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Erteilen einer mandantenweiten Administratoreinwilligung für eine Anwendung

Erfahren Sie, wie Sie die Benutzererfahrung verbessern können, indem Sie eine mandantenweite Administratoreinwilligung für eine Anwendung erteilen. In diesem Artikel werden die verschiedenen Methoden dafür erläutert. Diese Methoden gelten für alle Endbenutzer in Ihrem Azure AD-Mandanten.

Weitere Informationen zur Einwilligung für Anwendungen finden Sie unter [Azure Active Directory-Zustimmungsframework](../develop/consent-framework.md).

## <a name="prerequisites"></a>Voraussetzungen

Sie müssen sich als [globaler Administrator](../roles/permissions-reference.md#global-administrator), als [Anwendungsadministrator](../roles/permissions-reference.md#application-administrator) oder als [Cloudanwendungsadministrator](../roles/permissions-reference.md#cloud-application-administrator) anmelden, um eine mandantenweite Administratoreinwilligung erteilen zu können.

> [!IMPORTANT]
> Wenn für eine Anwendung die mandantenweite Administratoreinwilligung erteilt wurde, können sich alle Benutzer bei der Anwendung anmelden, es sei denn eine Benutzerzuweisung ist erforderlich. Sie benötigen die Benutzerzuweisung und müssen dann der Anwendung Benutzer oder Gruppen zuweisen, um zu bestimmen, welche Benutzer sich bei der Anwendung anmelden dürfen. Weitere Informationen finden Sie unter [Zuweisen von Benutzern und Gruppen zu einer Anwendung in Azure Active Directory](./assign-user-or-group-access-portal.md).
>
> Die Rolle „Globaler Administrator“ ist erforderlich, um die Administratoreinwilligung für die Anwendungsberechtigungen für die Microsoft Graph-API zu erteilen.

> [!WARNING]
> Durch das Erteilen der mandantenweiten Administratoreinwilligung für eine Anwendung erhält die App und der Herausgeber der App Zugriff auf die Daten Ihrer Organisation. Überprüfen Sie vor dem Erteilen einer Einwilligung sorgfältig die Berechtigungen, die von der Anwendung angefordert werden.
>
> Die Rolle „Globaler Administrator“ ist erforderlich, um die Administratoreinwilligung für die Anwendungsberechtigungen für die Microsoft Graph-API zu erteilen.

## <a name="grant-admin-consent-from-the-azure-portal"></a>Erteilen der Administratoreinwilligung über das Azure-Portal

### <a name="grant-admin-consent-in-enterprise-apps"></a>Erteilen der Administratoreinwilligung in Unternehmensanwendungen

Sie können die mandantenweite Administratoreinwilligung über *Unternehmensanwendungen* erteilen, wenn die Anwendung bereits in Ihrem Mandanten bereitgestellt wurde. Eine Anwendung wurde z. B. in Ihrem Mandanten bereitgestellt, wenn mindestens ein Benutzer bereits eine Einwilligung für die Anwendung erteilt hat. Weitere Informationen finden Sie unter [Wie und warum werden Anwendungen zu Azure AD hinzugefügt?](../develop/active-directory-how-applications-are-added.md).

So erteilen Sie die mandantenweite Administratoreinwilligung für eine in **Unternehmensanwendungen** aufgeführte Anwendung:

1. Melden Sie sich als [globaler Administrator](../roles/permissions-reference.md#global-administrator), als [Anwendungsadministrator](../roles/permissions-reference.md#application-administrator) oder als [Cloudanwendungsadministrator](../roles/permissions-reference.md#cloud-application-administrator) beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **Azure Active Directory** und anschließend auf **Unternehmensanwendungen**.
3. Wählen Sie die Anwendung aus, der Sie eine mandantenweite Administratoreinwilligung erteilen möchten.
4. Wählen Sie **Berechtigungen** aus, und klicken Sie dann auf **Administratoreinwilligung gewähren**.
5. Überprüfen Sie sorgfältig die Berechtigungen, die die Anwendung anfordert.
6. Erteilen Sie die Einwilligung, wenn Sie mit den angeforderten Berechtigungen der Anwendung einverstanden sind. Klicken Sie andernfalls auf **Abbrechen**, oder schließen Sie das Fenster.

> [!WARNING]
> Durch das Erteilen der mandantenweiten Administratorzustimmung über **Unternehmens-Apps** werden alle Berechtigungen widerrufen, die zuvor mandantenweit erteilt wurden. Berechtigungen, die zuvor von Benutzern im eigenen Auftrag erteilt wurden, sind nicht betroffen. 

### <a name="grant-admin-consent-in-app-registrations"></a>Erteilen der Administratoreinwilligung in App-Registrierungen

Bei Anwendungen, die Ihre Organisation entwickelt hat, oder die direkt in Ihrem Azure AD-Mandant registriert sind, können Sie auch eine mandantenweite Administratoreinwilligung in **App-Registrierungen** im Azure-Portal erteilen.

So erteilen Sie die mandantenweite Administratoreinwilligung in **App-Registrierungen**:

1. Melden Sie sich als [globaler Administrator](../roles/permissions-reference.md#global-administrator), als [Anwendungsadministrator](../roles/permissions-reference.md#application-administrator) oder als [Cloudanwendungsadministrator](../roles/permissions-reference.md#cloud-application-administrator) beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **Azure Active Directory** und anschließend auf **App-Registrierungen**.
3. Wählen Sie die Anwendung aus, der Sie eine mandantenweite Administratoreinwilligung erteilen möchten.
4. Wählen Sie **API-Berechtigungen** aus, und klicken Sie dann auf **Administratoreinwilligung erteilen**.
5. Überprüfen Sie sorgfältig die Berechtigungen, die die Anwendung anfordert.
6. Erteilen Sie die Einwilligung, wenn Sie mit den angeforderten Berechtigungen der Anwendung einverstanden sind. Klicken Sie andernfalls auf **Abbrechen**, oder schließen Sie das Fenster.

> [!WARNING]
> Durch das Erteilen der mandantenweiten Administratorzustimmung über **App-Registrierungen** werden alle Berechtigungen widerrufen, die zuvor mandantenweit erteilt wurden. Berechtigungen, die zuvor von Benutzern im eigenen Auftrag erteilt wurden, sind nicht betroffen. 

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Erstellen der URL zum Erteilen der mandantenweiten Administratoreinwilligung

Wenn Sie mithilfe einer der oben beschriebenen Methoden die mandantenweite Administratoreinwilligung erteilen, öffnet sich im Azure-Portal ein Fenster mit der Aufforderung zur mandantenweiten Administratoreinwilligung. Wenn Sie die Client-ID (auch Anwendungs-ID) der Anwendung kennen, können Sie die gleiche URL erstellen, um die mandantenweite Administratoreinwilligung zu erteilen.

Die URL für die mandantenweite Administratoreinwilligung verfügt über folgendes Format:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

Dabei gilt:

* `{client-id}` ist die Client-ID der Anwendung, auch App-ID.
* `{tenant-id}` ist die Mandanten-ID Ihrer Organisation oder ein beliebiger verifizierter Domänenname.

Überprüfen Sie vor dem Erteilen einer Einwilligung wie immer sorgfältig die Berechtigungen, die von einer Anwendung angefordert werden.

> [!WARNING]
> Durch das Erteilen der mandantenweiten Administratorzustimmung über diese URL werden alle Berechtigungen widerrufen, die zuvor mandantenweit erteilt wurden. Berechtigungen, die zuvor von Benutzern im eigenen Auftrag erteilt wurden, sind nicht betroffen. 

## <a name="next-steps"></a>Nächste Schritte

[Konfigurieren der Art der Benutzereinwilligung für eine Anwendung in Azure Active Directory](configure-user-consent.md)

[Konfigurieren des Workflows für die Administratoreinwilligung (Vorschau)](configure-admin-consent-workflow.md)

[Berechtigungen und Zustimmung im Microsoft Identity Platform-Endpunkt](../develop/v2-permissions-and-consent.md)

[Azure AD bei Microsoft Q&A](https://docs.microsoft.com/answers/topics/azure-active-directory.html)