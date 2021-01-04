---
title: Best Practices und Empfehlungen für B2B-Funktionen in Azure Active Directory
description: Lernen Sie Best Practices und Empfehlungen für den B2B-Gastbenutzerzugriff in Azure Active Directory kennen.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 11/30/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: elisol
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f9ea8b1c1346deee9fed591493607270f18ad5b
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96547762"
---
# <a name="azure-active-directory-b2b-best-practices"></a>Best Practices für B2B-Funktionen in Azure Active Directory
Dieser Artikel enthält Empfehlungen und Best Practices für die B2B-Zusammenarbeit (Business-to-Business) in Azure Active Directory (Azure AD).

   > [!IMPORTANT]
   > **Ab März 2021** wird das Einlösen von Einladungen durch die Erstellung von nicht verwalteten Azure AD-Konten und -Mandanten („viral“ oder „just in time“) für B2B-Zusammenarbeitsszenarien von Microsoft nicht mehr unterstützt. Zu diesem Zeitpunkt wird das Feature für die Einmalkennung per E-Mail für alle vorhandenen Mandanten aktiviert und für neue Mandanten standardmäßig aktiviert. Wir aktivieren das Feature für die Einmalkennung per E-Mail, da es eine nahtlose Fallbackauthentifizierungsmethode für Ihre Gastbenutzer bietet. Sie können dieses Feature jedoch deaktivieren, wenn Sie es nicht verwenden möchten. Weitere Informationen finden Sie unter [Authentifizierung mit Einmalkennung per E-Mail](one-time-passcode.md).


