---
title: Anzeigen und Herunterladen der Azure-Nutzung und -Gebühren | Microsoft-Dokumentation
description: Hier wird beschrieben, wie Sie Azure-Daten zur täglichen Nutzung und zu den Gebühren herunterladen oder anzeigen.
keywords: Nutzungsabrechnung, Nutzungsgebühren, Nutzung herunterladen, Nutzung anzeigen, Azure-Rechnung, Azure-Nutzung
services: billing
documentationcenter: ''
author: genlin
manager: jureid
editor: ''
tags: billing
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: banders
ms.openlocfilehash: 520d3f6a45b44ba2023dee34642f796689f48221
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60918985"
---
# <a name="view-and-download-your-azure-usage-and-charges"></a>Anzeigen und Herunterladen der Azure-Nutzung und -Gebühren

Wenn Sie EA-Kunde sind oder über eine [Microsoft-Kundenvereinbarung](#check-your-access-to-a-microsoft-customer-agreement) verfügen, können Sie im [Azure-Portal](https://portal.azure.com/) die Azure-Nutzung und -Gebühren herunterladen. Wenn Sie Nutzungsdaten für andere Abonnements herunterladen möchten, navigieren Sie zum [Azure-Kontocenter](https://account.azure.com/Subscriptions).

Nur bestimmte Rollen (z. B. „Kontoadministrator“ oder „Unternehmensadministrator“) verfügen über die Berechtigung zum Abrufen von Azure-Nutzungsinformationen. Weitere Informationen zum Zugriff auf Abrechnungsinformationen finden Sie unter [Verwalten des Zugriffs auf die Azure-Abrechnung mithilfe von Rollen](billing-manage-access.md).

Wenn Sie über eine [Microsoft-Kundenvereinbarung](#check-your-access-to-a-microsoft-customer-agreement) verfügen, müssen Sie über die Rolle „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ oder „Rechnungs-Manager“ verfügen, um Ihre Azure-Nutzung und -Gebühren anzeigen zu können. Weitere Informationen zu Abrechnungsrollen für Microsoft-Kundenvereinbarungen finden Sie unter [Rollen und Aufgaben für ein Abrechnungsprofil](billing-understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="download-usage-from-the-account-center-csv"></a>Herunterladen der Nutzungsdaten aus dem Kontocenter (CSV)

1. Melden Sie sich als Kontoadministrator im [Azure-Kontocenter](https://account.windowsazure.com/subscriptions) an.

2. Wählen Sie das Abonnement, für das Sie die Rechnung und Nutzungsinformationen abrufen möchten.

3. Klicken Sie auf **ABRECHNUNGSVERLAUF**.

    ![Screenshot mit Abrechnungsverlaufsoption](./media/billing-download-azure-invoice-daily-usage-date/Billinghisotry.png)

4. Sie können Ihre Aufstellungen für die letzten sechs Abrechnungszeiträume sowie den aktuellen, nicht berechneten Zeitraum anzeigen.

    ![Screenshot, der Abrechnungszeiträume, Optionen zum Herunterladen von Rechnungen und Daten zur täglichen Nutzung sowie Gesamtgebühren für jeden Abrechnungszeitraum anzeigt](./media/billing-download-azure-invoice-daily-usage-date/billingSum.png)

5. Wählen Sie **Aktuelle Abrechnung anzeigen** , um eine Schätzung der Kosten zum Zeitpunkt der Erstellung der Schätzung anzuzeigen. Diese Informationen werden nur einmal täglich aktualisiert und umfassen möglicherweise nicht die gesamte Nutzung. Ihre monatliche Abrechnung kann von dieser Schätzung abweichen.

    ![Screenshot mit der Option „Aktuelle Erklärung anzeigen“](./media/billing-download-azure-invoice-daily-usage-date/billingSum2.png)

    ![Screenshot mit der Schätzung der aktuellen Gebühren](./media/billing-download-azure-invoice-daily-usage-date/billingSum3.png)

6. Wählen Sie **Nutzung herunterladen**, um die täglichen Nutzungsdaten als CSV-Datei herunterzuladen. Wenn zwei Versionen verfügbar sind, laden Sie Version 2 herunter.

    ![Screenshot mit der Option „Nutzung herunterladen“](./media/billing-download-azure-invoice-daily-usage-date/DLusage.png)

Nur der Kontoadministrator hat Zugriff auf das Azure-Kontocenter. Andere Abrechnungsadministratoren, z.B. Besitzer, erhalten mithilfe der [Abrechnungs-APIs](billing-usage-rate-card-overview.md) Informationen zur Nutzung.

Weitere Informationen über Ihre Daten zur täglichen Nutzung finden Sie unter [Erläuterungen zur Rechnung für Microsoft Azure](billing-understand-your-bill.md). Hilfe beim Verwalten Ihrer Kosten finden Sie unter [Vermeiden unerwarteter Kosten bei der Azure-Abrechnung und -Kostenverwaltung](billing-getting-started.md).

## <a name="download-usage-for-ea-customers"></a>Herunterladen von Nutzungsdaten für EA-Kunden

Sie müssen Unternehmensadministrator, Kontobesitzer oder Abteilungsadministrator sein, um Nutzungsdaten als EA-Kunde anzeigen und herunterladen zu können. Außerdem muss die Richtlinie zum Anzeigen von Gebühren aktiviert sein.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Suchen Sie nach *Kostenverwaltung + Abrechnung*.

    ![Screenshot, der die Suche im Azure-Portal zeigt](./media/billing-download-azure-invoice-daily-usage-date/portal-cm-billing-search.png)

1. Wählen Sie **Verbrauch + Gebühren** aus.
1. Wählen Sie für den Monat, den Sie herunterladen möchten, **Herunterladen** aus.

## <a name="download-usage-for-your-microsoft-customer-agreement"></a>Herunterladen der Nutzungsdaten für Ihre Microsoft-Kundenvereinbarung

Wenn Sie über eine Microsoft-Kundenvereinbarung verfügen, können Sie die Azure-Nutzung und -Gebühren für Ihr Abrechnungsprofil herunterladen. Um die CSV-Datei zu Azure-Nutzung und -Gebühren herunterladen zu können, müssen Sie über die Rolle „Besitzer des Abrechnungsprofils“, „Mitwirkender am Abrechnungsprofil“, „Benutzer mit Leseberechtigung für das Abrechnungsprofil“ oder „Rechnungs-Manager“ verfügen.

### <a name="download-usage-for-billed-charges"></a>Herunterladen der Nutzung für in Rechnung gestellte Gebühren

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
3. Wählen Sie ein Abrechnungsprofil aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise zuerst ein Abrechnungskonto auswählen.
4. Wählen Sie **Rechnungen** aus.
5. Suchen Sie im Rechnungsraster nach der Zeile der Rechnung, die der Nutzung entspricht, die Sie herunterladen möchten.
6. Klicken Sie am Ende der Zeile auf die Auslassungspunkte (`...`).

    ![Screenshot, in dem am Ende der Zeile die Auslassungspunkte abgebildet sind](./media/billing-download-azure-invoice/billingprofile-invoicegrid.png)

7. Wählen Sie im Kontextmenü „Download“ die Option **Azure-Nutzung und -Gebühren** aus.

     ![Screenshot, in dem die Option „Azure-Nutzung und -Gebühren“ ausgewählt ist](./media/billing-download-azure-usage/contextmenu-usage.png)

### <a name="download-usage-for-pending-charges"></a>Herunterladen der ausstehenden Nutzungsgebühren

Sie können auch die Nutzungsgebühren für den bisherigen Kalendermonat für den aktuellen Abrechnungszeitraum herunterladen. Diese Nutzungsgebühren wurden noch nicht in Rechnung gestellt.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Suchen Sie nach **Kostenverwaltung + Abrechnung**.
3. Wählen Sie ein Abrechnungsprofil aus. Abhängig von Ihren Zugriffsberechtigungen müssen Sie möglicherweise zuerst ein Abrechnungskonto auswählen.
4. Suchen Sie auf dem Blatt **Übersicht** nach den Downloadlinks unter den Gebühren für den bisherigen Kalendermonat.
5. Wählen Sie **Azure-Nutzung und -Gebühren** aus.

    ![Screenshot des Blatts „Übersicht“ mit dem Downloadlink](./media/billing-download-azure-usage/open-usage.png)

## <a name="check-your-access-to-a-microsoft-customer-agreement"></a>Überprüfen des Zugriffs auf eine Microsoft-Kundenvereinbarung
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Kontakt

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

Lesen Sie die folgenden Artikel, um mehr über Ihre Rechnung und Nutzungsgebühren zu erfahren:

- [Grundlegendes über Benennungen zu den Gebühren in der Datei mit ausführlichen Nutzungsdaten zu Microsoft Azure](billing-understand-your-usage.md)
- [Informationen zu Ihrer Rechnung für Microsoft Azure](billing-understand-your-bill.md)
- [Anzeigen und Herunterladen der Microsoft Azure-Rechnung](billing-download-azure-invoice.md)
- [Anzeigen und Herunterladen der Azure-Preise für Ihre Organisation](billing-ea-pricing.md)

Wenn Sie über eine Microsoft-Kundenvereinbarung verfügen, lesen Sie die folgenden Artikel:

- [Grundlegendes zu den Begriffen in der CSV-Datei zu Azure-Nutzung und -Gebühren für eine Microsoft-Kundenvereinbarung](billing-mca-understand-your-usage.md)
- [Grundlegendes zu den Gebühren auf der Rechnung für eine Microsoft-Kundenvereinbarung](billing-mca-understand-your-bill.md)
- [Anzeigen und Herunterladen der Microsoft Azure-Rechnung](billing-download-azure-invoice.md)
- [Anzeigen und Herunterladen von Steuerdokumenten für Ihre Microsoft-Kundenvereinbarung](billing-mca-download-tax-document.md)
- [Anzeigen und Herunterladen der Azure-Preise für Ihre Organisation](billing-ea-pricing.md)
