---
title: Verwalten des Benutzerzugriffs mit Zugriffsüberprüfungen – Azure Active Directory | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie den Benutzerzugriff als Mitgliedschaft in einer Gruppe oder als Zuweisung zu einer Anwendung mit Azure Active Directory-Zugriffsüberprüfungen verwalten.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: rolyon
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9d873f1dace3e3be6a8767e77d57db5ac554afd7
ms.sourcegitcommit: cababb51721f6ab6b61dda6d18345514f074fb2e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/04/2019
ms.locfileid: "66473810"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Verwalten des Benutzerzugriffs mit Azure AD-Zugriffsüberprüfungen

Mit Azure Active Directory (Azure AD) können Sie ganz einfach den entsprechenden Zugriff von Benutzern sicherstellen. Hierzu können Sie die Benutzer selbst oder einen Entscheidungsträger bitten, an einer Zugriffsüberprüfung teilzunehmen und den Zugriff des Benutzers erneut zu zertifizieren (oder zu „attestieren“). Basierend auf Vorschlägen von Azure AD können die Prüfer die Notwendigkeit des weiteren Zugriffs der einzelnen Benutzer abwägen. Nach Abschluss einer Zugriffsüberprüfung können Sie dann Änderungen vornehmen und Zugriffsrechte für Benutzer entfernen, die diese nicht mehr benötigen.

> [!NOTE]
> Wenn Sie nur den Zugriff von Gastbenutzern und nicht aller Benutzertypen überprüfen möchten, finden Sie entsprechende Informationen unter [Verwalten des Gastzugriffs mit Azure AD-Zugriffsüberprüfungen](manage-guest-access-with-access-reviews.md). Wenn Sie die Benutzermitgliedschaft in Administratorrollen wie „Globaler Administrator“ überprüfen möchten, finden Sie entsprechende Informationen unter [Starten einer Zugriffsüberprüfung in Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).

## <a name="prerequisites"></a>Voraussetzungen

- Azure AD Premium P2

Weitere Informationen finden Sie unter [Welche Benutzer benötigen Lizenzen?](access-reviews-overview.md#which-users-must-have-licenses)

## <a name="create-and-perform-an-access-review"></a>Erstellen und Durchführen einer Zugriffsüberprüfung

Sie können einen oder mehrere Benutzer als Prüfer einer Zugriffsüberprüfung festlegen.  

1. Wählen Sie eine Gruppe in Azure AD mit mindestens einem Mitglied aus. Oder wählen Sie eine mit Azure AD verbundene Anwendung aus, der mindestens ein Benutzer zugewiesen ist. 

2. Legen Sie fest, ob jeder Benutzer seinen eigenen Zugriff oder ob ein oder mehrere Benutzer den Zugriff aller Benutzer überprüfen soll/sollen.

3. Als globaler Administrator oder Benutzeradministrator navigieren Sie zur Seite [Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Erstellen Sie die Zugriffsüberprüfung. Weitere Informationen finden Sie unter [Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md).

5. Bitten Sie die Prüfer zu Beginn der Zugriffsüberprüfung, ihre Einschätzung abzugeben. Standardmäßig erhalten alle Prüfer eine E-Mail von Azure AD mit einem Link zu dem Zugriffsbereich, in dem sie [den Zugriff auf Gruppen oder Anwendungen überprüfen](perform-access-review.md).

6. Wenn die Prüfer keine Angaben gemacht haben, kann Azure AD ihnen eine Erinnerung senden. Standardmäßig sendet Azure AD automatisch eine Erinnerung, wenn die Prüfer nach der Hälfte der Zeit noch nicht reagiert haben.

7. Nachdem die Prüfer ihre Einschätzung abgegeben haben, können Sie die Zugriffsüberprüfung beenden und die Änderungen anwenden. Weitere Informationen finden Sie unter [Abschließen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](complete-access-review.md).


## <a name="next-steps"></a>Nächste Schritte

[Erstellen einer Zugriffsüberprüfung von Gruppen oder Anwendungen](create-access-review.md)




