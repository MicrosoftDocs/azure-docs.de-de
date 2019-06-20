---
title: Erstellen einer mit Azure kompatiblen VHD für den Azure Marketplace
description: Dieser Artikel erläutert, wie Sie eine VHD für ein VM-Angebot im Azure Marketplace erstellen.
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
ms.service: marketplace
ms.topic: article
ms.date: 08/27/2018
ms.author: pabutler
ms.openlocfilehash: a47d16108d98c5449d57d1db4892bffcead7e5f2
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67072606"
---
# <a name="create-an-azure-compatible-vhd"></a>Erstellen einer Azure-kompatiblen VHD

Dieser Artikel beschreibt die Schritte, die ausgeführt werden müssen, um eine virtuelle Festplatte (VHD) für ein VM-Angebot im Azure Marketplace zu erstellen.  Er beschreibt auch bewährte Methoden für verschiedene Aspekte, z.B. die Verwendung des Remotedesktopprotokolls (RDP), die Auswahl einer Größe für die VM, die Installation der neuesten Windows-Updates und die Generalisierung des VF-Images.  In den folgenden Abschnitten geht es primär um Windows-basierte VHDs. Informationen zum Erstellen von Linux-basierten VHDs finden Sie unter [Linux auf von Azure unterstützten Distributionen](../../../virtual-machines/linux/endorsed-distros.md). 

> [!WARNING]
> Es wird dringend empfohlen, die Anleitung in diesem Artikel zu befolgen, um Azure zum Erstellen einer VM mit einem vorkonfigurierten, unterstützten Betriebssystem zu verwenden.  Wenn dies nicht mit Ihrer Lösung kompatibel ist, ist es auch möglich, einen lokalen virtuellen Computer mit einem genehmigten Betriebssystem zu erstellen und zu konfigurieren.  Diesen können Sie für den Upload vorbereiten, wie unter [Vorbereiten einer Windows-VHD oder -VHDX zum Hochladen in Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) beschrieben.


## <a name="select-an-approved-base"></a>Auswählen eines genehmigten Basisimages
Die Betriebssystem-VHD für Ihr VM-Image muss auf einem von Azure genehmigten Basisimage basieren, das Windows Server oder SQL Server enthält.
Erstellen Sie zunächst einen virtuellen Computer aus einem der folgenden Images, die sich im Microsoft Azure-Portal befinden:

