---
title: Blockieren von Legacyauthentifizierungsprotokollen in Azure AD
description: Erfahren Sie, wie und warum Organisationen Legacyauthentifizierungsprotokolle blockieren sollten.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 24640254f32270b8c96c790dca7db31e285cc27f
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98895287"
---
# <a name="blocking-legacy-authentication"></a>Blockieren der Legacyauthentifizierung
 
Um Ihren Benutzern den einfachen Zugriff auf Ihre Cloud-Apps zu ermöglichen, unterstützt Azure Active Directory (Azure AD) eine Vielzahl von Authentifizierungsprotokollen einschließlich der Legacyauthentifizierung. Der Begriff „Legacyauthentifizierung“ bezieht sich auf eine Authentifizierungsanforderung von:

- älteren Office-Clients, die keine moderne Authentifizierung verwenden (z.B. Office 2010-Client)
- jedem Client, der veraltete E-Mail-Protokolle wie IMAP/SMTP/POP3 verwendet

Heute stammt der Großteil aller gefährdenden Anmeldeversuche von Legacyauthentifizierungen. Die Legacyauthentifizierung unterstützt keine mehrstufige Authentifizierung (Multi-Factor Authentication, MFA). Auch wenn Sie eine MFA-Richtlinie für Ihr Verzeichnis aktiviert haben, kann ein böswilliger Benutzer sich mithilfe eines älteren Protokolls authentifizieren und MFA umgehen. Die beste Möglichkeit zum Schutz Ihres Kontos vor böswilligen Authentifizierungsanforderungen durch ältere Protokolle ist, alle diese Versuche zu blockieren.

## <a name="identify-legacy-authentication-use"></a>Identifizieren der Verwendung der Legacyauthentifizierung

Bevor Sie die Legacyauthentifizierung in Ihrem Verzeichnis blockieren können, müssen Sie zuerst wissen, ob Ihre Benutzer über Apps verfügen, die die Legacyauthentifizierung verwenden, und wie sich dies auf Ihr gesamtes Verzeichnis auswirkt. Sie können Azure AD-Anmeldungsprotokolle verwenden, um herauszufinden, ob Sie die Legacyauthentifizierung verwenden.

1. Navigieren Sie zu **Azure-Portal** > **Azure Active Directory** > **Anmeldungen**.
1. Falls die Spalte **Client-App** nicht angezeigt wird, fügen Sie sie durch Klicken auf  **Spalten** > **Client-App** hinzu.
1. Filtern Sie nach **Client-App**, und aktivieren Sie alle angezeigten Optionen für **Legacy-Authentifizierungsclients**.
1. Filtern Sie nach **Status** > **Erfolg**. 
1. Erweitern Sie ggf. den Datumsbereich mithilfe des Filters **Datum**.
1. Wenn Sie die [neuen Berichte zu Anmeldeaktivitäten (Vorschau)](../reports-monitoring/concept-all-sign-ins.md) aktiviert haben, wiederholen Sie die obigen Schritte auch auf der Registerkarte **Benutzeranmeldungen (nicht interaktiv)** .

Beim Filtern werden nur erfolgreiche Anmeldeversuche der ausgewählten Legacyauthentifizierungsprotokolle angezeigt. Bei Klicken auf jeden einzelnen Anmeldeversuch werden Ihnen weitere Details angezeigt. Wenn Sie eine einzelne Datenzeile auswählen, wird in der Spalte „Client-App“ oder im Feld „Client-App“ (auf der Registerkarte „Grundlegende Infos“) angezeigt, welches Legacyauthentifizierungsprotokoll verwendet wurde. Diese Protokolle geben an, welche Benutzer weiterhin von der Legacyauthentifizierung abhängig sind, und welche Anwendungen ältere Protokolle für Authentifizierungsanforderungen verwenden. Für Benutzer, die nicht in diesen Protokollen aufgeführt sind und für die bestätigt wurde, dass sie keine veraltete Authentifizierungsmethode verwenden, implementieren Sie eine Richtlinie für bedingten Zugriff. Oder aktivieren Sie nur für diese Benutzer die Basisrichtlinie zum Blockieren der Legacyauthentifizierung.

## <a name="moving-away-from-legacy-authentication"></a>Der Abschied von der Legacyauthentifizierung 

Sobald Sie sich einen besseren Überblick darüber verschafft haben, wer die Legacyauthentifizierung in Ihrem Verzeichnis verwendet, und welche Anwendungen davon abhängen, besteht der nächste Schritt darin, Ihre Benutzer für die moderne Authentifizierung zu aktualisieren. Die moderne Authentifizierung ist eine Methode der Identitätsverwaltung, die eine sicherere Benutzerauthentifizierung und -autorisierung bietet. Wenn eine MFA-Richtlinie in Ihrem Verzeichnis vorhanden ist, stellt die moderne Authentifizierung sicher, dass der Benutzer bei Bedarf zur MFA aufgefordert wird. Es ist die sicherere Alternative zu Legacyauthentifizierungsprotokollen.

