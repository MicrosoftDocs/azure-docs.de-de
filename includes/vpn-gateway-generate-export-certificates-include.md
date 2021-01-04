---
title: include file
description: include file
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/29/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 946ff043828034340ae3273fc0629e32de755540
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026354"
---
## <a name="create-a-self-signed-root-certificate"></a><a name="rootcert"></a>Erstellen eines selbstsignierten Stammzertifikats

Verwenden Sie das Cmdlet „New-SelfSignedCertificate“, um ein selbstsigniertes Stammzertifikat zu erstellen. Zusätzliche Informationen zu den Parametern finden Sie in der Beschreibung zu [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate).

1. Öffnen Sie auf einem Computer unter Windows 10 oder Windows Server 2016 eine Windows PowerShell-Konsole mit erhöhten Rechten. Diese Beispiele funktionieren nicht in der Testversion von Azure Cloud Shell. Sie müssen diese Beispiele lokal ausführen.
1. Verwenden Sie das folgende Beispiel, um das selbstsignierte Stammzertifikat zu erstellen. Das folgende Beispiel erstellt ein selbstsigniertes Stammzertifikat mit dem Namen P2SRootCert, das automatisch in „Certificates-Current User\Personal\Certificates“ installiert wird. Sie können das Zertifikat anzeigen, indem Sie *certmgr.msc* öffnen oder die Option *Benutzerzertifikate verwalten* auswählen.

   Melden Sie sich mithilfe des Cmdlets „`Connect-AzAccount`“ an. Führen Sie dann das folgende Beispiel mit allen erforderlichen Änderungen aus.

   ```powershell
   $cert = New-SelfSignedCertificate -Type Custom -KeySpec Signature `
   -Subject "CN=P2SRootCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" -KeyUsageProperty Sign -KeyUsage CertSign
   ```

1. Lassen Sie die PowerShell-Konsole geöffnet, und fahren Sie mit den nächsten Schritten fort, um ein Clientzertifikat zu generieren.

## <a name="generate-a-client-certificate"></a><a name="clientcert"></a>Generieren eines Clientzertifikats

Auf jedem Clientcomputer, der per Punkt-zu-Standort eine Verbindung mit einem VNet herstellt, muss ein Clientzertifikat installiert sein. Sie generieren ein Clientzertifikat aus dem selbstsignierten Stammzertifikat und exportieren und installieren es anschließend. Wenn das Clientzertifikat nicht installiert ist, tritt bei der Authentifizierung ein Fehler auf. 

Die folgenden Schritte führen Sie durch das Generieren eines Clientzertifikats aus einem selbstsignierten Stammzertifikat. Sie können mehrere Clientzertifikate aus demselben Stammzertifikat generieren. Wenn Sie mithilfe der folgenden Schritte Clientzertifikate generieren, wird das Clientzertifikat automatisch auf dem Computer installiert, mit dem Sie das Zertifikat generiert haben. Falls Sie ein Clientzertifikat auf einem anderen Clientcomputer installieren möchten, können Sie es exportieren.

In den Beispielen wird das Cmdlet „New-SelfSignedCertificate“ zum Generieren eines Clientzertifikats verwendet, das in einem Jahr abläuft. Zusätzliche Informationen zu den Parametern, z.B. Festlegen eines anderen Ablaufwerts für das Clientzertifikat, finden Sie in der Beschreibung zu [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate).

### <a name="example-1---powershell-console-session-still-open"></a>Beispiel 1: PowerShell-Konsolensitzung weiterhin geöffnet.

Verwenden Sie dieses Beispiel, wenn Sie die PowerShell-Konsole nach dem Erstellen des selbstsignierten Stammzertifikats nicht geschlossen haben. Dieses Beispiel wird aus dem vorherigen Abschnitt fortgesetzt und verwendet die deklarierte Variable „$cert“. Wenn Sie die PowerShell-Konsole nach dem Erstellen des selbstsignierten Stammzertifikats geschlossen haben oder zusätzliche Clientzertifikate in einer neuen PowerShell-Konsolensitzung erstellen, verwenden Sie die Schritte in [Beispiel 2](#ex2).

Ändern Sie das Beispiel, und führen Sie es aus, um ein Clientzertifikat zu generieren. Wenn Sie das folgende Beispiel ausführen, ohne es zu ändern, ist das Ergebnis ein Clientzertifikat mit dem Namen P2SChildCert.  Wenn Sie dem untergeordneten Zertifikat einen anderen Namen geben möchten, ändern Sie den CN-Wert. Ändern Sie die TextExtension nicht, wenn Sie dieses Beispiel ausführen. Das Clientzertifikat, das Sie generieren, wird auf Ihrem Computer automatisch in „Certificates - Current User\Personal\Certificate“ installiert.

```powershell
New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
-Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
-HashAlgorithm sha256 -KeyLength 2048 `
-CertStoreLocation "Cert:\CurrentUser\My" `
-Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
```

### <a name="example-2---new-powershell-console-session"></a><a name="ex2"></a>Beispiel 2: neue PowerShell-Konsolensitzung

Wenn Sie zusätzliche Clientzertifikate erstellen oder nicht die gleiche PowerShell-Sitzung verwenden, in der Sie Ihr selbstsigniertes Stammzertifikat erstellt haben, führen Sie folgende Schritte aus:

1. Identifizieren Sie das selbstsignierte Stammzertifikat, das auf dem Computer installiert ist. Dieses Cmdlet gibt eine Liste von Zertifikaten zurück, die auf Ihrem Computer installiert sind.

   ```powershell
   Get-ChildItem -Path "Cert:\CurrentUser\My"
   ```

1. Suchen Sie den Antragstellernamen in der zurückgegebenen Liste, und kopieren Sie den Fingerabdruck, der sich daneben in einer Textdatei befindet. Im folgenden Beispiel sind zwei Zertifikate vorhanden. Der CN-Name ist der Name des selbstsignierten Stammzertifikats, aus dem Sie ein untergeordnetes Zertifikat generieren möchten. In diesem Fall P2SRootCert.

   ```
   Thumbprint                                Subject
  
   AED812AD883826FF76B4D1D5A77B3C08EFA79F3F  CN=P2SChildCert4
   7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655  CN=P2SRootCert
   ```

1. Deklarieren Sie eine Variable für das Stammzertifikat mit dem Fingerabdruck aus dem vorherigen Schritt. Ersetzen Sie THUMBPRINT mit dem Namen des Stammzertifikats, aus dem Sie ein untergeordnetes Zertifikat generieren möchten.

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\THUMBPRINT"
   ```

   Bei Verwendung des Fingerabdrucks für P2SRootCert im vorherigen Schritt sieht die Variable z.B. wie folgt aus:

   ```powershell
   $cert = Get-ChildItem -Path "Cert:\CurrentUser\My\7181AA8C1B4D34EEDB2F3D3BEC5839F3FE52D655"
   ```

