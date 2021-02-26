---
title: Upgraden einer öffentlichen Load Balancer-Instanz von Basic auf Standard – Azure Load Balancer
description: In diesem Artikel erfahren Sie, wie Sie eine öffentliche Azure Load Balancer-Instanz von der Basic- auf die Standard-SKU upgraden.
services: load-balancer
author: irenehua
ms.service: load-balancer
ms.topic: how-to
ms.date: 01/23/2020
ms.author: irenehua
ms.openlocfilehash: 3bf910d3309285c8b700c39af68fb90715f8863a
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99987712"
---
# <a name="upgrade-azure-public-load-balancer"></a>Upgraden einer öffentlichen Azure Load Balancer-Instanz
[Azure Load Balancer Standard](load-balancer-overview.md) bietet umfangreiche Funktionen sowie Hochverfügbarkeit durch Zonenredundanz. Weitere Informationen zu Load Balancer-SKUs finden Sie in der [Vergleichstabelle](./skus.md#skus).

Ein Upgrade umfasst zwei Phasen:

1. Ändern Sie die IP-Zuordnungsmethode von dynamisch in statisch.
2. Führen Sie das PowerShell-Skript aus, um das Upgrade und die Migration von Datenverkehr abzuschließen.

> [!IMPORTANT]
> Das Skript wird zurzeit gewartet. Informationen zum Upgraden von öffentlichen IP-Adressen von der Basic-SKU zur Standard-SKU finden Sie in [diesen Anweisungen](https://docs.microsoft.com/azure/virtual-network/virtual-network-public-ip-address-upgrade?tabs=option-upgrade-cli%2Coption-migrate-powershell#tabpanel_CeZOj-G++Q_option-upgrade-cli).

## <a name="upgrade-overview"></a>Upgradeübersicht

Es gibt ein Azure PowerShell-Skript, in dem folgende Vorgänge ausgeführt werden:

* Erstellt einen Load Balancer der Standard-SKU mit dem von Ihnen angegebenen Speicherort in der gleichen Ressourcengruppe wie der Load Balancer Basic Standard.
* Aktualisiert eine öffentliche IP-Adresse von der Basic-SKU auf die Standard-SKU.
* Nahtloses Kopieren der Konfigurationen der Load Balancer-Instanz mit Basic-SKU in die neu erstellte Load Balancer Standard-Instanz.
* Erstellt eine Standardausgangsregel, die ausgehende Konnektivität ermöglicht.

### <a name="caveatslimitations"></a>Vorbehalte/Einschränkungen

* Das Skript unterstützt nur Upgrades für öffentliche Load Balancer-Instanzen. Anleitungen für ein Upgrade des internen Load Balancers im Tarif „Basic“ finden Sie auf [dieser Seite](./upgrade-basicinternal-standard.md).
* Die Zuordnungsmethode der öffentlichen IP-Adresse muss vor dem Ausführen des Skripts in „static“ geändert werden. 
* Wenn Ihre Load Balancer-Instanz über keine Front-End-IP-Konfiguration oder über keinen Back-End-Pool verfügt, tritt beim Ausführen des Skripts wahrscheinlich ein Fehler auf. Stellen Sie sicher, dass die Angaben vorhanden sind.

### <a name="change-allocation-method-of-the-public-ip-address-to-static"></a>Ändern der Zuordnungsmethode der öffentlichen IP-Adresse in „static“

* **Die folgenden Schritte werden empfohlen:

    1. Melden Sie sich zur Durchführung der Aufgaben dieser Schnellstartanleitung am [Azure-Portal](https://portal.azure.com) an.
 
    1. Wählen Sie im Menü auf der linken Seite die Option **Alle Ressourcen** und dann in der Ressourcenliste die Option für die **Öffentliche IP-Basisadresse, die Load Balancer Basic zugeordnet ist**, aus.
   
    1. Wählen Sie unter **Einstellungen** die Option **Konfigurationen** aus.
   
    1. Wählen Sie unter **Zuweisung** die Option **Statisch** aus.
    1. Wählen Sie **Speichern** aus.
    >[!NOTE]
    >Für virtuelle Computer mit öffentlichen IP-Adressen müssen zuerst Standard-IP-Adressen erstellt werden. Hierbei können allerdings keine identischen IP-Adressen garantiert werden. Heben Sie die Zuordnung der virtuellen Computer zu Basic-IP-Adressen auf, und ordnen Sie sie den neu erstellten Standard-IP-Adressen zu. Anschließend können Sie die Schritte zum Hinzufügen von virtuellen Computern zum Back-End-Pool der Load Balancer Standard-Instanz ausführen. 

* **Erstellen neuer virtueller Computer, um sie den Back-End-Pools der neu erstellten öffentlichen Load Balancer Standard-Instanz hinzuzufügen:**
    * Weitere Informationen zum Erstellen eines virtuellen Computers sowie zum Zuordnen des virtuellen Computers zu Load Balancer Standard finden Sie [hier](./quickstart-load-balancer-standard-public-portal.md#create-virtual-machines).


## <a name="download-the-script"></a>Herunterladen des Skripts

Laden Sie das Migrationsskript aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/AzurePublicLBUpgrade/4.0) herunter.
## <a name="use-the-script"></a>Verwenden des Skripts

Je nach dem, wie Ihre lokale PowerShell-Umgebung eingerichtet und eingestellt ist, gibt es zwei Optionen für Sie:

* Haben Sie die Azure Az-Module nicht installiert, oder haben Sie kein Problem damit, die Azure Az-Module zu deinstallieren, sollten Sie die `Install-Script`-Option verwenden, um das Skript auszuführen.
* Wenn Sie die Azure Az-Module beibehalten müssen, besteht die beste Vorgehensweise darin, das Skript herunterzuladen und direkt auszuführen.

Um festzustellen, ob Sie die Azure Az-Module installiert haben, führen Sie `Get-InstalledModule -Name az` aus. Werden keine installierten Az-Module angezeigt, können Sie die `Install-Script`-Methode verwenden.

### <a name="install-using-the-install-script-method"></a>Installieren mit der Install-Script-Methode

Damit Sie diese Option nutzen können, dürfen keine Azure Az-Module auf Ihrem Computer installiert sein. Wenn diese installiert sind, zeigt der folgende Befehl einen Fehler an. Sie können entweder die Azure Az-Module deinstallieren oder die andere Option nutzen, um das Skript manuell herunterzuladen und es auszuführen.
  
Führen Sie das Skript mit dem folgenden Befehl aus:

`Install-Script -Name AzurePublicLBUpgrade`

Mit diesem Befehl werden auch die erforderlichen Az-Module installiert.  

### <a name="install-using-the-script-directly"></a>Installieren durch direktes Verwenden des Skripts

Haben Sie einige Azure Az-Module installiert, und können (oder möchten) Sie diese nicht deinstallieren, laden Sie das Skript manuell herunter, indem Sie die Registerkarte **Manual Download** im Skript-Downloadlink verwenden. Das Skript wird als reine NUPKG-Datei heruntergeladen. Informationen, wie das Skript aus dieser NUPKG-Datei installiert wird, finden Sie unter [Manuelles Herunterladen des Pakets](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

So führen Sie das Skript aus

1. Verwenden Sie `Connect-AzAccount`, um eine Verbindung mit Azure herzustellen.

1. Verwenden Sie `Import-Module Az`, um die Az-Module zu importieren.

1. Untersuchen Sie die erforderlichen Parameter:

   * **oldRgName: [Zeichenfolge]: Erforderlich:** Die Ressourcengruppe für die vorhandene Load Balancer Basic-Instanz, die Sie upgraden möchten. Wählen Sie zur Ermittlung dieses Zeichenfolgenwerts im Azure-Portal Ihre Load Balancer Basic-Quelle aus, und klicken Sie auf die **Übersicht** für den Lastenausgleich. Die Ressourcengruppe befindet sich auf dieser Seite.
   * **oldLBName: [Zeichenfolge]: Erforderlich:** Der Name der vorhandene Load Balancer Basic-Instanz, die Sie upgraden möchten. 
   * **newLBName: [Zeichenfolge]: Erforderlich:** Der Name der zu erstellenden Load Balancer Standard-Instanz.
1. Führen Sie das Skript mit den entsprechenden Parametern aus. Es kann fünf bis sieben Minuten dauern, bis es fertig ist.

    **Beispiel**

   ```azurepowershell
   AzurePublicLBUpgrade.ps1 -oldRgName "test_publicUpgrade_rg" -oldLBName "LBForPublic" -newLbName "LBForUpgrade"
   ```

### <a name="create-an-outbound-rule-for-outbound-connection"></a>Erstellen einer Ausgangsregel für ausgehende Verbindungen

Befolgen Sie die [Anweisungen](./quickstart-load-balancer-standard-public-powershell.md#create-outbound-rule-configuration), um eine Ausgangsregel zu erstellen, die Ihnen Folgendes ermöglicht:
* Eine ausgehende NAT von Grund auf neu definieren
* Das Verhalten einer vorhandenen NAT für ausgehenden Datenverkehr skalieren und optimieren

## <a name="common-questions"></a>Häufig gestellte Fragen

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Gibt es irgendwelche Einschränkungen mit dem Azure PowerShell-Skript zum Migrieren der Konfiguration von v1 zu v2?

Ja. Lesen Sie [Vorbehalte/Einschränkungen](#caveatslimitations).

### <a name="how-long-does-the-upgrade-take"></a>Wie lange dauert das Upgrade?

Es dauert in der Regel ungefähr 5 bis 10 Minuten, bis das Skript abgeschlossen ist. Abhängig von der Komplexität der Load Balancer-Konfiguration kann dies auch länger dauern. Berücksichtigen Sie daher die Ausfallzeiten, und planen Sie ggf. ein Failover ein.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-basic-load-balancer-to-the-newly-created-standard-load-balancer"></a>Leitet das Azure PowerShell-Skript auch den Datenverkehr von meiner Load Balancer Basic-Instanz zur neu erstellten Load Balancer Standard-Instanz um?

Ja. Das Azure PowerShell-Skript aktualisiert nicht nur die öffentliche IP-Adresse und kopiert die Konfiguration aus Basic in Load Balancer Standard, sondern migriert auch die VM hinter den neu erstellten öffentlichen Load Balancer Standard. 

## <a name="next-steps"></a>Nächste Schritte

[Übersicht über Standard Load Balancer](load-balancer-overview.md)
