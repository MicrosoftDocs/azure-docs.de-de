---
title: Behandeln von Problemen bei Azure Migrate-Projekten
description: Hilft Ihnen bei der Problembehandlung bei der Erstellung und Verwaltung von Azure Migrate-Projekten.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: troubleshooting
ms.date: 01/01/2020
ms.openlocfilehash: f68a57d3780f388488d48835f322ff04ab7c7187
ms.sourcegitcommit: ea551dad8d870ddcc0fee4423026f51bf4532e19
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/07/2020
ms.locfileid: "96753381"
---
# <a name="troubleshoot-azure-migrate-projects"></a>Behandeln von Problemen bei Azure Migrate-Projekten

Dieser Artikel hilft Ihnen bei der Problembehandlung beim Erstellen und Verwalten von [Azure Migrate](migrate-services-overview.md)-Projekten.

## <a name="how-to-add-new-project"></a>Wie wird ein neues Projekt hinzugefügt?

Ein Abonnement kann mehrere Azure Migrate-Projekte aufweisen. [Erfahren Sie](./create-manage-projects.md), wie Sie ein Projekt zum ersten Mal erstellen oder [weitere Projekte hinzufügen](create-manage-projects.md#create-additional-projects) können.

## <a name="what-azure-permissions-are-needed"></a>Welche Azure-Berechtigungen sind erforderlich?

Sie benötigen Berechtigungen für Mitwirkende oder Eigentümer im Abonnement, um ein Azure Migrate-Projekt zu erstellen.

## <a name="cant-find-a-project"></a>Projekt kann nicht gefunden werden

Die Suche nach einem bestehenden Azure Migrate-Projekt hängt davon ab, ob Sie die aktuelle oder die alte Version von Azure Migrate verwenden. [Folgen Sie: ](create-manage-projects.md#find-a-project).


## <a name="cant-find-a-geography"></a>Geografie kann nicht gefunden werden

Sie können ein Azure Migrate-Projekt in unterstützten geografischen Regionen für [öffentliche](migrate-support-matrix.md#supported-geographies-public-cloud) und [Azure Government-Clouds](migrate-support-matrix.md#supported-geographies-azure-government) erstellen.

## <a name="what-are-vm-limits"></a>Was sind Grenzwerte für virtuelle Computer?

Sie können bis zu 35.000 VMware-VMs oder bis zu 35.000 Hyper-V-VMs in einem einzelnen Projekt bewerten. Ein Projekt kann im Rahmen der Bewertungseinschränkungen sowohl VMware-VMs als auch Hyper-V-VMs umfassen.

## <a name="can-i-upgrade-old-project"></a>Kann ich ein Upgrade für ein altes Projekt durchführen?

Projekte aus der vorherigen Version von Azure Migrate können nicht aktualisiert werden. Sie müssen [ein neues Projekt](./create-manage-projects.md) erstellen und ihm Tools hinzufügen.

## <a name="cant-create-a-project"></a>Projekt kann nicht erstellt werden

Wenn Sie versuchen, ein Projekt zu erstellen und einen Bereitstellungsfehler erhalten:

- Versuchen Sie, das Projekt erneut zu erstellen, falls es sich um einen vorübergehenden Fehler handelt. Klicken Sie in **Bereitstellungen** auf **Erneut bereitstellen**, um es erneut zu versuchen.
- Vergewissern Sie sich, dass Sie über die Berechtigungen „Mitwirkender“ oder „Besitzer“ im Abonnement verfügen.
- Wenn Sie in einer neu hinzugefügten Geografie bereitstellen, warten Sie kurz, und versuchen Sie es erneut.
- Wenn Sie den Fehler „Anforderungen müssen Benutzeridentitätsheader enthalten“ erhalten, könnte dies darauf hinweisen, dass Sie keinen Zugriff auf den Azure Active Directory-Mandanten (Azure AD) der Organisation haben. In diesem Fall:
    - Wenn Sie erstmalig zu einem Azure AD-Mandanten hinzugefügt werden, erhalten Sie eine E-Mail-Einladung, dem Mandanten beizutreten.
    - Nehmen Sie die Einladung an, die dem Mandanten hinzugefügt werden soll.
    - Wenn die E-Mail nicht angezeigt wird, wenden Sie sich an einen Benutzer mit Zugriff auf den Mandanten, und bitten Sie ihn, [die Einladung erneut zu senden](../active-directory/external-identities/add-users-administrator.md#resend-invitations-to-guest-users).
    - Nachdem Sie die Einladungs-E-Mail erhalten haben, öffnen Sie die E-Mail, und wählen Sie den Link aus, um die Einladung anzunehmen. Melden Sie sich dann beim Azure-Portal ab und wieder an. (Das Aktualisieren des Browsers funktioniert hierbei nicht.) Sie können dann mit dem Erstellen des Migrationsprojekts beginnen.

## <a name="how-do-i-delete-a-project"></a>Wie lösche ich ein Projekt?

[Befolgen Sie diese Anleitung](create-manage-projects.md#delete-a-project), um ein Projekt zu löschen. Beachten Sie, dass beim Löschen eines Projekts sowohl das Projekt als auch die Metadaten über gefundene Computer im Projekt gelöscht werden.

## <a name="added-tools-dont-show"></a>Hinzugefügte Tools werden nicht angezeigt

Stellen Sie sicher, dass Sie das richtige Projekt ausgewählt haben. Klicken Sie im Azure Migrate-Hub > **Server** oder in **Datenbanken** in der rechten oberen Ecke des Bildschirms auf **Ändern** (neben **Projekt migrieren (Ändern)** ). Wählen Sie das richtige Abonnement, den richtigen Projektnamen und dann **OK** aus. Die Seite sollte mit den hinzugefügten Tools des ausgewählten Projekts aktualisiert werden.

## <a name="next-steps"></a>Nächste Schritte

Fügen Sie den Azure Migrate-Projekten Tools für die [Bewertung](how-to-assess.md) oder [Migration](how-to-migrate.md) hinzu.