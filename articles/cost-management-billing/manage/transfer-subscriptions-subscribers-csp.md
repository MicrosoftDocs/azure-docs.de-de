---
title: Übertragen von Azure-Abonnements zwischen Abonnenten und CSPs
description: Erfahren Sie, wie Azure-Abonnements zwischen Abonnenten und CSPs übertragen werden können.
author: bandersmsft
ms.reviewer: dhgandhi
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 11/17/2020
ms.author: banders
ms.openlocfilehash: 4cc213db19a567f18c5ff483b64a75e3bbeef892
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94962109"
---
# <a name="transfer-azure-subscriptions-between-subscribers-and-csps"></a>Übertragen von Azure-Abonnements zwischen Abonnenten und CSPs

In diesem Artikel werden allgemeine Schritte zum Übertragen von Azure-Abonnements zwischen Cloud Solution Provider-Partnern (CSP-Partnern) und ihren Kunden beschrieben. Die hier aufgeführten Informationen sollen den Azure-Abonnenten bei der Koordination mit dem Partner unterstützen. Die Informationen, die Microsoft-Partner für den Übertragungsprozess verwenden, werden in [Erfahren Sie, wie Sie die Azure-Abonnements eines Kunden auf einen anderen Partner übertragen](/partner-center/switch-azure-subscriptions-to-a-different-partner) beschrieben.

Bevor Sie eine Übertragungsanforderung starten, sollten Sie alle Kosten- und Abrechnungsinformationen herunterladen oder exportieren, die Sie behalten möchten. Abrechnungs- und Nutzungsinformationen werden nicht mit dem Abonnement übertragen. Weitere Informationen zum Exportieren von Cost Management-Daten finden Sie unter [Erstellen und Verwalten von exportierten Daten](../costs/tutorial-export-acm-data.md). Weitere Informationen zum Herunterladen Ihrer Rechnungs- und Nutzungsdaten finden Sie unter [Herunterladen oder Anzeigen Ihrer Azure-Rechnungen und täglichen Nutzungsdaten](download-azure-invoice-daily-usage-date.md).

Wenn Sie bereits über Reservierungen verfügen, sind diese nach der Übertragung eines Abonnements nicht mehr gültig. Achten Sie darauf, [alle Reservierungen zu stornieren und zu erstatten](../reservations/exchange-and-refund-azure-reservations.md), bevor Sie ein Abonnement übertragen.

## <a name="transfer-ea-subscriptions-to-a-csp-partner"></a>Übertragen von Enterprise Agreement-Abonnements auf einen CSP-Partner

