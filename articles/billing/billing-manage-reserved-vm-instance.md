---
title: Verwalten von Azure-Reservierungen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Abonnementumfang ändern und den Zugriff auf Azure-Reservierungen verwalten können.
ms.service: billing
documentationcenter: ''
author: yashesvi
manager: yashesvi
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/13/2019
ms.author: banders
ms.openlocfilehash: 9a5b200ffb9441b90875c7764786004ff5f1e8a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66127141"
---
# <a name="manage-reservations-for-azure-resources"></a>Verwalten von Reservierungen für Azure-Ressourcen

Nachdem Sie eine Reservierung für Azure erworben haben, müssen Sie sie möglicherweise auf ein anderes Abonnement anwenden, ändern, wer die Reservierung verwalten kann, oder den Reservierungsumfang ändern. Sie können auch eine Reservierung in zwei Reservierungen aufteilen, um einige der von Ihnen erworbenen Instanzen auf ein anderes Abonnement anzuwenden.

Wenn Sie Azure Reserved Virtual Machine Instances erworben haben, können Sie die Optimierungseinstellung für die Reservierung ändern. Der Reservierungsrabatt kann für virtuelle Computer (Virtual Machines, VMs) in der gleichen Reihe gelten. Sie können aber auch Rechenzentrumskapazität für eine bestimmte VM-Größe reservieren.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="reservation-order-and-reservation"></a>Reservierungsauftrag und Reservierung

Beim Kauf einer Reservierung werden zwei Objekte erstellt: **Reservierungsauftrag** und **Reservierung**.

Beim Kauf umfasst ein Reservierungsauftrag eine Reservierung. Bei Aktionen wie z. B. Teilung, Zusammenführung, teilweiser Erstattung oder Austausch werden neue Reservierungen unter dem **Reservierungsauftrag** erstellt.

Zum Anzeigen eines Reservierungsauftrags navigieren Sie zu **Reservierungen**, wählen Sie die Reservierung aus, und klicken Sie dann auf die **Reservierungsauftrags-ID**.

![Beispiel für die Details eines Reservierungsauftrags mit Reservierungsauftrags-ID ](./media/billing-manage-reserved-vm-instance/reservation-order-details.png)

Eine Reservierung erbt die Berechtigungen des entsprechenden Reservierungsauftrags.

## <a name="change-the-reservation-scope"></a>Ändern des Reservierungsumfangs

 Ihr Reservierungsrabatt gilt für virtuelle Computer, SQL-Datenbank-Instanzen, Azure Cosmos DB oder andere Ressourcen, die mit Ihrer Reservierung übereinstimmen und im Reservierungsumfang ausgeführt werden. Der Abrechnungskontext ist abhängig vom Abonnement, das verwendet wurde, um die Reservierung zu erwerben.

So aktualisieren Sie den Bereich einer Reservierung:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **Alle Dienste** > **Reservierungen**.
3. Wählen Sie die Reservierung aus.
4. Wählen Sie **Einstellungen** > **Konfiguration** aus.
5. Ändern Sie den Bereich.

Wenn Sie den Bereich aus „Freigegeben“ in „Einzeln“ ändern, können Sie nur Abonnements auswählen, deren Besitzer Sie sind. Es können nur Abonnements ausgewählt werden, die sich im gleichen Abrechnungskontext wie die Reservierung befinden.

Der Bereich gilt nur für das Angebot mit nutzungsbasierter Zahlung MS-AZR-0003P oder MS-AZR-0023P, für das Enterprise-Angebot MS-AZR-0017P oder MS-AZR-0148P oder für CSP-Abonnements.

## <a name="add-or-change-users-who-can-manage-a-reservation"></a>Hinzufügen oder Ändern von Benutzern, die eine Reservierung verwalten können

Sie können die Verwaltung einer Reservierung delegieren, indem Sie den Rollen im Reservierungsauftrag oder in der Reservierung Personen hinzufügen. Standardmäßig verfügen die Person, die den Reservierungsauftrag erteilt, und der Kontoadministrator über die Rolle „Besitzer“ für den Reservierungsauftrag und die Reservierung.

