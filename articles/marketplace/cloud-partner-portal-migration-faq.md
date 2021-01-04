---
title: 'Häufig gestellte Fragen zur Umstellung vom Cloud-Partnerportal auf Partner Center: Kommerzieller Marketplace von Microsoft'
description: Enthält Antworten auf häufig gestellte Fragen zur Umstellung von Angeboten vom Cloud-Partnerportal auf Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: 8346edd0f6016f1751a392213e5d6321f875baf2
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96455654"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>Häufig gestellte Fragen zur Umstellung vom Cloud-Partnerportal auf Partner Center

Das Cloud-Partnerportal wurde auf Partner Center umgestellt. Partner Center verfügt über eine optimierte integrierte Benutzeroberfläche für die Veröffentlichung neuer Angebote bei [Microsoft AppSource](https://appsource.microsoft.com/) oder auf dem [Azure Marketplace](https://azuremarketplace.microsoft.com/) und für die Verwaltung vorhandener Angebote, die aus dem Cloud-Partnerportal migriert wurden. Alle Funktionen des Cloud-Partnerportals sind auch in Partner Center verfügbar. Dieser Artikel enthält häufig gestellte Fragen zu diesem Thema.

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>Was bedeutet die Umstellung auf Partner Center für mich?

Sie können im Partner Center weiterarbeiten:

| Bereich<img src="" width=200px> | Änderungen |
| --- | --- |
| Konto | Sie müssen kein neues Partner Center-Konto erstellen, und Sie können Ihre vorhandenen Anmeldeinformationen für das Cloud-Partnerportal verwenden, um sich an Partner Center anzumelden. In Partner Center verwalten Sie nun Ihr Konto, die Benutzer, die Berechtigungen und die Abrechnung. Der Herausgebervertrag und die Informationen zum Unternehmensprofil werden zu Ihrem neuen Partner Center-Konto migriert. Dies gilt auch für alle Auszahlungsprofilinformationen, Benutzerkonten und Berechtigungen sowie für die aktiven Angebote. Weitere Informationen finden Sie unter [Verwalten Ihres Kontos im kommerziellen Marketplace in Partner Center](partner-center-portal/manage-account.md). |
| Benutzerumgebung für die Veröffentlichung und Verwaltung von Angeboten | Wir haben Ihre Angebotsdaten aus dem Cloud-Partnerportal in Partner Center verschoben. Sie greifen auf Ihre Angebote nun in Partner Center zu und können die verbesserte Benutzerumgebung und eine intuitive Benutzeroberfläche verwenden. Informieren Sie sich über das [Aktualisieren eines bestehenden Angebots im kommerziellen Marketplace](partner-center-portal/update-existing-offer.md). |
| Verfügbarkeit Ihrer Angebote auf dem kommerziellen Marketplace | Keine Änderungen. Wenn Ihr Angebot im kommerziellen Marketplace live geschaltet ist, bleibt dieser Livezustand unverändert. |
| Neue Käufe und Bereitstellungen | Keine Änderungen. Ihre Kunden können Ihre Angebote ohne Unterbrechung weiter erwerben und bereitstellen. |
| Auszahlungen | Alle Auszahlungen für Käufe und Bereitstellungen werden wie bisher für Sie durchgeführt. Erfahren Sie mehr zum [Zahlungserhalt im kommerziellen Marketplace](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context). |
| API-Integrationen mit vorhandenen [Cloud-Partnerportal-APIs](cloud-partner-portal-api-overview.md) | Vorhandene Cloud-Partnerportal-APIs werden weiterhin unterstützt, und Ihre vorhandenen Integrationen funktionieren. Weitere Informationen hierzu finden Sie unter [Werden die Cloud-Partnerportal-REST-APIs nach der Migration unterstützt?](#are-the-cloud-partner-portal-rest-apis-still-supported). |
| Analytics | Sie können mit dem Überwachen der Verkäufe, Auswerten der Leistung und Optimieren Ihrer Angebote auf dem kommerziellen Marketplace fortfahren, indem Sie die Analysedaten in Partner Center anzeigen. Analyseberichte werden im Cloud-Partnerportal (CPP) und in Partner Center auf unterschiedliche Weise angezeigt. Beispielsweise verfügt **Verkäufer-Insights** in CPP über die Registerkarte **Bestellungen und Nutzung**, auf der Daten für nutzungsbasierte und nicht nutzungsbasierte Angebote angezeigt werden. Im Partner Center enthält die Seite **Bestellungen** hingegen eine separate Registerkarte für SaaS-Angebote. Weitere Informationen finden Sie unter [Zugreifen auf Analysen für den kommerziellen Marketplace in Partner Center](partner-center-portal/analytics.md). |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>Muss ich ein neues Konto erstellen, um meine Angebote in Partner Center verwalten zu können?

Nein. Ihr Konto wird beibehalten. Dies bedeutet, dass Sie als bestehender Partner Ihre vorhandenen Anmeldeinformationen für Ihr Cloud-Partnerportal-Konto nutzen können, um sich bei Partner Center anzumelden. Erstellen Sie kein neues Konto, da alle im Cloud-Partnerportal erstellten Angebote, die nach Partner Center verschoben wurden, sonst nicht richtig zugeordnet werden.

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Welche Seiten in Partner Center entsprechen den Seiten, die ich im Cloud-Partnerportal verwendet habe?

Unten sind Partner Center-Links für Seiten angegeben, die im Cloud-Partnerportal häufig verwendet werden. Wenn Sie die Cloud-Partnerportal-Links als Lesezeichen gespeichert haben, sollten Sie sie aktualisieren.

| Seite mit dem Cloudpartnerportal <img src="" width=100px>| Link auf Cloud-Partnerportal-Seite | Link auf Partner Center-Seite |
| --- | --- | --- |
| Seite „Alle Angebote“ | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| Seite „Alle Herausgeber“ | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| Herausgeberprofil | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| Seite „Benutzer“ | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| Seite „Verlauf“ | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | Die Funktion „Verlauf“ wird in Partner Center noch nicht unterstützt. |
| Dashboard „Insights“ | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| Auszahlungsbericht | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="payout-report-differences"></a>Unterschiede zwischen Auszahlungsberichten

Bei Auszahlungsberichten gibt es zwischen dem außer Betrieb genommenen Cloud-Partnerportal und dem aktuellen Partner Center folgende Unterschiede:

| Cloudpartnerportal | Partner Center |
| --- | --- |
| **Link**: https://cloudpartner.azure.com/ | **Link**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory und https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navigation**: In Insights Payout bereitgestellte Auszahlungsberichte | **Navigation**: In Partner Center bereitgestellte Auszahlungsberichte – Auszahlungssymbol |
| **Bereich:**<ul><li>Angezeigt wird die Transaktion pro Position, und zwar für „Inkasso in Bearbeitung“, „Eingezogen“ und „Bezahlt“.</li><li>Bericht: Enthält nach dem Erstellen der Bestellung alle Positionen (einschließlich „Inkasso in Bearbeitung“ und „Abrechnung in Bearbeitung“) sowie den Inkassostatus und die Positionen, die noch nicht für eine Zahlung qualifiziert sind.</li></ul> | **Bereich:**<ul><li>Zeigt die Positionen, sobald sie als qualifizierte Einnahmen betrachtet werden.</li><li>Die Kundenzahlung geht zuerst an Microsoft. Anschließend können die ISVs dann sehen, wie der Auszahlungsbericht gestartet wird.</li><li>Im Auszahlungsbericht werden „Inkasso in Bearbeitung“ und „Abrechnung in Bearbeitung“ nicht angezeigt.</li></ul> |
| **Transaktion nicht auszahlungsbereit**: Abrechnung in Bearbeitung | **Transaktion nicht auszahlungsbereit**: Nächste voraussichtliche Zahlung: Die Auszahlung befindet sich im Status „Nicht verarbeitet“. |
| **Auszahlungsstatus**: – | **Auszahlungsstatus**:<ul><li>Nicht verarbeitet: Die Einnahme ist für eine Auszahlung qualifiziert.</li><li>Anstehend: Die Einnahme wird in die nächste monatliche Auszahlung an den Herausgeber eingeschlossen.</li><li>Gesendet: Die Zahlung wurde an Ihre Bank gesendet.</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>Was passiert mit den Angeboten, die ich im Cloud-Partnerportal veröffentlicht habe?

Die Angebote wurden nach Partner Center verschoben und sind für Sie zugänglich, nachdem Sie sich bei Partner Center angemeldet haben (mit Ausnahme von Angeboten für „Verwalteter Dynamics NAV-Dienst“ und „Cortana Intelligence“). Wenn sich Ihr Angebot auf dem kommerziellen Marketplace im Livezustand befunden hat, wird dieser Zustand beibehalten, und Ihre Kunden können es ohne Unterbrechung erwerben und bereitstellen. Ausführlichere Informationen finden Sie unter der nächsten Frage: **Welche Angebote wurden nach Partner Center verschoben?** .

## <a name="what-offers-were-moved-to-partner-center"></a>Welche Angebote wurden nach Partner Center verschoben?

Alle Angebotstypen, die bisher im Cloud-Partnerportal unterstützt wurden, werden auch in Partner Center unterstützt – mit Ausnahme der Angebote für „Verwalteter Dynamics NAV-Dienst“ und „Cortana Intelligence“.

Für die in Partner Center unterstützten Angebotstypen wurden alle Angebote unabhängig von ihrem Status verschoben. Dies gilt auch für Angebote mit dem Status „Entwurf“, „Entfernt“ und „Vorschau“.

| Angebotstyp <img src="" width=150px>| Nach Partner Center verschoben? <img src="" width=100px>| Nächste Schritte |
| --- | --- | --- |
| SaaS | Ja | Melden Sie sich bei Partner Center an, um neue Angebote zu erstellen und Angebote, die im Cloud-Partnerportal erstellt wurden, zu verwalten. Weitere Informationen finden Sie unter [Planen eines SaaS-Angebots im kommerziellen Marketplace](plan-saas-offer.md). |
| Virtual Machine | Ja | Melden Sie sich bei Partner Center an, um neue Angebote zu erstellen und Angebote, die im Cloud-Partnerportal erstellt wurden, zu verwalten. Weitere Informationen finden Sie unter [Planen eines VM-Angebots](marketplace-virtual-machines.md). |
| Azure-Anwendung | Ja | Melden Sie sich bei Partner Center an, um neue Angebote zu erstellen und Angebote, die im Cloud-Partnerportal erstellt wurden, zu verwalten. Weitere Informationen finden Sie unter [Erstellen eines Azure-Anwendungsangebots](create-new-azure-apps-offer.md). |
| Dynamics 365 Business Central | Ja | Melden Sie sich bei Partner Center an, um neue Angebote zu erstellen und Angebote, die im Cloud-Partnerportal erstellt wurden, zu verwalten. Weitere Informationen finden Sie unter [Erstellen eines Angebots für Dynamics 365 Business Central](partner-center-portal/create-new-business-central-offer.md). |
| Dynamics 365 for Customer Engagement & PowerApps | Ja | Melden Sie sich bei Partner Center an, um neue Angebote zu erstellen und Angebote, die im Cloud-Partnerportal erstellt wurden, zu verwalten. Weitere Informationen finden Sie unter [Erstellen eines Angebots für Dynamics 365 for Customer Engagement und PowerApps](partner-center-portal/create-new-customer-engagement-offer.md). |
| Dynamics 365 for Operations | Ja | Melden Sie sich bei Partner Center an, um neue Angebote zu erstellen und Angebote, die im Cloud-Partnerportal erstellt wurden, zu verwalten. Weitere Informationen finden Sie unter [Erstellen eines Angebots für Dynamics 365 for Operations](partner-center-portal/create-new-operations-offer.md). |
| Power BI-App | Ja | Melden Sie sich bei Partner Center an, um neue Angebote zu erstellen und Angebote, die im Cloud-Partnerportal erstellt wurden, zu verwalten. Weitere Informationen finden Sie unter [Erstellen einer Power BI-App für Microsoft AppSource](partner-center-portal/create-power-bi-app-offer.md). |
| IoT Edge-Modul | Ja | Melden Sie sich bei Partner Center an, um neue Angebote zu erstellen und Angebote, die im Cloud-Partnerportal erstellt wurden, zu verwalten. Weitere Informationen finden Sie unter [Erstellen, Konfigurieren und Veröffentlichen eines IoT Edge-Modulangebots im Azure Marketplace](partner-center-portal/azure-iot-edge-module-creation.md). |
| Container | Ja | Melden Sie sich bei Partner Center an, um neue Angebote zu erstellen und Angebote, die im Cloud-Partnerportal erstellt wurden, zu verwalten. Weitere Informationen finden Sie unter [Erstellen eines Azure-Containerangebots](./create-azure-container-offer.md). |
| Beratungsdienst | Ja | Melden Sie sich bei Partner Center an, um neue Angebote zu erstellen und Angebote, die im Cloud-Partnerportal erstellt wurden, zu verwalten. Weitere Informationen finden Sie unter [Erstellen eines Beratungsdienstangebots](./create-consulting-service-offer.md). |
| Verwalteter Dienst | Ja | Melden Sie sich bei Partner Center an, um neue Angebote zu erstellen und Angebote, die im Cloud-Partnerportal erstellt wurden, zu verwalten. Weitere Informationen finden Sie unter [Erstellen eines Angebots für einen verwalteten Dienst](partner-center-portal/create-new-managed-service-offer.md). |
| Verwalteter Dynamics NAV-Dienst | Nein | Microsoft hat „Verwalteter Dynamics NAV-Dienst“ zu [Dynamics 365 Business Central](/dynamics365/business-central/) weiterentwickelt. Aus diesem Grund wurden Liveangebote für „Verwalteter Dynamics NAV-Dienst“ aus AppSource entfernt. Diese Angebote sind für Kunden nicht mehr auffindbar und wurden nicht nach Partner Center verschoben. Passen Sie Ihre Angebote an die Dynamics 365 Business Central-Angebote an, und übermitteln Sie sie im [Partner Center](https://partner.microsoft.com/), um sie in AppSource verfügbar zu machen. Weitere Informationen finden Sie unter [Erstellen eines Angebots für Dynamics 365 Business Central](partner-center-portal/create-new-business-central-offer.md). |
| Cortana Intelligence | Nein | Da Microsoft die Produktroadmap für Cortana Intelligence weiterentwickelt hat, wurden Cortana Intelligence-Liveangebote aus AppSource entfernt. Diese Angebote sind für Kunden nicht mehr auffindbar und wurden nicht nach Partner Center verschoben. Passen Sie Ihre Angebote an die SaaS-Angebote (Software-as-a-Service) an, und übermitteln Sie sie im [Partner Center](https://partner.microsoft.com/), um sie auf dem kommerziellen Marketplace verfügbar zu machen. Weitere Informationen finden Sie unter [Prüfliste für die Erstellung von SaaS-Angeboten](./plan-saas-offer.md). |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>Ich kann meine Cloud-Partnerportal-Angebote im Partner Center nicht finden

Die in Partner Center angezeigten Informationen richten sich nach den Programmen, für die Sie sich registriert haben, Ihren Konten und den Benutzerrollen und Berechtigungen, die Ihnen zugewiesen wurden. Es sind viele Partner Center-Programme verfügbar, und Sie können sich für mehrere Programme registrieren. Sie haben mit denselben Benutzeranmeldeinformationen auch Zugriff auf mehrere Konten.

Die Angebote, die Sie im Cloud-Partnerportal erstellt haben, sind in Partner Center unter dem Programm **Kommerzieller Marketplace** und unter dem Konto verfügbar, das zum Erstellen der Angebote verwendet wird. Führen Sie die unten angegebenen Schritte aus, um sicherzustellen, dass Sie das richtige Programm und das richtige Konto anzeigen. Weitere Tipps zur Problembehandlung finden Sie unter [Verwalten Ihres Partner Center-Kontos](/partner-center/partner-center-account-setup).

### <a name="access-the-right-program-in-partner-center"></a>Zugreifen auf das richtige Programm in Partner Center

1. Melden Sie sich bei [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) mit denselben Anmeldeinformationen an, die Sie zum Anmelden beim Cloud-Partnerportal verwendet haben. Im Navigationsbereich auf der linken Seite werden Optionen angezeigt, die den Programmen, für die Sie registriert sind, zugeordnet sind.

    Nehmen wir beispielsweise an, dass Sie Zugriff auf drei Programme haben: „MPN“, „Empfehlungen“ und „Kommerzieller Marketplace“. Wenn Sie sich bei Partner Center anmelden, werden diese drei Programme im Navigationsbereich angezeigt.

2. Wählen Sie **Kommerzieller Marketplace** > **Übersicht** aus, um auf Ihre Angebote zuzugreifen.

    Falls das Programm „Kommerzieller Marketplace“ im Navigationsbereich auf der linken Seite nicht angezeigt wird, befinden Sie sich unter Umständen im falschen Konto. Führen Sie die Schritte im nächsten Abschnitt aus, um auf das richtige Konto zuzugreifen.

    [![Screenshot: Menü „Übersicht“ in Partner Center](media/cpp-pc-faq/overview-menu.png "Menü „Übersicht“ in Partner Center")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>Zugreifen auf das richtige Konto in Partner Center

Falls Sie mehrere Konten nutzen, wird in Partner Center im linken Navigationsmenü eine Schaltfläche mit zwei Pfeilen zum Auswählen des Kontos angezeigt. Wählen Sie die Schaltfläche zum Auswählen des Kontos aus, um eine Liste mit allen Konten anzuzeigen, die Sie nutzen. Wählen Sie ein Konto in der Liste aus, um zu diesem Konto zu wechseln und alle zugehörigen Programme und Informationen anzuzeigen. Wenn im Navigationsmenü keine Schaltfläche zum Auswählen des Kontos angezeigt wird, nutzen Sie nur ein Konto.

[![Screenshot: Schaltfläche für Kontoauswahl in Partner Center](media/cpp-pc-faq/picker-button.png "Schaltfläche für Kontoauswahl in Partner Center")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>Wie kann ich neue Angebote erstellen?

Greifen Sie in [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) auf das Programm „Kommerzieller Marketplace“ zu, um neue Angebote zu erstellen. Wählen Sie auf der Seite „Übersicht“ die Option **+ Neues Angebot** aus.

[![Screenshot: Menü „Übersicht“ in Partner Center](media/cpp-pc-faq/new-offer.png "Menü „Übersicht“ in Partner Center")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>Ich kann mich nicht anmelden und muss ein Supportticket erstellen

Falls Sie sich nicht bei Ihrem Konto anmelden können, können Sie [hier ein Supportticket erstellen](https://partner.microsoft.com/support/v2/?stage=1).

## <a name="where-are-instructions-for-using-partner-center"></a>Wo finde ich eine Anleitung zur Verwendung von Partner Center?

Wechseln Sie zur [Dokumentation zum kommerziellen Marketplace](index.yml), und erweitern Sie **Portal für den kommerziellen Marketplace in Partner Center**. Erweitern Sie **Neues Angebot erstellen**, um Hilfeartikel zum Erstellen von Angeboten in Partner Center anzuzeigen.

## <a name="what-are-the-publishing-and-offer-management-differences"></a>Was ist der Unterschied zwischen der Veröffentlichungs- und Angebotsverwaltung?

Hier sind einige Unterschiede zwischen dem Cloud-Partnerportal und Partner Center aufgeführt.

### <a name="modular-publishing-capabilities"></a>Modulare Veröffentlichungsfunktionen

Partner Center bietet eine modulare Veröffentlichungsoption, mit der Sie die zu veröffentlichenden Änderungen auswählen können, statt immer alle Aktualisierungen auf einmal zu veröffentlichen. Auf dem folgenden Bildschirm ist beispielsweise zu sehen, dass die einzigen Änderungen, die für die Veröffentlichung ausgewählt sind, die Änderungen von **Eigenschaften** und **Angebotsliste** sind. Die Änderungen, die Sie auf der Seite „Vorschau“ vornehmen, werden nicht veröffentlicht.

[![Screenshot: Seite für Überprüfung und Veröffentlichung in Partner Center](media/cpp-pc-faq/review-page.png "Seite für Überprüfung und Veröffentlichung in Partner Center")](media/cpp-pc-faq/review-page.png#lightbox)

Die Aktualisierungen, die Sie nicht veröffentlichen, werden als Entwürfe gespeichert. Verwenden Sie weiterhin Ihre Angebotsvorschau, um Ihr Angebot zu überprüfen, bevor Sie es für die Öffentlichkeit live schalten.

### <a name="enhanced-preview-options"></a>Erweiterte Vorschauoptionen

Partner Center enthält eine [Vergleichsfunktion](partner-center-portal/update-existing-offer.md#compare-changes-to-your-offer) mit verbesserten Filteroptionen. Damit können Sie die Vorschauversion mit der Liveversion des Angebots vergleichen.

[![Screenshot: Vergleichsfunktion in Partner Center](media/cpp-pc-faq/compare.png "Vergleichsfunktion in Partner Center")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>Branding- und Navigationsänderungen

Es wurden einige Änderungen am Branding vorgenommen. *SKUs* werden in Partner Center beispielsweise als *Pläne* bezeichnet:

[![Screenshot: Seite „Pläne“ in Partner Center](media/cpp-pc-faq/plans.png "Seite „Pläne“ in Partner Center")](media/cpp-pc-faq/plans.png#lightbox)

Darüber hinaus werden die Informationen, die Sie bisher im Cloud-Partnerportal auf den Seiten **Marketplace** oder **Storefrontdetails** (Beratungsdienst, Power BI-App) angegeben haben, nun auf der Seite **Angebotsliste** in Partner Center erfasst:

[![Screenshot mit der Seite „Angebotsliste“ im Partner Center](media/cpp-pc-faq/offer-listing.png](media/cpp-pc-faq/offer-listing.png#lightbox)

Die Informationen, die Sie für SKUs bisher auf einer einzelnen Seite im Cloud-Partnerportal angegeben haben, können nun in Partner Center auf mehreren Seiten erfasst werden:

- Seite „Plansetup“
- Seite „Planlisting“
- Seite für die Planverfügbarkeit
- Seite für die technische Konfiguration für den Plan:

[![Veranschaulicht die Seite „Technische Konfiguration“ im Partner Center](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

Ihre Angebots-ID wird nun in der linken Navigationsleiste des Angebots angezeigt:

![Angebots-ID in Partner Center](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>Beenden des Verkaufs eines Angebots

Sie können direkt im Partner Center-Portal die [Beendigung des Verkaufs eines Angebots](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) im Marketplace anfordern. Die entsprechende Option ist auf der Seite **Angebotsübersicht** für Ihr Angebot verfügbar.

[![Screenshot: Seite zum Beenden des Verkaufs eines Angebots in Partner Center](media/cpp-pc-faq/stop-sell.png "Seite zum Beenden des Verkaufs eines Angebots in Partner Center")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>Werden die REST-APIs des Cloud-Partnerportals weiterhin unterstützt?

Die Cloud-Partnerportal-APIs sind in Partner Center integriert und funktionieren auch weiterhin. Bei der Umstellung auf Partner Center wurden kleinere Änderungen vorgenommen. Mit den Informationen in der Tabelle unten können Sie sicherstellen, dass Ihr Code auch in Partner Center funktioniert.

| API <img src="" width=100px>| Beschreibung der Änderung: | Auswirkung |
| --- | --- | --- |
| POST Publish, GoLive, Cancel | Bei migrierten Angeboten weist der Antwortheader ein anderes Format auf. Er funktioniert jedoch weiterhin auf dieselbe Weise und gibt einen relativen Pfad zum Abrufen des Vorgangsstatus an. | Beim Senden der entsprechenden POST-Anforderungen für ein Angebot weist der Adressheader je nach Migrationsstatus des Angebots eins von zwei Formaten auf: <ul><li>Nicht migrierte Angebote: `/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Migrierte Angebote: `/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| GET-Vorgang | Für Angebote, für die bisher in der Antwort das Feld „notification-email“ unterstützt wurde, wird dieses Feld entfernt und für migrierte Angebote nicht mehr zurückgegeben. | Bei migrierten Angeboten werden keine Benachrichtigungen mehr an die Liste der in den Anforderungen angegebenen E-Mail-Adressen gesendet. Stattdessen wird der API-Dienst an den Benachrichtigungs-E-Mail-Prozess in Partner Center angepasst, um E-Mails zu senden. Dies bedeutet, dass Benachrichtigungen über den Vorgangsstatus an die E-Mail-Adresse gesendet werden, die in Partner Center in den Kontoeinstellungen im Abschnitt mit den Kontaktinformationen zum Verkäufer festgelegt ist.<br><br>Stellen Sie sicher, dass die E-Mail-Adresse, die in Partner Center in den [Kontoeinstellungen](https://partner.microsoft.com/dashboard/account/management) im Abschnitt mit den Kontaktinformationen zum Verkäufer festgelegt ist, richtig angegeben ist, damit Sie Benachrichtigungen erhalten. |
|||