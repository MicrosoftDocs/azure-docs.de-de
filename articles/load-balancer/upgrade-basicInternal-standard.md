---
title: Upgraden einer internen Load Balancer-Instanz von Basic auf Standard – Azure Load Balancer
description: In diesem Artikel erfahren Sie, wie Sie eine interne Azure Load Balancer-Instanz von der Basic- auf die Standard-SKU upgraden.
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 08/07/2020
ms.author: irenehua
ms.openlocfilehash: 59bf5eb22289238633b1f07c29a878bd0a9ae620
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94696165"
---
# <a name="upgrade-azure-internal-load-balancer--no-outbound-connection-required"></a>Aktualisieren einer internen Azure Load Balancer-Instanz: keine ausgehende Verbindung erforderlich
[Azure Load Balancer Standard](load-balancer-overview.md) bietet umfangreiche Funktionen sowie Hochverfügbarkeit durch Zonenredundanz. Weitere Informationen zu Load Balancer-SKUs finden Sie in der [Vergleichstabelle](./skus.md#skus).

In diesem Artikel wird ein PowerShell-Skript eingeführt, das einen Load Balancer Standard mit derselben Konfiguration erstellt wie der des Load Balancers Basic zusammen mit der Migration von Datenverkehr vom Load Balancer Basic zum Load Balancer Standard.

## <a name="upgrade-overview"></a>Upgradeübersicht

Es gibt ein Azure PowerShell-Skript, in dem folgende Vorgänge ausgeführt werden:

* Erstellt einen internen SKU-Lastenausgleich im Tarif „Standard“ an dem von Ihnen angegebenen Standort. Beachten Sie, dass keine [ausgehende Verbindung](./load-balancer-outbound-connections.md) durch den internen Lastenausgleich im Tarif „Standard“ bereitgestellt wird.
* Nahtloses Kopieren der Konfigurationen der Load Balancer-Instanz mit Basic-SKU in die neu erstellte Load Balancer Standard-Instanz.
* Nahtloses Verschieben der privaten IPs vom Load Balancer Basic in den neu erstellten Load Balancer Standard.
* Nahtloses Verschieben der VMs aus dem Back-End-Pool des Load Balancers Basic in den Back-End-Pool des Load Balancers Standard

### <a name="caveatslimitations"></a>Vorbehalte/Einschränkungen

* Das Skript unterstützt nur das Upgrade des internen Lastenausgleichs, wenn keine ausgehende Verbindung erforderlich ist. Wenn Sie für einige Ihrer VMs eine [ausgehende Verbindung](./load-balancer-outbound-connections.md) benötigen, finden Sie auf dieser [Seite](upgrade-InternalBasic-To-PublicStandard.md) entsprechende Anweisungen. 
* Load Balancer Basic muss sich in derselben Ressourcengruppe wie die virtuellen Back-End-VMs und -NICs befinden.
* Wenn die Load Balancer Standard-Instanz in einer anderen Region erstellt wird, können die virtuellen Computer aus der alten Region nicht der neu erstellten Load Balancer Standard-Instanz zugeordnet werden. Erstellen Sie zur Umgehung dieser Einschränkung einen neuen virtuellen Computer in der neuen Region.
* Wenn Ihre Load Balancer-Instanz über keine Front-End-IP-Konfiguration oder über keinen Back-End-Pool verfügt, tritt beim Ausführen des Skripts wahrscheinlich ein Fehler auf. Stellen Sie sicher, dass die Angaben vorhanden sind.

## <a name="change-ip-allocation-method-to-static-for-frontend-ip-configuration-ignore-this-step-if-its-already-static"></a>Ändern Sie die IP-Zuordnungsmethode für die Front-End-IP-Konfiguration in „Statisch“ (ignorieren Sie diesen Schritt, wenn die Methode bereits statisch ist).

1. Wählen Sie im linken Menü **Alle Dienste** > **Alle Ressourcen** und anschließend Ihren Load Balancer Basic in der Ressourcenliste aus.

2. Wählen Sie unter **Einstellungen** die Option **Front-End-IP-Konfigurations** aus, und wählen Sie dann die erste Front-End-IP-Konfiguration aus. 

3. Wählen Sie als **Zuweisung** die Option **Statisch** aus.

4. Wiederholen Sie Schritt 3 für alle Front-End-IP-Konfigurationen des Load Balancer Basic.


## <a name="download-the-script"></a>Herunterladen des Skripts

Laden Sie das Migrationsskript aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/AzureILBUpgrade/5.0) herunter.
## <a name="use-the-script"></a>Verwenden des Skripts

Je nach dem, wie Ihre lokale PowerShell-Umgebung eingerichtet und eingestellt ist, gibt es zwei Optionen für Sie:

