---
title: Konfigurieren von Benachrichtigungen und E-Mail-Vorlagen in Azure API Management | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Benachrichtigungen und E-Mail-Vorlagen in Azure API Management konfigurieren.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/02/2018
ms.author: apimpm
ms.openlocfilehash: 2a959c9d131c6aa0bdc99450cf2b6f09a5d8bfa7
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60528375"
---
# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Konfigurieren von Benachrichtigungen und E-Mail-Vorlagen in Azure API Management
API Management bietet die Möglichkeit, Benachrichtigungen über spezifische Ereignisse sowie die E-Mail-Vorlagen zu konfigurieren, die zur Kommunikation mit Administratoren und Entwicklern einer API Management-Instanz verwendet werden. Dieser Artikel beschreibt die Konfiguration von Benachrichtigungen für verfügbare Ereignisse und bietet eine Übersicht über die Konfiguration der E-Mail-Vorlagen für diese Ereignisse.

## <a name="prerequisites"></a>Voraussetzungen

Wenn Sie nicht über eine API Management-Dienstinstanz verfügen, absolvieren Sie den folgenden Schnellstart: [Erstellen einer neuen Azure API Management-Dienstinstanz](get-started-create-service-instance.md)

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="publisher-notifications"> </a>Konfigurieren von Benachrichtigungen

1. Wählen Sie Ihre **API MANAGEMENT**-Instanz aus.
2. Klicken Sie auf **Benachrichtigungen**, um die verfügbaren Benachrichtigungen anzuzeigen.

    ![Publisher Notifications][api-management-publisher-notifications]

    Die folgenden Ereignisse können für Benachrichtigungen konfiguriert werden.

   * **Abonnementanfragen (mit ausstehender Genehmigung)** - Die angegebenen E-Mail-Empfänger und Benutzer erhalten E-Mail-Benachrichtigungen über Abonnementanfragen für API-Anfragen mit ausstehender Genehmigung.
   * **Neue Abonnements** - Die angegebenen E-Mail-Empfänger und Benutzer erhalten E-Mail-Benachrichtigungen über neue API-Produktabonnements.
   * **Anwendungsgalerie-Anfragen** - Die angegebenen E-Mail-Empfänger und Benutzer erhalten E-Mail-Benachrichtigungen, wenn neue Anwendungen in die Anwendungsgalerie übermittelt werden.
   * **BCC** - Die angegebenen E-Mail-Empfänger und Benutzer erhalten BCC-Kopien aller E-Mails, die an Entwickler verschickt werden.
   * **Neues Problem oder Kommentar** - Die angegebenen E-Mail-Empfänger und Benutzer erhalten E-Mail-Benachrichtigungen, wenn neue Probleme oder Kommentare im Entwicklerportal erstellt werden.
   * **Kontoschließung** - Die angegebenen E-Mail-Empfänger und Benutzer erhalten E-Mail-Benachrichtigungen, wenn Konten geschlossen werden.
   * **Kurz vor Abonnement-Kontingent** - Die angegebenen E-Mail-Empfänger und Benutzer erhalten E-Mail-Benachrichtigungen, wenn sich die Abonnementnutzung dem Nutzungskontingent annähert.

     Für jedes Ereignis können Sie bestimmte E-Mail-Empfänger in das Textfeld E-Mail-Adresse eingeben oder Benutzer aus einer Liste auswählen.

3. Geben Sie die E-Mail-Adressen einfach in das Textfeld ein, um die zu benachrichtigenden E-Mail-Adressen zu konfigurieren. Trennen Sie mehrere E-Mail-Adressen mit Kommas.

    ![Benachrichtigungsempfänger][api-management-email-addresses]
4. Klicken Sie auf **Hinzufügen**.

## <a name="email-templates"> </a>Konfigurieren von Benachrichtigungsvorlagen
API Management enthält Benachrichtigungsvorlagen für die E-Mail-Nachrichten, die im Rahmen von Verwaltung und Nutzung des Diensts versendet werden. Die folgenden E-Mail-Vorlagen werden angeboten.

* Übermittlung in Anwendungsgalerie genehmigt
* Entwickler-Abschiedsbrief
* Entwickler-Nutzungskontingent beinahe erreicht
* Benutzer einladen
* Neuer Kommentar zu einem Problem
* Neues Problem erhalten
* Neues Abonnement aktiviert
* Abonnementerneuerung Bestätigung
* Abonnementerneuerung abgelehnt
* Abonnementerneuerung erhalten

Diese Vorlagen können nach Belieben angepasst werden.

Um die E-Mail-Vorlagen für Ihre API Management-Instanz anzuzeigen und zu konfigurieren, klicken Sie auf **Benachrichtigungsvorlagen**.

![E-Mail-Vorlagen][api-management-email-templates]

Jede E-Mail-Vorlage enthält einen Betreff im Nur-Text-Format und einen Text im HTML-Format. Jedes dieser Elemente kann nach Belieben angepasst werden.

![Editor für E-Mail-Vorlagen][api-management-email-template]

Die **Parameter** aus der Liste können in den Betreff oder den Text eingefügt werden und werden beim Versand der E-Mail durch den entsprechenden Wert ersetzt. Um einen Parameter einzufügen, platzieren Sie den Cursor an die gewünschte Stelle, und klicken Sie auf den Pfeil links neben dem Parameternamen.

> [!NOTE] 
> Die Parameter in der Vorschau und beim Testversand werden nicht durch tatsächliche Werte ersetzt.

Klicken Sie auf **Speichern**, um die Änderungen an der E-Mail-Vorlage zu speichern, oder auf **Verwerfen**, um die Änderungen zu verwerfen.
 

[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
