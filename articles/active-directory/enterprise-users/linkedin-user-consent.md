---
title: LinkedIn-Datenfreigabe und -Einwilligung – Azure Active Directory | Microsoft-Dokumentation
description: Es wird beschrieben, wie bei der LinkedIn-Integration Daten über Microsoft-Apps in Azure Active Directory freigegeben werden.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: beengen
ms.custom: it-pro;seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8d13ae1e5198666e115507fd91bb0fedb1c5fa64
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96545909"
---
# <a name="linkedin-account-connections-data-sharing-and-consent"></a>Datenfreigabe und Benutzereinwilligung bei LinkedIn-Kontoverbindungen

Sie können Benutzern in Ihrer Azure Active Directory-Organisation (Azure AD) die Einwilligung ermöglichen, ihr Microsoft-Geschäfts-, -Schul- oder -Unikonto mit ihrem LinkedIn-Konto zu verbinden. Wenn Benutzer ihre Konten verbinden, sind die Informationen und Highlights von LinkedIn in einigen Microsoft-Apps und Diensten verfügbar. Benutzer können ebenfalls damit rechnen, dass ihre Netzwerkerfahrung bei LinkedIn verbessert und mit Informationen von Microsoft angereichert wird.

Um LinkedIn-Informationen in Microsoft-Apps und -Diensten anzuzeigen, müssen Benutzer einwilligen, ihre eigenen Microsoft- und LinkedIn-Konten zu verbinden. Benutzer werden aufgefordert, ihre Konten zu verbinden, wenn sie zum ersten Mal auf einer Profilkarte in Outlook, OneDrive oder SharePoint Online klicken, um LinkedIn-Informationen von jemandem anzuzeigen. LinkedIn-Kontoverbindungen werden für Ihre Benutzer erst dann vollständig aktiviert, wenn sie ihre Einwilligung zu der Erfahrung und dem Verbinden Ihrer Konten gegeben haben.

[!INCLUDE [active-directory-gdpr-note](../../../includes/gdpr-hybrid-note.md)]

## <a name="benefits-of-sharing-linkedin-information"></a>Vorteile des Teilens von LinkedIn-Informationen

Zugriff auf LinkedIn-Informationen in Microsoft-Apps und -Diensten erleichtert es Ihren Benutzern, sich zu vernetzen, zu engagieren und professionelle Beziehungen mit Kollegen, Kunden und Partnern innerhalb und außerhalb Ihrer Organisation aufzubauen. Neue Benutzer gewöhnen sich schneller ein, indem sie sich mit Kollegen verbinden, mehr über sie erfahren und einfacher auf weitere Informationen zugreifen können. Hier ist ein Beispiel dafür, wie LinkedIn-Informationen auf der Profilkarte in Microsoft-Apps angezeigt werden:

![Aktivieren der LinkedIn-Integration in Ihrer Organisation](./media/linkedin-user-consent/display-example.png)

## <a name="enable-and-announce-linkedin-integration"></a>Aktivieren und das Ankündigen der LinkedIn-Integration

Sie müssen Azure Active Directory-Administrator sein, um die Einstellung für Ihre Organisation zu verwalten. Sie können sie für alle Benutzer oder nur für eine bestimmte Gruppe von Benutzern aktivieren.

