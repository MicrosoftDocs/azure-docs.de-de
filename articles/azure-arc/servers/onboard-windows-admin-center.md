---
title: Verbinden von Hybridcomputern mit Azure über Windows Admin Center
description: In diesem Artikel wird beschrieben, wie Sie Azure Arc-fähige Server über Windows Admin Center verwenden, um den Agent zu installieren und Computer mit Azure zu verbinden.
ms.date: 10/12/2020
ms.topic: conceptual
ms.openlocfilehash: 8fa118a254689feade2897f083bb3aa2dc9db02c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/26/2020
ms.locfileid: "96183412"
---
# <a name="connect-hybrid-machines-to-azure-from-windows-admin-center"></a>Verbinden von Hybridcomputern mit Azure über Windows Admin Center

Sie können Azure Arc-fähige Server für einen oder mehrere Windows-Computer in Ihrer Umgebung aktivieren, indem Sie einige manuelle Schritte ausführen. Alternativ können Sie [Windows Admin Center](/windows-server/manage/windows-admin-center/understand/what-is) nutzen, um den Connected Machine-Agent bereitzustellen und Ihre lokalen Server zu registrieren, ohne dass Sie außerhalb dieses Tools irgendwelche Schritte ausführen müssen.

## <a name="prerequisites"></a>Voraussetzungen

* Arc-fähige Server: Sehen Sie sich die Informationen zu den [Voraussetzungen](agent-overview.md#prerequisites) an, und stellen Sie sicher, dass die Anforderungen für Ihr Abonnement, Ihr Azure-Konto und die zugehörigen Ressourcen erfüllt sind.

* Windows Admin Center: Sehen Sie sich die Anforderungen zur [Vorbereitung Ihrer Umgebung](/windows-server/manage/windows-admin-center/deploy/prepare-environment) an, um die [Azure-Integration zu konfigurieren](/windows-server/manage/windows-admin-center/azure/azure-integration) und bereitzustellen.

* Ein Azure-Abonnement. Sollten Sie kein Abonnement besitzen, können Sie ein [kostenloses Konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) erstellen, bevor Sie beginnen.

* Die Windows-Zielserver, die Sie verwalten möchten, müssen für den Zugriff auf Azure über eine Internetverbindung verfügen.

### <a name="security"></a>Sicherheit

Für diese Bereitstellungsmethode benötigen Sie Administratorrechte auf dem Windows-Zielcomputer oder -server, um den Agent installieren und konfigurieren zu können. Darüber hinaus muss Ihnen die Rolle [**Gatewaybenutzer**](/windows-server/manage/windows-admin-center/plan/user-access-options#gateway-access-roles) zugewiesen sein.

## <a name="deploy"></a>Bereitstellen

Führen Sie die folgenden Schritte aus, um den Windows-Server mit Arc-fähigen Servern zu konfigurieren.

1. Melden Sie sich bei Windows Admin Center an.

1. Wählen Sie in der Verbindungsliste auf der Seite **Übersicht** einen der vorhandenen Windows-Server aus, um die Verbindung damit herzustellen.

1. Wählen Sie im linken Bereich die Option **Azure-Hybriddienste** aus.

1. Wählen Sie auf der Seite **Azure-Hybriddienste** die Option **Discover Azure services** (Azure-Dienste ermitteln) aus.

1. Wählen Sie auf der Seite **Discover Azure services** (Azure-Dienste ermitteln) unter **Leverage Azure policies and solutions to manage your servers with Azure Arc** (Azure-Richtlinien und -Lösungen zum Verwalten Ihrer Server mit Azure Arc nutzen) die Option **Set up** (Einrichten) aus.

1. Führen Sie bei Anzeige der entsprechenden Aufforderung auf der Seite **Einstellungen > Azure Arc für Server** die Authentifizierung für Azure durch, und wählen Sie anschließend die Option **Erste Schritte** aus.

1. Geben Sie auf der Seite **Connect server to Azure** (Server mit Azure verbinden) Folgendes an:

    1. Wählen Sie in der Dropdownliste **Azure-Abonnement** das Azure-Abonnement aus.
    1. Wählen Sie unter **Ressourcengruppe** die Option **Neu** aus, um eine neue Ressourcengruppe zu erstellen. Alternativ können Sie in der Dropdownliste **Ressourcengruppe** eine vorhandene Ressourcengruppe auswählen, die Sie für die Registrierung und Verwaltung des Computers nutzen möchten.
    1. Wählen Sie in der Dropdownliste **Regionen** die Azure-Region aus, in der die Metadaten des Servers gespeichert werden sollen.
    1. Wählen Sie die Option **Proxyserver verwenden** aus, wenn der Computer oder der Server die Internetverbindung über einen Proxyserver herstellt. Geben Sie die IP-Adresse des Proxyservers oder den Namen und die Portnummer für die Kommunikation mit dem Proxyserver an.

1. Wählen Sie **Set up** (Einrichten) aus, um mit dem Konfigurieren des Windows-Servers mit Azure Arc-fähigen Servern fortzufahren.

Der Windows-Server stellt eine Verbindung mit Azure her, lädt den Connected Machine-Agent herunter und installiert ihn und führt die Registrierung bei Azure Arc-fähigen Servern durch. Wählen Sie im Menü die Option **Benachrichtigungen** aus, um Informationen zum Status zu erhalten.

Wählen Sie in Windows Admin Center im linken Bereich die Option [**Ereignisse**](/windows-server/manage/windows-admin-center/use/manage-servers#events) aus, um die *MsiInstaller*-Ereignisse im Anwendungsereignisprotokoll anzuzeigen und sicherzustellen, dass die Installation des Connected Machine-Agents erfolgreich war.

## <a name="verify-the-connection-with-azure-arc"></a>Überprüfen der Verbindung mit Azure Arc

Vergewissern Sie sich im Azure-Portal, dass die Serververbindung erfolgreich hergestellt wurde, nachdem Sie den Agent installiert und für die Verbindungsherstellung mit Azure Arc-fähigen Servern konfiguriert haben. Sehen Sie sich Ihre Computer im [Azure-Portal](https://portal.azure.com) an.

:::image type="content" source="./learn/media/quick-enable-hybrid-vm/enabled-machine.png" alt-text="Erfolgreiche Computerverbindung" border="false":::

## <a name="next-steps"></a>Nächste Schritte

* Informationen zur Problembehandlung finden Sie im [Handbuch zur Problembehandlung des Connected Machine-Agents](troubleshoot-agent-onboard.md).

* Erfahren Sie, wie Sie Ihren Computer mithilfe von [Azure Policy](../../governance/policy/overview.md) verwalten, wie z. B. bei der VM-[Gastkonfiguration](../../governance/policy/concepts/guest-configuration.md), dem Überprüfen, ob der Computer dem erwarteten Log Analytics-Arbeitsbereich Bericht erstattet, beim Aktivieren der Überwachung mit [Azure Monitor mit VMs](../../azure-monitor/insights/vminsights-enable-policy.md) und vieles mehr.

* Weitere Informationen zum [Log Analytics-Agent](../../azure-monitor/platform/log-analytics-agent.md). Der Log Analytics-Agent für Windows und Linux ist erforderlich, wenn Sie Daten zur Betriebssystem- und Workloadüberwachung erfassen, diese mithilfe von Automation Runbooks oder Funktionen wie Updateverwaltung oder andere Azure-Dienste wie [Azure Security Center](../../security-center/security-center-introduction.md) nutzen möchten.