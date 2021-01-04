---
title: E-Mail-Benachrichtigungen für Azure AD Domain Services | Microsoft-Dokumentation
description: Hier erfahren Sie, wie Sie E-Mail-Benachrichtigungen konfigurieren, durch die Sie über Probleme in einer durch Azure Active Directory Domain Services verwalteten Domäne informiert werden.
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 4242092af86f8bc06ef9c9eba068ce07725de01f
ms.sourcegitcommit: 8192034867ee1fd3925c4a48d890f140ca3918ce
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/05/2020
ms.locfileid: "96619163"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Konfigurieren von E-Mail-Benachrichtigungen für Probleme in Azure Active Directory Domain Services

Die Integrität einer durch Azure Active Directory Domain Services (Azure AD DS) verwalteten Domäne wird von der Azure-Plattform überwacht. Auf der Seite für den Integritätsstatus im Azure-Portal werden alle Warnungen für die verwaltete Domäne angezeigt. Damit sichergestellt ist, dass rechtzeitig auf Probleme reagiert wird, können E-Mail-Benachrichtigungen konfiguriert werden, um Integritätswarnungen zu melden, sobald diese in der durch Azure AD DS verwalteten Domäne festgestellt werden.

In diesem Artikel erfahren Sie, wie Sie Empfänger von E-Mail-Benachrichtigungen für eine verwaltete Domäne konfigurieren.

## <a name="email-notification-overview"></a>Übersicht über E-Mail-Benachrichtigungen

Damit Sie über Probleme bei einer verwalteten Domäne informiert werden, können Sie E-Mail-Benachrichtigungen konfigurieren. In diesen E-Mail-Benachrichtigungen sind die verwaltete Domäne, zu der die Warnung gemeldet wurde, der Zeitpunkt der Erkennung sowie ein Link zur Integritätsseite im Azure-Portal angegeben. Sie können dann den genannten Hinweisen zur Problembehandlung folgen, um die Probleme zu beheben.

Im folgenden Beispiel einer E-Mail-Benachrichtigung ist angegeben, dass in der verwalteten Domäne eine kritische Warnung generiert wurde:

![Beispiel für E-Mail-Benachrichtigung](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Stellen Sie immer sicher, dass die E-Mail von einem überprüften Microsoft-Absender stammt, bevor Sie auf die Links in der Nachricht klicken. Die E-Mail-Benachrichtigungen kommen immer von der Adresse `azure-noreply@microsoft.com`.

### <a name="why-would-i-receive-email-notifications"></a>In welchen Situationen würde ich E-Mail-Benachrichtigungen erhalten?

Azure AD DS sendet E-Mail-Benachrichtigungen bei wichtigen Updates zur verwalteten Domäne. Diese Benachrichtigungen betreffen ausschließlich dringende Probleme, die Auswirkungen auf den Dienst haben und umgehend behoben werden sollten. Jede E-Mail-Benachrichtigung wird durch eine Warnung in der verwalteten Domäne ausgelöst. Die Warnungen werden auch im Azure-Portal angezeigt und können auf der [Seite zur Integrität von Azure AD DS][check-health] angezeigt werden.

Azure AD DS sendet keine E-Mails für Ankündigungen, Aktualisierungen oder Verkaufszwecke.

### <a name="when-will-i-receive-email-notifications"></a>Wann erhalte ich E-Mail-Benachrichtigungen?

Es wird sofort eine Benachrichtigung gesendet, wenn eine [neue Warnung][troubleshoot-alerts] in einer verwalteten Domäne auftritt. Wenn die Warnung nicht behoben wird, werden alle vier Tage weitere E-Mail-Benachrichtigungen zur Erinnerung gesendet.

### <a name="who-should-receive-the-email-notifications"></a>Wer sollte die E-Mail-Benachrichtigungen empfangen?

Die Liste der E-Mail-Empfänger für Azure AD DS sollte die Personen umfassen, die die verwaltete Domäne verwalten und Änderungen daran vornehmen können. Stellen Sie sich diese E-Mail-Liste als Ihre „erste Anlaufstelle“ für alle Warnungen und Probleme vor.

Sie können bis zu fünf zusätzliche Empfänger für E-Mail-Benachrichtigungen hinzufügen. Wenn Sie mehr als fünf E-Mail-Empfänger wünschen, erstellen Sie eine Verteilerliste, und fügen Sie stattdessen diese der Benachrichtigungsliste hinzu.

Sie können auch festlegen, dass alle *globalen Administratoren* des Azure AD-Verzeichnisses sowie alle Mitglieder der Gruppe *AAD DC-Administratoren* E-Mail-Benachrichtigungen erhalten. Azure AD DS sendet eine Benachrichtigung nur an maximal 100 E-Mail-Adressen. Diese Zahl schließt auch die Liste der globalen Administratoren und AAD DC-Administratoren ein.

## <a name="configure-email-notifications"></a>Konfigurieren von E-Mail-Benachrichtigungen

Führen Sie die folgenden Schritte aus, um die vorhandenen Empfänger von E-Mail-Benachrichtigungen anzuzeigen oder weitere Empfänger hinzuzufügen:

1. Suchen Sie im Azure-Portal nach dem Eintrag **Azure AD Domain Services**, und wählen Sie ihn aus.
1. Wählen Sie Ihre verwaltete Domäne (z. B. *aaddscontoso.com*) aus.
1. Wählen Sie links im Azure AD DS-Ressourcenfenster die Option **Benachrichtigungseinstellungen** aus. Die vorhandenen Empfänger für E-Mail-Benachrichtigungen werden angezeigt.
1. Um einen E-Mail-Empfänger hinzuzufügen, geben Sie die E-Mail-Adresse in die Tabelle mit zusätzlichen Empfängern ein.
1. Klicken Sie anschließend im oberen Navigationsbereich auf **Speichern**.

> [!WARNING]
> Wenn Sie die Benachrichtigungseinstellungen ändern, werden diese für die gesamte verwaltete Domäne aktualisiert – nicht nur für Sie selbst.

## <a name="frequently-asked-questions"></a>Häufig gestellte Fragen

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Ich habe eine E-Mail-Benachrichtigung für eine Warnung erhalten, aber nach der Anmeldung im Azure-Portal wurde keine Warnung angezeigt. Was ist passiert?

Wenn eine Warnung behoben wurde, wird sie aus dem Azure-Portal gelöscht. Die wahrscheinlichste Ursache hierfür ist, dass ein anderer Benutzer, der E-Mail-Benachrichtigungen erhält, die Warnung in der verwalteten Domäne behoben hat oder dass sie von der Azure-Plattform automatisch gelöst wurde.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Warum kann ich die Benachrichtigungseinstellungen nicht bearbeiten?

Wenn Sie im Azure-Portal nicht auf die Seite mit den Benachrichtigungseinstellungen zugreifen können, verfügen Sie nicht über die Berechtigungen zum Bearbeiten der verwalteten Domäne. Sie müssen sich an einen globalen Administrator wenden, um entweder die Berechtigungen zum Bearbeiten einer Azure AD DS-Ressource zu erhalten oder aus der Empfängerliste entfernt zu werden.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Ich empfange anscheinend keine E-Mail-Benachrichtigungen, obwohl ich meine E-Mail-Adresse angegeben habe. Warum?

Überprüfen Sie den Spam- oder Junk-Ordner in Ihrem E-Mail-Client auf die Benachrichtigung, und legen Sie den Absender `azure-noreply@microsoft.com` als zulässig fest.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zur Behebung von Problemen, die möglicherweise gemeldet werden, finden Sie unter [Beheben von Warnungen in einer verwalteten Domäne][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