Dieser Abschnitt bietet eine schrittweise Übersicht zur Aktualisierung Ihrer Umgebung auf die moderne Authentifizierung. Lesen Sie vor dem Aktivieren einer Richtlinie zum Blockieren der Legacyauthentifizierung in Ihrer Organisation die folgenden Schritte.

### <a name="step-1-enable-modern-authentication-in-your-directory"></a>Schritt 1: Aktivieren der modernen Authentifizierung in Ihrem Verzeichnis

Im ersten Schritt zum Aktivieren der modernen Authentifizierung stellen Sie sicher, dass Ihr Verzeichnis die moderne Authentifizierung unterstützt. Moderne Authentifizierung ist standardmäßig für Verzeichnisse aktiviert, die am 1. August 2017 oder später erstellt wurden. Wenn Ihr Verzeichnis vor diesem Datum erstellt wurde, müssen Sie die moderne Authentifizierung für Ihr Verzeichnis mit den folgenden Schritten manuell aktivieren:

1. Überprüfen Sie durch Ausführen von  `Get-CsOAuthConfiguration`  im Modul  [Skype for Business Online-PowerShell](/office365/enterprise/powershell/manage-skype-for-business-online-with-office-365-powershell), ob Ihr Verzeichnis bereits die moderne Authentifizierung unterstützt.
1. Wenn der Befehl eine leere  `OAuthServers` -Eigenschaft zurückgibt, ist die moderne Authentifizierung deaktiviert. Aktualisieren Sie die Einstellung, um die moderne Authentifizierung mit  `Set-CsOAuthConfiguration` zu aktivieren. Wenn Ihre `OAuthServers` -Eigenschaft einen Eintrag enthält, können Sie loslegen.

Schließen Sie diesen Schritt unbedingt ab, bevor Sie fortfahren. Ihre Verzeichniskonfigurationen müssen unbedingt zuerst geändert werden, da sie bestimmen, welches Protokoll von allen Office-Clients verwendet wird. Auch wenn Sie Office-Clients verwenden, die die moderne Authentifizierung unterstützen, werden sie standardmäßig ältere Protokolle verwenden, wenn die moderne Authentifizierung in Ihrem Verzeichnis deaktiviert ist.

### <a name="step-2-office-applications"></a>Schritt 2: Office-Anwendungen

Nachdem Sie die moderne Authentifizierung in Ihrem Verzeichnis aktiviert haben, können Sie mit dem Aktualisieren von Anwendungen beginnen, indem Sie die moderne Authentifizierung für Office-Clients aktivieren. Office 2016- oder neuere Clients unterstützen die moderne Authentifizierung standardmäßig. Es sind keine besonderen Schritte erforderlich.

Wenn Sie Office 2013-Windows-Clients oder frühere verwenden, sollten Sie auf Office 2016 oder höher aktualisieren. Auch nach dem Abschluss des vorherigen Schritts zum Aktivieren der modernen Authentifizierung in Ihrem Verzeichnis werden die älteren Office-Anwendungen weiterhin Legacyauthentifizierungsprotokolle verwenden. Wenn Sie Office 2013-Clients verwenden und nicht sofort auf Office 2016 oder höher aktualisieren können, führen Sie die Schritte im folgenden Artikel zum  [Aktivieren der modernen Authentifizierung für Office 2013 auf Windows-Geräten](/office365/admin/security-and-compliance/enable-modern-authentication) aus. Um Ihr Konto zu schützen, während Sie die Legacyauthentifizierung verwenden, sollten Sie in Ihrem gesamten Verzeichnis sichere Kennwörter verwenden. Informieren Sie sich unter  [Azure AD-Kennwortschutz](../authentication/concept-password-ban-bad.md) , um unsichere Kennwörter in Ihrem Verzeichnis auszuschließen.

Office 2010 unterstützt keine moderne Authentifizierung. Sie müssen alle Office 2010-Benutzer auf eine neuere Version von Office aktualisieren. Sie sollten ein Upgrade auf Office 2016 oder höher durchführen, da dies die Legacyauthentifizierung standardmäßig blockiert.

Wenn Sie macOS verwenden, sollten Sie ein Upgrade auf Office für Mac 2016 oder höher durchführen. Wenn Sie den nativen E-Mail-Client verwenden, benötigen Sie macOS-Version 10.14 oder höher auf allen Geräten.

### <a name="step-3-exchange-and-sharepoint"></a>Schritt 3: Exchange und SharePoint

