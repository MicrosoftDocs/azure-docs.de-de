---
title: Verknüpfen eines Azure-Abonnements – Azure Active Directory B2C | Microsoft-Dokumentation
description: Enthält eine Schritt-für-Schritt-Anleitung zur Vorgehensweise, mit der die Abrechnung für einen Azure AD B2C-Mandanten in einem Azure-Abonnement ermöglicht wird.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.date: 01/24/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: d2440b638a9c8eb27505b747d26917de8032e6cd
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66510127"
---
# <a name="link-an-azure-subscription-to-an-azure-active-directory-b2c-tenant"></a>Verknüpfen eines Azure-Abonnements mit einem Azure Active Directory B2C-Mandanten

> [!IMPORTANT]
> Die aktuellen Informationen zur Abrechnung von Nutzungsgebühren und zu den Preisen für Azure Active Directory (Azure AD) B2C finden Sie unter [Azure AD B2C Preise](https://azure.microsoft.com/pricing/details/active-directory-b2c/).

Nutzungsgebühren für Azure AD B2C werden über ein Azure-Abonnement abgerechnet. Beim Erstellen eines Azure AD B2C-Mandanten muss der Mandantenadministrator den Azure AD B2C-Mandanten explizit mit einem Azure-Abonnement verknüpfen. In diesem Artikel erfahren Sie, welche Schritte erforderlich sind.

> [!NOTE]
> Ein Abonnement, das mit einem Azure AD B2C-Mandanten verknüpft ist, kann für die Abrechnung der Azure AD B2C-Nutzung oder anderer Azure-Ressourcen einschließlich zusätzlicher Azure AD B2C-Ressourcen verwendet werden.  Das Abonnement kann nicht verwendet werden, um weitere lizenzbasierte Azure-Dienste oder Office 365-Lizenzen innerhalb des Azure AD B2C-Mandanten hinzuzufügen.

Für die Abonnementverknüpfung wird innerhalb des Azure-Zielabonnements eine Azure AD B2C-Ressource erstellt. Viele Azure AD B2C-Ressourcen können innerhalb eines einzelnen Azure-Abonnements zusammen mit anderen Azure-Ressourcen (beispielsweise virtuelle Computer, Datenspeicher, Logik-Apps) erstellt werden. Alle Ressourcen innerhalb des Abonnements können Sie unter dem Azure AD-Mandanten anzeigen, dem das Abonnement zugeordnet ist.

Azure Cloud Solution Provider (CSP)-Abonnements werden in Azure AD B2C unterstützt. Die Funktionalität ist über APIs oder das Azure-Portal für Azure AD B2C und für alle Azure-Ressourcen verfügbar. CSP-Abonnementadministratoren können auf die gleiche Weise wie für alle anderen Azure-Ressourcen Beziehungen mit Azure AD B2C verknüpfen, verschieben und löschen. Die Verwaltung von Azure AD B2C mit der rollenbasierten Zugriffssteuerung wird durch die Zuordnung eines Azure AD B2C-Mandanten zu einem CSP-Abonnement nicht beeinflusst. Die rollenbasierte Zugriffssteuerung wird mithilfe von mandantenbasierten und nicht abonnementbasierten Rollen realisiert.

Für die weitere Vorgehensweise wird ein gültiges Azure-Abonnement benötigt.

## <a name="create-an-azure-ad-b2c-tenant"></a>Erstellen eines Azure AD B2C-Mandanten

Sie müssen zunächst [einen Azure AD B2C-Mandanten erstellen](active-directory-b2c-get-started.md), mit dem Sie ein Abonnement verknüpfen möchten. Überspringen Sie diesen Schritt, falls Sie bereits einen Azure AD B2C-Mandanten erstellt haben.

## <a name="open-azure-portal-in-the-azure-ad-tenant-that-shows-your-azure-subscription"></a>Öffnen des Azure-Portals in dem Azure AD-Mandanten mit Ihrem Azure-Abonnement

Navigieren Sie zu dem Azure AD-Mandanten mit Ihrem Azure-Abonnement. Öffnen Sie das [Azure-Portal](https://portal.azure.com), und navigieren Sie zu dem Azure AD-Mandanten mit dem gewünschten Azure-Abonnement.

![Wechseln zu Ihrem Azure AD-Mandanten](./media/active-directory-b2c-how-to-enable-billing/SelectAzureADTenant.png)

## <a name="find-azure-ad-b2c-in-the-azure-marketplace"></a>Suchen von Azure AD B2C im Azure Marketplace

Klicken Sie auf die Schaltfläche **Ressource erstellen**. Geben Sie im Feld **Marketplace durchsuchen** die Zeichenfolge `B2C` ein.

![Hervorgehobene Schaltfläche „Hinzufügen“ und Text „Azure AD B2C“ im Feld „Marketplace durchsuchen“](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c.png)

Klicken Sie in der Ergebnisliste auf **Azure Active Directory B2C**.

![Ergebnisliste mit ausgewählter Option „Azure Active Directory B2C“](../../includes/media/active-directory-b2c-create-tenant/find-azure-ad-b2c-result.png)

Details zu „Azure Active Directory B2C“ werden angezeigt. Klicken Sie auf die Schaltfläche **Erstellen**, um mit der Konfiguration Ihres neuen Azure Active Directory B2C-Mandanten zu beginnen.

Klicken Sie im Bildschirm für die Ressourcenerstellung auf **Vorhandenen Azure AD B2C-Mandanten mit meinem Azure-Abonnement verknüpfen**.

## <a name="create-an-azure-ad-b2c-resource-within-the-azure-subscription"></a>Erstellen einer Azure AD B2C-Ressource innerhalb des Azure-Abonnements

Wählen Sie im Dialogfeld für die Ressourcenerstellung einen Azure AD B2C-Mandanten aus der Dropdownliste aus. Es werden alle Mandanten angezeigt, für die Sie als globaler Administrator fungieren, sowie Mandanten, die noch nicht mit einem Abonnement verknüpft sind.

Der Name der Azure AD B2C-Ressource ist vorgegeben und entspricht dem Domänennamen des Azure AD B2C-Mandanten.

Wählen Sie als Abonnement ein aktives Azure-Abonnement aus, für das Sie als Administrator fungieren.

Wählen Sie eine Ressourcengruppe und einen Ressourcengruppenstandort aus. Die hier getroffene Auswahl hat keine Auswirkung auf den Standort, die Leistung oder den Abrechnungsstatus Ihres Azure AD B2C-Mandanten.

![Erstellen einer B2C-Ressource](./media/active-directory-b2c-how-to-enable-billing/createresourceb2c.png)

## <a name="manage-your-azure-ad-b2c-tenant-resources"></a>Verwalten Ihrer Azure AD B2C-Mandantenressourcen

Nach erfolgreicher Erstellung einer Azure AD B2C-Ressource innerhalb des Azure-Abonnements wird zusammen mit Ihren anderen Azure-Ressourcen eine neue Ressource vom Typ „B2C-Mandant“ angezeigt.

Diese Ressource ermöglicht Folgendes:

- Navigieren zum Abonnement, um die Abrechnungsinformationen zu überprüfen
- Navigieren zu Ihrem Azure AD B2C-Mandanten
- Senden Sie eine Supportanfrage.
- Verschieben Ihrer Azure AD B2C-Mandantenressource in ein anderes Azure-Abonnement oder in eine andere Ressourcengruppe

![Einstellungen für die B2C-Ressource](./media/active-directory-b2c-how-to-enable-billing/b2cresourcesettings.png)

## <a name="known-issues"></a>Bekannte Probleme

### <a name="self-imposed-restrictions"></a>Selbstauferlegte Einschränkungen

Ein Benutzer hat möglicherweise eine regionale Einschränkung für die Erstellung von Azure-Ressourcen eingerichtet. Diese Einschränkung verhindert unter Umständen die Erstellung der Azure AD B2C-Ressource. Um das Problem zu lösen, lockern Sie diese Einschränkung.

## <a name="next-steps"></a>Nächste Schritte

Nachdem Sie diese Schritte für die einzelnen Azure AD B2C-Mandanten ausgeführt haben, wird die Abrechnung für Ihr Azure-Abonnement gemäß Ihren Azure Direct- bzw. Enterprise Agreement-Details abgewickelt.

Sie können die Nutzungs und Abrechnungsdetails innerhalb Ihres ausgewählten Azure-Abonnement prüfen. Darüber hinaus stehen Ihnen über die [Berichterstattungs-API](active-directory-b2c-reference-usage-reporting-api.md) ausführliche Berichte zur tagtäglichen Nutzung zur Verfügung.
