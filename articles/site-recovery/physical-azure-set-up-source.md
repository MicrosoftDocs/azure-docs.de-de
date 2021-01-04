---
title: Einrichten des Konfigurationsservers für die Notfallwiederherstellung von physischen Servern in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die Einrichtung des lokalen Konfigurationsservers für die Notfallwiederherstellung von lokalen physischen Servern in Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/03/2019
ms.author: ramamill
ms.openlocfilehash: 327e995a8fe2f66903548fba054804768d2538ab
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001552"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Einrichten des Konfigurationsservers für die Notfallwiederherstellung von physischen Servern in Azure

Dieser Artikel beschreibt, wie Sie Ihre lokale Umgebung einrichten, um mit dem Replizieren von physischen Servern unter Windows oder Linux in Azure zu beginnen.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie über Folgendes bereits verfügen:
- Einen Recovery Services-Tresor im [Azure-Portal](https://portal.azure.com "Azure-Portal").
- Einen physischen Computer, um den Konfigurationsserver zu installieren.
- Falls Sie TLS 1.0 auf dem Computer deaktiviert haben, auf dem der Konfigurationsserver installiert wird, vergewissern Sie sich, dass auf dem Computer TLS 1.2 aktiviert und mindestens die .NET Framework-Version 4.6 (mit aktivierter starker Kryptografie) installiert ist. [Weitere Informationen](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1)

### <a name="configuration-server-minimum-requirements"></a>Mindestanforderungen für den Konfigurationsserver
Die folgende Tabelle enthält die minimalen Hardware-, Software- und Netzwerkanforderungen für einen Konfigurationsserver.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> HTTPS-basierte Proxyserver werden vom Konfigurationsserver nicht unterstützt.

## <a name="choose-your-protection-goals"></a>Auswählen der Schutzziele

1. Navigieren Sie im Azure-Portal zum Blatt **Recovery Services-Tresore**, und wählen Sie den Tresor aus.
2. Klicken Sie im Menü **Ressource** des Tresors auf **Erste Schritte** > **Site Recovery** > **Schritt 1: Vorbereiten der Infrastruktur** > **Schutzziel**.

    ![Screenshot: Auswählen des Schutzziels](./media/physical-azure-set-up-source/choose-goals.png)
3. Wählen Sie unter **Schutzziel** die Option **In Azure** und dann **Nicht virtualisiert/Andere** aus. Klicken Sie anschließend auf **OK**.

    ![Ziele wählen](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Einrichten der Quellumgebung

1. Falls Sie über keinen Konfigurationsserver verfügen, klicken Sie unter **Quelle vorbereiten** auf **+Konfigurationsserver**, um einen Konfigurationsserver hinzuzufügen.

   ![Screenshot: Auswählen des Konfigurationsservers](./media/physical-azure-set-up-source/plus-config-srv.png)
2. Überprüfen Sie auf dem Blatt **Server hinzufügen**, ob unter **Servertyp** die Option **Konfigurationsserver** angezeigt wird.
4. Laden Sie die Installationsdatei für das einheitliche Setup von Site Recovery herunter.
5. Laden Sie den Tresorregistrierungsschlüssel herunter. Sie benötigen den Registrierungsschlüssel, wenn Sie das einheitliche Setup ausführen. Der Schlüssel ist nach der Erstellung fünf Tage lang gültig.

    ![Quelle einrichten](./media/physical-azure-set-up-source/set-source2.png)
6. Führen Sie auf dem Computer, den Sie als Konfigurationsserver verwenden, das **einheitliche Setup von Azure Site Recovery** aus, um den Konfigurationsserver, den Prozessserver und den Masterzielserver zu installieren.

#### <a name="run-azure-site-recovery-unified-setup"></a>Ausführen des einheitlichen Setups von Azure Site Recovery

> [!TIP]
> Die Registrierung des Konfigurationsservers schlägt fehl, wenn die Systemuhr auf Ihrem Computer um mehr als fünf Minuten von der Ortszeit abweicht. Synchronisieren Sie vor der Installation die Systemuhr mit einem [Zeitserver](/windows-server/networking/windows-time-service/windows-time-service-top).

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Der Konfigurationsserver kann über eine Befehlszeile installiert werden. [Weitere Informationen](physical-manage-configuration-server.md#install-from-the-command-line)


## <a name="common-issues"></a>Häufige Probleme

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie [Ihre Zielumgebung](physical-azure-set-up-target.md) in Azure einrichten.
