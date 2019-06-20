---
title: 'Azure Active Directory Domain Services: Benachrichtigungseinstellungen | Microsoft-Dokumentation'
description: Benachrichtigungseinstellungen für Azure AD Domain Services
services: active-directory-ds
documentationcenter: ''
author: MikeStephens-MS
manager: daveba
editor: curtand
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: mstephen
ms.openlocfilehash: fab680e8e26584e6beeaf4bdb205c721e1b0b91e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245439"
---
# <a name="notification-settings-in-azure-ad-domain-services"></a>Benachrichtigungseinstellungen in Azure AD Domain Services

Mit Benachrichtigungen für Azure AD Domain Services erhalten Sie aktuelle Informationen, sobald eine Integritätswarnung für Ihre verwaltete Domäne erkannt wird.  

Dieses Feature steht nur für verwaltete Domänen, die sich nicht in klassischen virtuellen Netzwerken befinden, zur Verfügung.


## <a name="how-to-check-your-azure-ad-domain-services-email-notification-settings"></a>So überprüfen Sie Ihre E-Mail-Benachrichtigungseinstellungen für Azure AD Domain Services

1. Navigieren Sie im Azure-Portal zur [Seite „Azure AD Domain Services“](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices) im Azure-Portal
2. Auswählen Ihrer verwalteten Domäne in der Tabelle
3. Auswählen von **Benachrichtigungseinstellungen** im linken Navigationsbereich

Auf der Seite werden alle E-Mail-Empfänger für E-Mail-Benachrichtigungen für Azure AD Domain Services aufgelistet.

## <a name="what-does-an-email-notification-look-like"></a>Wie sieht eine E-Mail-Benachrichtigung aus?

Die folgende Abbildung zeigt ein Beispiel für eine E-Mail-Benachrichtigung:

![Beispiel für E-Mail-Benachrichtigung](./media/active-directory-domain-services-alerts/email-alert.png)

Die E-Mail gibt die verwaltete Domäne, zu der die Warnung gemeldet wurde, den Zeitpunkt der Erkennung sowie einen Link auf die Statusseite von Azure AD Domain Services im Azure-Portal an.

> [!WARNING]
> Stellen Sie immer sicher, dass die E-Mail von einem überprüften Microsoft-Absender stammt, bevor Sie auf die Links in Ihren E-Mails klicken. Die E-Mail-Nachrichten stammen immer von der E-Mail-Adresse azure-noreply@microsoft.com.


## <a name="why-would-i-receive-email-notifications"></a>In welchen Situationen würde ich E-Mail-Benachrichtigungen erhalten?

Azure AD Domain Services sendet E-Mail-Benachrichtigungen bei wichtigen Updates zu Ihrer Domäne.  Diese Benachrichtigungen betreffen ausschließlich dringende Themen, die Auswirkungen auf Ihren Dienst haben und umgehend behoben werden sollten. Jede E-Mail-Benachrichtigung wird durch eine Warnung in Ihrer verwalteten Domäne ausgelöst. Diese Warnungen werden auch im Azure-Portal angezeigt und können auf der [Statusseite von Azure AD Domain Services](check-health.md) angezeigt werden.

Azure AD Domain Services sendet an diese Liste weder Werbung noch Informationen zu Updates noch E-Mails zu Vertriebszwecken.

## <a name="when-will-i-receive-email-notifications"></a>Wann erhalte ich E-Mail-Benachrichtigungen?

Es wird sofort eine Benachrichtigung gesendet, wenn eine [neue Warnung](troubleshoot-alerts.md) in Ihrer verwalteten Domäne auftritt. Wenn die Warnung nicht behoben wird, wird alle vier Tage eine E-Mail-Benachrichtigung zur Erinnerung gesendet.

