---
title: Löschen eines ausführenden Azure Automation-Kontos
description: In diesem Artikel erfahren Sie, wie Sie ein ausführendes Konto mithilfe von PowerShell oder über das Azure-Portal löschen.
services: automation
ms.subservice: process-automation
ms.date: 01/06/2021
ms.topic: conceptual
ms.openlocfilehash: 6924a9a9394d237b08db878ef910de6767ca9b39
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056837"
---
# <a name="delete-an-azure-automation-run-as-account"></a>Löschen eines ausführenden Azure Automation-Kontos

Ausführende Konten in Azure Automation ermöglichen die Authentifizierung für die Verwaltung von Ressourcen mit dem Azure Resource Manager- oder dem klassischen Azure-Bereitstellungsmodell mithilfe von Automation-Runbooks und anderen Automation-Features. In diesem Artikel erfahren Sie, wie Sie ein ausführendes Konto oder ein klassisches ausführendes Konto löschen. Bei dieser Aktion wird das Automation-Konto beibehalten. Nachdem Sie das ausführende Konto gelöscht haben, können Sie es im Azure-Portal oder mit dem bereitgestellten PowerShell-Skript neu erstellen.

## <a name="delete-a-run-as-or-classic-run-as-account"></a>Löschen eines ausführenden Kontos oder klassischen ausführenden Kontos

1. Öffnen Sie das Automation-Konto im Azure-Portal.

2. Wählen Sie im linken Bereich im Abschnitt „Kontoeinstellungen“ **Ausführende Konten** aus.

3. Wählen Sie auf der Eigenschaftenseite „Ausführende Konten“ entweder das ausführende Konto oder das klassische ausführende Konto aus, das Sie löschen möchten.

4. Klicken Sie auf der Seite „Eigenschaften“ für das ausgewählte Konto auf **Löschen**.

   ![Löschen des ausführenden Azure-Kontos](media/delete-run-as-account/automation-account-delete-run-as.png)

5. Während das Konto gelöscht wird, können Sie den Status im Menü unter **Benachrichtigungen** verfolgen.

## <a name="next-steps"></a>Nächste Schritte

Informationen zum erneuten Erstellen Ihres ausführenden Kontos oder Ihres klassischen ausführenden Kontos finden Sie unter [Erstellen eines ausführenden Azure Automation-Kontos](create-run-as-account.md).