---
title: 'Generieren und Exportieren von Zertifikaten für Point-to-Site-Verbindungen: PowerShell: Azure | Microsoft-Dokumentation'
description: Hier erhalten Sie Informationen zum Erstellen selbstsignierter Stammzertifikate, Exportieren des öffentlichen Schlüssels und Generieren von Clientzertifikaten mit PowerShell unter Windows 10 oder Windows Server 2016.
services: vpn-gateway
documentationcenter: na
author: cherylmc
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: cherylmc
ms.openlocfilehash: 74639dee6fb548e1c9067cae6fc22f6e3cc872c3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60763890"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-powershell"></a>Generieren und Exportieren von Zertifikaten für Punkt-zu-Standort-Verbindungen mithilfe von PowerShell

Punkt-zu-Standort-Verbindungen verwenden Zertifikate zur Authentifizierung. In diesem Artikel wird beschrieben, wie Sie mithilfe von PowerShell unter Windows 10 oder Windows Server 2016 ein selbstsigniertes Stammzertifikat erstellen und Clientzertifikate generieren. Anleitungen für andere Zertifikate finden Sie unter [Zertifikate – Linux](vpn-gateway-certificates-point-to-site-linux.md) oder [Zertifikate – MakeCert](vpn-gateway-certificates-point-to-site-makecert.md).

Sie müssen die in diesem Artikel beschriebenen Schritte auf einem Computer unter Windows 10 oder Windows Server 2016 ausführen. Die zum Generieren von Zertifikaten verwendeten PowerShell-Cmdlets sind Teil des Betriebssystems und können in anderen Versionen von Windows nicht ausgeführt werden. Der Windows 10- oder Windows Server 2016-Computer wird lediglich zum Generieren der Zertifikate benötigt. Nachdem die Zertifikate generiert wurden, können sie hochgeladen oder unter jedem unterstützten Clientbetriebssystem installiert werden. 