-   Windows Server ([2016](https://www.microsoft.com/evalcenter/evaluate-windows-server-2016), [2012 R2 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2012 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), [2008 R2 SP1](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview))
-   [SQL Server 2014](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)
-   [SQL Server 2012 SP2](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-pricing-guidance) (Enterprise, Standard, Web)

> [!TIP]
> Wenn Sie das aktuelle Azure-Portal oder PowerShell verwenden, sind am 8. September 2014 und später veröffentlichte Windows Server-Images genehmigt.

Alternativ dazu bietet Azure eine Reihe von genehmigten Linux-Distributionen.  Eine aktuelle Liste finden Sie unter [Linux auf von Azure unterstützten Distributionen](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros).


## <a name="create-vm-in-the-azure-portal"></a>Erstellen der VM im Azure-Portal 

Erstellen Sie im [Microsoft Azure-Portal](https://ms.portal.azure.com/) das Basisimage, indem Sie folgende Schritte ausführen.

1. Melden Sie sich beim Portal mit dem Microsoft-Konto für das Azure-Abonnement an, in dem Sie Ihr VM-Angebot veröffentlichen möchten.
2. Erstellen Sie eine neue Ressourcengruppe, und geben Sie den **Ressourcengruppennamen**, das **Abonnement** und den **Ressourcengruppenstandort** an.  Weitere Informationen finden Sie unter [Verwalten von Ressourcengruppen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal).
3. Klicken Sie in der linken Menüleiste auf **Virtuelle Computer**, um die Seite mit den Einzelheiten zu Ihren virtuellen Computern anzuzeigen. 
4. Klicken Sie auf dieser neuen Seite auf **+ Hinzufügen**, um das Blatt **Compute** anzuzeigen.  Wenn Sie den gewünschten VM-Typ auf dem anfänglichen Bildschirm nicht sehen, können Sie nach dem Namen Ihrer Basis-VM suchen. Beispiel:

    ![Blatt „Compute“ für die neue VM](./media/publishvm_014.png)

5. Nachdem Sie das richtige virtuelle Image ausgewählt haben, geben Sie die folgenden Werte an:
   * Geben Sie auf dem Blatt **Grundlagen** einen **Namen** für den virtuellen Computer ein. Dieser muss 1–15 alphanumerische Zeichen umfassen. (In diesem Beispiel wird `DemoVm009` verwendet.)
   * Geben Sie einen **Benutzernamen** und ein sicheres **Kennwort** für die Erstellung eines lokalen Kontos auf dem virtuellen Computer ein.  (Hier wird `adminUser` verwendet.)  Das Kennwort muss 8 bis 123 Zeichen lang sein und drei der folgenden vier Komplexitätsanforderungen erfüllen: ein Kleinbuchstabe, ein Großbuchstabe, eine Zahl und ein Sonderzeichen. Weitere Informationen finden Sie im Artikel zu den [Anforderungen an Benutzernamen und Kennwörter](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-faq#what-are-the-username-requirements-when-creating-a-vm).
   * Wählen Sie die Ressourcengruppe aus, die Sie erstellt haben (hier: `DemoResourceGroup`).
   * Wählen Sie den **Standort** eines Azure-Rechenzentrums aus (hier: `West US`).
   * Klicken Sie auf **OK**, um diese Werte zu speichern. 

6. Wählen Sie anhand der folgenden Empfehlungen die Größe der VM aus, die Sie bereitstellen möchten:
   * Wenn Sie planen, die VHD lokal zu entwickeln, spielt die Größe keine Rolle. Die Verwendung eines kleineren virtuellen Computers wird empfohlen.
   * Wenn Sie vorhaben, das Image in Azure zu entwickeln, sollten Sie eine der empfohlenen VM-Größen für das ausgewählte Image verwenden.
   * Preisinformationen finden Sie im Portal in der Auswahl **Empfohlener Tarif** . Hier werden die drei empfohlenen Größen angezeigt, die vom Herausgeber bereitgestellt werden. (In diesem Fall ist der Herausgeber Microsoft.)

   ![Blatt „Größe“ für die neue VM](./media/publishvm_015.png)

7. Legen Sie auf dem Blatt **Einstellungen** die Option **Verwaltete Datenträger verwenden** auf **Nein** fest.  Auf diese Weise können Sie die neue VHD manuell verwalten. (Auf dem Blatt **Einstellungen** können Sie auch weitere Optionen für Speicher und Netzwerk ändern. Sie können z.B. unter **Datenträgertyp** die Option **Premium (SSD)** auswählen.)  Klicken Sie auf **OK** , um fortzufahren.

    ![Blatt „Einstellungen“ für die neue VM](./media/publishvm_016.png)

8. Klicken Sie auf **Zusammenfassung** , um Ihre Auswahl zu überprüfen. Wenn die Meldung **Überprüfung erfolgreich** angezeigt wird, klicken Sie auf **OK**.

    ![Blatt „Zusammenfassung“ für die neue VM](./media/publishvm_017.png)

Azure beginnt mit der Bereitstellung des von Ihnen angegebenen virtuellen Computers.  Sie können den Fortschritt nachverfolgen, indem Sie auf der linken Seite auf die Registerkarte **Virtuelle Computer** klicken.  Nach der Erstellung ändert sich der Status zu **Wird ausgeführt**.  An diesem Punkt können Sie eine [Verbindung mit dem virtuellen Computer herstellen](./cpp-connect-vm.md).


## <a name="next-steps"></a>Nächste Schritte

Wenn beim Erstellen Ihrer neuen Azure-basierten VHD Probleme auftreten, lesen Sie den Artikel [Häufige Probleme bei der VHD-Erstellung](./cpp-common-vhd-creation-issues.md).  Ansonsten besteht der nächste Schritt darin, [eine Verbindung mit den VMs herzustellen](./cpp-connect-vm.md), die Sie in Azure erstellt haben. 