## <a name="b2b-recommendations"></a>B2B-Empfehlungen
| Empfehlung | Kommentare |
| --- | --- |
| Um für ein optimales Benutzererlebnis bei der Anmeldung zu sorgen, erstellen Sie einen Verbund mit Identitätsanbietern. | Erstellen Sie nach Möglichkeit einen direkten Verbund mit Identitätsanbietern, um eingeladenen Benutzern die Anmeldung bei Ihren freigegebenen Apps und Ressourcen zu ermöglichen, ohne dass sie Microsoft-Konten (Microsoft Accounts, MSAs) oder Azure AD-Konten erstellen müssen. Sie können das [Feature zum Erstellen eines Verbunds mit Google](google-federation.md) verwenden, um B2B-Gastbenutzern die Anmeldung mit ihren Google-Konten zu ermöglichen. Alternativ dazu können Sie das [Feature für das Erstellen eines direkten Verbunds (Vorschau)](direct-federation.md) verwenden, um einen direkten Verbund mit jeder Organisation einzurichten, deren Identitätsanbieter (Identity Provider, IdP) das SAML 2.0- oder WS-Federation-Protokoll unterstützt. |
| Verwenden Sie das Feature für das Senden einer Einmalkennung per E-Mail für B2B-Gastbenutzer, die sich nicht auf andere Weise authentifizieren können. | Mit dem Feature [Einmalkennung per E-Mail](one-time-passcode.md) werden B2B-Gastbenutzer authentifiziert, wenn sie über andere Wege (z. B. über Azure AD, ein Microsoft-Konto (MSA) oder den Verbund mit Google) nicht authentifiziert werden können. Wenn der Gastbenutzer eine Einladung einlöst oder auf eine freigegebene Ressource zugreift, kann er einen temporären Code anfordern, der an seine E-Mail-Adresse gesendet wird. Anschließend gibt er diesen Code ein, um den Anmeldevorgang fortzusetzen. |
| Hinzufügen eines Unternehmensbrandings zu Ihrer Anmeldeseite | Sie können Ihre Anmeldeseite anpassen, damit sie für B2B-Gastbenutzer intuitiver ist. Informationen dazu finden Sie unter [Hinzufügen von Unternehmensbranding zur Anmelde- und Zugriffsbereichsseite](../fundamentals/customize-branding.md). |
| Fügen Sie Ihre Datenschutzerklärung zum Einlösungsprozess der B2B-Gastbenutzer hinzu. | Sie können die URL zur Datenschutzerklärung Ihrer Organisation in den Prozess zum Einlösen einer Einladung einfügen, sodass ein eingeladener Benutzer diesen Bestimmungen zustimmen muss, um fortfahren zu können. Weitere Informationen finden Sie unter [Vorgehensweise: Hinzufügen der Datenschutzinformationen mit Azure Active Directory](../fundamentals/active-directory-properties-area.md). |
| Verwenden Sie das Feature für Masseneinladungen (Vorschau), um mehrere B2B-Gastbenutzer gleichzeitig einzuladen. | Laden Sie mithilfe des Features für Masseneinladungen (Vorschau) im Azure-Portal mehrere Gastbenutzer gleichzeitig in Ihre Organisation ein. Mit diesem Feature können Sie eine CSV-Datei hochladen, um per Massenvorgang B2B-Gastbenutzer zu erstellen und Einladungen zu senden. Weitere Informationen finden Sie unter [Tutorial: Masseneinladen von B2B-Benutzern](tutorial-bulk-invite.md). |
| Erzwingen Sie Richtlinien für bedingten Zugriff für die Multi-Factor Authentication (MFA). | Es wird empfohlen, MFA-Richtlinien in den Apps zu erzwingen, die Sie für B2B-Benutzer von Partnern freigeben möchten. Auf diese Weise wird die MFA in allen Apps in Ihrem Mandanten erzwungen, unabhängig davon, ob die Partnerorganisation die MFA verwendet. Weitere Informationen finden Sie unter [Bedingter Zugriff für Benutzer von B2B Collaboration](conditional-access.md). |
| Wenn Sie gerätebasierte Richtlinien für bedingten Zugriff erzwingen, verwenden Sie Ausschlusslisten, um B2B-Benutzern Zugriff zu gewähren. | Wenn in Ihrer Organisation gerätebasierte Richtlinien für bedingten Zugriff aktiviert sind, werden Geräte von B2B-Gastbenutzern blockiert, weil sie nicht von Ihrer Organisation verwaltet werden. Sie können jedoch Ausschlusslisten mit bestimmten Partnerbenutzern erstellen, die von der gerätebasierten Richtlinie für bedingten Zugriff ausgeschlossen werden sollen. Weitere Informationen finden Sie unter [Bedingter Zugriff für Benutzer von B2B Collaboration](conditional-access.md). |
| Verwenden Sie beim Bereitstellen von direkten Links für Ihre B2B-Gastbenutzer eine mandantenspezifische URL. | Alternativ zur Einladungs-E-Mail können Sie einem Gast einen direkten Link zu Ihrer App oder Ihrem Portal zur Verfügung stellen. Dieser direkte Link muss mandantenspezifisch sein, also eine Mandanten-ID oder eine überprüfte Domäne enthalten, damit der Gast in Ihrem Mandanten, in dem sich die freigegebene App befindet, authentifiziert werden kann. Weitere Informationen finden Sie unter [Einlösung von Einladungen](redemption-experience.md). |
| Verwenden Sie beim Entwickeln einer App die UserType-Eigenschaft, um die Funktionalität für Gastbenutzer festzulegen.  | Wenn Sie eine Anwendung entwickeln und für Mandantenbenutzer und Gastbenutzer unterschiedliche Funktionalitäten bereitstellen möchten, verwenden Sie die Eigenschaft „UserType“. Der UserType-Anspruch ist derzeit nicht im Token enthalten. Anwendungen sollten die Microsoft Graph-API verwenden, um zum Abrufen der UserType-Eigenschaft eines Benutzers das Verzeichnis abzufragen. |
| Ändern Sie die Eigenschaft „UserType“ *nur*, wenn sich die Beziehung eines Benutzers zur Organisation ändert. | Sie können zwar die UserType-Eigenschaft eines Benutzers in PowerShell von „Mitglied“ zu „Gast“ (und umgekehrt) konvertieren, aber Sie sollten diese Eigenschaft nur ändern, wenn sich die Beziehung des Benutzers zu Ihrer Organisation ändert. Weitere Informationen finden Sie unter [Eigenschaften eines B2B-Gastbenutzers](user-properties.md).|

## <a name="next-steps"></a>Nächste Schritte

[Verwalten der B2B-Freigabe](delegate-invitations.md)