Wenn Sie keinen Zugriff auf einen Windows 10- oder Windows Server 2016-Computer haben, können Sie Zertifikate mit [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) generieren. Unabhängig davon, mit welcher Methode sie generiert wurden, können die Zertifikate unter jedem [unterstützten](vpn-gateway-howto-point-to-site-resource-manager-portal.md#faq) Clientbetriebssystem installiert werden.

## <a name="rootcert"></a>1. Erstellen eines selbstsignierten Stammzertifikats

Verwenden Sie das Cmdlet „New-SelfSignedCertificate“, um ein selbstsigniertes Stammzertifikat zu erstellen. Zusätzliche Informationen zu den Parametern finden Sie in der Beschreibung zu [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

1. Öffnen Sie auf einem Computer unter Windows 10 oder Windows Server 2016 eine Windows PowerShell-Konsole mit erhöhten Rechten. Diese Beispiele funktionieren nicht in der Testversion von Azure Cloud Shell. Sie müssen diese Beispiele lokal ausführen.
2. Verwenden Sie das folgende Beispiel, um das selbstsignierte Stammzertifikat zu erstellen. Das folgende Beispiel erstellt ein selbstsigniertes Stammzertifikat mit dem Namen P2SRootCert, das automatisch in „Certificates-Current User\Personal\Certificates“ installiert wird. Sie können das Zertifikat anzeigen, indem Sie *certmgr.msc* öffnen oder die Option *Benutzerzertifikate verwalten* auswählen.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

## <a name="clientcert"></a>2. Generieren eines Clientzertifikats

Auf jedem Clientcomputer, der per Punkt-zu-Standort eine Verbindung mit einem VNet herstellt, muss ein Clientzertifikat installiert sein. Sie generieren ein Clientzertifikat aus dem selbstsignierten Stammzertifikat und exportieren und installieren es anschließend. Wenn das Clientzertifikat nicht installiert ist, tritt bei der Authentifizierung ein Fehler auf. 

Die folgenden Schritte führen Sie durch das Generieren eines Clientzertifikats aus einem selbstsignierten Stammzertifikat. Sie können mehrere Clientzertifikate aus demselben Stammzertifikat generieren. Wenn Sie mithilfe der folgenden Schritte Clientzertifikate generieren, wird das Clientzertifikat automatisch auf dem Computer installiert, mit dem Sie das Zertifikat generiert haben. Falls Sie ein Clientzertifikat auf einem anderen Clientcomputer installieren möchten, können Sie es exportieren.

In den Beispielen wird das Cmdlet „New-SelfSignedCertificate“ zum Generieren eines Clientzertifikats verwendet, das in einem Jahr abläuft. Zusätzliche Informationen zu den Parametern, z.B. Festlegen eines anderen Ablaufwerts für das Clientzertifikat, finden Sie in der Beschreibung zu [New-SelfSignedCertificate](https://technet.microsoft.com/itpro/powershell/windows/pkiclient/new-selfsignedcertificate).

### <a name="example-1"></a>Beispiel 1

Verwenden Sie dieses Beispiel, wenn Sie die PowerShell-Konsole nach dem Erstellen des selbstsignierten Stammzertifikats nicht geschlossen haben. Dieses Beispiel wird aus dem vorherigen Abschnitt fortgesetzt und verwendet die deklarierte Variable „$cert“. Wenn Sie die PowerShell-Konsole nach dem Erstellen des selbstsignierten Stammzertifikats geschlossen haben oder zusätzliche Clientzertifikate in einer neuen PowerShell-Konsolensitzung erstellen, verwenden Sie die Schritte in [Beispiel 2](#ex2).

Ändern Sie das Beispiel, und führen Sie es aus, um ein Clientzertifikat zu generieren. Wenn Sie das folgende Beispiel ausführen, ohne es zu ändern, ist das Ergebnis ein Clientzertifikat mit dem Namen P2SChildCert.  Wenn Sie dem untergeordneten Zertifikat einen anderen Namen geben möchten, ändern Sie den CN-Wert. Ändern Sie die TextExtension nicht, wenn Sie dieses Beispiel ausführen. Das Clientzertifikat, das Sie generieren, wird auf Ihrem Computer automatisch in „Certificates - Current User\Personal\Certificate“ installiert.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="ex2"></a>Beispiel 2

Wenn Sie zusätzliche Clientzertifikate erstellen oder nicht die gleiche PowerShell-Sitzung verwenden, in der Sie Ihr selbstsigniertes Stammzertifikat erstellt haben, führen Sie folgende Schritte aus:

1. Identifizieren Sie das selbstsignierte Stammzertifikat, das auf dem Computer installiert ist. Dieses Cmdlet gibt eine Liste von Zertifikaten zurück, die auf Ihrem Computer installiert sind.

   ```powershell
   Get-ChildItem -Path “Cert:\CurrentUser\My”
   ```
2. Suchen Sie den Antragstellernamen in der zurückgegebenen Liste, und kopieren Sie den Fingerabdruck, der sich daneben in einer Textdatei befindet. Im folgenden Beispiel sind zwei Zertifikate vorhanden. Der CN-Name ist der Name des selbstsignierten Stammzertifikats, aus dem Sie ein untergeordnetes Zertifikat generieren möchten. In diesem Fall P2SRootCert.

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```
3. Deklarieren Sie eine Variable für das Stammzertifikat mit dem Fingerabdruck aus dem vorherigen Schritt. Ersetzen Sie THUMBPRINT mit dem Namen des Stammzertifikats, aus dem Sie ein untergeordnetes Zertifikat generieren möchten.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Bei Verwendung des Fingerabdrucks für P2SRootCert im vorherigen Schritt sieht die Variable z.B. wie folgt aus:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```
4. Ändern Sie das Beispiel, und führen Sie es aus, um ein Clientzertifikat zu generieren. Wenn Sie das folgende Beispiel ausführen, ohne es zu ändern, ist das Ergebnis ein Clientzertifikat mit dem Namen P2SChildCert. Wenn Sie dem untergeordneten Zertifikat einen anderen Namen geben möchten, ändern Sie den CN-Wert. Ändern Sie die TextExtension nicht, wenn Sie dieses Beispiel ausführen. Das Clientzertifikat, das Sie generieren, wird auf Ihrem Computer automatisch in „Certificates - Current User\Personal\Certificate“ installiert.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="cer"></a>3. Exportieren des öffentlichen Schlüssels des Stammzertifikats (CER-Datei)

[!INCLUDE [Export public key](../../includes/vpn-gateway-certificates-export-public-key-include.md)]


### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exportieren des selbstsignierten Stammzertifikats und des privaten Schlüssels zum Speichern (optional)

Möglicherweise möchten Sie das selbstsignierte Stammzertifikat exportieren und als Sicherung sicher speichern. Bei Bedarf können Sie es später auf einem anderen Computer installieren und weitere Clientzertifikate generieren. Um das selbstsignierte Stammzertifikat als PFX-Datei zu exportieren, wählen Sie das Stammzertifikat aus, und verwenden Sie die gleichen Schritte wie zum [Exportieren eines Clientzertifikats](#clientexport).

## <a name="clientexport"></a>4. Exportieren des Clientzertifikats

[!INCLUDE [Export client certificate](../../includes/vpn-gateway-certificates-export-client-cert-include.md)]


## <a name="install"></a>5. Installieren eines exportierten Clientzertifikats

Jeder Client, der über eine P2S-Verbindung eine Verbindung mit dem VNet herstellt, erfordert, dass ein Clientzertifikat lokal installiert ist.

Informationen zum Installieren eines Clientzertifikats finden Sie unter [Installieren eines Clientzertifikats für Point-to-Site-Verbindungen mit Azure-Zertifikatauthentifizierung](point-to-site-how-to-vpn-client-install-azure-cert.md).

## <a name="install"></a>6. Fortfahren mit den Schritten für die P2S-Konfiguration

Setzen Sie die Punkt-zu-Standort-Konfiguration fort.

* Schritte für das **Resource Manager**-Bereitstellungsmodell finden Sie unter [Konfigurieren einer Point-to-Site-Verbindung mit einem VNET unter Verwendung der Zertifikatauthentifizierung: Azure-Portal](vpn-gateway-howto-point-to-site-resource-manager-portal.md). 
* Schritte für das **klassische** Bereitstellungsmodell finden Sie unter [Konfigurieren einer Point-to-Site-Verbindung mit einem VNet über das Azure-Portal](vpn-gateway-howto-point-to-site-classic-azure-portal.md) (klassisch).
* Informationen zur P2S-Problembehandlung finden Sie unter [Problembehandlung: Probleme mit Azure P2S-Verbindungen (Point-to-Site)](vpn-gateway-troubleshoot-vpn-point-to-site-connection-problems.md).
