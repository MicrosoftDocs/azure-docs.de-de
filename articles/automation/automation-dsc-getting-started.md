---
title: Erste Schritte mit Azure Automation State Configuration
description: In diesem Artikel erfahren Sie, wie Sie die gängigsten Aufgaben in Azure Automation State Configuration ausführen.
services: automation
ms.subservice: dsc
ms.date: 04/15/2019
ms.topic: conceptual
ms.openlocfilehash: 95072970004ae53663f130a78af36a84a6fab685
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051497"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Erste Schritte mit Azure Automation State Configuration

Dieser Artikel bietet eine Schritt-für-Schritt-Anleitung zum Ausführen der gängigsten Aufgaben mit Azure Automation State Configuration, z. B. Erstellen, Importieren und Kompilieren von Konfigurationen, Aktivieren zu verwaltender Computer und Anzeigen von Berichten. Eine Übersicht über State Configuration finden Sie unter [Übersicht über Azure Automation State Configuration](automation-dsc-overview.md). Die DSC-Dokumentation (Desired State Configuration, Konfiguration des gewünschten Zustands) finden Sie unter [Windows PowerShell DSC – Übersicht](/powershell/scripting/dsc/overview/overview).

Wenn Sie eine bereits eingerichtete Beispielumgebung verwenden möchten, ohne die in diesem Artikel beschriebenen Schritte auszuführen, können Sie die [Vorlage für verwaltete Azure Automation-Knoten](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration) verwenden. Mit dieser Vorlage wird eine vollständige Umgebung für State Configuration eingerichtet, einschließlich eines virtuellen Azure-Computers, der von State Configuration (DSC) verwaltet wird.

## <a name="prerequisites"></a>Voraussetzungen

Um die Beispiele in diesem Artikel ausführen zu können, ist Folgendes erforderlich:

