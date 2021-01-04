---
title: Migrieren von V1 zu v2 – Azure Application Gateway
description: In diesem Artikel wird gezeigt, wie Sie Azure Application Gateway und Web Application Firewall von v1 zu v2 migrieren.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 03/31/2020
ms.author: victorh
ms.openlocfilehash: 3dd46f4033a568a278d7006c0d5aab451496ff47
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93397222"
---
# <a name="migrate-azure-application-gateway-and-web-application-firewall-from-v1-to-v2"></a>Migrieren von Azure Application Gateway und Web Application Firewall von v1 zu v2

[Azure Application Gateway und Web Application Firewall (WAF) v2](application-gateway-autoscaling-zone-redundant.md) sind ab sofort verfügbar und bieten zusätzliche Features wie automatische Skalierung und Verfügbarkeitszonenredundanz. Die vorhandenen v1-Gateways werden aber nicht automatisch auf v2 aktualisiert. Wenn Sie von v1 zu v2 migrieren möchten, führen Sie die Schritte in diesem Artikel aus.

Es gibt zwei Phasen in einer Migration:

1. Migrieren der Konfiguration
2. Migrieren des Clientdatenverkehrs

Dieser Artikel behandelt die Migration einer Konfiguration. Die Migration von Clientdatenverkehr erfolgt je nach Ihrer speziellen Umgebung unterschiedlich. Einige generelle Empfehlungen dazu finden Sie jedoch [hier](#migrate-client-traffic).

## <a name="migration-overview"></a>Übersicht zur Migration

Es gibt ein Azure PowerShell-Skript, in dem folgende Vorgänge ausgeführt werden:

* Es wird ein neues Standard_v2- oder WAF_v2-Gateway in einem von Ihnen angegebenen Subnetz eines virtuellen Netzwerks erstellt.
* Die Konfiguration, die zu dem v1-Standard oder -WAF-Gateway gehört, wird nahtlos in das neu erstellte Standard_v2- oder WAF_v2-Gateway kopiert.

### <a name="caveatslimitations"></a>Vorbehalte/Einschränkungen

* Das neue v2-Gateway hat neue öffentliche und private IP-Adressen. Es ist nicht möglich, die IP-Adressen, die dem vorhandenen v1-Gateway zugeordnet sind, nahtlos in v2 zu verschieben. Sie können dem neuen v2-Gateway jedoch eine vorhandene (nicht zugeordnete) öffentliche oder private IP-Adresse zuordnen.
* Sie müssen einen IP-Adressraum für ein anderes Subnetz in Ihrem virtuellen Netzwerk bereitstellen, in dem sich Ihr v1-Gateway befindet. Mit dem Skript kann das v2-Gateway nicht in einem vorhandenen Subnetz erstellt werden, in dem es bereits ein v1-Gateway v1 gibt. Hat das vorhandene Subnetz jedoch bereits ein v2-Gateway, funktioniert dieses möglicherweise weiterhin, sofern genügend IP-Adressraum verfügbar ist.
* Wenn Sie dem Subnetz des v2-Gateways eine Netzwerksicherheitsgruppe oder benutzerdefinierte Routen zugeordnet haben, vergewissern Sie sich, dass sie die [NSG-Anforderungen](../application-gateway/configuration-infrastructure.md#network-security-groups) und [UDR-Anforderungen](../application-gateway/configuration-infrastructure.md#supported-user-defined-routes) erfüllen, um eine erfolgreiche Migration sicherzustellen.
* [Richtlinien für VNET-Dienstendpunkte](../virtual-network/virtual-network-service-endpoint-policies-overview.md) werden derzeit in einem Application Gateway-Subnetz nicht unterstützt.
* Um eine TLS/SSL-Konfiguration zu migrieren, müssen Sie alle TLS/SSL-Zertifikate angeben, die in Ihrem v1-Gateway verwendet werden.
* Haben Sie für Ihr v1-Gateway den FIPS-Modus aktiviert, wird dieser nicht in Ihr neues v2-Gateway migriert. Der FIPS-Modus wird in v2 nicht unterstützt.
* In v2 wird IPv6 nicht unterstützt, weshalb v1-Gateways, für die IPv6 aktiviert ist, nicht migriert werden. Wenn Sie das Skript ausführen, wird es möglicherweise nicht vollständig ausgeführt.
* Wenn das v1-Gateway nur eine private IP-Adresse hat, erstellt das Skript eine öffentliche IP-Adresse und eine private IP-Adresse für das neue v2-Gateway. v2-Gateways unterstützen derzeit nicht nur private IP-Adressen.
* Header mit Namen, die etwas anderes als Buchstaben, Ziffern, Bindestriche und Unterstriche enthalten, werden nicht an Ihre Anwendung übergeben. Dies gilt nur für Headernamen, nicht für Headerwerte. Hierbei handelt es sich um einen Breaking Change gegenüber v1.

## <a name="download-the-script"></a>Herunterladen des Skripts

Laden Sie das Migrationsskript aus dem [PowerShell-Katalog](https://www.powershellgallery.com/packages/AzureAppGWMigration) herunter.

## <a name="use-the-script"></a>Verwenden des Skripts

Je nach dem, wie Ihre lokale PowerShell-Umgebung eingerichtet und eingestellt ist, gibt es zwei Optionen für Sie:

* Haben Sie die Azure Az-Module nicht installiert, oder haben Sie kein Problem damit, die Azure Az-Module zu deinstallieren, sollten Sie die `Install-Script`-Option verwenden, um das Skript auszuführen.
* Wenn Sie die Azure Az-Module beibehalten müssen, besteht die beste Vorgehensweise darin, das Skript herunterzuladen und direkt auszuführen.

Um festzustellen, ob Sie die Azure Az-Module installiert haben, führen Sie `Get-InstalledModule -Name az` aus. Werden keine installierten Az-Module angezeigt, können Sie die `Install-Script`-Methode verwenden.

### <a name="install-using-the-install-script-method"></a>Installieren mit der Install-Script-Methode

Damit Sie diese Option nutzen können, dürfen keine Azure Az-Module auf Ihrem Computer installiert sein. Wenn diese installiert sind, zeigt der folgende Befehl einen Fehler an. Sie können entweder die Azure Az-Module deinstallieren oder die andere Option nutzen, um das Skript manuell herunterzuladen und es auszuführen.
  
Führen Sie das Skript mit dem folgenden Befehl aus, um die neueste Version zu erhalten:

`Install-Script -Name AzureAppGWMigration -Force`

Mit diesem Befehl werden auch die erforderlichen Az-Module installiert.  

### <a name="install-using-the-script-directly"></a>Installieren durch direktes Verwenden des Skripts

Haben Sie einige Azure Az-Module installiert, und können (oder möchten) Sie diese nicht deinstallieren, laden Sie das Skript manuell herunter, indem Sie die Registerkarte **Manual Download** im Skript-Downloadlink verwenden. Das Skript wird als reine NUPKG-Datei heruntergeladen. Informationen, wie das Skript aus dieser NUPKG-Datei installiert wird, finden Sie unter [Manuelles Herunterladen des Pakets](/powershell/scripting/gallery/how-to/working-with-packages/manual-download).

So führen Sie das Skript aus

1. Verwenden Sie `Connect-AzAccount`, um eine Verbindung mit Azure herzustellen.

1. Verwenden Sie `Import-Module Az`, um die Az-Module zu importieren.

1. Führen Sie `Get-Help AzureAppGWMigration.ps1` aus, um sich die erforderlichen Parameter anzusehen:

   ```
   AzureAppGwMigration.ps1
    -resourceId <v1 application gateway Resource ID>
    -subnetAddressRange <subnet space you want to use>
    -appgwName <string to use to append>
    -sslCertificates <comma-separated SSLCert objects as above>
    -trustedRootCertificates <comma-separated Trusted Root Cert objects as above>
    -privateIpAddress <private IP string>
    -publicIpResourceId <public IP name string>
    -validateMigration -enableAutoScale
   ```

   Parameter für das Skript:
   * **resourceId: [Zeichenfolge]: Erforderlich** – Dies ist die Azure-Ressourcen-ID für Ihr vorhandenes Standard-v1- oder WAF-v1-Gateway. Um diesen Zeichenfolgenwert zu finden, navigieren Sie zum Azure-Portal, wählen Sie Ihr Anwendungsgateway oder Ihre WAF-Ressource aus, und klicken Sie auf den **Eigenschaften** -Link für das Gateway. Die Ressourcen-ID wird auf dieser Seite angezeigt.

     Sie können auch die folgenden Azure PowerShell-Befehle ausführen, um die Ressourcen-ID zu ermitteln:

     ```azurepowershell
     $appgw = Get-AzApplicationGateway -Name <v1 gateway name> -ResourceGroupName <resource group Name> 
     $appgw.Id
     ```

   * **subnetAddressRange: [Zeichenfolge]:  Erforderlich** – Dies ist der IP-Adressraum, den Sie einem neuen Subnetz, das Ihr neues v2-Gateway enthält, zugewiesen haben (oder zuweisen wollen). Diese Angabe muss in der CIDR-Notation erfolgen. Beispiel: 10.0.0.0/24. Sie müssen dieses Subnetz nicht im Voraus anlegen. Das Skript erstellt es für Sie, wenn es nicht vorhanden ist.
   * **appgwName: [Zeichenfolge]: Optional**. Dies ist eine Zeichenfolge, die als der Name des neuen Standard_v2- oder WAF_v2-Gateways verwendet werden soll. Ist dieser Parameter nicht angegeben, wird der Name Ihres vorhandenen v1-Gateways verwendet, an den das Suffix *_v2* angefügt wird.
   * **sslCertificates: [PSApplicationGatewaySslCertificate]: Optional**.  Eine Liste aus PSApplicationGatewaySslCertificate-Objekten, die Sie mit Kommas als Trennzeichen erstellt haben und in der die TLS/SSL-Zertifikate aus Ihrem v1-Gateway aufgeführt sind, muss in das neue v2-Gateway hochgeladen werden. Für jedes Ihrer TLS/SSL-Zertifikate, die für Ihr Standard-v1- oder -WAF-v1-Gateway konfiguriert sind, können Sie über den hier gezeigten `New-AzApplicationGatewaySslCertificate`-Befehl ein neues PSApplicationGatewaySslCertificate-Objekt erstellen. Sie benötigen den Pfad zu Ihrer TLS/SSL-Zertifikatsdatei und das Kennwort.

     Dieser Parameter ist nur optional, wenn Sie für das v1-Gateway oder die v1-WAF keine HTTPS-Listener konfiguriert haben. Sobald Sie mindestens einen HTTPS-Listener eingerichtet haben, müssen Sie diesen Parameter angeben.

      ```azurepowershell  
      $password = ConvertTo-SecureString <cert-password> -AsPlainText -Force
      $mySslCert1 = New-AzApplicationGatewaySslCertificate -Name "Cert01" `
        -CertificateFile <Cert-File-Path-1> `
        -Password $password 
      $mySslCert2 = New-AzApplicationGatewaySslCertificate -Name "Cert02" `
        -CertificateFile <Cert-File-Path-2> `
        -Password $password
      ```

     Sie können `$mySslCert1, $mySslCert2` (durch Komma getrennt) aus dem vorherigen Beispiel als Werte für diesen Parameter im Skript übergeben.
   * **trustedRootCertificates: [PSApplicationGatewayTrustedRootCertificate]: Optional**. Eine Liste aus PSApplicationGatewayTrustedRootCertificate-Objekten, die Sie mit Kommas als Trennzeichen erstellt haben und in der die [vertrauenswürdigen Stammzertifikate](ssl-overview.md) aufgeführt sind, die zur Authentifizierung Ihrer Back-End-Instanzen aus Ihrem v2-Gateway verwendet werden.
   
      ```azurepowershell
      $certFilePath = ".\rootCA.cer"
      $trustedCert = New-AzApplicationGatewayTrustedRootCertificate -Name "trustedCert1" -CertificateFile $certFilePath
      ```

      Informationen, wie eine Liste aus PSApplicationGatewayTrustedRootCertificate-Objekten erstellt wird, finden Sie unter [New-AzApplicationGatewayTrustedRootCertificate](/powershell/module/Az.Network/New-AzApplicationGatewayTrustedRootCertificate?view=azps-2.1.0&viewFallbackFrom=azps-2.0.0).
   * **privateIpAddress: [Zeichenfolge]: Optional**. Eine bestimmte private IP-Adresse, die Sie Ihrem neuen v2-Gateway zuordnen möchten.  Diese Adresse muss aus dem VNet stammen, das Sie für Ihr neues v2-Gateway zuordnen. Ist dieser Parameter nicht angegeben, weist das Skript eine private IP-Adresse für Ihr v2-Gateway zu.
   * **publicIpResourceId: [Zeichenfolge]: Optional**. Die Ressourcen-ID einer vorhandenen öffentlichen IP-Adresse (Standard-SKU-Ressource) in Ihrem Abonnement, die Sie dem neuen v2-Gateway zuordnen möchten. Wenn dies nicht angegeben ist, weist das Skript eine neue öffentliche IP-Adresse in der gleichen Ressourcengruppe zu. Der Name ist der Name des v2-Gateways, an den *-IP* angefügt ist.
   * **validateMigration: [Schalter]: Optional**. Verwenden Sie diesen Parameter, wenn das Skript nach der Erstellung des v2-Gateways und dem Kopieren der Konfiguration einige grundlegende Vergleichsprüfungen der Konfiguration durchführen soll. Standardmäßig erfolgt keine Prüfung.
   * **enableAutoScale: [Schalter]: Optional**. Verwenden Sie diesen Parameter, wenn das Skript die automatische Skalierung auf dem neuen v2-Gateway nach der Erstellung aktivieren soll. Standardmäßig ist automatische Skalierung deaktiviert. Sie können automatische Skalierung später für das neu erstellte v2-Gateway jederzeit manuell aktivieren.

1. Führen Sie das Skript mit den entsprechenden Parametern aus. Es kann fünf bis sieben Minuten dauern, bis es fertig ist.

    **Beispiel**

   ```azurepowershell
   AzureAppGWMigration.ps1 `
      -resourceId /subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/applicationGateways/myv1appgateway `
      -subnetAddressRange 10.0.0.0/24 `
      -appgwname "MynewV2gw" `
      -sslCertificates $mySslCert1,$mySslCert2 `
      -trustedRootCertificates $trustedCert `
      -privateIpAddress "10.0.0.1" `
      -publicIpResourceId "/subscriptions/8b1d0fea-8d57-4975-adfb-308f1f4d12aa/resourceGroups/MyResourceGroup/providers/Microsoft.Network/publicIPAddresses/MyPublicIP" `
      -validateMigration -enableAutoScale
   ```

## <a name="migrate-client-traffic"></a>Migrieren von Clientdatenverkehr

Überprüfen Sie zunächst, ob das Skript erfolgreich ein neues v2-Gateway mit genau der Konfiguration erstellt hat, die aus Ihrem v1-Gateway migriert wurde. Sie können dies über das Azure-Portal prüfen.

Senden Sie außerdem etwas Datenverkehr über das v2-Gateway als manuellen Test.
  
Es folgen einige Szenarien, in denen Ihr aktuelles Anwendungsgateway (Standard) Clientdatenverkehr empfangen kann, sowie unsere Empfehlungen für jedes Szenario:

* **Eine benutzerdefinierte DNS-Zone (z. B. „contoso.com“), in der auf die Front-End-IP-Adresse (mit einem A-Eintrag) verwiesen wird, die Ihrem Standard-v1- oder WAF-v1-Gateway zugeordnet ist**.

    Sie können Ihren DNS-Eintrag so aktualisieren, dass er auf die Front-End-IP-Adresse oder DNS-Bezeichnung verweist, die Ihrem Standard_v2-Anwendungsgateway zugeordnet ist. Abhängig davon, welche Gültigkeitsdauer für Ihren DNS-Eintrag konfiguriert ist, kann es einige Zeit dauern, bis Ihr gesamter Clientdatenverkehr zu Ihrem neuen v2-Gateway migriert wird.
* **Eine benutzerdefinierte DNS-Zone (z. B. „contoso.com“), die auf die DNS-Bezeichnung (Beispiel: *myappgw.eastus.cloudapp.azure.com* mit einem CNAME-Eintrag) verweist, die Ihrem v1-Gateway zugeordnet ist**.

   Sie haben zwei Möglichkeiten:

  * Wenn Sie öffentliche IP-Adressen in Ihrem Anwendungsgateway verwenden, können Sie eine kontrollierte, präzise Migration durchführen, indem Sie ein Traffic Manager-Profil verwenden, um Datenverkehr inkrementell an das neue v2-Gateway weiterzuleiten (gewichtete Routingmethode für Datenverkehr).

    Hierzu können Sie die DNS-Bezeichnungen vom v1- und vom v2-Anwendungsgateway zu dem [Traffic Manager-Profil](../traffic-manager/traffic-manager-routing-methods.md#weighted-traffic-routing-method) hinzufügen und Ihren benutzerdefinierten DNS-Eintrag (z. B. `www.contoso.com`) über CNAME-Einträge zur Traffic Manager-Domäne (z. B. „contoso.trafficmanager.net“) umleiten.
  * Alternativ können Sie Ihren benutzerdefinierten Domänen-DNS-Eintrag so aktualisieren, dass er auf die DNS-Bezeichnung des neuen v2-Anwendungsgateways verweist. Abhängig davon, welche Gültigkeitsdauer für Ihren DNS-Eintrag konfiguriert ist, kann es einige Zeit dauern, bis Ihr gesamter Clientdatenverkehr zu Ihrem neuen v2-Gateway migriert wird.
* **Ihre Client stellen Verbindungen mit der Front-End-IP-Adresse Ihres Anwendungsgateways her**.

   Aktualisieren Sie Ihre Clients, sodass diese die IP-Adressen verwenden, die dem neu erstellten v2-Anwendungsgateway zugeordnet sind. Es empfiehlt sich, dass Sie IP-Adressen nicht direkt verwenden. Dazu bietet es sich an, dass Sie die DNS-Namensbezeichnung (z. B. „yourgateway.eastus.cloudapp.azure.com“), die Ihrem Anwendungsgateway zugeordnet ist, über CNAME-Einträge auf Ihre eigene benutzerdefinierte DNS-Zone (z. B. „contoso.com“) umleiten.

## <a name="common-questions"></a>Häufig gestellte Fragen

### <a name="are-there-any-limitations-with-the-azure-powershell-script-to-migrate-the-configuration-from-v1-to-v2"></a>Gibt es irgendwelche Einschränkungen mit dem Azure PowerShell-Skript zum Migrieren der Konfiguration von v1 zu v2?

Ja. Lesen Sie [Vorbehalte/Einschränkungen](#caveatslimitations).

### <a name="is-this-article-and-the-azure-powershell-script-applicable-for-application-gateway-waf-product-as-well"></a>Gelten dieser Artikel und das Azure PowerShell-Skript auch für das Application Gateway-Produkt WAF? 

Ja.

### <a name="does-the-azure-powershell-script-also-switch-over-the-traffic-from-my-v1-gateway-to-the-newly-created-v2-gateway"></a>Leitet das Azure PowerShell-Skript auch den Datenverkehr von meinem v1-Gateway zu dem neu erstellten v2-Gateway um?

Nein. Das Azure PowerShell-Skript migriert nur die Konfiguration. Die Migration des Datenverkehrs liegt in Ihrer Verantwortung und muss von Ihnen gesteuert werden.

### <a name="is-the-new-v2-gateway-created-by-the-azure-powershell-script-sized-appropriately-to-handle-all-of-the-traffic-that-is-currently-served-by-my-v1-gateway"></a>Ist das neue v2-Gateway, das über das Azure PowerShell-Skript erstellt wurde, so dimensioniert, dass es den gesamten Datenverkehr bewältigen kann, der derzeit von meinem v1-Gateway verarbeitet wird?

Das Azure PowerShell-Skript erstellt ein neues v2-Gateway mit einer geeigneten Größe, um den Datenverkehr auf Ihrem bestehenden v1-Gateway zu bewältigen. Automatische Skalierung ist standardmäßig deaktiviert, aber Sie können automatische Skalierung aktivieren, wenn Sie das Skript ausführen.

### <a name="i-configured-my-v1-gateway--to-send-logs-to-azure-storage-does-the-script-replicate-this-configuration-for-v2-as-well"></a>Ich habe mein v1-Gateway konfiguriert, um Protokolle an den Azure-Speicher zu senden. Repliziert das Skript diese Konfiguration auch für v2?

Nein. Das Skript repliziert diese Konfiguration für v2 nicht. Sie müssen die Protokollkonfiguration separat zum migrierten v2-Gateway hinzufügen.

### <a name="does-this-script-support-certificates-uploaded-to-azure-keyvault-"></a>Unterstützt dieses Skript Zertifikate, die in Azure Key Vault hochgeladen wurden?

Nein. Das Skript unterstützt derzeit keine Zertifikate in Key Vault. Dies wird jedoch für eine zukünftige Version in Erwägung gezogen.

### <a name="i-ran-into-some-issues-with-using-this-script-how-can-i-get-help"></a>Ich hatte beim Verwenden dieses Skripts einige Probleme. Wie erhalte ich Hilfe?
  
Sie können den Azure-Support unter dem Thema „Konfiguration und Einrichtung/Migration zu V2-SKU“ kontaktieren. Weitere Informationen zum Azure-Support finden Sie [hier](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>Nächste Schritte

[Informationen zu Application Gateway v2](application-gateway-autoscaling-zone-redundant.md)