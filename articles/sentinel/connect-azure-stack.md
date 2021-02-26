---
title: Integrieren virtueller Azure Stack Hub-Computer in Azure Sentinel | Microsoft-Dokumentation
description: In diesem Artikel erfahren Sie, wie Sie die VM-Erweiterung zur Update- und Konfigurationsverwaltung für Azure Monitor auf virtuellen Azure Stack Hub-Computern bereitstellen und diese mit Azure Sentinel überwachen.
services: sentinel
documentationcenter: na
author: yelevin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/23/2019
ms.author: yelevin
ms.openlocfilehash: 120f160e3c7bc7a9a0e81586ea4b97ee827dd33e
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807427"
---
# <a name="connect-azure-stack-hub-virtual-machines-to-azure-sentinel"></a>Verbinden von virtuellen Azure Stack Hub-Computern mit Azure Sentinel

Mit Azure Sentinel können Sie Ihre virtuellen Computer, die in Azure und Azure Stack Hub ausgeführt werden, zentral an einem Ort überwachen. Zum Durchführen des Onboardings Ihrer Azure Stack-Computer in Azure Sentinel müssen Sie zunächst auf den vorhandenen virtuellen Azure Stack Hub-Computern die VM-Erweiterung hinzufügen. 

Nachdem Sie die Azure Stack Hub-Computer verbunden haben, steht Ihnen ein Katalog von Dashboards zur Anzeige der Erkenntnisse, die Sie aus Ihren Daten gewinnen, zur Auswahl. Diese Dashboards können einfach an Ihre Anforderungen angepasst werden.

## <a name="add-the-virtual-machine-extension"></a>Hinzufügen der VM-Erweiterung 

Fügen Sie die VM-Erweiterung zur **Update- und Konfigurationsverwaltung für Azure Monitor** den in Azure Stack Hub ausgeführten virtuellen Computern hinzu. 

1. Melden Sie sich auf einer neuen Browserregisterkarte beim [Azure Stack Hub-Portal](/azure-stack/user/azure-stack-use-portal#access-the-portal) an.

1. Wechseln Sie zur Seite **Virtuelle Computer**, und wählen Sie den virtuellen Computer aus, der mit Azure Sentinel geschützt werden soll. Informationen zum Erstellen eines virtuellen Computers in Azure Stack Hub finden Sie unter [Erstellen eines virtuellen Windows Server-Computers mit dem Azure Stack Hub-Portal](/azure-stack/user/azure-stack-quick-windows-portal) oder [Schnellstart: Erstellen Sie einen virtuellen Linux-Server mit dem Azure Stack Hub-Portal](/azure-stack/user/azure-stack-quick-linux-portal).

1. Wählen Sie **Erweiterungen**. Die Liste der auf dieser VM installierten VM-Erweiterungen wird angezeigt.

1. Klicken Sie auf die Registerkarte **Hinzufügen**. Das Menüblatt **Neue Ressource** mit einer Liste der verfügbaren VM-Erweiterungen wird geöffnet. 

1. Wählen Sie die Erweiterung **Azure Monitor, Update and Configuration Management** (Azure Monitor, Update- und Konfigurationsverwaltung) aus, und klicken Sie auf **Erstellen**. Das Konfigurationsfenster **Erweiterung installieren** wird geöffnet.

   ![Einstellungen für die Update- und Konfigurationsverwaltung für Azure Monitor](./media/connect-azure-stack/azure-monitor-extension-fix.png)  

   >[!NOTE]
   > Wenn die Erweiterung **Azure Monitor, Update- und Konfigurationsverwaltung** in Ihrem Marketplace nicht aufgeführt ist, können Sie sich an Ihren Azure Stack Hub-Betreiber wenden, damit sie zur Verfügung gestellt wird.

1. Wählen Sie im Azure Sentinel-Menü die Option **Arbeitsbereichseinstellungen** und dann **Erweitert** aus, und kopieren Sie die **Arbeitsbereichs-ID** und den **Arbeitsbereichsschlüssel (Primärschlüssel)** . 

1. Fügen Sie sie im Azure Stack Hub-Fenster **Erweiterung installieren** in den angegebenen Feldern ein, und klicken Sie auf **OK**.

1. Nach Abschluss der Installation wird die Erweiterung mit dem Status **Bereitstellung erfolgreich** angezeigt. Es kann bis zu einer Stunde dauern, bis der virtuelle Computer im Azure Sentinel-Portal angezeigt wird.

Weitere Informationen zur Installation und Konfiguration des Agents für Windows finden Sie unter [Verbinden von Windows-Computern](../azure-monitor/platform/agent-windows.md#install-agent-using-setup-wizard).

Informationen zum Beheben von Problemen mit dem Agent für Linux finden Sie unter [Behandeln von Problemen mit dem Log Analytics-Agent für Linux](../azure-monitor/platform/agent-linux-troubleshoot.md).

Im Azure Sentinel-Portal in Azure wird unter **Virtuelle Computer** eine Übersicht aller virtuellen und physischen Computer mit dem jeweiligen Status angezeigt. 

## <a name="clean-up-resources"></a>Bereinigen von Ressourcen

Wenn die Erweiterung nicht mehr benötigt wird, können Sie sie über das Azure Stack Hub-Portal von der VM entfernen.

So entfernen Sie die Erweiterung:

1. Öffnen Sie das **Azure Stack Hub-Portal**.

1. Wechseln Sie zur Seite **Virtuelle Computer**, und wählen Sie die VM aus, von der Sie die Erweiterung entfernen möchten.

1. Wählen Sie **Erweiterungen** und dann die Erweiterung **Microsoft.EnterpriseCloud.Monitoring** aus.

1. Klicken Sie auf **Deinstallieren**, und bestätigen Sie die Auswahl.

## <a name="next-steps"></a>Nächste Schritte

Weitere Informationen zu Azure Sentinel finden Sie in den folgenden Artikeln:

- Erfahren Sie, wie Sie [Einblick in Ihre Daten und potenzielle Bedrohungen erhalten](quickstart-get-visibility.md).
- Beginnen Sie mit der [Erkennung von Bedrohungen mithilfe von Azure Sentinel](tutorial-detect-threats-built-in.md).
- Streamen Sie Daten aus [CEF-Appliances (Common Event Format)](connect-common-event-format.md) an Azure Sentinel.