- Ein Azure Automation-Konto. Weitere Informationen zu Automation-Konten und den zugehörigen Anforderungen finden Sie unter [Übersicht über die Automation-Kontoauthentifizierung](./automation-security-overview.md).
- Eine Azure Resource Manager-VM (nicht klassisch), die ein [unterstütztes Betriebssystem](automation-dsc-overview.md#operating-system-requirements) ausführt. Eine Anleitung zum Erstellen einer VM finden Sie unter [Erstellen Ihres ersten virtuellen Windows-Computers im Azure-Portal](../virtual-machines/windows/quick-create-portal.md)

## <a name="create-a-dsc-configuration"></a>Erstellen einer DSC-Konfiguration

Sie erstellen eine einfache [DSC-Konfiguration](/powershell/scripting/dsc/configurations/configurations), die abhängig von der Knotenzuweisung entweder das Vorhandensein oder Nichtvorhandensein des Windows- bzw. IIS-Features **Web-Server** sicherstellt.

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
1. Speichern Sie die Datei unter dem Namen **TestConfig.ps1**.

Mit dieser Konfiguration wird eine Ressource in jedem Knotenblock aufgerufen, die [WindowsFeature-Ressource](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource). Diese Ressource stellt das Vorhandensein oder das Fehlen der Funktion **Webserver** sicher.

## <a name="import-a-configuration-into-azure-automation"></a>Importieren einer Konfiguration in Azure Automation

Als Nächstes importieren Sie die Konfiguration in das Automation-Konto.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Wählen Sie auf der Seite „Automation-Konto“ unter **Konfigurationsverwaltung** die Option **State Configuration (DSC)** aus.
1. Klicken Sie auf der Seite „State Configuration (DSC)“ auf die Registerkarte **Konfigurationen** und dann auf **Hinzufügen**.
1. Wechseln Sie auf der Seite „Konfiguration importieren“ zur Datei `TestConfig.ps1` auf Ihrem Computer.

   ![Screenshot des Blatts „Konfiguration importieren“](./media/automation-dsc-getting-started/AddConfig.png)

1. Klicken Sie auf **OK**.

## <a name="view-a-configuration-in-azure-automation"></a>Anzeigen einer Konfiguration in Azure Automation

Nachdem Sie eine Konfiguration importiert haben, können Sie sie im Azure-Portal anzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Wählen Sie auf der Seite „Automation-Konto“ unter **Konfigurationsverwaltung** die Option **State Configuration (DSC)** aus.
1. Klicken Sie auf der Seite „State Configuration (DSC)“ auf die Registerkarte **Konfigurationen** und dann auf **TestConfig**. Dies ist der Name der Konfiguration, die Sie im vorherigen Verfahren importiert haben.
1. Klicken Sie auf der Seite „TestConfig-Konfiguration“ auf **Konfigurationsquelle anzeigen**.

   ![Screenshot des Blatts „TestConfig-Konfiguration“](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Der Bereich „TestConfig-Konfigurationsquelle“ wird geöffnet, und der PowerShell-Code der Konfiguration wird angezeigt.

## <a name="compile-a-configuration-in-azure-automation"></a>Kompilieren einer Konfiguration in Azure Automation

Bevor Sie einen gewünschten Status auf einen Knoten anwenden können, muss eine DSC-Konfiguration, die diesen Status definiert, in eine oder mehrere Knotenkonfigurationen (MOF-Dokumente) kompiliert und auf dem Pullserver von Automation DSC abgelegt werden. Eine ausführlichere Beschreibung der Kompilierung von Konfigurationen in State Configuration finden Sie unter [Kompilieren von DSC-Konfigurationen in Azure Automation State Configuration](automation-dsc-compile.md).
Weitere Informationen zum Kompilieren von Konfigurationen finden Sie unter [DSC-Konfigurationen](/powershell/scripting/dsc/configurations/configurations).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf der Seite „Automation-Konto“ unter **Konfigurationsverwaltung** auf **State Configuration (DSC)** .
1. Klicken Sie auf der Seite „State Configuration (DSC)“ auf die Registerkarte **Konfigurationen** und dann auf **TestConfig**. Dies ist der Name der zuvor importierten Konfiguration.
1. Klicken Sie auf der Seite „TestConfig-Konfiguration“ auf **Kompilieren** und dann auf **Ja**. Ein Kompilierungsauftrag wird gestartet.

   ![Screenshot der Seite „TestConfig-Konfiguration“ mit hervorgehobener Schaltfläche „Kompilieren“](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Wenn Sie eine Konfiguration in Azure Automation kompilieren, werden automatisch alle erstellten MOF-Dateien mit der Knotenkonfiguration auf dem Pullserver bereitgestellt.

## <a name="view-a-compilation-job"></a>Anzeigen eines Kompilierungsauftrags

Nachdem Sie eine Kompilierung gestartet haben, können Sie sie auf der Seite **Konfiguration** auf der Kachel **Kompilierungsaufträge** anzeigen. Die Kachel **Kompilierungsaufträge** zeigt derzeit ausgeführte, abgeschlossene und fehlerhafte Aufträge. Wenn Sie die Seite eines Kompilierungsauftrags öffnen, werden Informationen zum Auftrag angezeigt, etwa aufgetretene Fehler und Warnungen, in der Konfiguration verwendete Eingabeparameter und Kompilierungsprotokolle.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf der Seite „Automation-Konto“ unter **Konfigurationsverwaltung** auf **State Configuration (DSC)** .
1. Klicken Sie auf der Seite „State Configuration (DSC)“ auf die Registerkarte **Konfigurationen** und dann auf **TestConfig**. Dies ist der Name der zuvor importierten Konfiguration.
1. Wählen Sie unter **Kompilierungsaufträge** den Kompilierungsauftrag aus, den Sie anzeigen möchten. Ein Bereich vom Typ „Kompilierungsauftrag“ wird geöffnet, die mit dem Startdatum des Kompilierungsauftrags bezeichnet ist.

   ![Screenshot der Seite „Kompilierungsauftrag“](./media/automation-dsc-getting-started/CompilationJob.png)

1. Klicken Sie auf im Bereich „Kompilierungsauftrag“ auf eine Kachel, um weitere Details zum Auftrag zu erhalten.

## <a name="view-node-configurations"></a>Anzeigen von Knotenkonfigurationen

Bei erfolgreicher Erstellung eines Kompilierungsauftrags werden eine oder mehrere neue Knotenkonfigurationen erzeugt. Eine Knotenkonfiguration ist ein MOF-Dokument, das auf dem Pullserver bereitgestellt wird und per Pull abgerufen und auf Knoten angewendet werden kann. Die Knotenkonfigurationen finden Sie in Ihrem Automation-Konto auf der Seite „State Configuration (DSC)“. Die Namen von Knotenkonfigurationen weisen das Format `ConfigurationName.NodeName` auf.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf der Seite „Automation-Konto“ unter **Konfigurationsverwaltung** auf **State Configuration (DSC)** .
1. Klicken Sie auf der Seite „State Configuration (DSC)“ auf die Registerkarte **Kompilierte Konfigurationen**.

   ![Screenshot der Registerkarte „Kompilierte Konfigurationen“](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="enable-an-azure-resource-manager-vm-for-management-with-state-configuration"></a>Aktivieren eines virtuellen Azure Resource Manager-Computers für die Verwaltung mit State Configuration

Mit State Configuration können Sie virtuelle Azure-Computer (mit dem klassischen Modell oder dem Resource Manager-Modell), lokale virtuelle Computer, Linux-Computer, virtuelle AWS-Computer und lokale physische Computer verwalten. In diesem Artikel erfahren Sie, wie ausschließlich Azure Resource Manager-VMs aktiviert werden. Informationen zum Aktivieren anderer Computertypen finden Sie unter [Aktivieren von Computern zur Verwaltung durch Azure Automation State Configuration](automation-dsc-onboarding.md).

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf der Seite „Automation-Konto“ unter **Konfigurationsverwaltung** auf **State Configuration (DSC)** .
1. Wählen Sie auf der Seite „State Configuration (DSC)“ die Registerkarte **Knoten** aus, und klicken Sie dann auf **+ Hinzufügen**.

   ![Screenshot der Seite „DSC-Knoten“ mit hervorgehobener Schaltfläche „Azure-VM hinzufügen“](./media/automation-dsc-getting-started/OnboardVM.png)

1. Wählen Sie im Bereich „Virtuelle Computer“ Ihren virtuellen Computer aus.
1. Klicken Sie im Detailbereich „Virtueller Computer“ auf **+ Verbinden**.

   > [!IMPORTANT]
   > Der virtuelle Computer muss eine Azure Resource Manager-VM sein, auf der ein [unterstütztes Betriebssystem](automation-dsc-overview.md#operating-system-requirements) ausgeführt wird.

2. Wählen Sie auf der Seite „Registrierung“ im Feld **Name der Knotenkonfiguration** den Namen der Knotenkonfiguration aus, die Sie auf den virtuellen Computer anwenden möchten. Das Angeben eines Namens an dieser Stelle ist optional. Sie können die zugewiesene Knotenkonfiguration nach dem Aktivieren des Knotens ändern.

3. Aktivieren Sie **Starten Sie den Knoten ggf. neu**, und klicken Sie dann auf **OK**.

   ![Screenshot des Blatts „Registrierung“](./media/automation-dsc-getting-started/RegisterVM.png)

   Die angegebene Knotenkonfiguration wird in den Intervallen auf die VM angewandt, die durch den Wert für **Konfigurationsmodushäufigkeit** angegeben sind. Der virtuelle Computer sucht in den vom Wert **Aktualisierungshäufigkeit** angegebenen Intervallen nach Updates für die Knotenkonfiguration. Weitere Informationen zur Verwendung dieser Werte finden Sie unter [Konfigurieren des lokalen Konfigurations-Managers](/powershell/scripting/dsc/managing-nodes/metaConfig).

Azure startet den Prozess der Aktivierung des virtuellen Computers. Wenn dies abgeschlossen ist, wird der virtuelle Computer im Automation-Konto auf der Seite „State Configuration (DSC)“ auf der Registerkarte **Knoten** angezeigt.

## <a name="view-the-list-of-managed-nodes"></a>Anzeigen der Liste mit verwalteten Knoten

Auf der Registerkarte **Knoten** der Seite „State Configuration (DSC)“ können Sie die Liste aller Computer anzeigen, die zur Verwaltung in Ihr Automation-Konto aktiviert wurden.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf der Seite „Automation-Konto“ unter **Konfigurationsverwaltung** auf **State Configuration (DSC)** .
1. Klicken Sie auf der Seite „State Configuration (DSC)“ auf die Registerkarte **Knoten**.

## <a name="view-reports-for-managed-nodes"></a>Anzeigen von Berichten für verwaltete Knoten

Immer wenn State Configuration eine Konsistenzprüfung auf einem verwalteten Knoten ausführt, sendet der Knoten einen Statusbericht zurück an den Pullserver. Sie können diese Berichte auf der Seite dieses Knotens anzeigen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf der Seite „Automation-Konto“ unter **Konfigurationsverwaltung** auf **State Configuration (DSC)** .
1. Klicken Sie auf der Seite „State Configuration (DSC)“ auf die Registerkarte **Knoten**. Hier ist eine Übersicht über den Konfigurationszustand und die Details für jeden Knoten:

   ![Screenshot der Seite mit den Knoten](./media/automation-dsc-getting-started/NodesTab.png)

1. Klicken Sie auf der Registerkarte **Knoten** auf den Knotendatensatz, um die Berichte zu öffnen. Klicken Sie auf den gewünschten Bericht, um zusätzliche Berichtsdetails anzuzeigen.

   ![Screenshot des Blatts „Bericht“](./media/automation-dsc-getting-started/NodeReport.png)

Auf dem Blatt einen einzelnen Berichts können Sie die folgenden Statusinformationen für die zugehörige Konsistenzprüfung finden:

- Der Berichtstatus. Mögliche Werte:
    * Konform: Der Knoten ist laut Überprüfung konform.
   * Fehler: Die Konfiguration hat die Überprüfung nicht bestanden.
   * Nicht konform: Der Knoten befindet sich im Modus `ApplyandMonitor`, und der Computer weist nicht den gewünschten Zustand auf.
- Startzeit der Konsistenzprüfung.
- Gesamtdauer der Konsistenzprüfung.
- Typ der Konsistenzprüfung.
- Fehler, einschließlich Fehlercode und -meldung.
- Alle in der Konfiguration verwendeten DSC-Ressourcen und der Zustand jeder Ressource (ob der Knoten den gewünschten Status für diese Ressource aufweist). Sie können auf die einzelnen Ressourcen klicken, um ausführlichere Informationen zu erhalten.
- Name, IP-Adresse und Konfigurationsmodus des Knotens.

Sie können auch auf **Unformatierten Bericht anzeigen** klicken, um die tatsächlichen Daten anzuzeigen, die der Knoten an den Server sendet.
Weitere Informationen zur Verwendung dieser Daten finden Sie unter [Verwenden eines DSC-Berichtsservers](/powershell/scripting/dsc/pull-server/reportserver).

Nachdem ein Knoten aktiviert wurde, kann es einige Zeit dauern, bis der erste Bericht verfügbar ist. Sie müssen möglicherweise bis zu 30 Minuten auf den ersten Bericht warten, nachdem Sie einen Knoten aktiviert haben.

## <a name="reassign-a-node-to-a-different-node-configuration"></a>Neuzuweisen eines Knotens zu einer anderen Knotenkonfiguration

Sie können einen Knoten einer anderen Knotenkonfiguration als der ursprünglich zugewiesenen zuweisen.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf der Seite „Automation-Konto“ unter **Konfigurationsverwaltung** auf **State Configuration (DSC)** .
1. Klicken Sie auf der Seite „State Configuration (DSC)“ auf die Registerkarte **Knoten**.
1. Klicken Sie auf der Registerkarte **Knoten** auf den Namen des Knotens, den Sie neu zuweisen möchten.
1. Klicken Sie auf der Seite dieses Knotens auf **Knotenkonfiguration zuweisen**.

    ![Screenshot der Seite „Knotendetails“ mit hervorgehobener Schaltfläche „Knotenkonfiguration zuweisen“](./media/automation-dsc-getting-started/AssignNode.png)

1. Wählen Sie auf der Seite „Knotenkonfiguration zuweisen“ die Knotenkonfiguration aus, der Sie den Knoten zuweisen möchten, und klicken Sie dann auf **OK**.

    ![Screenshot der Seite „Knotenkonfiguration zuweisen“](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregister-a-node"></a>Aufheben der Registrierung eines Knotens

Wenn ein Knoten nicht mehr von State Configuration verwaltet werden soll, können Sie seine Registrierung aufheben.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
1. Klicken Sie links auf **Alle Ressourcen** und dann auf den Namen des Automation-Kontos.
1. Klicken Sie auf der Seite „Automation-Konto“ unter **Konfigurationsverwaltung** auf **State Configuration (DSC)** .
1. Klicken Sie auf der Seite „State Configuration (DSC)“ auf die Registerkarte **Knoten**.
1. Klicken Sie auf der Registerkarte **DSC-Knoten** auf den Namen des Knotens, dessen Registrierung Sie aufheben möchten.
1. Klicken Sie im Bereich dieses Knotens auf **Registrierung aufheben**.

    ![Screenshot der Seite „Knotendetails“ mit hervorgehobener Schaltfläche „Registrierung aufheben“](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="next-steps"></a>Nächste Schritte

- Eine Übersicht finden Sie unter [Übersicht über Azure Automation State Configuration](automation-dsc-overview.md).
- Informationen zum Aktivieren des Features für virtuelle Computer in Ihrer Umgebung finden Sie unter [Aktivieren von Azure Automation State Configuration](automation-dsc-onboarding.md).
- Eine Einführung in PowerShell DSC finden Sie unter [Windows PowerShell DSC – Übersicht](/powershell/scripting/dsc/overview/overview).
- Eine Preisübersicht finden Sie unter [Automation – Preise](https://azure.microsoft.com/pricing/details/automation/).
- Eine Referenz zu den PowerShell-Cmdlets finden Sie unter [Az.Automation](/powershell/module/az.automation).
