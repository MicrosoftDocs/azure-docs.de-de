---
title: Verwalten des Zugriffs auf Azure-Abrechnungen | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie Mitgliedern Ihres Teams Zugriff auf Ihre Azure-Abrechnungsinformationen gewähren.
services: ''
documentationcenter: ''
author: vikramdesai01
manager: amberb
editor: ''
tags: billing
ms.assetid: e4c4d136-2826-4938-868f-a7e67ff6b025
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/02/2018
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 8c2843840790d1e0dbfd4a789775c6c7ceb51a54
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60918520"
---
# <a name="manage-access-to-billing-information-for-azure"></a>Verwalten des Zugriffs auf Abrechnungsinformationen für Azure

Für die meisten Abonnements können Sie den Mitgliedern Ihres Teams im Azure-Portal unter **Abonnements** Abrechnungsinformationen zur Verfügung stellen. Wenn Sie Azure-Kunde mit einem Enterprise Agreement (EA-Kunde) und Unternehmensadministrator sind, können Sie Abteilungsadministratoren und Kontobesitzern im Enterprise Portal Berechtigungen erteilen.

## <a name="give-access-to-billing"></a>Gewähren des Zugriffs auf Abrechnungen

Alle Benutzer – mit Ausnahme von EA-Kunden – können den Zugriff auf die Abrechnungsinformationen von Azure gewähren, indem sie Teammitgliedern eine der folgenden Benutzerrollen zuweisen:

- Kontoadministrator
- Dienstadministrator
- Co-Admin
- Owner (Besitzer)
- Mitwirkender
- Leser
- Abrechnungsleser

Wie Sie Rollen zuweisen, erfahren Sie unter [Verwalten des Zugriffs mithilfe der RBAC und des Azure-Portals](../role-based-access-control/role-assignments-portal.md).