Damit Windows-basierte Outlook-Clients die moderne Authentifizierung verwenden können, muss die moderne Authentifizierung ebenfalls für Exchange Online aktiviert sein. Wenn die moderne Authentifizierung für Exchange Online deaktiviert ist, verwenden Windows-basierte Outlook-Clients, die die moderne Authentifizierung unterstützen (Outlook 2013 oder höher), die Standardauthentifizierung zum Herstellen von Verbindungen mit Exchange Online-Postfächern.

SharePoint Online ermöglicht standardmäßig die moderne Authentifizierung. Für Verzeichnisse, die nach dem 1. August 2017 erstellt wurden, ist die moderne Authentifizierung in Exchange Online standardmäßig aktiviert. Wenn Sie allerdings vorher die moderne Authentifizierung deaktiviert haben oder ein Verzeichnis verwenden, das vor diesem Datum erstellt wurde, führen Sie die Schritte im folgenden Artikel zum  [Aktivieren der modernen Authentifizierung in Exchange Online](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online) aus.

### <a name="step-4-skype-for-business"></a>Schritt 4: Skype for Business

Um Legacyauthentifizierungsanforderungen von Skype for Business zu verhindern, ist es notwendig, die moderne Authentifizierung für Skype for Business Online zu aktivieren. Für Verzeichnisse, die nach dem 1. August 2017 erstellt wurden, ist die moderne Authentifizierung für Skype for Business standardmäßig aktiviert.

Sie sollten auf Microsoft Teams umstellen, das die moderne Authentifizierung standardmäßig unterstützt. Wenn Sie die Migration zu diesem Zeitpunkt nicht durchführen können, müssen Sie die moderne Authentifizierung für Skype for Business Online aktivieren, sodass Skype for Business-Clients beginnen, die moderne Authentifizierung zu verwenden. Befolgen Sie die Schritte im Artikel  [Unterstützung von Skype for Business-Topologien mit moderner Authentifizierung](/skypeforbusiness/plan-your-deployment/modern-authentication/topologies-supported) zum Aktivieren der modernen Authentifizierung für Skype for Business.

Zusätzlich zum Aktivieren der modernen Authentifizierung für Skype for Business Online sollte die moderne Authentifizierung für Exchange Online aktiviert werden, wenn die moderne Authentifizierung für Skype for Business aktiviert wird. Dieser Prozess synchronisiert den Zustand der modernen Authentifizierung in Exchange Online und Skype for Business Online und verhindert mehrere Anmeldeaufforderungen für Skype for Business-Clients.

### <a name="step-5-using-mobile-devices"></a>Schritt 5: Verwenden mobiler Geräte

Anwendungen auf Ihrem mobilen Gerät müssen auch die Legacyauthentifizierung blockieren. Sie sollten Outlook für mobile Geräte verwenden. Outlook für mobile Geräte unterstützt standardmäßig die moderne Authentifizierung und erfüllt andere MFA-Baselineschutzrichtlinien.

Um den nativen iOS-Mail-Client zu verwenden, müssen Sie die iOS-Version 11.0 oder höher ausführen, um sicherzustellen, dass der E-Mail-Client zum Blockieren der Legacyauthentifizierung aktualisiert wurde.

### <a name="step-6-on-premises-clients"></a>Schritt 6: Lokale Clients

Wenn Sie ein Hybridkunde sind, der Exchange Server und Skype for Business lokal verwendet, müssen beide Dienste aktualisiert werden, um die moderne Authentifizierung zu aktivieren. Wenn Sie die moderne Authentifizierung in einer Hybridumgebung verwenden, authentifizieren Sie Benutzer weiterhin lokal. Der Verlauf der Autorisierung Ihres Zugriffs auf Ressourcen (Dateien oder E-Mails) ändert sich.

Stellen Sie sicher, dass Sie die Voraussetzungen erfüllt haben, bevor Sie die moderne Authentifizierung lokal aktivieren. Jetzt können Sie die moderne Authentifizierung lokal aktivieren.

Schritte zum Aktivieren der modernen Authentifizierung finden Sie in den folgenden Artikeln:

* [Vorgehensweise: Lokales Konfigurieren von Exchange Server zur Verwendung der modernen hybriden Authentifizierung](/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
* [Verwendung der modernen Authentifizierung (ADAL) mit Skype for Business](/skypeforbusiness/manage/authentication/use-adal)

## <a name="next-steps"></a>Nächste Schritte

- [Vorgehensweise: Lokales Konfigurieren von Exchange Server zur Verwendung der modernen hybriden Authentifizierung](/office365/enterprise/configure-exchange-server-for-hybrid-modern-authentication)
- [Verwendung der modernen Authentifizierung (ADAL) mit Skype for Business](/skypeforbusiness/manage/authentication/use-adal)
- [Blockieren älterer Authentifizierungsmethoden](../conditional-access/block-legacy-authentication.md)