Sie können den Zugriff auf Reservierungsaufträge und Reservierungen unabhängig von den Abonnements verwalten, die den Reservierungsrabatt erhalten. Wenn Sie einer Person die Berechtigung zum Verwalten eines Reservierungsauftrags oder der Reservierung erteilen, erhält diese keine Berechtigung zum Verwalten des Abonnements. Wenn Sie einer Person die Berechtigung erteilen, ein Abonnement im Geltungsbereich der Reservierung zu verwalten, erhält diese Person analog dazu keine Rechte zum Verwalten des Reservierungsauftrags oder der Reservierung.

Für eine Erstattung oder einen Austausch muss der Benutzer Zugriff auf den Reservierungsauftrag haben. Wenn Sie einem Benutzer Berechtigungen erteilen, empfiehlt es sich, die Berechtigungen für den Reservierungsauftrag und nicht für die Reservierung zu erteilen.


So delegieren Sie die Zugriffsverwaltung für eine Reservierung:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Wählen Sie **Alle Dienste** > **Reservierungen** aus, um Reservierungen aufzulisten, auf die Sie Zugriff haben.
3. Wählen Sie die Reservierung aus, für die Sie den Zugriff an andere Benutzer delegieren möchten.
4. Wählen Sie die Option **Zugriffssteuerung (IAM)** .
5. Wählen Sie **Rollenzuweisung hinzufügen** > **Rolle** > **Besitzer** aus. Oder wählen Sie eine andere Rolle aus, wenn Sie eingeschränkten Zugriff erteilen möchten.
6. Geben Sie die E-Mail-Adresse des Benutzers ein, den Sie als Besitzer hinzufügen möchten.
7. Wählen Sie den Benutzer und dann **Speichern** aus.

## <a name="split-a-single-reservation-into-two-reservations"></a>Aufteilten einer einzelnen Reservierung in zwei Reservierungen

 Wenn Sie mehrere Ressourceninstanzen innerhalb einer Reservierung erworben haben, können Sie Instanzen innerhalb dieser Reservierung verschiedenen Abonnements zuweisen. Standardmäßig weisen alle Instanzen einen Bereich auf: ein Einzelabonnement oder „Freigegeben“. Beispiel: Sie haben 10 Reservierungsinstanzen erworben und den Bereich als Abonnement A festgelegt. Sie können nun den Bereich für sieben Reservierungen in Abonnement A und die restlichen drei in Abonnement B ändern. Durch die Aufteilung einer Reservierung können Sie Instanzen für die fein abgestufte Bereichsverwaltung verteilen. Sie können die Zuordnung zu Abonnements vereinfachen, indem Sie den Bereich „Freigegeben“ auswählen. Aus Gründen der Kostenverwaltung oder der Budgetierung können Sie jedoch bestimmten Abonnements Mengen zuordnen.

 Sie können eine Reservierung mithilfe von PowerShell, der CLI oder über die API in zwei Reservierungen aufteilen.

### <a name="split-a-reservation-by-using-powershell"></a>Aufteilen einer Reservierung mithilfe von PowerShell

1. Rufen Sie die Reservierungsauftrags-ID ab, indem Sie den folgenden Befehl ausführen:

    ```powershell
    # Get the reservation orders you have access to
    Get-AzReservationOrder
    ```

2. Abrufen der Details einer Reservierung:

    ```powershell
    Get-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a
    ```

3. Aufteilen der Reservierung in zwei Reservierungen und Verteilen der Instanzen:

    ```powershell
    # Split the reservation. The sum of the reservations, the quantity, must equal the total number of instances in the reservation that you're splitting.
    Split-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId b8be062a-fb0a-46c1-808a-5a844714965a -Quantity 3,2
    ```
4. Sie können den Bereich aktualisieren, indem Sie den folgenden Befehl ausführen:

    ```powershell
    Update-AzReservation -ReservationOrderId a08160d4-ce6b-4295-bf52-b90a5d4c96a0 -ReservationId 5257501b-d3e8-449d-a1ab-4879b1863aca -AppliedScopeType Single -AppliedScope /subscriptions/15bb3be0-76d5-491c-8078-61fe3468d414
    ```

## <a name="cancellations-and-exchanges"></a>Stornierungen und Umtausch

Abhängig vom Reservierungstyp können Sie eine Reservierung stornieren oder ändern. Weitere Informationen finden Sie in den Abschnitten zu Stornierung und Umtausch in den folgenden Themen:

- [Vorauszahlen für virtuelle Computer mit Azure Reserved VM Instances](..//virtual-machines/windows/prepay-reserved-vm-instances.md#cancellations-and-exchanges)
- [Vorauszahlen für SUSE-Softwarepläne aus Azure-Reservierungen](../virtual-machines/linux/prepay-suse-software-charges.md#cancellation-and-exchanges-not-allowed)
- [Vorauszahlen von SQL-Datenbank-Computeressourcen mit reservierter Azure SQL-Datenbank-Kapazität](../sql-database/sql-database-reserved-capacity.md#cancellations-and-exchanges)

## <a name="change-optimize-setting-for-reserved-vm-instances"></a>Ändern der Optimierungseinstellung für reservierte VM-Instanzen

 Wenn Sie eine reservierte VM-Instanz erwerben, wählen Sie die Flexibilität bei der Instanzgröße oder die Kapazitätspriorität aus. Bei Auswahl der Flexibilität bei der Instanzgröße wird der Reservierungsrabatt auf weitere VMs in der [Gruppe mit VMs derselben Größe](https://aka.ms/RIVMGroups) angewendet. Die Option „Kapazitätspriorität“ priorisiert Rechenzentrumskapazität für Ihre Bereitstellungen. Diese Option bietet Ihnen zusätzliche Sicherheit, dass die VM-Instanzen gestartet werden können, wenn sie benötigt werden.

Wenn der Reservierungsumfang „Freigegeben“ lautet, ist die Flexibilität bei der Instanzgröße standardmäßig aktiviert. Die Rechenzentrumskapazität wird bei VM-Bereitstellungen nicht priorisiert.

Für Reservierungen, bei denen der Umfang auf Einzelabonnements festgelegt ist, können Sie die Reservierung anstelle von Flexibilität bei der VM-Instanzgröße im Hinblick auf die Kapazitätspriorität optimieren.

Gehen Sie wie folgt vor, um die Optimierungseinstellung für die Reservierung zu aktualisieren:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie auf **Alle Dienste** > **Reservierungen**.
3. Wählen Sie die Reservierung aus.
4. Wählen Sie **Einstellungen** > **Konfiguration** aus.
5. Ändern Sie die Einstellung **Optimiert für**.

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure-Reservierungen finden Sie in den folgenden Artikeln:

- [Was sind Azure-Reservierungen?](billing-save-compute-costs-reservations.md)

Kaufen eines Serviceplans:
- [Vorauszahlen für virtuelle Computer mit Azure Reserved VM Instances](../virtual-machines/windows/prepay-reserved-vm-instances.md)
- [Vorauszahlen von SQL-Datenbank-Computeressourcen mit reservierter Azure SQL-Datenbank-Kapazität](../sql-database/sql-database-reserved-capacity.md)
- [Vorauszahlen für Azure Cosmos DB-Ressourcen mit reservierter Azure Cosmos DB-Kapazität](../cosmos-db/cosmos-db-reserved-capacity.md)

Kaufen eines Softwareplans:
- [Vorauszahlen für Red Hat-Softwarepläne aus Azure-Reservierungen](../virtual-machines/linux/prepay-rhel-software-charges.md)
- [Vorauszahlen für SUSE-Softwarepläne aus Azure-Reservierungen](../virtual-machines/linux/prepay-suse-software-charges.md)

Grundlegendes zu Rabatt und Nutzung:
- [Grundlegendes zur Anwendung des Rabatts für VM-Reservierungen](billing-understand-vm-reservation-charges.md)
- [Grundlegendes zur Anwendung des Rabatts für den Red Hat Linux Enterprise-Softwareplan](../billing/billing-understand-rhel-reservation-charges.md)
- [Grundlegendes zur Anwendung des Rabatts für den SUSE Linux Enterprise-Softwareplan](../billing/billing-understand-suse-reservation-charges.md)
- [Grundlegendes zur Anwendung anderer Reservierungsrabatte](billing-understand-reservation-charges.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für das Abonnement mit nutzungsbasierter Bezahlung](billing-understand-reserved-instance-usage.md)
- [Grundlegendes zur Nutzung von Azure-Reservierungen für den Konzernbeitritt](billing-understand-reserved-instance-usage-ea.md)
- [Nicht in Azure-Reservierungen enthaltene Windows-Softwarekosten](billing-reserved-instance-windows-software-costs.md)