Diese Rollen haben Zugriff auf Abrechnungsinformationen im [Azure-Portal](https://portal.azure.com/). Benutzer, denen diese Rollen zugewiesen sind, können auch die [Abrechnungs-APIs](billing-usage-rate-card-overview.md) verwenden, um Rechnungen und Nutzungsdetails programmgesteuert abzurufen. Weitere Informationen finden Sie unter [Rollen für die rollenbasierte Zugriffssteuerung in Azure](../role-based-access-control/built-in-roles.md).

### <a name="opt-in"></a> Berechtigen von Benutzern zum Herunterladen von Rechnungen

Nachdem Sie den Mitgliedern Ihres Teams die entsprechenden Rollen zugewiesen haben, muss der Kontoadministrator den Zugriff zum Herunterladen von Rechnungen im Azure-Portal aktivieren. Rechnungen vor Dezember 2016 stehen nur dem Kontoadministrator zur Verfügung.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie als Kontoadministrator auf dem Blatt [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) im Azure-Portal Ihr Abonnement aus.

1. Wählen Sie **Rechnungen** und dann **Access to invoices** (Zugriff auf Rechnungen) aus.

    ![Der Screenshot zeigt das Delegieren des Zugriffs auf Rechnungen](./media/billing-manage-access/AA-optin.png)

1. Wählen Sie **An** und „Speichern“ aus.

    ![Der Screenshot zeigt das Ein/Aus-Schalten zum Delegieren des Zugriffs auf Rechnungen](./media/billing-manage-access/AA-optinAllow.png)

Der Kontoadministrator kann auch konfigurieren, dass ihm Rechnungen per E-Mail gesendet werden. Weitere Informationen finden Sie unter [Empfangen Ihrer Rechnung per E-Mail](billing-download-azure-invoice-daily-usage-date.md).

## <a name="give-read-only-access-to-billing"></a>Gewähren des Lesezugriffs auf Abrechnungen

Weisen Sie die Rolle „Abrechnungsleser“ einem Benutzer zu, der Lesezugriff auf die Abrechnungsinformationen des Abonnements erhalten soll, Azure-Dienste jedoch nicht verwalten oder erstellen darf. Diese Rolle eignet sich für die Benutzer in einer Organisation, die für die Finanz- und Kostenverwaltung für Azure-Abonnements zuständig sind.

Wenn Sie EA-Kunde sind, kann ein Kontobesitzer oder Abteilungsadministrator Teammitgliedern die Rolle „Abrechnungsleser“ zuweisen. Damit Abrechnungsleser jedoch auf Abrechnungsinformationen für die Abteilung oder das Konto zugreifen können, muss der Unternehmensadministrator im Enterprise Portal die Richtlinie **Kontobesitzer können Gebühren anzeigen** bzw. **Abteilungsadministratoren können Gebühren anzeigen** aktivieren.

Das Feature Abrechnungsleser befindet sich in der Vorschauversion und unterstützt noch keine nicht globalen Clouds.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an.
1. Wählen Sie auf dem Blatt [Abonnements](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) im Azure-Portal Ihr Abonnement aus.

1. Wählen Sie die Option **Zugriffssteuerung (IAM)** .
1. Wählen Sie **Rollenzuweisungen** aus, um alle Rollenzuweisungen für dieses Abonnement anzuzeigen.
1. Wählen Sie **Rollenzuweisung hinzufügen** aus.
1. Wählen Sie in der Dropdownliste **Rolle** **Abrechnungsleser** aus.
1. Geben Sie im Textfeld **Auswählen** den Namen oder die E-Mail-Adresse des Benutzers ein, den Sie hinzufügen möchten.
1. Wählen Sie den Benutzer aus.
1. Wählen Sie **Speichern** aus.
1. Nach einigen Augenblicken wird dem Benutzer im Abonnementbereich die Rolle „Abrechnungsleser“ zugewiesen.
1. Der Abrechnungsleser erhält eine E-Mail mit einem Link zum Anmelden.

    ![Screenshot: Inhalte, die der Abrechnungsleser im Azure-Portal sehen kann](./media/billing-manage-access/billing-reader-view.png)

## <a name="allow-department-administrator-or-account-owner-billing-access"></a>Gewähren des Zugriffs auf Abrechnungen für Abteilungsadministratoren oder Kontobesitzer

Der Unternehmensadministrator kann Abteilungsadministratoren und Kontobesitzern erlauben, Nutzungsdetails und die Kosten für die von ihnen verwalteten Abteilungen und Konten anzusehen.

1. Melden Sie sich als Unternehmensadministrator beim [Enterprise Agreement-Portal](https://ea.azure.com/) an.
1. Wählen Sie **Verwalten** aus.
1. Ändern Sie unter **Registrierung** **Abteilungsadministratoren können Gebühren anzeigen** zu **Aktiviert**, damit der Abteilungsadministrator die Nutzung und die Kosten anzeigen kann.
1. Ändern Sie **Kontobesitzer können Gebühren anzeigen** zu **Aktiviert**, damit der Kontobesitzer die Nutzung und die Kosten anzeigen kann.


Weitere Informationen finden Sie unter [Informationen zu Azure Enterprise Agreement-Administratorrollen in Azure](billing-understand-ea-roles.md).

## <a name="only-account-admins-can-access-account-center"></a>Gewähren des Zugriffs auf das Kontocenter nur für Kontoadministratoren

Der Kontoadministrator ist der rechtmäßige Besitzer des Abonnements. Standardmäßig ist die Person, die sich für das Azure-Abonnement registriert oder dieses erworben hat, als Kontoadministrator festgelegt, es sei denn, der [Besitz des Abonnements wurde auf einen anderen Benutzer übertragen](billing-subscription-transfer.md). Der Kontoadministrator kann im [Kontocenter](https://account.azure.com/Subscriptions) Abonnements erstellen und kündigen, die Rechnungsadresse für ein Abonnement ändern und Zugriffsrichtlinien für das Abonnement verwalten.

## <a name="next-steps"></a>Nächste Schritte

- Benutzer in anderen Rollen, z.B. „Besitzer“ oder „Mitwirkender“, können nicht nur auf Abrechnungsinformationen, sondern auch auf Azure-Dienste zugreifen. Informationen zum Verwalten dieser Rollen finden Sie unter [Verwalten des Zugriffs mithilfe der RBAC und des Azure-Portals](../role-based-access-control/role-assignments-portal.md).
- Weitere Informationen zu Rollen finden Sie unter [Integrierte Rollen für Azure-Ressourcen](../role-based-access-control/built-in-roles.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).
