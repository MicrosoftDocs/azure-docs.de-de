---
title: Einrichten des Konfigurationsservers für die Notfallwiederherstellung von physischen Servern in Azure mit Azure Site Recovery | Microsoft-Dokumentation
description: Dieser Artikel beschreibt die Einrichtung des lokalen Konfigurationsservers für die Notfallwiederherstellung von lokalen physischen Servern in Azure.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: ramamill
ms.openlocfilehash: 5f0578026e95378065fc68198434e347a87eb1fe
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62123913"
---
# <a name="set-up-the-configuration-server-for-disaster-recovery-of-physical-servers-to-azure"></a>Einrichten des Konfigurationsservers für die Notfallwiederherstellung von physischen Servern in Azure

Dieser Artikel beschreibt, wie Sie Ihre lokale Umgebung einrichten, um mit dem Replizieren von physischen Servern unter Windows oder Linux in Azure zu beginnen.

## <a name="prerequisites"></a>Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie über Folgendes bereits verfügen:
- Einen Recovery Services-Tresor im [Azure-Portal](https://portal.azure.com "Azure-Portal").
- Einen physischen Computer, um den Konfigurationsserver zu installieren.
- Wenn Sie TLS 1.0 auf dem Computer deaktiviert haben, auf dem der Konfigurationsserver installiert wird, stellen Sie sicher, dass TLS 1.2 aktiviert ist und .NET Framework Version 4.6 oder höher auf dem Computer (mit deaktivierter starker Kryptografie) installiert ist. [Weitere Informationen](https://support.microsoft.com/help/4033999/how-to-resolve-azure-site-recovery-agent-issues-after-disabling-tls-1)

### <a name="configuration-server-minimum-requirements"></a>Mindestanforderungen für den Konfigurationsserver
Die folgende Tabelle enthält die minimalen Hardware-, Software- und Netzwerkanforderungen für einen Konfigurationsserver.
[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

> [!NOTE]
> HTTPS-basierte Proxyserver werden vom Konfigurationsserver nicht unterstützt.

## <a name="choose-your-protection-goals"></a>Auswählen der Schutzziele

1. Navigieren Sie im Azure-Portal zum Blatt **Recovery Services-Tresore**, und wählen Sie den Tresor aus.
2. Klicken Sie im Menü **Ressource** des Tresors auf **Erste Schritte** > **Site Recovery** > **Schritt 1: Vorbereiten der Infrastruktur** > **Schutzziel**.

    ![Ziele wählen](./media/physical-azure-set-up-source/choose-goals.png)
3. Wählen Sie unter **Schutzziel** die Option **In Azure** und dann **Nicht virtualisiert/Andere** aus. Klicken Sie anschließend auf **OK**.

    ![Ziele wählen](./media/physical-azure-set-up-source/physical-protection-goal.png)

## <a name="set-up-the-source-environment"></a>Einrichten der Quellumgebung

1. Falls Sie über keinen Konfigurationsserver verfügen, klicken Sie unter **Quelle vorbereiten** auf **+Konfigurationsserver**, um einen Konfigurationsserver hinzuzufügen.

   ![Quelle einrichten](./media/physical-azure-set-up-source/plus-config-srv.png)
2. Überprüfen Sie auf dem Blatt **Server hinzufügen**, ob unter **Servertyp** die Option **Konfigurationsserver** angezeigt wird.
4. Laden Sie die Installationsdatei für das einheitliche Setup von Site Recovery herunter.
5. Laden Sie den Tresorregistrierungsschlüssel herunter. Sie benötigen den Registrierungsschlüssel, wenn Sie das einheitliche Setup ausführen. Der Schlüssel ist nach der Erstellung fünf Tage lang gültig.

    ![Quelle einrichten](./media/physical-azure-set-up-source/set-source2.png)
6. Führen Sie auf dem Computer, den Sie als Konfigurationsserver verwenden, das **einheitliche Setup von Azure Site Recovery** aus, um den Konfigurationsserver, den Prozessserver und den Masterzielserver zu installieren.

#### <a name="run-azure-site-recovery-unified-setup"></a>Ausführen des einheitlichen Setups von Azure Site Recovery

> [!TIP]
> Die Registrierung des Konfigurationsservers schlägt fehl, wenn die Systemuhr auf Ihrem Computer um mehr als fünf Minuten von der Ortszeit abweicht. Synchronisieren Sie vor der Installation die Systemuhr mit einem [Zeitserver](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service).

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Der Konfigurationsserver kann über eine Befehlszeile installiert werden. [Weitere Informationen](physical-manage-configuration-server.md#install-from-the-command-line)


## <a name="common-issues"></a>Häufige Probleme

[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]


## <a name="next-steps"></a>Nächste Schritte

Als Nächstes können Sie [Ihre Zielumgebung](physical-azure-set-up-target.md) in Azure einrichten.
