---
title: Probleme beim Hinzufügen einer Azure AD-Kataloganwendung | Microsoft-Dokumentation
description: Informationen zu den häufig auftretenden Problemen beim Hinzufügen von Azure AD-Kataloganwendungen und zur Behebung dieser Probleme
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b2b42880f99f3e87d75854166047896860f9eb14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65784426"
---
# <a name="problem-adding-an-azure-ad-gallery-application"></a>Probleme beim Hinzufügen einer Azure AD-Kataloganwendung

In diesem Artikel finden Sie Informationen zu den häufig auftretenden Problemen beim Hinzufügen von Azure AD-Kataloganwendungen sowie zu deren Behebung.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Nach dem Klicken auf die Schaltfläche „Hinzufügen“ wurde meine Anwendung erst nach einiger Zeit angezeigt.

Unter bestimmten Umständen kann es 1 bis 2 Minuten (manchmal auch länger) dauern, bis eine Anwendung angezeigt wird, nachdem sie Ihrem Verzeichnis hinzugefügt wurde. Auch wenn dies nicht der normalen erwarteten Leistung entspricht, können Sie sehen, dass die Hinzufügung der Anwendung durchgeführt wird, indem Sie auf das Symbol **Benachrichtigungen** (Glockensymbol) rechts oben im [Azure-Portal](https://portal.azure.com/) klicken und nach einer Benachrichtigung des Typs **In Bearbeitung** oder **Abgeschlossen** mit der Bezeichnung **Anwendung wird hinzugefügt** suchen.

Wenn die Anwendung nicht hinzugefügt wird oder wenn beim Klicken auf die Schaltfläche **Hinzufügen** ein Fehler auftritt, wird eine **Benachrichtigung** mit einem **Fehlerstatus** angezeigt. Wenn Sie weitere Details zu dem Fehler benötigen oder diese einem Supporttechniker weitergeben möchten, können Sie ausführliche Informationen zum Fehler anzeigen, indem Sie die Schritte im Abschnitt [Anzeigen von Details einer Portalbenachrichtigung](#how-to-see-the-details-of-a-portal-notification) ausführen.

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Nach dem Klicken auf die Schaltfläche „Hinzufügen“ wurde meine Anwendung nicht angezeigt

In manchen Fällen führen vorübergehende Probleme, Netzwerkprobleme oder ein Fehler dazu, dass eine Anwendung nicht hinzugefügt wird. Dies können Sie feststellen, wenn Sie rechts oben im Azure-Portal auf das Symbol **Benachrichtigungen** (Glockensymbol) klicken und ein rotes (!)-Symbol neben der Benachrichtigung **Anwendung wird hinzugefügt** angezeigt wird. Dies gibt an, dass beim Erstellen der Anwendung ein Fehler aufgetreten ist.

Wenn beim Klicken auf die Schaltfläche **Hinzufügen** ein Fehler auftritt, wird eine **Benachrichtigung** mit einem **Fehlerstatus** angezeigt. Wenn Sie weitere Details zu dem Fehler benötigen oder diese einem Supporttechniker weitergeben möchten, können Sie ausführliche Informationen zum Fehler anzeigen, indem Sie die Schritte im Abschnitt [Anzeigen von Details einer Portalbenachrichtigung](#how-to-see-the-details-of-a-portal-notification) ausführen.

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Ich weiß nicht, wie die Anwendung nach dem Hinzufügen eingerichtet wird.

Für allgemeine Informationen zu Anwendungen bietet sich der Artikel [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) als Ausgangspunkt an.

Zudem finden Sie weitere Informationen zur Verwendung und Funktionsweise des einmaligen Anmeldens mit Azure AD in der [Dokumentbibliothek zu Azure AD-Anwendungen](https://docs.microsoft.com/azure/active-directory/active-directory-apps-index).

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Anzeigen von Details einer Portalbenachrichtigung

Sie können die Details von Portalbenachrichtigungen anzeigen, indem Sie folgende Schritte ausführen:

1.  Wählen Sie im Azure-Portal in der rechten oberen Ecke das Symbol **Benachrichtigungen** (Glockensymbol) aus.

2.  Wählen Sie eine Benachrichtigung mit einem **Fehlerstatus** aus (mit einem roten (!) neben dem Namen).

    >[!NOTE]
    >Sie können auf keine Benachrichtigungen klicken, die den Status **Erfolgreich** oder **In Bearbeitung** aufweisen.
    >
    >

4.  Die Informationen unter **Benachrichtigungsdetails** helfen Ihnen, das Problem besser zu verstehen.

5.  Wenn Sie weitere Unterstützung benötigen, können Sie diese Informationen auch einem Supporttechniker oder der Produktgruppe mitteilen, um Hilfe bei Ihrem Problem zu erhalten.

6.  Klicken Sie auf das **Symbol** **Kopieren** rechts neben dem Textfeld **Fehler kopieren**, um alle Benachrichtigungsdetails zu kopieren und einem Support- oder Produktgruppentechniker mitzuteilen.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Erhalten von Unterstützung durch Senden von Benachrichtigungsdetails an einen Supporttechniker

Es ist sehr wichtig, dass Sie dem Supporttechniker **alle unten aufgelisteten Details** mitteilen, wenn Sie Hilfe benötigen, damit dieser schnell helfen kann. Hierfür können Sie ganz einfach **einen Screenshot erstellen**, oder Sie klicken auf das Symbol **Fehler kopieren** rechts neben dem Textfeld **Fehler kopieren**.

## <a name="notification-details-explained"></a>Erläuterung der Benachrichtigungsdetails

Weitere Informationen über Benachrichtigungen finden Sie in den folgenden Abschnitten.

### <a name="essential-notification-items"></a>Grundlegende Benachrichtigungselemente

- **Titel:** der beschreibende Titel der Benachrichtigung

  * Beispiel: **Anwendungsproxyeinstellungen**

- **Beschreibung:** Beschreibung des Ergebnisses des Vorgangs

  -   Beispiel: **Die eingegebene interne URL wird bereits von einer anderen Anwendung verwendet**

- **Benachrichtigungs-ID**: die eindeutige ID der Benachrichtigung

  -   Beispiel: **clientNotification-2adbfc06-2073-4678-a69f-7eb78d96b068**

- **Clientanforderungs-ID**: die spezifische Anforderungs-ID, die vom Browser erstellt wurde

  -   Beispiel: **302fd775-3329-4670-a9f3-bea37004f0bc**

- **Zeitstempel (UTC):** der Zeitstempel beim Auftreten der Benachrichtigung in UTC

  -   Beispiel: **2017-03-23T19:50:43.7583681Z**

- **Interne Transaktions-ID**: die interne ID, über die wir den Fehler in unseren Systemen suchen können

  -   Beispiel: **71a2f329-ca29-402f-aa72-bc00a7aca603**

- **UPN:** der Benutzer, der den Vorgang durchgeführt hat

  -   Beispiel: **tperkins\@f128.info**

- **Mandanten-ID:** die eindeutige ID des Mandanten, dem der Benutzer angehört, der den Vorgang durchgeführt hat

  -   Beispiel: **7918d4b5-0442-4a97-be2d-36f9f9962ece**

- **Benutzerobjekt-ID:** die eindeutige ID des Benutzers, der den Vorgang durchgeführt hat

  -   Beispiel: **17f84be4-51f8-483a-b533-383791227a99**

### <a name="detailed-notification-items"></a>Detaillierte Benachrichtigungselemente

-   **Anzeigename:** **(kann leer sein)** ein detaillierterer Anzeigename für den Fehler

    -   Beispiel: **Anwendungsproxyeinstellungen**

-   **Status:** der spezifische Status der Benachrichtigung

    -   Beispiel: **Fehler**

-   **Objekt-ID**: **(kann leer sein)** die Objekt-ID, für die der Vorgang durchgeführt wurde

    -   Beispiel: **8e08161d-f2fd-40ad-a34a-a9632d6bb599**

-   **Details:** detaillierte Beschreibung des Ergebnisses des Vorgangs

    -   Beispiel: **Interne URL `https://bing.com/` ist ungültig, da sie bereits verwendet wird**

-   **Fehler kopieren**: Klicken Sie auf das **Kopiersymbol** rechts neben dem Textfeld **Fehler kopieren**, um alle Benachrichtigungsdetails zu kopieren und einem Support- oder Produktgruppentechniker 
-   mitzuteilen.

    -   Beispiel: ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```