## <a name="who-should-receive-the-email-notifications"></a>Wer sollte die E-Mail-Benachrichtigungen empfangen?


 Es empfiehlt sich, in die Liste der E-Mail-Empfänger für Azure AD Domain Services alle Personen aufzunehmen, die die verwaltete Domäne verwalten und Änderungen an ihr vornehmen können. Stellen Sie sich diese E-Mail-Liste als Ihre „erste Anlaufstelle“ für alle gefundenen Probleme vor. Wenn Sie mehr als fünf zusätzliche E-Mail-Adressen hinzufügen möchten, sollten Sie stattdessen eine Verteilerliste erstellen und diese der Benachrichtigungsliste hinzufügen.

Sie können bis zu fünf zusätzliche E-Mail-Adressen für Benachrichtigungen in Bezug auf Azure AD Domain Services hinzufügen. Darüber hinaus können Sie auch festlegen, dass alle globalen Administratoren Ihres Verzeichnisses sowie alle Mitglieder der Gruppe „AAD DC-Administratoren“ E-Mail-Benachrichtigungen zu Azure AD Domain Services erhalten. Azure AD Domain Services sendet Benachrichtigungen nur an maximal 100 E-Mail-Adressen. Diese Zahl schließt auch die Liste der globalen Administratoren und AAD DC-Administratoren ein.


## <a name="how-to-add-an-additional-email-recipient"></a>Hinzufügen weiterer E-Mail-Empfänger

> [!WARNING]
> Wenn Sie die Benachrichtigungseinstellungen ändern, ändern Sie diese für die gesamte verwaltete Domäne – nicht nur für Sie selbst.

1. Navigieren Sie im Azure-Portal zur [Seite „Azure AD Domain Services“](https://portal.azure.com/#blade/HubsExtension/Resources/resourceType/Microsoft.AAD%2FdomainServices).
2. Klicken Sie auf Ihre verwaltete Domäne.
3. Klicken Sie im linken Navigationsbereich auf **Benachrichtigungseinstellungen**.
4. Um eine E-Mail-Adresse hinzuzufügen, geben Sie die E-Mail-Adresse in die Tabelle mit zusätzlichen Empfängern ein.
5. Klicken Sie im oberen Navigationsbereich auf „Speichern“.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

#### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Ich habe eine E-Mail-Benachrichtigung für eine Warnung erhalten, aber nach der Anmeldung im Azure-Portal wurde keine Warnung angezeigt. Was ist passiert?

Wenn eine Warnung behoben wurde, wird die Warnung aus dem Azure-Portal entfernt. Die wahrscheinlichste Ursache hierfür ist, dass ein anderer Benutzer, der die E-Mail-Benachrichtigungen erhalten hat, die Warnung in Ihrer verwalteten Domäne behoben hat oder dass sie von Azure AD Domain Services automatisch gelöst wurde.


#### <a name="why-can-i-not-edit-the-notification-settings"></a>Warum kann ich die Benachrichtigungseinstellungen nicht bearbeiten?

Wenn Sie im Azure-Portal nicht auf die Seite mit den Benachrichtigungseinstellungen zugreifen können, verfügen Sie nicht über die Berechtigungen zum Bearbeiten von Azure AD Domain Services. Wenden Sie sich an Ihren globalen Administrator, um entweder die Berechtigungen zum Bearbeiten von Azure AD Domain Services-Ressourcen zu erhalten oder aus der Empfängerliste entfernt zu werden.

#### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Ich empfange anscheinend keine E-Mail-Benachrichtigungen, obwohl ich meine E-Mail-Adresse angegeben habe. Warum?

Überprüfen Sie den Spam- oder Junk-Ordner in Ihrem E-Mail-Client auf die Benachrichtigung, und setzen Sie den Absender (azure-noreply@microsoft.com) auf die Whitelist.

## <a name="next-steps"></a>Nächste Schritte
- [Azure AD Domain Services – Problembehandlung von Warnungen](troubleshoot-alerts.md)
- [Weitere Informationen zu Azure AD Domain Services](overview.md)
- [Wenden Sie sich an das Produktteam](contact-us.md)

## <a name="contact-us"></a>Kontakt
Wenden Sie sich an das Produktteam der Azure Active Directory Domain Services, um [Feedback zu geben oder Support zu erhalten](contact-us.md).