* Haben Sie die Azure Az-Module nicht installiert, oder haben Sie kein Problem damit, die Azure Az-Module zu deinstallieren, sollten Sie die `Install-Script`-Option verwenden, um das Skript auszuführen.
* Wenn Sie die Azure Az-Module beibehalten müssen, besteht die beste Vorgehensweise darin, das Skript herunterzuladen und direkt auszuführen.

Um festzustellen, ob Sie die Azure Az-Module installiert haben, führen Sie `Get-InstalledModule -Name az` aus. Werden keine installierten Az-Module angezeigt, können Sie die `Install-Script`-Methode verwenden.

### <a name="install-using-the-install-script-method"></a>Installieren mit der Install-Script-Methode

Damit Sie diese Option nutzen können, dürfen keine Azure Az-Module auf Ihrem Computer installiert sein. Wenn diese installiert sind, zeigt der folgende Befehl einen Fehler an. Sie können entweder die Azure Az-Module deinstallieren oder die andere Option nutzen, um das Skript manuell herunterzuladen und es auszuführen.
  
Führen Sie das Skript mit dem folgenden Befehl aus:

`Install-Script -Name AzureILBUpgrade`

Mit diesem Befehl werden auch die erforderlichen Az-Module installiert.  

### <a name="install-using-the-script-directly"></a>Installieren durch direktes Verwenden des Skripts

Haben Sie einige Azure Az-Module installiert, und können (oder möchten) Sie diese nicht deinstallieren, laden Sie das Skript manuell herunter, indem Sie die Registerkarte **Manual Download** im Skript-Downloadlink verwenden. Das Skript wird als reine NUPKG-Datei heruntergeladen. Informationen, wie das Skript aus dieser NUPKG-Datei installiert wird, finden Sie unter [Manuelles Herunterladen des Pakets](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

So führen Sie das Skript aus

1. Verwenden Sie `Connect-AzAccount`, um eine Verbindung mit Azure herzustellen.

1. Verwenden Sie `Import-Module Az`, um die Az-Module zu importieren.

1. Untersuchen Sie die erforderlichen Parameter:

   * **rgName: [Zeichenfolge]: Erforderlich:** Die Ressourcengruppe für die vorhandene Load Balancer Basic-Instanz und die neue Load Balancer Standard-Instanz. Wählen Sie zur Ermittlung dieses Zeichenfolgenwerts im Azure-Portal Ihre Load Balancer Basic-Quelle aus, und klicken Sie auf die **Übersicht** für den Lastenausgleich. Die Ressourcengruppe befindet sich auf dieser Seite.
   * **oldLBName: [Zeichenfolge]: Erforderlich:** Der Name der vorhandene Load Balancer Basic-Instanz, die Sie upgraden möchten. 
   * **newlocation: [Zeichenfolge]: Erforderlich:** Der Standort, an dem die Load Balancer Standard-Instanz erstellt wird. Es empfiehlt sich, für die Load Balancer Standard-Instanz den Standort der gewählten Load Balancer Basic-Instanz zu übernehmen, um die Verknüpfung mit anderen vorhandenen Ressourcen zu erleichtern.
   * **newLBName: [Zeichenfolge]: Erforderlich:** Der Name der zu erstellenden Load Balancer Standard-Instanz.
1. Führen Sie das Skript mit den entsprechenden Parametern aus. Es kann fünf bis sieben Minuten dauern, bis es fertig ist.

    **Beispiel**

   ```azurepowershell
   AzureILBUpgrade.ps1 -rgName "test_InternalUpgrade_rg" -oldLBName "LBForInternal" -newlocation "centralus" -newLbName "LBForUpgrade"
   ```

## <a name="common-questions"></a>Häufig gestellte Fragen

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Gibt es irgendwelche Einschränkungen mit dem Azure PowerShell-Skript zum Migrieren der Konfiguration von v1 zu v2?

Ja. Lesen Sie [Vorbehalte/Einschränkungen](#caveatslimitations).

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Leitet das Azure PowerShell-Skript auch den Datenverkehr von meiner Load Balancer Basic-Instanz zur neu erstellten Load Balancer Standard-Instanz um?

Ja, der Datenverkehr wird migriert. Wenn Sie den Datenverkehr persönlich migrieren möchten, verwenden Sie [dieses Skript](https://www.powershellgallery.com/packages/AzureILBUpgrade/1.0), das virtuelle Computer nicht für Sie verschiebt.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Ich hatte beim Verwenden dieses Skripts einige Probleme. Wie erhalte ich Hilfe?
  
Sie können eine E-Mail an slbupgradesupport@microsoft.com senden, eine Supportanfrage beim Azure-Support öffnen, oder beides tun.

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über Standard Load Balancer](load-balancer-overview.md)