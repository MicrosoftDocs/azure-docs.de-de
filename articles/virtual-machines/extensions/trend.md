---
title: Installieren von Trend Micro Deep Security auf einem virtuellen Computer
description: Dieser Artikel beschreibt, wie Sie Trend Micro Security auf einem mit dem klassischen Bereitstellungsmodell erstellten virtuellen Computer in Azure installieren und konfigurieren.
author: axayjo
tags: azure-service-management
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/20/2018
ms.author: akjosh
ms.openlocfilehash: 9d6d80287d05517933f066d5e49fa31e78a48943
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966378"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Installieren und Konfigurieren von Trend Micro Deep Security als Dienst auf einem virtuellen Windows-Computer

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
Dieser Artikel zeigt Ihnen das Installieren und Konfigurieren von Trend Micro Deep Security als einen Dienst auf einem neuen oder vorhandenen virtuellen Computer unter Windows Server. Deep Security as a Service umfasst den Antischadsoftwareschutz, eine Firewall, ein Eindringschutzsystem und die Integritätsüberwachung.

Der Client wird als Sicherheitserweiterung über den VM-Agent installiert. Auf einem neuen virtuellen Computer installieren Sie den Deep Security Agent, da der VM-Agent automatisch durch das Azure-Portal erstellt wird.

Ein vorhandener virtueller Computer, der mit dem Azure-Portal, mit Azure CLI oder PowerShell erstellt wurde, verfügt möglicherweise nicht über den VM-Agent. Auf einem vorhandenen virtuellen Computer, der nicht über den VM-Agent verfügt, müssen Sie diesen zunächst herunterladen und installieren. Dieser Artikel deckt beide Situationen ab.

Wenn Sie über ein aktuelles Abonnement von Trend Micro für eine lokale Lösung verfügen, können Sie es zum Schützen Ihrer virtuellen Azure-Computer verwenden. Wenn Sie noch kein Kunde sind, können Sie sich für ein Testabonnement registrieren. Weitere Informationen zu dieser Lösung finden Sie im Trend Micro-Blogbeitrag [Microsoft Azure VM Agent Extension For Deep Security](https://go.microsoft.com/fwlink/p/?LinkId=403945)(Microsoft Azure-VM-Agent-Erweiterung für Deep Security, in englischer Sprache).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Installieren von Deep Security Agent auf einem neuen virtuellen Computer

Mit dem [Azure-Portal](https://portal.azure.com) können Sie die Trend Micro-Sicherheitserweiterung installieren, wenn Sie ein Image aus dem **Marketplace** zum Erstellen des virtuellen Computers verwenden. Wenn Sie einen einzelnen virtuellen Computer erstellen, ist die Verwendung des Portals eine einfache Möglichkeit, den Trend Micro-Schutz hinzuzufügen.

Durch Verwendung eines Eintrags aus dem **Marketplace** wird ein Assistent geöffnet, der Ihnen beim Einrichten des virtuellen Computers hilft. Sie verwenden das Blatt **Einstellungen** (das dritte Blatt des Assistenten), um die Trend Micro-Sicherheitserweiterung zu installieren.  Allgemeine Anweisungen dazu finden Sie unter [Erstellen eines virtuellen Computers unter Windows im Azure-Portal](../windows/quick-create-portal.md).

Führen Sie auf dem Blatt **Einstellungen** des Assistenten folgende Schritte aus:

1. Klicken Sie auf **Erweiterungen** und dann im nächsten Bereich auf **Erweiterung hinzufügen**.

   ![Hinzufügen der Erweiterung][1]

2. Wählen Sie im Bereich **Neue Ressource** den Eintrag **Deep Security Agent** aus. Klicken Sie im Bereich „Deep Security Agent“ auf **Erstellen**.

   ![Identifizieren von Deep Security Agent][2]

3. Geben Sie in **Tenant Identifier** den Mandantenbezeichner und in **Tenant Activation Password** das Mandantenaktivierungskennwort für die Erweiterung ein. Optional können Sie in **Security Policy Identifier** einen Bezeichner für die Sicherheitsrichtlinie eingeben. Klicken Sie auf **OK**, um den Client hinzufügen.

   ![Angeben der Erweiterungsdetails][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Installieren von Deep Security Agent auf einem vorhandenen virtuellen Computer
Um den Agent auf einem vorhandenen virtuellen Computer zu installieren, benötigen Sie folgende Elemente:

* Das Azure PowerShell-Modul Version 0.8.2 oder höher muss auf Ihrem lokalen Computer installiert sein. Sie können die installierte Version von Azure PowerShell mit dem Befehl **Get-Module azure | format-table version** überprüfen. Anweisungen und einen Link zur neuesten Version finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/powershell/azure/). Melden Sie sich mit `Add-AzureAccount`bei Ihrem Azure-Abonnement an.
* Der VM-Agent muss auf dem virtuellen Zielcomputer installiert sein.

Überprüfen Sie zunächst, ob der VM-Agent bereits installiert ist. Füllen Sie den Clouddienstnamen und den Namen des virtuellen Computers aus, und führen Sie dann die folgenden Befehle an einer Azure PowerShell-Eingabeaufforderung mit Administratorrechten aus. Ersetzen Sie alles innerhalb der Anführungszeichen, einschließlich der Zeichen < und >.

```azurepowershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Wenn Sie den Namen des Clouddiensts und des virtuellen Computers nicht kennen, führen Sie **Get-AzureVM** aus, um diese Informationen für alle virtuellen Computer im aktuellen Abonnement anzuzeigen.

Wenn der Befehl **write-host** den Wert **True** zurückgibt, ist der VM-Agent installiert. Wenn **False** zurückgegeben wird, nutzen Sie die Anweisungen und den Link zum Download im Azure-Blogbeitrag [VM Agent and Extensions - Part 2](https://go.microsoft.com/fwlink/p/?LinkId=403947)(VM-Agent und Erweiterungen, Teil 2, in englischer Sprache).

Wenn der VM-Agent installiert ist, führen Sie diese Befehle aus.

```azurepowershell
$Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM
```

## <a name="next-steps"></a>Nächste Schritte
Es dauert einige Minuten, bis der Agent gestartet wird, wenn er installiert ist. Danach müssen Sie Deep Security auf dem virtuellen Computer aktivieren, sodass es durch einen Deep Security Manager verwaltet werden kann. Weitere Anweisungen finden Sie in folgenden Artikeln:

* Artikel von Trend zu dieser Lösung [Instant-On Cloud Security for Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* [Windows PowerShell-Beispielskript](https://go.microsoft.com/fwlink/?LinkId=404100) zum Konfigurieren des virtuellen Computers
* [Anweisungen](https://go.microsoft.com/fwlink/?LinkId=404099) für das Beispiel

## <a name="additional-resources"></a>Zusätzliche Ressourcen
[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]

[Azure-VM-Erweiterungen und -Features]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Anmelden bei einem virtuellen Computer, auf dem Windows Server ausgeführt wird]:../windows/classic/connect-logon.md
[Azure-VM-Erweiterungen und -Features]: features-windows.md
