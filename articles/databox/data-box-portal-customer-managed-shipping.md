---
title: Microsoft Azure Data Box – Selbst verwalteter Versand | Microsoft-Dokumentation
description: Beschreibt den Workflow des selbst verwalteten Versands für Azure Data Box-Geräte.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: how-to
ms.date: 08/12/2020
ms.author: alkohli
ms.openlocfilehash: 9643e62f085888808b95698d068c5e383fb8d539
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/06/2020
ms.locfileid: "94337966"
---
# <a name="use-self-managed-shipping-for-azure-data-box-in-the-azure-portal"></a>Verwenden des selbst verwalteten Versands für Azure Data Box im Azure-Portal

In diesem Artikel werden die Aufgaben beim selbst verwalteten Versand (Bestellung, Abholung und Abgabe) eines Azure Data Box-Geräts beschrieben. Sie können das Data Box-Gerät über das Azure-Portal verwalten.

## <a name="prerequisites"></a>Voraussetzungen

Der selbst verwaltete Versand ist als Option verfügbar, wenn Sie [Azure Data Box bestellen](data-box-deploy-ordered.md). Der selbst verwaltete Versand ist nur in folgenden Regionen verfügbar:

* US Government
* Europa, Westen
* Japan
* Singapur
* Südkorea
* Indien
* Südafrika

## <a name="use-self-managed-shipping"></a>Verwenden des selbst verwalteten Versands

Beim Aufgeben einer Data Box-Bestellung können Sie die Option für selbst verwalteten Versand auswählen.

1. Wählen Sie in Ihrer Azure Data Box-Bestellung unter den **Kontaktdetails**  den Eintrag **+ Lieferadresse hinzufügen** aus.
 
   ![„Selbstverwalteter Versand“: „Lieferadresse hinzufügen“](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-1.png)

2. Wählen Sie bei der Wahl der Versandart die Option **Selbstverwalteter Versand** aus. Diese Option ist nur verfügbar, wenn Sie sich in einer unterstützten Region befinden (siehe die vorstehenden „Voraussetzungen“).

3. Nachdem Sie Ihre Lieferadresse angegeben haben, müssen Sie sie überprüfen und dann Ihre Bestellung abschließen.

   ![„Selbstverwalteter Versand“: Adresse überprüfen und hinzufügen](media\data-box-portal-customer-managed-shipping\choose-self-managed-shipping-2.png)

4. Sobald das Gerät vorbereitet ist und Sie eine E-Mail-Benachrichtigung dafür erhalten, können Sie eine Abholung planen.

   Wechseln Sie in Ihrer Azure Data Box-Bestellung zu **Übersicht**, und wählen Sie **Abholung planen** aus.

   ![Data Box-Auftrag: Option „Abholung planen“](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-01.png)

5. Befolgen Sie die Anweisungen unter **Abholung für Azure planen**.

   Bevor Sie Ihren Autorisierungscode abrufen können, müssen Sie eine E-Mail an [adbops@microsoft.com](mailto:adbops@microsoft.com) senden, um die Abholung des Geräts aus dem Rechenzentrum Ihrer Region zu planen.

   ![Anweisungen zu „Abholung für Azure planen“](media\data-box-portal-customer-managed-shipping\data-box-portal-schedule-pickup-email-01.png)

6. Nachdem Sie die Abholung Ihres Geräts geplant haben, können Sie Ihren Geräteautorisierungscode im Bereich **Abholung für Azure planen** anzeigen.

   ![Anzeigen Ihres Geräteautorisierungscodes](media\data-box-portal-customer-managed-shipping\data-box-portal-auth-01b.png)

   Notieren Sie sich diesen **Autorisierungscode**. Gemäß den Sicherheitsanforderungen muss bei der Planung der Abholung der Name der Person angegeben werden, die das Gerät abholt.

   Darüber hinaus müssen Sie Details zu der Person angeben, die zur Abholung in das Rechenzentrum kommen wird. Sie oder Ihre Kontaktperson müssen einen amtlichen Lichtbildausweis mit sich führen, der im Rechenzentrum überprüft wird.

   Außerdem muss die Person, die das Gerät abholt, ebenfalls über den **Autorisierungscode** verfügen. Der Autorisierungscode wird bei der Abholung im Rechenzentrum überprüft.

7. Sobald das Gerät im Rechenzentrum abgeholt wurde, ändert sich der Status Ihrer Bestellung automatisch in **Abgeholt**.

    ![Ein Auftrag mit dem Zustand „Abgeholt“](media\data-box-portal-customer-managed-shipping\data-box-portal-picked-up-boxed-01.png)

8. Nachdem das Gerät abgeholt wurde, kopieren Sie die Daten in die Data Box an Ihrem Standort. Nach Abschluss des Datenkopiervorgangs können Sie die Data Box für den Versand vorbereiten. Weitere Informationen finden Sie unter [Für den Versand vorbereiten](data-box-deploy-picked-up.md#prepare-to-ship).

   Der Schritt **Versandvorbereitung** muss ohne kritische Fehler abgeschlossen werden. Andernfalls müssen Sie diesen Schritt erneut ausführen, nachdem Sie die erforderlichen Korrekturen vorgenommen haben. Nach erfolgreichem Abschluss der Versandvorbereitung können Sie den Autorisierungscode für die Abgabe auf der lokalen Benutzeroberfläche anzeigen.

   > [!NOTE]
   > Versenden Sie den Autorisierungscode nicht per E-Mail. Dieser muss im Datacenter nämlich nur während der Abgabe überprüft werden.

9. Wenn Sie einen Termin für die Abgabe erhalten haben, sollte der Status der Bestellung im Azure-Portal **Bereit für den Empfang im Azure-Rechenzentrum** lauten. Befolgen Sie die Anweisungen unter **Abgabe planen**, um das Gerät zurückzusenden.

   ![Anweisungen für Geräteabgabe](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-02b.png)

10. Nachdem Ihre ID und Ihr Autorisierungscode überprüft wurden und Sie das Gerät im Rechenzentrum abgegeben haben, sollte der Auftragsstatus **Empfangen** lauten.

    ![Ein Auftrag mit dem Status „Empfangen“](media\data-box-portal-customer-managed-shipping\data-box-portal-received-complete-01.png)

11. Nachdem das Gerät empfangen wurde, wird der Datenkopiervorgang fortgesetzt. Nach Beendigung des Kopiervorgangs ist die Bestellung abgeschlossen.

## <a name="next-steps"></a>Nächste Schritte

* [Erste Schritte mit Azure Data Box](data-box-quickstart-portal.md)