1. Um die Integration zu aktivieren bzw. zu deaktivieren, führen Sie die Schritte unter [Einwilligen in die LinkedIn-Integration für Ihre Azure AD Organisation](linkedin-integration.md) aus.
2. Wenn Sie die LinkedIn-Integration in Ihrer Organisation ankündigen, verweisen Sie Ihre Benutzer auf die häufig gestellten Fragen (FAQ) zu [LinkedIn-Informationen in Microsoft-Apps und -Diensten](https://support.office.com/article/about-linkedin-information-and-features-in-microsoft-apps-and-services-dc81cc70-4d64-4755-9f1c-b9536e34d381). Der Artikel enthält Informationen dazu, wo LinkedIn-Informationen angezeigt werden, wie [Datenfreigabe und Datenschutz](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400) funktionieren, [wie Konten verbunden werden](https://support.microsoft.com/office/connect-your-linkedin-and-work-or-school-accounts-c7c245f2-fa56-4c9b-ba20-3fceb23c5772) und mehr.

Sie müssen die LinkedIn-Integration für Ihre Benutzer ankündigen und ihnen alle Informationen im Zusammenhang mit [Datenfreigabe und Datenschutz bei der LinkedIn-Integration](https://support.microsoft.com/office/your-data-ae9c08a7-4d06-45b5-a065-320a97bc1400) bereitstellen. 

## <a name="user-consent-for-data-access-in-microsoft-and-linkedin"></a>Benutzereinwilligung zum Datenzugriff in Microsoft und LinkedIn

Daten, auf die von LinkedIn zugegriffen wird, werden nicht dauerhaft in Microsoft-Diensten gespeichert. Daten, auf die von Microsoft zugegriffen wird, werden nicht dauerhaft in LinkedIn gespeichert, außer Kontakten. Microsoft-Kontakte werden in LinkedIn gespeichert, bis Benutzer sie entfernen, wie unter [Löschen importierter Kontakte aus LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377) beschrieben.

Wenn Benutzer ihre Konten verbinden, sind die Informationen und Einblicke aus LinkedIn in einigen Microsoft-Apps verfügbar, z. B. auf der Profilkarte. Benutzer können ebenfalls damit rechnen, dass ihre Netzwerkerfahrung bei LinkedIn verbessert und mit Informationen von Microsoft angereichert wird.
Beim Verbinden ihrer Geschäfts-, Schul- oder Unikonten von LinkedIn und Microsoft haben Benutzer in Ihrer Organisation zwei Möglichkeiten:

* Die Berechtigung für den Zugriff auf Daten aus beiden Konten erteilen. Dies bedeutet, dass sie ihrem Microsoft- oder Geschäftskonto die Berechtigung zum Zugriff auf Daten aus ihrem LinkedIn-Konto erteilen und [ihrem LinkedIn-Konto den Zugriff auf Daten aus ihrem Microsoft-Geschäfts-, -Schul- oder -Unikonto](https://www.linkedin.com/help/linkedin/answer/84077).
* Nur die Berechtigung für den Zugriff auf LinkedIn-Daten von ihrem Microsoft-Geschäfts-, -Schul- oder -Unikonto aus erteilen.

Benutzer können jederzeit Konten trennen und Datenzugriffsberechtigungen entfernen, und [Benutzer können kontrollieren, wie ihr eigenes LinkedIn-Profil angezeigt wird](https://www.linkedin.com/help/linkedin/answer/83), z .B. ob ihr Profil in Microsoft-Apps angezeigt werden kann.

### <a name="linkedin-account-data"></a>LinkedIn-Kontodaten

Wenn Sie Ihre Microsoft- und LinkedIn-Konten verbinden, gestatten Sie LinkedIn, ;Microsoft die folgenden Daten bereitzustellen:

* Profildaten: umfassen LinkedIn-Identität, Kontaktinformationen und die Informationen, die Sie mit anderen in Ihrem [LinkedIn-Profil](https://www.linkedin.com/help/linkedin/answer/15493) teilen.
* Interessendaten: umfassen Interessen auf LinkedIn wie Personen und Themen, denen Sie folgen, Kurse, Gruppen, und Inhalte, die Ihnen gefallen und die Sie teilen.
* Abonnementdaten: umfassen Abonnements von LinkedIn-Anwendungen und -Diensten zusammen mit zugehörigen Daten. 
* Verbindungsdaten: umfassen Ihr [LinkedIn-Netzwerk](https://www.linkedin.com/help/linkedin/answer/110), einschließlich Profile und Kontaktinformationen Ihrer Verbindungen des 1. Grads.

Daten, auf die von LinkedIn zugegriffen wird, werden nicht dauerhaft in Microsoft-Diensten gespeichert. Weitere Informationen zur Verwendung persönlicher Daten durch Microsoft finden Sie in den [Datenschutzbestimmungen von Microsoft](https://privacy.microsoft.com/privacystatement/).

### <a name="microsoft-work-or-school-account-data"></a>Microsoft-Geschäfts-, -Schul- oder -Unikontodaten

Wenn Sie Ihre Microsoft- und LinkedIn-Konten verbinden, gestatten Sie Microsoft, LinkedIn die folgenden Daten bereitzustellen:

* Profildaten: umfassen Informationen wie Ihren Vornamen, Nachnamen, Profilfoto, E-Mail-Adresse, Vorgesetzten und Personen, die Ihnen unterstehen.
* Kalenderdaten: umfassen Besprechungen in Ihren Kalendern, deren Zeiten, Orte und die Kontaktinformationen der Teilnehmer. Informationen zu der Besprechung wie Tagesordnung, Inhalt oder Besprechungstitel sind in den Kalenderdaten nicht enthalten.
* Interessendaten: umfassen die Ihrem Konto zugeordneten Interessen, basierend auf Ihrer Verwendung von Microsoft-Diensten wie Cortana und Bing für Unternehmen.
* Abonnementdaten: umfasst Abonnements, die von Ihrer Organisation für Microsoft-Apps und -Dienste bereitgestellt werden, z. B. Microsoft 365.
* Kontaktdaten: umfasst Kontaktlisten in Outlook, Skype und anderen Microsoft-Kontodiensten, einschließlich der Kontaktinformationen für Personen, mit denen Sie häufig kommunizieren oder zusammenarbeiten. Kontakte werden in regelmäßigen Abständen importiert, gespeichert und von LinkedIn verwendet, z. B. um Verbindungen vorzuschlagen, beim Organisieren von Kontakten zu helfen sowie Aktualisierungen von Kontakten anzuzeigen.

Daten, auf die von Microsoft zugegriffen wird, werden nicht dauerhaft in LinkedIn gespeichert, außer Kontakten. Microsoft-Kontakte werden in LinkedIn gespeichert, bis Benutzer sie entfernen. Weitere Informationen zum [Löschen importierter Kontakte aus LinkedIn](https://www.linkedin.com/help/linkedin/answer/43377).

Weitere Informationen zur Verwendung persönlicher Daten durch LinkedIn finden Sie in den [Datenschutzbestimmungen von LinkedIn](https://www.linkedin.com/legal/privacy-policy). Bei Diensten, Datenübertragungen und Speicherung von LinkedInkönnen Daten aus der Europäischen Union in die Vereinigten Staaten und zurück fließen, wobei Ihr Datenschutz wie in Datenübertragungen in der Europäischen Union beschrieben gewährleistet wird.

## <a name="next-steps"></a>Nächste Schritte

* [LinkedIn in Microsoft-Anwendungen mit Ihrem Geschäfts-, Schul- oder Unikonto](https://www.linkedin.com/help/linkedin/answer/84077)
