---
title: Echtzeitwarnungen in Azure CDN | Microsoft-Dokumentation
description: Echtzeitwarnungen in Microsoft Azure CDN. Echtzeitwarnungen informieren über die Leistung der Endpunkte in Ihrem CDN-Profil.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 1e85b809-e1a9-4473-b835-69d1b4ed3393
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: e20161147aa16456e31aff2bd3cc6337c3690e89
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60335761"
---
# <a name="real-time-alerts-in-microsoft-azure-cdn"></a>Echtzeitwarnungen in Microsoft Azure CDN
[!INCLUDE [cdn-premium-feature](../../includes/cdn-premium-feature.md)]

## <a name="overview"></a>Übersicht
In diesem Dokument werden Echtzeitwarnungen in Microsoft Azure CDN erläutert. Diese Funktion informiert in Echtzeit über die Leistung der Endpunkte in Ihrem CDN-Profil.  Sie können E-Mail- oder HTTP-Warnungen auf der Grundlage folgender Faktoren einrichten:

* Bandbreite
* Statuscodes
* Cachestatus
* Verbindungen

## <a name="creating-a-real-time-alert"></a>Erstellen einer Echtzeitwarnung
1. Navigieren Sie im [Azure-Portal](https://portal.azure.com)zu Ihrem CDN-Profil.
   
    ![CDN-Profil](./media/cdn-real-time-alerts/cdn-profile-blade.png)
1. Klicken Sie auf dem Blatt „CDN-Profil“ auf die Schaltfläche **Verwalten** .
   
    ![Verwaltungsschaltfläche für CDN-Profile](./media/cdn-real-time-alerts/cdn-manage-btn.png)
   
    Das CDN-Verwaltungsportal wird geöffnet.
3. Zeigen Sie auf die Registerkarte **Analysen** und dann auf das Flyout **Echtzeitstatistiken**.  Klicken Sie auf **Real-Time Alerts**(Echtzeitwarnungen).
   
    ![CDN-Verwaltungsportal](./media/cdn-real-time-alerts/cdn-premium-portal.png)
   
    Eine Liste mit ggf. bereits vorhandenen Warnungskonfigurationen wird angezeigt.
4. Klicken Sie auf die Schaltfläche **Warnung hinzufügen** .
   
    ![Blatt „Warnung hinzufügen“](./media/cdn-real-time-alerts/cdn-add-alert.png)
   
    Ein Formular zum Erstellen einer neuen Warnung wird angezeigt.
   
    ![Formular „Neue Warnung“](./media/cdn-real-time-alerts/cdn-new-alert.png)
5. Wenn diese Warnung nach dem Klicken auf **Speichern** aktiviert werden soll, aktivieren Sie das Kontrollkästchen **Warnung aktiviert**.
6. Geben Sie im Feld **Name** einen aussagekräftigen Namen für die Warnung ein.
7. Wählen Sie in der Dropdownliste **Medientyp** die Option **HTTP Large Object** aus.
   
    ![Medientyp mit ausgewählter Option „HTTP Large Object“](./media/cdn-real-time-alerts/cdn-http-large.png)
   
   > [!IMPORTANT]
   > Für **Medientyp** muss unbedingt **HTTP Large Object** ausgewählt werden.  Die anderen Optionen werden von **Azure CDN von Verizon**nicht verwendet.  Bei Verwendung einer anderen Option als **HTTP Large Object** wird die Warnung niemals ausgelöst.
   > 
   > 
8. Erstellen Sie einen zu überwachenden **Ausdruck**. Wählen Sie hierzu Werte für **Metrik**, **Operator** und **Triggerwert** aus.
   
   * Wählen Sie unter **Metrik**die Art der Bedingung aus, die Sie überwachen möchten.  **Bandwidth Mbps** (Bandbreite MBit/s) ist die Bandbreitenauslastung in Megabits pro Sekunde.  **Verbindungen insgesamt** ist die Anzahl gleichzeitiger HTTP-Verbindungen mit unseren Edgeservern.  Definitionen der verschiedenen Cachestatusoptionen und Statuscodes finden Sie unter [Azure CDN Cache Status Codes (in englischer Sprache)](/previous-versions/azure/mt759237(v=azure.100)) sowie unter [Azure CDN HTTP Status Codes (in englischer Sprache)](/previous-versions/azure/mt759238(v=azure.100)).
   * **Operator** ist der mathematische Operator, der die Beziehung zwischen Metrik und Triggerwert herstellt.
   * **Triggerwert** ist der Schwellenwert, ab dem eine Benachrichtigung gesendet wird.
     
     Im folgenden Beispiel gibt der erstellte Ausdruck an, dass eine Benachrichtigung gesendet wird, wenn die Anzahl der 404-Statuscodes größer als 25 ist.
     
     ![Echtzeitwarnung mit Beispielausdruck](./media/cdn-real-time-alerts/cdn-expression.png)
9. Geben Sie unter **Intervall**an, wie häufig der Ausdruck ausgewertet werden soll.
10. Wählen Sie in der Dropdownliste **Notify on** (Benachrichtigungszeitpunkt) aus, wann Sie benachrichtigt werden möchten, wenn der Ausdruck erfüllt ist.
    
    * **Condition Start** (Bedingungsbeginn) gibt an, dass eine Benachrichtigung gesendet wird, wenn die angegebene Bedingung erstmals erfüllt wird.
    * **Condition End** (Bedingungsende) gibt an, dass eine Benachrichtigung gesendet wird, wenn die angegebene Bedingung nicht mehr erfüllt wird. Diese Benachrichtigung kann nur ausgelöst werden, wenn unser Netzwerküberwachungssystem zuvor erkannt hat, dass die angegebene Bedingung aufgetreten ist.
    * **Continuous** (Fortlaufend) gibt an, dass immer dann eine Benachrichtigung gesendet wird, wenn das Netzwerküberwachungssystem die angegebene Bedingung erkennt. Bedenken Sie, dass pro Intervall immer nur einmal geprüft wird, ob die angegebene Bedingung erfüllt ist.
    * **Condition Start and End** (Bedingungsbeginn und -ende) gibt an, dass eine Benachrichtigung gesendet wird, wenn die angegebene Bedingung erstmals erfüllt wird, und eine weitere Benachrichtigung, wenn die Bedingung nicht mehr erfüllt wird.
1. Wenn Sie Benachrichtigungen per E-Mail erhalten möchten, aktivieren Sie das Kontrollkästchen **Notify by Email** (Per E-Mail benachrichtigen).  
    
    ![Formular für E-Mail-Benachrichtigungen](./media/cdn-real-time-alerts/cdn-notify-email.png)
    
    Geben Sie im Feld **An** die E-Mail-Adresse ein, an die die Benachrichtigungen gesendet werden sollen. Für **Betreff** und **Text** können Sie entweder die Standardeinstellung beibehalten oder die Nachricht mithilfe der Liste **verfügbarer Schlüsselwörter** anpassen, sodass beim Senden der Nachricht automatisch Warnungsdaten eingefügt werden.
    
    > [!NOTE]
    > Klicken Sie zum Testen der E-Mail-Benachrichtigung nach dem Speichern der Warnungskonfiguration auf die Schaltfläche **Testbenachrichtigung** .
    > 
    > 
12. Wenn Benachrichtigungen auf einem Webserver veröffentlicht werden sollen, aktivieren Sie das Kontrollkästchen **Notify by HTTP Post** (Per HTTP POST benachrichtigen).
    
    ![Formular für HTTP Post-Benachrichtigungen](./media/cdn-real-time-alerts/cdn-notify-http.png)
    
    Geben Sie im Feld **URL** die URL ein, unter der die HTTP-Nachricht veröffentlicht werden soll. Geben Sie im Textfeld **Header** die HTTP-Header ein, die in der Anforderung gesendet werden sollen.  Unter **Text** können Sie die Nachricht mithilfe der Liste **verfügbarer Schlüsselwörter** anpassen, sodass beim Senden der Nachricht automatisch Warnungsdaten eingefügt werden.  **Header** und **Text** werden standardmäßig auf eine XML-Nutzlast festgelegt, die der Angabe im folgenden Beispiel ähnelt:
    
    ```
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/">
        <![CDATA[Expression=Status Code : 404 per second > 25&Metric=Status Code : 404 per second&CurrentValue=[CurrentValue]&NotificationCondition=Condition Start]]>
    </string>
    ```
    
    > [!NOTE]
    > Klicken Sie zum Testen der HTTP Post-Benachrichtigung nach dem Speichern der Warnungskonfiguration auf die Schaltfläche **Testbenachrichtigung** .
    > 
    > 
13. Klicken Sie auf die Schaltfläche **Speichern** , um die Warnungskonfiguration zu speichern.  Falls Sie in Schritt 5 das Kontrollkästchen **Warnung aktiviert** aktiviert haben, ist die Warnung nun aktiv.

## <a name="next-steps"></a>Nächste Schritte
* Analysieren Sie [Echtzeitstatistiken in Azure CDN](cdn-real-time-stats.md)
* Informieren Sie sich ausführlicher über [erweiterte HTTP-Berichte](cdn-advanced-http-reports.md)
* Analyse von [Verwendungsmustern](cdn-analyze-usage-patterns.md)

