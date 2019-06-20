---
title: Erste Schritte mit Azure Automation State Configuration
description: Erläuterung und Beispiele für die gängigsten Aufgaben in Azure Automation State Configuration (DSC, Desired State Configuration)
services: automation
ms.service: automation
ms.subservice: dsc
author: bobbytreed
ms.author: robreed
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 60cd2d21167739e824489e30ebd187a5fc0cc12d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61074445"
---
# <a name="getting-started-with-azure-automation-state-configuration"></a>Erste Schritte mit Azure Automation State Configuration

In diesem Artikel wird erläutert, wie die gängigsten Aufgaben mit Azure Automation State Configuration ausgeführt werden, z.B. Erstellen, Importieren und Kompilieren von Konfigurationen, Hinzufügen zu verwaltender Computer und Anzeigen von Berichten. Eine Übersicht über Azure Automation State Configuration finden Sie unter [Übersicht über Azure Automation State Configuration](automation-dsc-overview.md). Die DSC-Dokumentation (Desired State Configuration, Konfiguration des gewünschten Zustands) finden Sie unter [Windows PowerShell DSC – Übersicht](/powershell/dsc/overview).

Dieser Artikel enthält eine detaillierte Anleitung zur Verwendung von Azure Automation State Configuration. Wenn Sie eine Beispielumgebung wünschen, die bereits eingerichtet ist, ohne die in diesem Artikel beschriebenen Schritte zu befolgen, können Sie die folgende Resource Manager-Vorlage verwenden: [Vorlage für verwaltete Azure Automation-Knoten](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Mit dieser Vorlage wird eine vollständige Umgebung für Azure Automation State Configuration eingerichtet, einschließlich eines virtuellen Azure-Computers, der von Azure Automation State Configuration verwaltet wird.

## <a name="prerequisites"></a>Voraussetzungen

Um die Beispiele in diesem Artikel ausführen zu können, ist Folgendes erforderlich:

- Ein Azure Automation-Konto. Informationen zum Erstellen eines ausführenden Azure Automation-Kontos finden Sie unter [Azure Run As Account](automation-sec-configure-azure-runas-account.md)(Ausführendes Azure-Konto).
- Eine Azure Resource Manager-VM (nicht klassisch), die ein [unterstütztes Betriebssystem](automation-dsc-overview.md#operating-system-requirements) ausführt. Eine Anleitung zum Erstellen einer VM finden Sie unter [Erstellen Ihres ersten virtuellen Windows-Computers im Azure-Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="creating-a-dsc-configuration"></a>Erstellen einer DSC-Konfiguration

Sie erstellen eine einfache [DSC-Konfiguration](/powershell/dsc/configurations), die abhängig von der Knotenzuweisung entweder das Vorhandensein oder Nichtvorhandensein des Windows- bzw. IIS-Features **Web-Server** sicherstellt.

1. Starten Sie [VSCode](https://code.visualstudio.com/docs) (oder einen beliebigen Text-Editor).
1. Geben Sie den folgenden Text ein:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Speichern Sie die Datei als `TestConfig.ps1`.

Diese Konfiguration ruft in jedem Knotenblock die Ressource [WindowsFeature](/powershell/dsc/windowsfeatureresource)auf, die das Vorhandensein oder Nichtvorhandensein des Features **Web-Server** sicherstellt.

## <a name="importing-a-configuration-into-azure-automation"></a>Importieren einer Konfiguration in Azure Automation

Als Nächstes importieren Sie die Konfiguration in das Automation-Konto.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf der Seite **Automation-Konto** unter **Konfigurationsverwaltung** auf **Zustandskonfiguration (DSC)** .
1. Klicken Sie auf der Seite **Zustandskonfiguration (DSC)** auf die Registerkarte **Konfigurationen** und dann auf **+ Hinzufügen**.
1. Wechseln Sie auf der Seite **Konfiguration importieren** zur Datei `TestConfig.ps1` auf Ihrem Computer.

   ![Screenshot des Blatts „Konfiguration importieren“](./media/automation-dsc-getting-started/AddConfig.png)

1. Klicken Sie auf **OK**.

## <a name="viewing-a-configuration-in-azure-automation"></a>Anzeigen einer Konfiguration in Azure Automation

Nachdem Sie eine Konfiguration importiert haben, können Sie sie im Azure-Portal anzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf der Seite **Automation-Konto** unter **Konfigurationsverwaltung** auf **Zustandskonfiguration (DSC)** .
1. Klicken Sie auf der Seite **Zustandskonfiguration (DSC)** auf die Registerkarte **Konfigurationen** und anschließend auf **TestConfig** (Name der Konfiguration, die Sie zuvor importiert haben).
1. Klicken Sie auf der Seite **TestConfig-Konfiguration** auf **Konfigurationsquelle anzeigen**.

   ![Screenshot des Blatts „TestConfig-Konfiguration“](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Die Seite **TestConfig-Konfigurationsquelle** wird geöffnet, und der PowerShell-Code der Konfiguration wird angezeigt.

## <a name="compiling-a-configuration-in-azure-automation"></a>Kompilieren einer Konfiguration in Azure Automation

Bevor Sie einen gewünschten Status auf einen Knoten anwenden können, muss eine DSC-Konfiguration, die diesen Status definiert, in eine oder mehrere Knotenkonfigurationen (MOF-Dokumente) kompiliert und auf dem Pullserver von Automation DSC abgelegt werden. Eine ausführlichere Beschreibung der Kompilierung von Konfigurationen in Azure Automation State Configuration finden Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation DSC](automation-dsc-compile.md).
Weitere Informationen zum Kompilieren von Konfigurationen finden Sie unter [DSC-Konfigurationen](/powershell/dsc/configurations).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf der Seite **Automation-Konto** unter **Konfigurationsverwaltung** auf **Zustandskonfiguration (DSC)** .
1. Klicken Sie auf der Seite **Zustandskonfiguration (DSC)** auf die Registerkarte **Konfigurationen** und anschließend auf **TestConfig** (Name der zuvor importierten Konfiguration).
1. Klicken Sie auf der Seite **TestConfig-Konfiguration** auf **Kompilieren** und dann auf **Ja**. Ein Kompilierungsauftrag wird gestartet.

   ![Screenshot der Seite „TestConfig-Konfiguration“ mit hervorgehobener Schaltfläche „Kompilieren“](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Wenn Sie eine Konfiguration in Azure Automation kompilieren, werden alle erstellten MOF-Dateien mit der Knotenkonfiguration automatisch auf dem Pullserver bereitgestellt.

## <a name="viewing-a-compilation-job"></a>Anzeigen eines Kompilierungsauftrags

Nachdem Sie eine Kompilierung gestartet haben, können Sie sie auf der Seite **Konfiguration** auf der Kachel **Kompilierungsaufträge** anzeigen. Die Kachel **Kompilierungsaufträge** zeigt derzeit ausgeführte, abgeschlossene und fehlerhafte Aufträge. Wenn Sie die Seite eines Kompilierungsauftrags öffnen, werden Informationen zum Auftrag angezeigt, etwa aufgetretene Fehler und Warnungen, in der Konfiguration verwendete Eingabeparameter und Kompilierungsprotokolle.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf der Seite **Automation-Konto** unter **Konfigurationsverwaltung** auf **Zustandskonfiguration (DSC)** .
1. Klicken Sie auf der Seite **Zustandskonfiguration (DSC)** auf die Registerkarte **Konfigurationen** und anschließend auf **TestConfig** (Name der zuvor importierten Konfiguration).
1. Wählen Sie unter **Kompilierungsaufträge** den Kompilierungsauftrag aus, den Sie anzeigen möchten. Eine Seite vom Typ **Kompilierungsauftrag** wird geöffnet, dessen Bezeichnung mit dem Startdatum des Kompilierungsauftrags versehen ist.

   ![Screenshot der Seite „Kompilierungsauftrag“](./media/automation-dsc-getting-started/CompilationJob.png)

1. Klicken Sie auf der Seite **Kompilierungsauftrag** auf eine Kachel, um weitere Details zum Auftrag zu erhalten.

## <a name="viewing-node-configurations"></a>Anzeigen von Knotenkonfigurationen

Bei erfolgreicher Erstellung eines Kompilierungsauftrags werden eine oder mehrere neue Knotenkonfigurationen erzeugt. Eine Knotenkonfiguration ist ein MOF-Dokument, das auf dem Pullserver bereitgestellt wird und per Pull abgerufen und auf Knoten angewendet werden kann. Die Knotenkonfigurationen finden Sie in Ihrem Automation-Konto auf der Seite **Zustandskonfiguration (DSC)** . Knotenkonfigurationen werden im Format *Konfigurationsname*.*Knotenname* benannt.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf dem Blatt **Automation-Konto** unter **Konfigurationsverwaltung** auf **Zustandskonfiguration (DSC)** .
1. Klicken Sie auf der Seite **Zustandskonfiguration (DSC)** auf die Registerkarte  **Kompilierte Konfigurationen**.

   ![Screenshot der Registerkarte „Kompilierte Konfigurationen“](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration"></a>Integrieren eines virtuellen Azure-Computers zur Verwaltung mit Azure Automation State Configuration

Mit Azure Automation State Configuration können Sie virtuelle Azure-Computer (mit dem klassischen Modell oder dem Resource Manager-Modell), lokale virtuelle Computer, Linux-Computer, virtuelle AWS-Computer und lokale physische Computer verwalten. In diesem Artikel erfahren Sie, wie ausschließlich Azure Resource Manager-VMs integriert werden. Informationen zum Integrieren anderer Computertypen finden Sie unter [Onboarding von Computern zur Verwaltung durch Azure Automation DSC](automation-dsc-onboarding.md).

### <a name="to-onboard-an-azure-resource-manager-vm-for-management-by-azure-automation-state-configuration"></a>So integrieren Sie einen virtuellen Azure Resource Manager-Computer für die Verwaltung mit Azure Automation State Configuration

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf dem Blatt **Automation-Konto** unter **Konfigurationsverwaltung** auf **Zustandskonfiguration (DSC)** .
1. Klicken Sie auf der Seite **Zustandskonfiguration (DSC)** auf der Registerkarte **Knoten** auf **+ Hinzufügen**.

   ![Screenshot der Seite „DSC-Knoten“ mit hervorgehobener Schaltfläche „Azure-VM hinzufügen“](./media/automation-dsc-getting-started/OnboardVM.png)

1. Wählen Sie auf der Seite **Virtuelle Computer** Ihren virtuellen Computer aus.
1. Klicken Sie auf der Detailseite **Virtueller Computer** auf **+ Verbinden**.

   > [!IMPORTANT]
   > Dies muss eine Azure Resource Manager-VM sein, die ein [unterstütztes Betriebssystem](automation-dsc-overview.md#operating-system-requirements) ausführt.

2. Wählen Sie auf der Seite **Registrierung** im Feld **Name der Knotenkonfiguration** den Namen der Knotenkonfiguration aus, die Sie dem virtuellen Computer zuweisen möchten. Das Angeben eines Namens an dieser Stelle ist optional. Sie können die zugewiesene Knotenkonfiguration nach dem Integrieren des Knotens ändern.
   Aktivieren Sie **Starten Sie den Knoten ggf. neu**, und klicken Sie dann auf **OK**.

   ![Screenshot des Blatts „Registrierung“](./media/automation-dsc-getting-started/RegisterVM.png)

   Die angegebene Knotenkonfiguration wird in unter **Konfigurationsmodushäufigkeit** angegebenen Intervallen auf den virtuellen Computer angewendet, und der virtuelle Computer sucht in unter **Aktualisierungshäufigkeit** angegebenen Intervallen nach Updates für die Knotenkonfiguration. Weitere Informationen zur Verwendung dieser Werte finden Sie unter [Konfigurieren des lokalen Konfigurations-Managers](https://msdn.microsoft.com/PowerShell/DSC/metaConfig).

Azure startet den Prozess der Integration des virtuellen Computers. Wenn dies abgeschlossen ist, wird der virtuelle Computer auf der Registerkarte **Knoten** der Seite **Zustandskonfiguration (DSC)** im Automation-Konto angezeigt.

## <a name="viewing-the-list-of-managed-nodes"></a>Anzeigen der Liste mit verwalteten Knoten

Auf der Registerkarte **Knoten** der Seite **Zustandskonfiguration (DSC)** können Sie die Liste aller Computer anzeigen, die zur Verwaltung in Ihr Automation-Konto integriert wurden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf dem Blatt **Automation-Konto** unter **Konfigurationsverwaltung** auf **Zustandskonfiguration (DSC)** .
1. Klicken Sie auf der Seite **Zustandskonfiguration (DSC)** auf die Registerkarte **Knoten**.

## <a name="viewing-reports-for-managed-nodes"></a>Anzeigen von Berichten für verwaltete Knoten

Immer wenn Azure Automation State Configuration eine Konsistenzprüfung auf einem verwalteten Knoten ausführt, sendet der Knoten einen Statusbericht zurück an den Pullserver. Sie können diese Berichte auf der Seite dieses Knotens anzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf dem Blatt **Automation-Konto** unter **Konfigurationsverwaltung** auf **Zustandskonfiguration (DSC)** .
1. Klicken Sie auf der Seite **Zustandskonfiguration (DSC)** auf die Registerkarte **Knoten**. Hier ist eine Übersicht über den Konfigurationszustand und die Details für jeden Knoten:

   ![Screenshot der Seite mit den Knoten](./media/automation-dsc-getting-started/NodesTab.png)

1. Klicken Sie auf der Registerkarte **Knoten** auf den Knotendatensatz, um die Berichte zu öffnen. Klicken Sie auf den gewünschten Bericht, um zusätzliche Berichtsdetails anzuzeigen.

   ![Screenshot des Blatts „Bericht“](./media/automation-dsc-getting-started/NodeReport.png)

Auf dem Blatt einen einzelnen Berichts können Sie die folgenden Statusinformationen für die zugehörige Konsistenzprüfung finden:

- Berichtstatus: Gibt an, ob der Knoten kompatibel, die Konfiguration fehlerhaft oder der Knoten nicht kompatibel ist (Knoten befindet sich im Modus **ApplyandMonitor** und Computer nicht im gewünschten Zustand).
- Startzeit der Konsistenzprüfung.
- Gesamtdauer der Konsistenzprüfung.
- Typ der Konsistenzprüfung.
- Fehler, einschließlich Fehlercode und -meldung.
- Alle in der Konfiguration verwendeten DSC-Ressourcen und der Status jeder Ressource (ob der Knoten den gewünschten Status für diese Ressource hat): Sie können auf die einzelnen Ressourcen klicken, um detailliertere Informationen zu erhalten.
- Name, IP-Adresse und Konfigurationsmodus des Knotens.

Sie können auch auf **Unformatierten Bericht anzeigen** klicken, um die tatsächlichen Daten anzuzeigen, die der Knoten an den Server sendet.
Weitere Informationen zur Verwendung dieser Daten finden Sie unter [Verwenden eines DSC-Berichtsservers](/powershell/dsc/reportserver).

Nachdem ein Knoten in die Verwaltung integriert wurde, kann es einige Zeit dauern, bis der erste Bericht verfügbar ist. Sie müssen möglicherweise bis zu 30 Minuten auf den ersten Bericht warten, nachdem Sie einen Knoten integriert haben.

## <a name="reassigning-a-node-to-a-different-node-configuration"></a>Neuzuweisen eines Knotens zu einer anderen Knotenkonfiguration

Sie können einen Knoten einer anderen Knotenkonfiguration als der ursprünglich zugewiesenen zuweisen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf dem Blatt **Automation-Konto** unter **Konfigurationsverwaltung** auf **Zustandskonfiguration (DSC)** .
1. Klicken Sie auf der Seite **Zustandskonfiguration (DSC)** auf die Registerkarte **Knoten**.
1. Klicken Sie auf der Registerkarte **Knoten** auf den Namen des Knotens, den Sie neu zuweisen möchten.
1. Klicken Sie auf der Seite dieses Knotens auf **Knotenkonfiguration zuweisen**.

    ![Screenshot der Seite „Knotendetails“ mit hervorgehobener Schaltfläche „Knotenkonfiguration zuweisen“](./media/automation-dsc-getting-started/AssignNode.png)

1. Wählen Sie auf der Seite **Knotenkonfiguration zuweisen** die Knotenkonfiguration aus, der Sie den Knoten zuweisen möchten, und klicken Sie dann auf **OK**.

    ![Screenshot der Seite „Knotenkonfiguration zuweisen“](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregistering-a-node"></a>Aufheben der Registrierung eines Knotens

Wenn ein Knoten nicht mehr von Azure Automation DSC verwaltet werden soll, können Sie seine Registrierung aufheben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf dem Blatt **Automation-Konto** unter **Konfigurationsverwaltung** auf **Zustandskonfiguration (DSC)** .
1. Klicken Sie auf der Seite **Zustandskonfiguration (DSC)** auf die Registerkarte **Knoten**.
1. Klicken Sie auf der Registerkarte **DSC-Knoten** auf den Namen des Knotens, dessen Registrierung Sie aufheben möchten.
1. Klicken Sie auf der Seite dieses Knotens auf **Registrierung aufheben**.

    ![Screenshot der Seite „Knotendetails“ mit hervorgehobener Schaltfläche „Registrierung aufheben“](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="related-articles"></a>Verwandte Artikel

- [Übersicht über die Azure Automation-Zustandskonfiguration](automation-dsc-overview.md)
- [Onboarding von Computern zur Verwaltung durch Azure Automation DSC](automation-dsc-onboarding.md)
- [Windows PowerShell DSC – Übersicht](/powershell/dsc/overview)
- [Azure Automation State Configuration-Cmdlets](/powershell/module/azurerm.automation/#automation)
- [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/)
