---
title: Häufig gestellte Fragen zu Cloudyn in Azure | Microsoft-Dokumentation
description: Dieser Artikel bietet Antworten auf einige der häufig gestellten Fragen zu Cloudyn.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: troubleshooting
ms.service: cost-management
manager: benshy
ms.custom: seodec18
ms.openlocfilehash: 02a03adb128c140343032075ec334cbd6d88729b
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002028"
---
# <a name="frequently-asked-questions-for-cloudyn"></a>Häufig gestellte Fragen zu Cloudyn

In diesem Artikel werden einige häufige Fragen zu Cloudyn beantwortet. Wenn Sie Fragen zu Cloudyn haben, können Sie diese in [Häufig gestellte Fragen zu Cloudyn](https://social.msdn.microsoft.com/Forums/en-US/231bf072-2c71-4121-8339-ac9d868137b9/faqs-for-cloudyn-cost-management?forum=Cloudyn) stellen.

## <a name="how-can-i-resolve-common-indirect-enterprise-setup-problems"></a>Wie kann ich Fehler beheben, die häufig bei der indirekten Enterprise-Einrichtung auftreten?

Wenn Sie das Cloudyn-Portal zum ersten Mal verwenden, werden möglicherweise die folgenden Meldungen angezeigt, wenn Sie ein Benutzer von Enterprise Agreement oder Cloud Solution Provider (CSP) sind:

- Im **Assistenten für die Einrichtung von Cloudyn**: „Der angegebene API-Schlüssel ist kein Schlüssel für die Registrierung auf der obersten Ebene.“
- Im Enterprise Agreement-Portal: „Direct Enrollment – No“ (Direkte Registrierung – Nein)
- Im Cloudyn-Portal: „No usage data was found for the last 30 days.“ Please contact your distributor to make sure markup was enabled for your Azure account“ (Für die letzten 30 Tage wurden keine Nutzungsdaten gefunden. Wenden Sie sich an Ihren Distributor, um sicherzustellen, dass für Ihr Azure-Konto Markup aktiviert wurde.)

Mit den obigen Fehlermeldungen wird angegeben, dass Sie über einen Handelspartner oder CSP ein Azure Enterprise Agreement (EA) erworben haben. Ihr Handelspartner oder CSP muss _Markup_ für Ihr Azure-Konto aktivieren, damit Sie Ihre Daten in Cloudyn anzeigen können.

Sie können die Probleme wie folgt beheben:

1. Ihr Handelspartner muss _Markup_ für Ihr Konto aktivieren. Die Anleitung finden Sie unter [Indirect Customer Onboarding Guide (Anleitung für indirektes Kundenonboarding)](https://ea.azure.com/api/v3Help/v2IndirectCustomerOnboardingGuide).

2. Sie generieren den Azure EA-Schlüssel für die Verwendung mit Cloudyn. Eine Anleitung hierzu finden Sie unter [Adding Your Azure EA (Hinzufügen Ihres Azure EA)](quick-register-ea.md#register-with-cloudyn) oder [How to Find Your EA Enrollment ID and API Key (Ermitteln Ihrer EA-Registrierungs-ID und Ihres API-Schlüssels)](https://youtu.be/u_phLs_udig).

Cloudyn kann nur von einem Azure-Dienstadministrator aktiviert werden. Co-Administrator-Berechtigungen sind nicht ausreichend.

Sie müssen die Azure-Abrechnungs-API aktivieren, bevor Sie den Azure EA-API-Schlüssel für die Einrichtung von Cloudyn generieren können. Befolgen Sie hierfür diese Anleitungen:

- [Überblick über Berichterstellungs-APIs für Unternehmenskunden](../billing/billing-enterprise-api.md)
- [Microsoft Azure Enterprise Portal-Berichterstellungs-API](https://ea.azure.com/helpdocs/reportingAPI) unter **Aktivieren des Datenzugriffs für die API**


Unter Umständen müssen Sie auch Abteilungsadministratoren, Kontobesitzern und Unternehmensadministratoren Berechtigungen zum _Anzeigen von Gebühren_ per Abrechnungs-API gewähren.

## <a name="why-dont-i-see-optimizer-recommendations"></a>Warum werden mir keine Optimierungsempfehlungen angezeigt?

Empfehlungen sind nur für aktivierte Konten verfügbar. In Berichtskategorien für **Optimierung** werden für Konten, die *deaktiviert* sind, keine Empfehlungsinformationen angezeigt. Dazu zählen:

- Optimierungs-Manager
- Größenoptimierung
- Ineffizienzen

Falls Sie keine Empfehlungsdaten für die Optimierung anzeigen können, sind einige Ihrer Konten höchstwahrscheinlich deaktiviert. Um ein Konto zu aktivieren, müssen Sie es mit Ihren Azure-Anmeldeinformationen registrieren.

So aktivieren Sie ein Konto:

1.  Klicken Sie im Cloudyn-Portal in der rechten oberen Ecke auf **Settings** (Einstellungen), und wählen Sie **Cloud Accounts** (Cloudkonten) aus.
2.  Suchen Sie auf der Registerkarte „Microsoft Azure-Konten“ nach Konten, für die **deaktivierte** Abonnements vorliegen.
3.  Klicken Sie rechts neben einem deaktivierten Konto auf das Symbol **Bearbeiten**, das wie ein Bleistift aussieht.
4.  Die Mandanten-ID und die Kosten-ID werden automatisch ermittelt. Klicken Sie auf **Weiter**.
5.  Sie werden zum Azure-Portal weitergeleitet. Melden Sie sich beim Portal an, und autorisieren Sie für Cloudyn-Collector den Zugriff auf Ihre Azure-Daten.
6.  Sie werden als Nächstes auf die Verwaltungsseite für Cloudyn-Konten weitergeleitet, und Ihr Abonnement wird aktualisiert, sodass als Kontostatus **aktiv** angezeigt wird. Es wird ein grünes Häkchen angezeigt.
7.  Sollte für manche Abonnements kein grünes Häkchen angezeigt werden, bedeutet das, dass Sie nicht zum Erstellen einer Lese-App (Cloudyn-Collector) für das Abonnement berechtigt sind. Ein Benutzer mit höheren Berechtigungen für das Abonnement muss die Schritte 3 und 4 wiederholen.  

Nachdem Sie die vorherigen Schritte ausgeführt haben, können Sie innerhalb von ein bis zwei Tagen Optimierungsempfehlungen anzeigen. Allerdings kann es bis zu fünf Tage dauern, bis vollständige Optimierungsdaten verfügbar sind.


## <a name="how-do-i-enable-suspended-or-locked-out-users"></a>Wie kann ich angehaltene oder gesperrte Benutzer aktivieren?

Dies ist das häufigste Szenario, wie Benutzerkonten den Status *InitiallySuspended* erhalten.

> Admin1 kann ein Microsoft Cloud Solution Provider oder Enterprise Agreement-Benutzer sein. Seine Organisation ist bereit, Cloudyn einzuführen.  Er registriert sich über das Azure-Portal und meldet sich im Cloudyn-Portal an. Da er den Cloudyn-Dienst registriert und sich im Cloudyn-Portal anmeldet, wird Admin1 zum *primären Administrator*. Admin1 erstellt keine Benutzerkonten. Mithilfe des Cloudyn-Portals erstellt er jedoch Azure-Konten und eine Entitätshierarchie. Admin1 informiert Admin2, einen Mandantenadministrator, dass er sich für Cloudyn registrieren und sich beim Cloudyn-Portal anmelden muss.
>
> Admin2 registriert sich über das Azure-Portal. Wenn er jedoch versucht, sich beim Cloudyn-Portal anzumelden, wird eine Fehlermeldung zurückgegeben, die besagt, dass sein Konto **gesperrt** ist. Der primäre Administrator, Admin1, wird über die Kontosperrung benachrichtigt. Admin1 muss das Konto von Admin2 aktivieren, *Administratorzugriff auf die richtigen Entitäten* gewähren, bestimmt Benutzerverwaltungszugriff und das Benutzerkonto aktivieren.


Wenn Sie eine Warnung mit der Anforderung erhalten, dass der Zugriff für einen Benutzer zugelassen werden soll, müssen Sie das Benutzerkonto aktivieren.

Aktivieren Sie das Benutzerkonto wie folgt:

1. Melden Sie sich bei Cloudyn mit dem Azure-Administratorbenutzerkonto an, das Sie zum Einrichten von Cloudyn verwendet haben. Oder melden Sie sich mit einem Benutzerkonto an, für das der Administratorzugriff gewährt wurde.
2. Klicken Sie oben rechts auf das Zahnradsymbol, und klicken Sie dann auf **User Management** (Benutzerverwaltung).
3. Suchen Sie nach dem Benutzer, und klicken Sie dann auf das Stiftsymbol, um den Benutzer zu bearbeiten.
4. Ändern Sie den Status unter **User status** (Benutzerstatus) von **Suspended** (Angehalten) in **Active** (Aktiv).

Für Cloudyn-Benutzerkonten wird eine Verbindung hergestellt, indem das einmalige Anmelden von Azure verwendet wird. Wenn ein Benutzer sein Kennwort falsch eingibt, wird er ggf. für Cloudyn gesperrt, auch wenn der Zugriff auf Azure weiterhin möglich ist.

Wenn Sie Ihre E-Mail-Adresse in Cloudyn von der Standardadresse in Azure in eine andere ändern, kann dies dazu führen, dass Ihr Konto gesperrt wird. Unter Umständen wird der folgende Fehler angezeigt: „Status initiallySuspended“. Wenn Ihr Benutzerkonto gesperrt ist, können Sie einen anderen Administrator bitten, Ihr Konto zurückzusetzen.

Es wird empfohlen, mindestens zwei Cloudyn-Administratorkonten zu erstellen, falls eines der Konten gesperrt wird.

Wenn Sie sich nicht beim Cloudyn-Portal anmelden können, sollten Sie sicherstellen, dass Sie die richtige URL für die Anmeldung bei Cloudyn verwenden. Verwenden Sie [https://azure.cloudyn.com](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/CloudynMainBlade).

Vermeiden Sie die Verwendung der direkten Cloudyn-URL https://app.cloudyn.com.

## <a name="how-do-i-activate-unactivated-accounts-with-azure-credentials"></a>Wie aktiviere ich deaktivierte Konten mit Azure-Anmeldeinformationen?

Nachdem Ihre Azure-Konten von Cloudyn ermittelt wurden, werden in kostenbasierten Berichten sofort Kostendaten bereitgestellt. Damit Cloudyn Daten zur Nutzung und Leistung bereitstellen kann, müssen Sie Ihre Azure-Anmeldeinformationen für die Konten registrieren. Anweisungen finden Sie unter [Hinzufügen eines Kontos oder Aktualisieren eines Abonnements](activate-subs-accounts.md#add-an-account-or-update-a-subscription).

Klicken Sie zum Hinzufügen von Azure-Anmeldeinformationen für ein Konto im Cloudyn-Portal auf das Bearbeitungssymbol rechts vom Kontonamen (nicht neben dem Abonnement).

Bis Ihre Azure-Anmeldeinformationen Cloudyn hinzugefügt wurden, wird Ihr Konto als _deaktiviert_ angezeigt.

## <a name="how-do-i-add-multiple-accounts-and-entities-to-an-existing-subscription"></a>Wie füge ich einem vorhandenen Abonnement mehrere Konten und Entitäten hinzu?

Zusätzliche Entitäten werden verwendet, um einem Cloudyn-Abonnement weitere Enterprise Agreements hinzuzufügen. Weitere Informationen finden Sie unter [Tutorial: Zuweisen des Zugriffs auf Cloudyn-Daten](tutorial-user-access.md#create-and-manage-entities).

Für CSPs:

Wählen Sie zum Hinzufügen von zusätzlichen CSP-Konten zu einer Entität beim Erstellen der neuen Entität anstelle von **Enterprise** die Option **MSP Access** (MSP-Zugriff). Wenn Ihr Konto als Enterprise Agreement registriert ist und Sie CSP-Anmeldeinformationen hinzufügen möchten, müssen Ihre Kontoeinstellungen ggf. vom Cloudyn-Support geändert werden. Wenn Sie über ein kostenpflichtiges Azure-Abonnement verfügen, können Sie im Azure-Portal eine neue Supportanforderung erstellen. Klicken Sie auf **Hilfe + Support** und dann auf **Neue Supportanforderung**.

## <a name="currency-symbols-in-cloudyn-reports"></a>Währungssymbole in Cloudyn-Berichten

Sie verfügen möglicherweise über mehrere Azure-Konten mit verschiedenen Währungen. In Kostenberichten in Cloudyn wird jedoch nicht mehr als ein Währungstyp pro Bericht angezeigt.

Wenn Sie über mehrere Abonnements mit verschiedenen Währungen verfügen, werden eine übergeordnete Entität und ihre untergeordneten Entitätenwährungen in USD **$** angezeigt. Es wird empfohlen, die Verwendung unterschiedlicher Währungen in derselben Entitätshierarchie zu vermeiden. Anders gesagt: Alle Ihre Abonnements, die in einer Entitätsstruktur organisiert sind, sollten dieselbe Währung verwenden.

Cloudyn erkennt automatisch die Währung Ihres Enterprise Agreement-Abonnements Währung und stellt sie ordnungsgemäß in Berichten dar.  Allerdings zeigt Cloudyn nur USD **$** für CSP- und Azure-Web-Direct-Konten an.

## <a name="what-are-cloudyn-data-refresh-timelines"></a>Was sind Cloudyn-Zeitachsen für die Datenaktualisierung?

Cloudyn verfügt über die folgenden Zeitachsen für die Datenaktualisierung:

- **Initial** (Anfang): Nach dem Einrichten kann es bis zu 24 Stunden dauern, bis Kostendaten in Cloudyn angezeigt werden. Es kann auch bis zu zehn Tage dauern, bis Cloudyn genügend Daten gesammelt hat, um Größenempfehlungen anzeigen zu können.
- **Daily** (Täglich): Vom 10. bis zum Ende jedes Monats sollte der aktuelle Stand Ihrer Daten in Cloudyn jeweils bis zum vorherigen Tag angezeigt werden (ab ca. UTC+3 am nächsten Tag).
- **Monthly** (Monatlich): Vom 1. bis zum 10. des Monats werden Ihre Daten in Cloudyn möglicherweise nur bis zum Ende des Vormonats angezeigt.

In Cloudyn werden Daten für den vorherigen Tag verarbeitet, wenn alle Daten des vorherigen Tags verfügbar sind. Die Daten des vorherigen Tags stehen in Cloudyn normalerweise jeweils ab ca. UTC+3 zur Verfügung. Die Verarbeitung einiger Daten, z.B. von Tags, kann 24 Stunden länger dauern.

Daten für den aktuellen Monat können am Anfang des Monats nicht gesammelt werden. Während dieses Zeitraums schließen Dienstanbieter ihre Abrechnungen für den vorherigen Monat ab. Die Daten des vorherigen Monats werden in Cloudyn fünf bis zehn Tage nach Beginn eines Monats angezeigt. Während dieses Zeitraums werden ggf. nur amortisierte Kosten des vorherigen Monats angegeben. Tägliche Abrechnungs- oder Nutzungsdaten werden unter Umständen nicht angezeigt. Wenn die Daten verfügbar sind, werden sie von Cloudyn im Nachhinein verarbeitet. Nach der Verarbeitung werden alle monatlichen Daten für die Zeit zwischen dem 5. und 10. jedes Monats angezeigt.

Wenn es beim Senden von Daten aus Azure an Cloudyn zu einer Verzögerung kommt, werden die Daten trotzdem in Azure aufgezeichnet. Die Daten werden an Cloudyn übertragen, nachdem die Verbindung wiederhergestellt wurde.

## <a name="cost-fluctuations-in-cloudyn-cost-reports"></a>Kostenschwankungen in Cloudyn-Kostenberichten

Wenn Clouddienstanbieter aktualisierte Abrechnungsdateien senden, kann dies zu Kostenschwankungen bei Kostenberichten führen. Schwankende Kosten entstehen, wenn außerhalb des üblichen täglichen oder monatlichen Berichterstellungszeitplans neue Dateien von einem Clouddienstanbieter empfangen werden. Kostenänderungen werden nicht durch eine Neuberechnung in Cloudyn verursacht.

Alle Abrechnungsdateien, die von Ihrem Clouddienstanbieter im Laufe des Monats gesendet werden, stellen eine Schätzung Ihrer täglichen Kosten dar. In einigen Fällen werden Daten häufig aktualisiert, mitunter mehrmals täglich. Updates kommen bei AWS häufiger vor als bei Azure. Die Gesamtkosten sollten stabil bleiben, wenn die Abrechnung für den vorherigen Monat abgeschlossen ist und die endgültige Abrechnungsdatei empfangen wurde. Stichtag hierbei ist in der Regel der 10. des Monats.

Änderungen kommen vor, wenn Kostenanpassungen seitens Ihres Clouddienstanbieters vorgenommen werden. Ein Beispiel hierfür ist die Zuteilung von Guthaben. Änderungen können Monate nach dem betreffenden Monat, in dem die Abrechnung abgeschlossen wurde, vorkommen. Änderungen werden immer dann angezeigt, wenn eine Neuberechnung seitens Ihres Clouddienstanbieters vorgenommen wurde. Cloudyn aktualisiert seine Verlaufsdaten, um sicherzustellen, dass alle Anpassungen neu berechnet werden. Darüber hinaus wird überprüft, ob die Kosten korrekt in den jeweiligen Berichten aufgeführt werden.

## <a name="how-can-a-direct-csp-configure-cloudyn-access-for-indirect-csp-customers-or-partners"></a>Wie kann ein direkter Cloudlösungsanbieter (CSP) den Cloudyn-Zugriff für Kunden oder Partner eines indirekten CSP konfigurieren?

Eine Anleitung hierzu finden Sie unter [Configure indirect CSP access in Cloudyn (Konfigurieren des Zugriffs für indirekte CSPs in Cloudyn)](quick-register-csp.md#configure-indirect-csp-access-in-cloudyn).

## <a name="what-causes-the-optimizer-menu-item-to-appear"></a>Was bewirkt, dass das Menüelement „Optimierer“ angezeigt wird?

Nachdem Sie den Azure Resource Manager-Zugriff hinzugefügt haben und die Daten gesammelt werden, sehen Sie die Option **Optimierer**. Wie Sie Azure Resource Manager-Zugriff aktivieren, erfahren Sie unter [Wie aktiviere ich deaktivierte Konten mit Azure-Anmeldeinformationen?](#how-do-i-activate-unactivated-accounts-with-azure-credentials)

## <a name="is-cloudyn-agent-based"></a>Basiert Cloudyn auf Agents?

Nein. Agents werden nicht verwendet. Metrikdaten für Azure-VMs werden von der Microsoft Insights-API erfasst. Wenn Sie metrische Daten von virtuellen Azure-Computern sammeln möchten, müssen für sie Diagnoseeinstellungen aktiviert sein.

## <a name="do-cloudyn-reports-show-more-than-one-ad-tenant-per-report"></a>Zeigt ein einzelner Cloudyn-Bericht mehrere AD-Mandanten?

Ja. Sie können [eine entsprechende Cloudkontoentität](tutorial-user-access.md#create-and-manage-entities) für jeden Ihrer AD-Mandanten erstellen. Anschließend können Sie alle Ihre Azure AD-Mandantendaten und andere Cloudplattformanbieter einschließlich Amazon Web Services und Google Cloud Platform anzeigen.
