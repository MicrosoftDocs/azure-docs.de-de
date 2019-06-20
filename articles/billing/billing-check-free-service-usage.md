---
title: Überwachen und Nachverfolgen der Verwendung kostenloser Azure-Dienste | Microsoft Docs
description: Erfahren Sie, wie Sie die Nutzung von kostenlosen Diensten überprüfen. Verwenden Sie das Azure-Portal und die Nutzungs-CSV-Datei.
services: ''
documentationcenter: ''
author: amberbhargava
manager: amberb
editor: ''
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: banders
ms.openlocfilehash: 4e940a12cd57ef136cfd9ead298f9afcd2d6ad1f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60617775"
---
# <a name="check-usage-of-free-services-included-with-your-azure-free-account"></a>Überprüfen der Verwendung der kostenlosen Dienste, die in Ihrem kostenlosen Azure-Konto enthalten sind 

Ihnen werden keine Gebühren für kostenlose Dienste, die in Ihrem kostenlosen Azure-Konto enthalten sind, in Rechnung gestellt, solange Sie die Grenzwerte dieser Dienste nicht überschreiten. Um die Grenzwerte einzuhalten, können Sie entweder das Azure-Portal oder die Nutzungsdatendatei zum Überwachen und Nachverfolgen der Verwendung kostenloser Dienste nutzen. 

## <a name="check-usage-on-the-azure-portal"></a>Überprüfen der Nutzung im Azure-Portal

1.  Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.

2.  Wählen Sie im Navigationsbereich links **Alle Dienste** aus.

3.  Wählen Sie **Abonnements**.

4.  Wählen Sie das Abonnement aus, das Sie erstellt haben, als Sie sich für das kostenlose Konto registriert haben.

    ![Screenshot mit allen Abonnements](./media/billing-check-usage-of-free-services/select-free-account-subscription.png)

5.  Der Übersichtsabschnitt zeigt grundlegende Informationen zu Ihrem Abonnement an, z.B. Abonnement-ID, Angebotstyp und Abonnementnamen. Sie erhalten auch Informationen zum Ablauf des Guthabens auf Ihrem kostenlosen Konto.

    ![Screenshot mit grundlegenden Abonnementinformationen](./media/billing-check-usage-of-free-services/subscription-essential-information.png)

6.  Scrollen Sie nach unten, um Informationen zu Ihren aktuellen und prognostizierten Kosten anzuzeigen. Die Kosten beinhalten die Nutzung der Dienste, die nicht in Ihrem kostenlosen Konto enthalten sind, sowie die Nutzung, die die Grenzwerte der kostenlosen Dienste überschreitet. 

    ![Screenshot mit Abonnementkosteninformationen](./media/billing-check-usage-of-free-services/subscription-cost-information.png)

7.  Der letzte Teil des Übersichtsabschnitts enthält eine Tabelle zur Verwendung der kostenlosen Dienste. 

    ![Screenshot mit Verwendung der kostenlosen Dienste](./media/billing-check-usage-of-free-services/subscription-usage-free-services.png)

    Die Tabelle enthält folgende Spalten:

* **Name der Verbrauchseinheit:** Gibt die Maßeinheit für die genutzte Verbrauchseinheit an. Informationen zur Zuordnung von Diensten zu Verbrauchseinheiten finden Sie unter [Grundlegendes zur Zuordnung kostenloser Dienste zu Verbrauchseinheiten](billing-understand-free-service-meter-mapping.md).
* **Nutzung/Limit:** Nutzung und Grenzwert für die Verbrauchseinheit für den aktuellen Monat. Diese Informationen finden Sie auch in der Statusleiste.
* **Status:** Verwendungsstatus der Verbrauchseinheit. Basierend auf Ihrem Nutzungsmuster können Sie einen dieser Status besitzen.
  * **Nicht in Verwendung:** Sie haben diese Verbrauchseinheit nicht verwendet, oder die Verwendung der Verbrauchseinheit hat das Abrechnungssystem nicht erreicht.
  * **Überschritten am \<Datum>:** Sie haben den Grenzwert für die Verbrauchseinheit am \<Datum> überschritten.
  * **Überschreitung unwahrscheinlich:** Es ist unwahrscheinlich, dass Sie den Grenzwert für diese Verbrauchseinheit überschreiten.
  * **Überschreitung am \<Datum>:** Sie werden den Grenzwert für die Verbrauchseinheit voraussichtlich am \<Datum> überschreiten.

## <a name="check-usage-through-the-usage-file"></a>Überprüfen der Verwendung mit der Nutzungsdatendatei

Ihre Nutzungsdatendatei enthält detaillierte Informationen über Ihr Azure-Abonnement. Sie können Ihre monatliche und tägliche Nutzungsdatendatei vom Azure-Kontocenter herunterladen. Informationen zum Herunterladen der Nutzungsdatendatei und erforderlichen Zugriffsrechten finden Sie unter [Herunterladen oder Anzeigen Ihrer Azure-Rechnungen und täglichen Nutzungsdaten](billing-download-azure-invoice-daily-usage-date.md). Weitere Informationen zu Spalten in der Nutzungsdatendatei finden Sie unter [Grundlegendes über Benennungen zu den Gebühren in der Datei mit ausführlichen Nutzungsdaten zu Microsoft Azure](billing-understand-your-usage.md).

Die Nutzungsdatendatei enthält Nutzungsinformationen zu kostenlosen und kostenpflichtigen Diensten. An das Ende des Namens kostenloser Dienstverbrauchseinheiten ist **Kostenlos** angefügt. Um nach kostenlosen Verbrauchseinheiten zu suchen, öffnen Sie die Datei in Excel, und filtern Sie die Spalte **Kategorie der Verbrauchseinheit** nach Zellen, die den Text **– Kostenlos** enthalten („Textfilter“ &rarr; Filter „Enthält“). &nbsp;

![Screenshot mit Verwendung der kostenlosen Dienste](./media/billing-check-usage-of-free-services/free-services-usage-csv.png)

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).