1. Ändern Sie das Beispiel, und führen Sie es aus, um ein Clientzertifikat zu generieren. Wenn Sie das folgende Beispiel ausführen, ohne es zu ändern, ist das Ergebnis ein Clientzertifikat mit dem Namen P2SChildCert. Wenn Sie dem untergeordneten Zertifikat einen anderen Namen geben möchten, ändern Sie den CN-Wert. Ändern Sie die TextExtension nicht, wenn Sie dieses Beispiel ausführen. Das Clientzertifikat, das Sie generieren, wird auf Ihrem Computer automatisch in „Certificates - Current User\Personal\Certificate“ installiert.

   ```powershell
   New-SelfSignedCertificate -Type Custom -DnsName P2SChildCert -KeySpec Signature `
   -Subject "CN=P2SChildCert" -KeyExportPolicy Exportable `
   -HashAlgorithm sha256 -KeyLength 2048 `
   -CertStoreLocation "Cert:\CurrentUser\My" `
   -Signer $cert -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")
   ```

## <a name="export-the-root-certificate-public-key-cer"></a><a name="cer"></a>Exportieren des öffentlichen Schlüssels des Stammzertifikats (CER-Datei)

[!INCLUDE [Export public key](vpn-gateway-certificates-export-public-key-include.md)]

### <a name="export-the-self-signed-root-certificate-and-private-key-to-store-it-optional"></a>Exportieren des selbstsignierten Stammzertifikats und des privaten Schlüssels zum Speichern (optional)

Möglicherweise möchten Sie das selbstsignierte Stammzertifikat exportieren und als Sicherung sicher speichern. Bei Bedarf können Sie es später auf einem anderen Computer installieren und weitere Clientzertifikate generieren. Um das selbstsignierte Stammzertifikat als PFX-Datei zu exportieren, wählen Sie das Stammzertifikat aus, und verwenden Sie die gleichen Schritte wie zum [Exportieren eines Clientzertifikats](#clientexport).

## <a name="export-the-client-certificate"></a><a name="clientexport"></a>Exportieren des Clientzertifikats

[!INCLUDE [Export client certificate](vpn-gateway-certificates-export-client-cert-include.md)]