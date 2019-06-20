---
title: Registrieren für Azure mit einem Office 365-Konto | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie mit einem Office 365-Konto ein Azure-Abonnement erstellen.
services: ''
documentationcenter: ''
author: JiangChen79
manager: adpick
editor: ''
tags: billing,top-support-issue
ms.assetid: 129cdf7a-2165-483d-83e4-8f11f0fa7f8b
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 06/15/2018
ms.author: banders
ms.openlocfilehash: 44ff08c84b3e95a5b598ebf4279fc2ffc46313e3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60369529"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Registrieren für ein Azure-Abonnement mit Ihrem Office 365-Konto
Wenn Sie über ein Office 365-Abonnement verfügen, können Sie mit Ihrem Office 365-Konto ein Azure-Abonnement erstellen. Melden Sie sich mit Ihrem Office 365-Benutzernamen und -Kennwort beim [Azure-Portal](https://portal.azure.com/) an. Wenn Sie virtuelle Computer einrichten oder andere Azure-Dienste verwenden möchten, müssen Sie sich für ein Azure-Abonnement registrieren. Sie können Ihr Azure-Abonnement gemeinsam mit anderen nutzen und [rollenbasierte Zugriffssteuerung verwenden, um den Zugriff auf Ihr Azure-Abonnement und Ressourcen zu verwalten](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal).

Wenn Sie bereits über ein Office 365-Konto und ein Azure-Abonnement verfügen, finden Sie weitere Informationen unter [Zuordnen eines Office 365-Mandanten zu einem Azure-Abonnement](billing-add-office-365-tenant-to-azure-subscription.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Abschließen eines Azure-Abonnements mit Ihrem Office 365-Konto

Sparen Sie Zeit, und vermeiden Sie die Anhäufung von Konten, indem Sie sich mit Ihrem Office 365-Benutzernamen und-Kennwort für Azure registrieren. 

1. Registrieren Sie sich unter [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs). 
2. Melden Sie sich mit Ihrem Office 365-Benutzernamen und -Kennwort an. Das Konto, das Sie verwenden, muss nicht über Administratorberechtigungen verfügen. Wenn Sie mehrere Office 365-Konten haben, stellen Sie sicher, dass Sie die Anmeldeinformationen für das Office 365-Konto verwenden, das Ihrem Azure-Abonnement zugeordnet werden soll. 

   ![Screenshot der Anmeldeseite.](./media/billing-use-existing-office-365-account-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Geben Sie die erforderlichen Informationen ein, und schließen Sie den Registrierungsvorgang ab. Einige Informationen sind möglicherweise nicht erforderlich, wenn Sie bereits über ein Office 365-Konto verfügen.

    ![Screenshot des Registrierungsformulars.](./media/billing-use-existing-office-365-account-azure-subscription/billing-azure-sign-up-fill-information.png)

- Wenn Sie dem Azure-Abonnement weitere Benutzer aus Ihrer Organisation hinzufügen möchten, finden Sie weitere Informationen unter [Erste Schritte mit der Zugriffsverwaltung im Azure-Portal](../role-based-access-control/overview.md). 

## <a id="more-about-subs">Weitere Informationen zu Azure- und Office 365-Abonnements</a>
Office 365 und Azure verwenden den Azure AD-Dienst zum Verwalten von Benutzern und Abonnements. Das Azure-Verzeichnis funktioniert wie ein Container, in dem Sie Benutzer und Abonnements gruppieren können. Damit Sie für Ihre Azure- und Office 365-Abonnements die gleichen Benutzerkonten verwenden können, müssen Sie sicherstellen, dass die Azure-Abonnements im gleichen Verzeichnis wie die Office 365-Abonnements erstellt werden. Berücksichtigen Sie dabei Folgendes:

* Ein Abonnement wird in einem Verzeichnis erstellt.
* Benutzer gehören Verzeichnissen an.
* Ein Abonnement wird in dem Verzeichnis des Benutzers erstellt, der das Abonnement erstellt. Das heißt, Ihr Office 365-Abonnement ist mit dem gleichen Konto verknüpft wie Ihr Azure-Abonnement.
* Azure-Abonnements gehören einzelnen Benutzern im Verzeichnis.
* Office 365-Abonnements gehören dem Verzeichnis selbst. Benutzer mit den richtigen Berechtigungen im Verzeichnis können diese Abonnements verwalten.

![Screenshot der Beziehung zwischen dem Verzeichnis, Benutzern und Abonnements.](./media/billing-use-existing-office-365-account-azure-subscription/19-background-information.png)

Weitere Informationen finden Sie unter [Beziehung zwischen Azure-Abonnements und Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Sie brauchen Hilfe? Wenden Sie sich an uns.

Wenn Sie weitere Fragen haben oder Hilfe benötigen, [erstellen Sie eine Supportanfrage](https://go.microsoft.com/fwlink/?linkid=2083458).