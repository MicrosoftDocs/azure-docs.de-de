---
title: Behandeln gängiger Probleme beim Hinzufügen oder Entfernen einer Anwendung in Azure Active Directory
description: Behandeln gängiger Probleme, die beim Hinzufügen oder Entfernen einer App in Azure Active Directory auftreten.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 09/11/2018
ms.author: kenwith
ms.openlocfilehash: 714cbb06506cc3e0dffe74007712d9cbad072284
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655067"
---
# <a name="troubleshoot-common-problem-adding-or-removing-an-application-to-azure-active-directory"></a>Behandeln gängiger Probleme beim Hinzufügen oder Entfernen einer Anwendung in Azure Active Directory
In diesem Artikel werden häufige Probleme beschrieben, die beim Hinzufügen oder Entfernen einer App in Azure Active Directory auftreten.

## <a name="i-clicked-the-add-button-and-my-application-took-a-long-time-to-appear"></a>Nach dem Klicken auf die Schaltfläche „Hinzufügen“ wurde meine Anwendung erst nach einiger Zeit angezeigt.
Unter bestimmten Umständen kann es 1 bis 2 Minuten (manchmal auch länger) dauern, bis eine Anwendung angezeigt wird, nachdem sie Ihrem Verzeichnis hinzugefügt wurde. Auch wenn dies nicht der normalen erwarteten Leistung entspricht, können Sie sehen, dass die Hinzufügung der Anwendung durchgeführt wird, indem Sie auf das Symbol **Benachrichtigungen** (Glockensymbol) rechts oben im [Azure-Portal](https://portal.azure.com/) klicken und nach einer Benachrichtigung des Typs **In Bearbeitung** oder **Abgeschlossen** mit der Bezeichnung **Anwendung wird hinzugefügt** suchen.

Wenn die Anwendung nicht hinzugefügt wird oder wenn beim Klicken auf die Schaltfläche **Hinzufügen** ein Fehler auftritt, wird eine **Benachrichtigung** mit einem **Fehlerstatus** angezeigt. Wenn Sie weitere Details zu dem Fehler benötigen oder diese einem Supporttechniker weitergeben möchten, können Sie ausführliche Informationen zum Fehler anzeigen, indem Sie die Schritte im Abschnitt [Anzeigen von Details einer Portalbenachrichtigung](#how-to-see-the-details-of-a-portal-notification) ausführen.

## <a name="i-clicked-the-add-button-and-my-application-didnt-appear"></a>Nach dem Klicken auf die Schaltfläche „Hinzufügen“ wurde meine Anwendung nicht angezeigt
In manchen Fällen führen vorübergehende Probleme, Netzwerkprobleme oder ein Fehler dazu, dass eine Anwendung nicht hinzugefügt wird. Dies können Sie feststellen, wenn Sie rechts oben im Azure-Portal auf das Symbol **Benachrichtigungen** (Glockensymbol) klicken und ein rotes (!)-Symbol neben der Benachrichtigung **Anwendung wird hinzugefügt** angezeigt wird. Dies gibt an, dass beim Erstellen der Anwendung ein Fehler aufgetreten ist.

Wenn beim Klicken auf die Schaltfläche **Hinzufügen** ein Fehler auftritt, wird eine **Benachrichtigung** mit einem **Fehlerstatus** angezeigt. Wenn Sie weitere Details zu dem Fehler benötigen oder diese einem Supporttechniker weitergeben möchten, können Sie ausführliche Informationen zum Fehler anzeigen, indem Sie die Schritte im Abschnitt [Anzeigen von Details einer Portalbenachrichtigung](#how-to-see-the-details-of-a-portal-notification) ausführen.

## <a name="i-dont-know-how-to-set-up-my-application-once-ive-added-it"></a>Ich weiß nicht, wie die Anwendung nach dem Hinzufügen eingerichtet wird.
Für allgemeine Informationen zu Anwendungen bietet sich der Artikel [Liste der Tutorials zur Integration von SaaS-Apps in Azure Active Directory](../saas-apps/tutorial-list.md) als Ausgangspunkt an.

Zudem finden Sie weitere Informationen zur Verwendung und Funktionsweise des einmaligen Anmeldens mit Azure AD in der [Dokumentbibliothek zu Azure AD-Anwendungen](./what-is-application-management.md).

## <a name="i-want-to-delete-an-application-but-the-delete-button-is-disabled"></a>Ich möchte eine Anwendung löschen, aber die Schaltfläche „Löschen“ ist deaktiviert

Die Schaltfläche „Löschen“ ist in den folgenden Fällen deaktiviert:

- Bei Unternehmensanwendungen, wenn Sie über keine der folgenden Rollen verfügen: Globaler Administrator, Cloudanwendungsadministrator, Anwendungsadministrator oder Besitzer des Dienstprinzipals.

- Bei Microsoft-Anwendungen können Sie diese nicht von der Benutzeroberfläche löschen, unabhängig von Ihrer Rolle

- Bei Dienstprinzipalen, die einer verwalteten Identität entsprechen. Dienstprinzipale für verwaltete Identitäten können auf dem Blatt „Enterprise apps“ (Unternehmens-Apps) nicht gelöscht werden. Sie müssen zur Azure-Ressource wechseln, um dies zu verwalten. Weitere Informationen zu verwalteten Identitäten finden Sie [hier](../managed-identities-azure-resources/overview.md).

## <a name="how-to-see-the-details-of-a-portal-notification"></a>Anzeigen von Details einer Portalbenachrichtigung
Sie können die Details von Portalbenachrichtigungen anzeigen, indem Sie folgende Schritte ausführen:
1.  Wählen Sie im Azure-Portal in der rechten oberen Ecke das Symbol **Benachrichtigungen** (Glockensymbol) aus.
2.  Wählen Sie eine Benachrichtigung mit einem **Fehlerstatus** aus (mit einem roten (!) neben dem Namen).
    >[!NOTE]
    >Sie können auf keine Benachrichtigungen klicken, die den Status **Erfolgreich** oder **In Bearbeitung** aufweisen.
4.  Die Informationen unter **Benachrichtigungsdetails** helfen Ihnen, das Problem besser zu verstehen.
5.  Wenn Sie weitere Unterstützung benötigen, können Sie diese Informationen auch einem Supporttechniker oder der Produktgruppe mitteilen, um Hilfe bei Ihrem Problem zu erhalten.
6.  Wählen Sie das **Kopiersymbol** rechts neben dem Textfeld **Fehler kopieren** aus, um sämtliche Benachrichtigungsdetails zu kopieren und an einen Support- oder Produktgruppentechniker zu senden.

## <a name="how-to-get-help-by-sending-notification-details-to-a-support-engineer"></a>Erhalten von Unterstützung durch Senden von Benachrichtigungsdetails an einen Supporttechniker
Es ist wichtig, dass Sie dem Supporttechniker **alle unten aufgelisteten Details** mitteilen, wenn Sie Hilfe benötigen. Nur so erhalten Sie schnellstmöglich Unterstützung. **Erstellen Sie einen Screenshot**, oder wählen Sie das Symbol **Fehler kopieren** rechts neben dem Textfeld **Fehler kopieren** aus.

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
-   **Anzeigename:****(kann leer sein)** ein detaillierterer Anzeigename für den Fehler
    -   Beispiel: **Anwendungsproxyeinstellungen**
-   **Status:** der spezifische Status der Benachrichtigung
    -   Beispiel: **Fehler**
-   **Objekt-ID**: **(kann leer sein)** die Objekt-ID, für die der Vorgang durchgeführt wurde
    -   Beispiel: **8e08161d-f2fd-40ad-a34a-a9632d6bb599**
-   **Details:** detaillierte Beschreibung des Ergebnisses des Vorgangs
    -   Beispiel: **Interne URL `https://bing.com/` ist ungültig, da sie bereits verwendet wird**
-   **Fehler kopieren**: Wählen Sie das **Kopiersymbol** rechts neben dem Textfeld **Fehler kopieren** aus, um sämtliche Benachrichtigungsdetails zu kopieren und an einen Support- oder Produktgruppenmitarbeiter zu senden 
-   mitzuteilen.
    -   Beispiel: ```{"errorCode":"InternalUrl\_Duplicate","localizedErrorDetails":{"errorDetail":"Internal url 'https://google.com/' is invalid since it is already in use"},"operationResults":\[{"objectId":null,"displayName":null,"status":0,"details":"Internal url 'https://bing.com/' is invalid since it is already in use"}\],"timeStampUtc":"2017-03-23T19:50:26.465743Z","clientRequestId":"302fd775-3329-4670-a9f3-bea37004f0bb","internalTransactionId":"ea5b5475-03b9-4f08-8e95-bbb11289ab65","upn":"tperkins@f128.info","tenantId":"7918d4b5-0442-4a97-be2d-36f9f9962ece","userObjectId":"17f84be4-51f8-483a-b533-383791227a99"}```