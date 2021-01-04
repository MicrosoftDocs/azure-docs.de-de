---
title: Schützen von Windows Admin Center-Servern mit Azure Security Center
description: In diesem Artikel wird erläutert, wie Sie Azure Security Center mit Windows Admin Center integrieren.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: memildin
ms.openlocfilehash: ca3dd410474af0a42a3cd5b4217c3bba90b8ff1a
ms.sourcegitcommit: df66dff4e34a0b7780cba503bb141d6b72335a96
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/02/2020
ms.locfileid: "96511983"
---
# <a name="protect-windows-admin-center-resources-with-security-center"></a>Schützen von Windows Admin Center-Ressourcen mit Azure Security Center

Windows Admin Center ist ein Verwaltungstool für Ihre Windows-Server. Es stellt den zentralen Ort dar, von dem aus Systemadministratoren auf die meisten der am häufigsten verwendeten Verwaltungstools zugreifen. Sie können direkt in Windows Admin Center das Onboarding in Azure Security Center für Ihre lokalen Server durchführen. Anschließend können Sie eine Zusammenfassung Ihrer Sicherheitsempfehlungen und -warnungen direkt auf der Benutzeroberfläche von Windows Admin Center anzeigen.

> [!NOTE]
> Für Ihr Azure-Abonnement und den zugehörigen Log Analytics-Arbeitsbereich muss Azure Defender aktiviert sein, damit die Integration mit Windows Admin Center aktiviert werden kann.
> Azure Defender ist die ersten 30 Tage lang kostenlos, sofern Sie ihn noch nicht für das Abonnement und den Arbeitsbereich verwendet haben. Weitere Preisinformationen finden Sie in der [Preisübersicht](security-center-pricing.md).
>

Wenn Sie in Windows Admin Center das Onboarding eines Servers in Azure Security Center erfolgreich durchgeführt haben, können Sie folgende Aktionen ausführen:

* Anzeigen von Sicherheitswarnungen und -empfehlungen innerhalb der Security Center-Erweiterung in Windows Admin Center
* Anzeigen des Sicherheitsstatus und Abrufen weiterer detaillierter Informationen zu Ihren mit Windows Admin Center verwalteten Servern in Security Center im Azure-Portal (oder über eine API)

Durch die Kombination dieser beiden Tools wird Security Center zum zentralen Anzeigebereich für alle Ihre Sicherheitsinformationen – und zwar unabhängig von der Ressource. Dadurch werden sowohl Ihre lokalen, in Windows Admin Center verwalteten Server als auch Ihre virtuellen Computer und alle sonstigen PaaS-Workloads geschützt.

## <a name="onboard-windows-admin-center-managed-servers-into-security-center"></a>Onboarding von in Windows Admin Center verwalteten Servern in Azure Security Center

1. Wählen Sie in Windows Admin Center einen Ihrer Server und dann im Bereich **Tools** die Azure Security Center-Erweiterung aus:

    ![Azure Security Center-Erweiterung in Windows Admin Center](./media/windows-admin-center-integration/onboarding-from-wac.png)

    > [!NOTE]
    > Wenn das Onboarding des Servers in Security Center bereits durchgeführt wurde, wird das Einrichtungsfenster nicht angezeigt.

1. Klicken Sie auf **Sign in to Azure and set up** (Bei Azure anmelden und einrichten).
    ![Onboarding der Windows Admin Center-Erweiterung in Azure Security Center](./media/windows-admin-center-integration/onboarding-from-wac-welcome.png)

1. Befolgen Sie die Anweisungen zum Verbinden Ihres Servers mit Security Center. Nachdem Sie die erforderlichen Informationen eingegeben und bestätigt haben, nimmt Security Center die erforderlichen Konfigurationsänderungen vor, um sicherzustellen, dass Folgendes gilt:
    * Ein Azure-Gateway ist registriert.
    * Der Server verfügt über einen Arbeitsbereich für Berichte und ein zugeordnetes Abonnement.
    * Die Log Analytics-Lösung von Security Center ist im Arbeitsbereich aktiviert. Diese Lösung bietet die Azure Defender-Features für *alle* Server und virtuellen Computer, die Berichte an diesen Arbeitsbereich übermitteln.
    * Azure Defender für Server ist für das Abonnement aktiviert.
    * Der Log Analytics-Agent ist auf dem Server installiert und so konfiguriert, dass er Berichte an den ausgewählten Arbeitsbereich übermittelt. Wenn der Server bereits Berichte an einen anderen Arbeitsbereich übermittelt, wird er so konfiguriert, dass er auch Berichte an den neu ausgewählten Arbeitsbereich übermittelt.

    > [!NOTE]
    > Es kann nach dem Onboarding einige Zeit dauern, bis Empfehlungen angezeigt werden. Tatsächlich kann es abhängig von Ihrer Serveraktivität auch vorkommen, dass Sie *überhaupt keine* Warnungen erhalten. Wenn Sie Testwarnungen generieren möchten, um die ordnungsgemäße Funktion Ihrer Warnungen zu testen, befolgen Sie die Anweisungen im [Verfahren zur Warnungsüberprüfung](security-center-alert-validation.md).


## <a name="view-security-recommendations-and-alerts-in-windows-admin-center"></a>Anzeigen von Sicherheitsempfehlungen und -warnungen in Windows Admin Center

Nach dem Onboarding können Sie Ihre Warnungen und Empfehlungen direkt im Azure Security Center-Bereich von Windows Admin Center anzeigen. Klicken Sie auf eine Empfehlung oder Warnung, um sie im Azure-Portal anzuzeigen. Dort erhalten Sie weitere Informationen und erfahren, wie Sie die Probleme korrigieren.

[![Security Center-Empfehlungen und -Warnungen in Windows Admin Center](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png)](media/windows-admin-center-integration/asc-recommendations-and-alerts-in-wac.png#lightbox)

## <a name="view-security-recommendations-and-alerts-for-windows-admin-center-managed-servers-in-security-center"></a>Anzeigen von Sicherheitsempfehlungen und -warnungen für in Windows Admin Center verwaltete Server in Security Center
In Azure Security Center:

* Wenn Sie Sicherheitsempfehlungen für alle Windows Admin Center-Server anzeigen möchten, öffnen Sie [Ressourcenbestand](asset-inventory.md), und filtern Sie nach dem Computertyp, den Sie untersuchen möchten. Wählen Sie die Registerkarte **VMs und Computer** aus.

* Um Sicherheitswarnungen für alle Ihre Windows Admin Center-Server anzuzeigen, öffnen Sie **Sicherheitswarnungen**. Klicken Sie auf **Filter**, und vergewissern Sie sich, dass **nur** „Nicht-Azure“ ausgewählt ist:

    :::image type="content" source="./media/windows-admin-center-integration/filtering-alerts-by-environment.png" alt-text="Filtern von Sicherheitswarnungen für in Windows Admin Center verwaltete Server" lightbox="./media/windows-admin-center-integration/filtering-alerts-by-environment.png":::