CSP-Partner mit direkter Abrechnung, die als [Azure Expert Managed Services Provider (MSP)](https://partner.microsoft.com/membership/azure-expert-msp) zertifiziert sind, können die Übertragung von Azure-Abonnements für ihre Kunden anfordern, die über ein direktes Enterprise Agreement (EA) verfügen. Abonnementübertragungen sind nur für Kunden zulässig, die mit dem CSP-Programm eine Microsoft-Kundenvereinbarung (Microsoft Customer Agreement, MCA) akzeptiert und einen Azure-Plan erworben haben.

Wenn die Anforderung genehmigt wird, kann der CSP seinen Kunden eine kombinierte Rechnung stellen. Weitere Informationen zum Übertragen von Abonnements durch CSPs finden Sie unter [Übernehmen des Abrechnungsbesitzes für Azure-Abonnements für Ihr MPA-Konto](mpa-request-ownership.md).

>[!IMPORTANT]
> Nachdem ein EA-Abonnement an einen CSP-Partner übertragen wurde, werden Kontingentsteigerungen, die zuvor auf das EA-Abonnement angewendet wurden, auf den Standardwert zurückgesetzt. Wenn nach der Übertragung des Abonnements ein zusätzliches Kontingent erforderlich ist, muss der CSP-Anbieter die Anforderung einer [Kontingenterhöhung](../../azure-portal/supportability/regional-quota-requests.md) einreichen. 

## <a name="other-subscription-transfers-to-a-csp-partner"></a>Übertragungen anderer Abonnements an einen CSP-Partner

Um andere Azure-Abonnements an einen CSP-Partner zu übertragen, muss der Abonnent Ressourcen aus Quellabonnements in CSP-Abonnements verschieben. Verwenden Sie die folgenden Anleitungen, um Ressourcen zwischen Abonnements zu verschieben.

1. Arbeiten Sie mit Ihrem CSP-Partner zusammen, um Azure CSP-Zielabonnements zu erstellen.
1. Stellen Sie sicher, dass sich Quell- und Zielabonnement des CSP in demselben Azure AD-Mandanten (Azure Active Directory) befinden.  
    Der Azure AD-Mandant für ein Azure CSP-Abonnement kann nicht geändert werden. Stattdessen müssen Sie das Quellabonnement dem Azure AD-Mandanten des CSP hinzufügen oder zuordnen. Weitere Informationen finden Sie unter [Zuordnen oder Hinzufügen eines Azure-Abonnements zu Ihrem Azure Active Directory-Mandanten](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    > [!IMPORTANT]
    > - Wenn Sie ein Abonnement einem anderen Azure AD-Verzeichnis zuordnen, verlieren Benutzer, denen Rollen mit der [rollenbasierten Zugriffssteuerung von Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) zugewiesen wurden, ihren Zugriff. Klassische Abonnementadministratoren (Dienstadministrator und Co-Admin) verlieren ebenfalls ihren Zugriff.
    > - Richtlinienzuweisungen werden auch aus einem Abonnement entfernt, wenn das Abonnement einem anderen Verzeichnis zugeordnet ist.
1. Das Benutzerkonto, das Sie für die Übertragung verwenden, muss für beide Abonnements über [Azure RBAC](add-change-subscription-administrator.md)-Besitzerzugriff verfügen.
1. [Überprüfen](/rest/api/resources/resources/validatemoveresources) Sie zu Beginn, ob alle Azure-Ressourcen aus dem Quellabonnement in das Zielabonnement verschoben werden können.  
    Einige Azure-Ressourcen können nicht zwischen Abonnements verschoben werden. Die komplette Liste der Azure-Ressourcen, die verschoben werden können, finden Sie unter [Unterstützung des Verschiebevorgangs für Ressourcen](../../azure-resource-manager/management/move-support-resources.md).
    > [!IMPORTANT]
    >  - Azure CSP unterstützt nur Azure Resource Manager-Ressourcen. Wenn Azure-Ressourcen im Quellabonnement mit dem klassischen Azure-Bereitstellungsmodell erstellt wurden, müssen diese zu [Azure Resource Manager](/azure/cloud-solution-provider/migration/ea-payg-to-azure-csp/ea-open-direct-asm-to-arm) migriert werden, damit sie verschoben werden können. Sie müssen ein Partner sein, um die Webseite anzeigen zu können.

1. Stellen Sie sicher, dass alle Quellabonnementdienste das Azure Resource Manager-Modell verwenden. Übertragen Sie dann Ressourcen aus dem Quellabonnement durch [Azure-Ressourcenverschiebung](../../azure-resource-manager/management/move-resource-group-and-subscription.md) in das Zielabonnement.
    > [!IMPORTANT]
    >  - Das Verschieben von Azure-Ressourcen zwischen Abonnements kann je nach den in den Abonnements enthaltenen Ressourcen zu Dienstdowntime führen.

## <a name="transfer-csp-subscription-to-other-offer"></a>Übertragen von CSP-Abonnements auf ein anderes Angebot

Um andere Abonnements von einem CSP-Partner auf ein anderes Azure-Angebot zu übertragen, muss der Abonnent Ressourcen zwischen CSP-Quellabonnements und Zielabonnements verschieben.

1. Erstellen Sie Azure-Zielabonnements.
1. Stellen Sie sicher, dass sich Quell- und Zielabonnement im gleichen Azure Active Directory-Mandanten (Azure AD) befinden. Weitere Informationen zum Ändern eines Azure AD-Mandanten finden Sie unter [Zuordnen oder Hinzufügen eines Azure-Abonnements zu Ihrem Azure Active Directory-Mandanten](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).
    Hinweis: Die Option „Verzeichnis ändern“ wird für das CSP-Abonnement nicht unterstützt. Sie nehmen beispielsweise eine Übertragung von einem CSP-Abonnement zu einem Abonnement mit nutzungsbasierter Bezahlung vor. Sie müssen das Verzeichnis des Abonnements mit nutzungsbasierter Bezahlung so ändern, dass es dem Verzeichnis entspricht.

    > [!IMPORTANT]
    >  - Wenn Sie ein Abonnement einem anderen Verzeichnis zuordnen, verlieren Benutzer, denen Rollen über [Azure RBAC](../../role-based-access-control/role-assignments-portal.md) zugewiesen wurden, ihren Zugriff. Klassische Abonnementadministratoren (Dienstadministrator und Co-Admin) verlieren ebenfalls ihren Zugriff.
    >  - Richtlinienzuweisungen werden auch aus einem Abonnement entfernt, wenn das Abonnement einem anderen Verzeichnis zugeordnet ist.

1. Das Benutzerkonto, das Sie für die Übertragung verwenden, muss für beide Abonnements über [Azure RBAC](add-change-subscription-administrator.md)-Besitzerzugriff verfügen.
1. [Überprüfen](/rest/api/resources/resources/validatemoveresources) Sie zu Beginn, ob alle Azure-Ressourcen aus dem Quellabonnement in das Zielabonnement verschoben werden können.
    > [!IMPORTANT]
    >  - Einige Azure-Ressourcen können nicht zwischen Abonnements verschoben werden. Die komplette Liste der Azure-Ressourcen, die verschoben werden können, finden Sie unter [Unterstützung des Verschiebevorgangs für Ressourcen](../../azure-resource-manager/management/move-support-resources.md).

1. Übertragen Sie Ressourcen aus dem Quellabonnement durch [Azure-Ressourcenverschiebung](../../azure-resource-manager/management/move-resource-group-and-subscription.md) in das Zielabonnement.
    > [!IMPORTANT]
    >  - Das Verschieben von Azure-Ressourcen zwischen Abonnements kann je nach den in den Abonnements enthaltenen Ressourcen zu Dienstdowntime führen.

## <a name="next-steps"></a>Nächste Schritte
- [Übernehmen des Abrechnungsbesitzes für Azure-Abonnements für Ihr MPA-Konto](mpa-request-ownership.md)
- [Verwalten von Konten und Abonnements mit der Azure-Abrechnung](../index.yml)
