---
title: Aufgaben für Kontoadministratoren im Azure-Portal
description: Hier erfahren Sie, wie Sie Zahlungsvorgänge im Azure-Portal ausführen.
author: bandersmsft
ms.reviewer: judupont
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/26/2020
ms.author: banders
ms.custom: contperf-fy21q2
ms.openlocfilehash: bd46e7b2f0713da67842def47dfeadc837027d8f
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97027967"
---
# <a name="account-administrator-tasks-in-the-azure-portal"></a>Aufgaben für Kontoadministratoren im Azure-Portal

In diesem Artikel erfahren Sie, wie Sie folgende Aufgaben im Azure-Portal ausführen:
- Verwalten der Zahlungsmethoden Ihres Abonnements
- Entfernen des Ausgabenlimits Ihres Abonnements
- Hinzufügen von Guthaben zu Ihrem Azure in Open-Abonnement

Diese Aufgaben können nur vom Kontoadministrator ausgeführt werden.

## <a name="navigate-to-your-subscriptions-payment-methods"></a>Navigieren zu den Zahlungsmethoden Ihres Abonnements

1. Melden Sie sich als Kontoadministrator beim Azure-Portal an.

1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

    ![Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ ](./media/account-admin-tasks/search-bar.png)

1. Wählen Sie in der Liste **Meine Abonnements** das Abonnement aus, dem Sie die Kreditkarte hinzufügen möchten.

   ![Screenshot der Seite „Kostenverwaltung + Abrechnung“, auf der Sie ein Abonnement auswählen können.](./media/account-admin-tasks/cost-management-billing-overview-x.png)

   > [!NOTE]
   > Wenn einige Ihrer Abonnements hier nicht angezeigt werden, liegt dies möglicherweise daran, dass Sie das Abonnementverzeichnis zu irgendeinem Zeitpunkt geändert haben. Für diese Abonnements müssen Sie das Verzeichnis in das ursprüngliche Verzeichnis ändern (das Verzeichnis, in dem Sie sich anfänglich registriert haben). Wiederholen Sie anschließend Schritt 2.

1. Wählen Sie die Option **Zahlungsmethoden**.

    ![Screenshot der Seite „Zahlungsmethoden“, auf der Sie eine Zahlungsmethode hinzufügen können](./media/account-admin-tasks/subscription-payment-methods-blade.png)

Hier können Sie eine neue Kreditkarte hinzufügen, die aktive Zahlungsmethode ändern, Kreditkartendetails bearbeiten und Kreditkarten löschen.

### <a name="change-active-payment-method"></a>Ändern der aktiven Zahlungsmethode

Sie können die aktive Zahlungsmethode ändern, indem Sie eine neue Kreditkarte hinzufügen oder eine bereits gespeicherte Kreditkarte auswählen. So legen Sie eine neue Kreditkarte als aktive Zahlungsmethode fest:

1. Wählen Sie links oben das Pluszeichen (+) aus, um eine Kreditkarte hinzuzufügen.

    ![Screenshot: Pluszeichen](./media/account-admin-tasks/subscription-payment-methods-plus.png)

1. Geben Sie im Formular auf der rechten Seite Kreditkartendetails ein.

    ![Screenshot: Formular zum Hinzufügen einer Kreditkarte](./media/account-admin-tasks/subscription-add-payment-method-x.png)

1. Aktivieren Sie oberhalb des Formulars das Kontrollkästchen **Diese Zahlungsmethode als aktive Methode festlegen**, um diese Karte als Ihre aktive Zahlungsmethode festzulegen. Diese Karte wird zum aktiven Zahlungsmittel für alle Abonnements, indem dieselbe Karte wie für das ausgewählte Abonnement verwendet wird.

    ![Screenshot: Kontrollkästchen, um die Karte als aktive Zahlungsmethode festzulegen](./media/account-admin-tasks/subscription-make-active-payment-method-x.png)

1. Wählen Sie **Weiter** aus.

So legen Sie die aktive Zahlungsmethode auf eine bereits gespeicherte Kreditkarte fest:

1. Aktivieren Sie das Kontrollkästchen neben der Karte, die Sie als aktive Zahlungsmethode festlegen möchten.

    ![Screenshot: Aktiviertes Kontrollkästchen neben der Kreditkarte](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Klicken Sie auf der Befehlsleiste auf **Als aktiv festlegen**.

    ![Screenshot: Schaltfläche „Als aktiv festlegen“](./media/account-admin-tasks/subscription-checked-payment-method-set-active.png)

### <a name="edit-credit-card-details"></a>Bearbeiten von Kreditkartendetails

Wenn Sie Kreditkartendetails wie das Ablaufdatum oder die Adresse bearbeiten möchten, klicken Sie auf die Kreditkarte, die Sie bearbeiten möchten. Rechts wird ein Kreditkartenformular angezeigt.

![Screenshot: Ausgewählte Kreditkarte](./media/account-admin-tasks/subscription-edit-payment-method-x.png)

Aktualisieren Sie die Kreditkartendetails, und klicken Sie anschließend auf **Speichern**.

### <a name="remove-a-credit-card-from-the-account"></a>Entfernen einer Kreditkarte aus dem Konto

1. Aktivieren Sie das Kontrollkästchen neben der Karte, die Sie löschen möchten.

    ![Screenshot: Aktiviertes Kontrollkästchen neben der Kreditkarte](./media/account-admin-tasks/subscription-checked-payment-method-x.png)

1. Klicken Sie auf der Befehlsleiste auf **Löschen**.

    ![Screenshot: Schaltfläche „Löschen“](./media/account-admin-tasks/subscription-checked-payment-method-delete.png)

Wenn Ihre Kreditkarte die aktive Zahlungsmethode für eines Ihrer Microsoft-Abonnements ist, können Sie sie nicht aus Ihrem Azure-Konto entfernen. Ändern Sie die aktive Zahlungsmethode für alle Abonnements, die mit dieser Kreditkarte verknüpft sind, und versuchen Sie es noch mal.

### <a name="switch-to-invoice-payment"></a>Umstellen auf Zahlung per Rechnung

Wenn Sie zur Zahlung per Rechnung (Scheck/Überweisung) berechtigt sind, können Sie Ihr Abonnement im Azure-Portal auf Zahlung per Rechnung (Scheck/Überweisung) umstellen.

1. Wählen Sie auf der Befehlsleiste die Option **Zahlung per Rechnung** aus.

    ![Screenshot der Seite „Zahlungsmethoden“ mit ausgewählter Option „Zahlung per Rechnung“](./media/account-admin-tasks/subscription-payment-methods-pay-by-invoice.png)

1. Geben Sie die Adresse für die Zahlungsmethode „Rechnung“ ein.
1. Klicken Sie auf **Weiter**.

Informationen zum Beantragen der Zahlung per Rechnung finden Sie unter [Zahlen für Ihr Azure-Abonnement auf Rechnung](pay-by-invoice.md).

### <a name="edit-invoice-payment-address"></a>Bearbeiten der Adresse für die Zahlung per Rechnung

Wenn Sie die Adresse für die Zahlungsmethode „Rechnung“ bearbeiten möchten, klicken Sie in der Liste mit den Zahlungsmethoden für Ihr Abonnement auf **Rechnung**. Daraufhin wird auf der rechten Seite das Adressformular geöffnet.

## <a name="remove-spending-limit"></a>Entfernen des Ausgabenlimits

Mit dem Ausgabenlimit in Azure wird verhindert, dass Sie Ihr Guthaben überschreiten. Wenn für Ihr Abonnement eine gültige Zahlungsmethode festgelegt wurde, können Sie das Ausgabenlimit jederzeit entfernen. Bei Abonnementtypen mit Guthaben für mehrere Monate (beispielsweise Visual Studio Enterprise und Visual Studio Professional) kann das Ausgabenlimit zu Beginn des nächsten Abrechnungszeitraums reaktiviert werden.

Für Abonnements mit Verpflichtungsplänen oder mit nutzungsbasierter Bezahlung ist kein Ausgabenlimit verfügbar.

1. Melden Sie sich als Kontoadministrator beim Azure-Portal an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

    ![Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ ](./media/account-admin-tasks/search-bar.png)

1. Wählen Sie in der Liste **Meine Abonnements** Ihr Visual Studio Enterprise-Abonnement aus.

   ![Screenshot des Bereichs „Meine Abonnements“, in dem Sie Ihr Visual Studio Enterprise-Abonnement auswählen können](./media/account-admin-tasks/cost-management-overview-msdn-x.png)

    > [!NOTE]
    > Wenn einige Ihrer Visual Studio-Abonnements hier nicht angezeigt werden, liegt dies möglicherweise daran, dass Sie ein Abonnementverzeichnis zu irgendeinem Zeitpunkt geändert haben. Für diese Abonnements müssen Sie das Verzeichnis in das ursprüngliche Verzeichnis ändern (das Verzeichnis, in dem Sie sich anfänglich registriert haben). Wiederholen Sie anschließend Schritt 2.

1. Klicken Sie in der Abonnementübersicht auf das orangefarbene Banner, um das Ausgabenlimit zu entfernen.

    ![Screenshot: Banner zum Entfernen des Ausgabenlimits](./media/account-admin-tasks/msdn-remove-spending-limit-banner-x.png)

1. Wählen Sie aus, ob Sie das Ausgabenlimit dauerhaft oder nur für den aktuellen Abrechnungszeitraum entfernen möchten.

   ![Screenshot: Blatt zum Entfernen des Ausgabenlimits](./media/account-admin-tasks/remove-spending-limit-blade-x.png)

1. Klicken Sie auf **Zahlungsmethode auswählen**, um eine Zahlungsmethode für Ihr Abonnement auszuwählen. Diese wird als aktive Zahlungsmethode für Ihr Abonnement verwendet.

1. Klicken Sie auf **Fertig stellen**.

## <a name="add-credits-to-azure-in-open-subscription"></a>Hinzufügen von Guthaben zu einem Azure in Open-Abonnement

Wenn Sie über ein Azure in Open License-Abonnement verfügen, können Sie Ihrem Abonnement im Azure-Portal Guthaben hinzufügen, indem Sie einen Product Key einlösen oder Guthaben mit einer Kreditkarte erwerben.

1. Melden Sie sich als Kontoadministrator beim Azure-Portal an.
1. Suchen Sie nach **Kostenverwaltung + Abrechnung**.

    ![Screenshot: Suchen nach „Kostenverwaltung + Abrechnung“ ](./media/account-admin-tasks/search-bar.png)

1. Wählen Sie in der Liste **Meine Abonnements** Ihr Azure in Open-Abonnement aus.

    ![Screenshot des Bereichs „Meine Abonnements“, in dem Sie Ihr Azure in Open-Abonnement auswählen können](./media/account-admin-tasks/cost-management-overview-aio-x.png)

   > [!NOTE]
   > Wenn Ihr Abonnement hier nicht angezeigt wird, liegt dies möglicherweise daran, dass Sie das zugehörige Verzeichnis zu irgendeinem Zeitpunkt geändert haben. Sie müssen das Verzeichnis dieses Abonnements in das ursprüngliche Verzeichnis ändern (das Verzeichnis, in dem Sie sich anfänglich registriert haben). Wiederholen Sie anschließend Schritt 2.

1. Wählen Sie **Bonitätsgeschichte** aus.

    ![Screenshot: Bonitätsgeschichte](./media/account-admin-tasks/aio-credit-history-blade.png)

1. Wählen Sie links oben das Pluszeichen (+) aus, um Guthaben hinzuzufügen.

    ![Screenshot: Schaltfläche zum Hinzufügen von Guthaben](./media/account-admin-tasks/aio-credit-history-plus.png)

1. Wählen Sie im Dropdownfeld einen Zahlungsmethodentyp aus. Sie können entweder einen Product Key hinzufügen oder Guthaben mit einer Kreditkarte erwerben.

    ![Screenshot: Dropdownfeld für die Zahlungsmethode auf dem Blatt „Guthaben hinzufügen“](./media/account-admin-tasks/add-credits-select-payment-method.png)

1. Wenn Sie „Product Key“ ausgewählt haben, gehen Sie wie folgt vor:
    - Geben Sie den Product Key ein.
    - Klicken Sie auf **Überprüfen**.

1. Wenn Sie „Kreditkarte“ ausgewählt haben, gehen Sie wie folgt vor:
    - Klicken Sie auf **Zahlungsmethode auswählen**, um eine Kreditkarte hinzuzufügen oder eine vorhandene Kreditkarte auszuwählen.
    - Geben Sie an, wie viel Guthaben Sie hinzufügen möchten.

1. Klicken Sie auf **Anwenden**.

## <a name="troubleshooting"></a>Problembehandlung
Virtuelle Karten oder Prepaidkarten werden nicht unterstützt. Wenn beim Hinzufügen oder Aktualisieren einer gültigen Kreditkarte Fehler auftreten, versuchen Sie, den Browser im privaten Modus zu öffnen.

## <a name="next-steps"></a>Nächste Schritte
- Weitere Informationen über das [Analysieren unerwarteter Gebühren](../understand/analyze-unexpected-charges.md)
