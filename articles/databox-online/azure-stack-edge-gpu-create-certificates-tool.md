---
title: Erstellen von Zertifikaten mit dem Microsoft Azure Stack Hub Readiness Checker-Tool | Microsoft-Dokumentation
description: In diesem Artikel wird beschrieben, wie Sie Zertifikatsanforderungen erstellen und dann mithilfe des Azure Stack Hub Readiness Checker-Tools Zertifikate auf Ihrem Azure Stack Edge Pro-GPU-Gerät erhalten und installieren.
services: Azure Stack Edge Pro
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 11/24/2020
ms.author: alkohli
ms.openlocfilehash: 2dc211b7564b08e9aa36b1e394a66a4c175922e2
ms.sourcegitcommit: 5a999764e98bd71653ad12918c09def7ecd92cf6
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 02/16/2021
ms.locfileid: "100549439"
---
# <a name="create-certificates-for-your-azure-stack-edge-pro-using-azure-stack-hub-readiness-checker-tool"></a>Erstellen von Zertifikaten für Azure Stack Edge Pro-Geräte mit dem Azure Stack Hub Readiness Checker-Tool 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

In diesem Artikel wird beschrieben, wie Sie Zertifikate für Ihr Azure Stack Edge Pro-Gerät mit dem Azure Stack Hub Readiness Checker-Tool erstellen. 

## <a name="using-azure-stack-hub-readiness-checker-tool"></a>Verwenden des Azure Stack Hub Readiness Checker-Tools

Erstellen Sie mit dem Azure Stack Hub Readiness Checker-Tool Zertifikatsignieranforderungen (Certificate Signing Requests, CSRs), die für eine Azure Stack Edge Pro-Gerätebereitstellung geeignet sind. Sie können diese Anforderungen erstellen, nachdem Sie ein Azure Stack Edge Pro-Gerät bestellt haben und auf dessen Lieferung warten.

> [!NOTE]
> Verwenden Sie dieses Tool nur für Test- oder Entwicklungszwecke und nicht für Produktionsgeräte. 

Mit dem Azure Stack Hub Readiness Checker-Tool (AzsReadinessChecker) können Sie die folgenden Zertifikate anfordern:

- Azure Resource Manager-Zertifikat
- Zertifikat für lokale Benutzeroberfläche
- Knotenzertifikat
- Blobzertifikat
- VPN-Zertifikat


## <a name="prerequisites"></a>Voraussetzungen

Stellen Sie Folgendes sicher, um Zertifikatsignieranforderungen für die Azure Stack Edge Pro-Gerätebereitstellung zu erstellen: 

- Sie haben einen Client, auf dem Windows 10 oder Windows Server 2016 oder höher ausgeführt wird. 
- Sie haben das Microsoft Azure Stack Hub Readiness Checker-Tool [aus dem PowerShell-Katalog](https://aka.ms/AzsReadinessChecker) auf dieses System heruntergeladen.
- Halten Sie die folgenden Informationen für die Zertifikate bereit:
  - Mediumname
  - Knotenseriennummer
  - Externer vollqualifizierter Domänenname (FQDN)

## <a name="generate-certificate-signing-requests"></a>Generieren von Zertifikatsignieranforderungen

Führen Sie die folgenden Schritte aus, um die Azure Stack Edge Pro-Gerätezertifikate vorzubereiten:

1. Führen Sie PowerShell als Administrator aus (5.1 oder höher).
2. Installieren Sie das Azure Stack Hub Readiness Checker-Tool. Geben Sie an der PowerShell-Eingabeaufforderung Folgendes ein: 

    ```azurepowershell
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

    Um abzufragen, welche Version installiert ist, geben Sie Folgendes ein:  

    ```azurepowershell
    Get-InstalledModule -Name Microsoft.AzureStack.ReadinessChecker  | ft Name, Version 
    ```

3. Erstellen Sie ein Verzeichnis für alle Zertifikate, sofern Sie noch keines haben. Typ: 
    
    ```azurepowershell
    New-Item "C:\certrequest" -ItemType Directory
    ``` 
    
4. Um eine Zertifikatanforderung zu erstellen, geben Sie die folgenden Informationen an. Wenn Sie ein VPN-Zertifikat erstellen, sind einige dieser Eingaben nicht erforderlich.
    
    |Eingabe |BESCHREIBUNG  |
    |---------|---------|
    |`OutputRequestPath`|Der Dateipfad auf dem lokalen Client, auf dem die Zertifikatanforderungen erstellt werden sollen.        |
    |`DeviceName`|Der Name Ihres Geräts auf der Seite **Geräte** in der lokalen Webbenutzeroberfläche Ihres Geräts. <br> Dieses Feld ist für ein VPN-Zertifikat nicht erforderlich.         |
    |`NodeSerialNumber`|Die Seriennummer des Geräteknotens auf der Seite **Netzwerk** in der lokalen Webbenutzeroberfläche Ihres Geräts. <br> Dieses Feld ist für ein VPN-Zertifikat nicht erforderlich.       |
    |`ExternalFQDN`|Der DNSDomain-Wert auf der Seite **Geräte** in der lokalen Webbenutzeroberfläche Ihres Geräts.         |
    |`RequestType`|Der Anforderungstyp kann `MultipleCSR` (verschiedene Zertifikate für die verschiedenen Endpunkte) oder `SingleCSR` (ein einzelnes Zertifikat für alle Endpunkte) sein. <br> Dieses Feld ist für ein VPN-Zertifikat nicht erforderlich.     |

    Geben Sie für alle Zertifikate mit Ausnahme des VPN-Zertifikats Folgendes ein: 
    
    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeDEVICE'
        DeviceName = 'myTEA1'
        NodeSerialNumber = 'VM1500-00025'
        externalFQDN = 'azurestackedge.contoso.com'
        requestType = 'MultipleCSR'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    Wenn Sie ein VPN-Zertifikat erstellen, geben Sie Folgendes ein: 

    ```azurepowershell
    $edgeCSRparams = @{
        CertificateType = 'AzureStackEdgeVPN'
        externalFQDN = 'azurestackedge.contoso.com'
        OutputRequestPath = "C:\certrequest"
    }
    New-AzsCertificateSigningRequest @edgeCSRparams
    ```

    
5. Die Zertifikatanforderungsdateien befinden sich in dem Verzeichnis, das Sie im obigen OutputRequestPath-Parameter angegeben haben. Wenn Sie den `MultipleCSR`-Parameter verwenden, werden die folgenden vier Dateien mit der Dateiendung `.req` angezeigt:

    
    |Dateinamen  |Typ der Zertifikatanforderung  |
    |---------|---------|
    |Beginnend mit Ihrem `DeviceName`     |Zertifikatanforderung für lokale Webbenutzeroberfläche      |
    |Beginnend mit Ihrem `NodeSerialNumber`     |Knotenzertifikatanforderung         |
    |Seit `login`     |Zertifikatanforderung für Azure Resource Manager-Endpunkt       |
    |Seit      |Blob Storage-Zertifikatanforderung. Sie enthält einen Platzhalter, da sie alle Speicherkonten abdeckt, die Sie möglicherweise auf dem Gerät erstellen.          |
    |Seit      |VPN-Client-Zertifikatanforderung.         |

    Außerdem wird ein INF-Ordner angezeigt. Dieser enthält eine management.<Name des Edge-Geräts>-Informationsdatei in Klartext, in der die Zertifikatdetails erläutert werden.  


6. Übermitteln Sie diese Dateien an Ihre Zertifizierungsstelle (intern oder öffentlich). Stellen Sie sicher, dass Ihre Zertifizierungsstelle mithilfe Ihrer generierten Anforderung Zertifikate generiert, die den Anforderungen an Azure Stack Edge Pro-Zertifikate für [Knotenzertifikate](azure-stack-edge-gpu-manage-certificates.md#node-certificates), [Endpunktzertifikate](azure-stack-edge-gpu-manage-certificates.md#endpoint-certificates) und [Zertifikate für die lokale Benutzeroberfläche](azure-stack-edge-gpu-manage-certificates.md#local-ui-certificates) entsprechen.

## <a name="prepare-certificates-for-deployment"></a>Vorbereiten von Zertifikaten für die Bereitstellung

Die Zertifikatsdateien, die Sie von der Zertifizierungsstelle erhalten, müssen mit Eigenschaften importiert und exportiert werden, die den Zertifikatanforderungen des Azure Stack Edge Pro-Geräts entsprechen. Führen Sie die folgenden Schritte auf demselben System aus, auf dem Sie die Zertifikatsignieranforderungen generiert haben.

- Zum Importieren der Zertifikate führen Sie die unter [Importieren von Zertifikaten auf den Clients, die auf das Azure Stack Edge Pro-Gerät zugreifen](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device) beschriebenen Schritte aus.

- Zum Exportieren der Zertifikate führen Sie die unter [Exportieren von Zertifikaten von dem Client, der auf das Azure Stack Edge Pro-Gerät zugreift](azure-stack-edge-gpu-manage-certificates.md#import-certificates-on-the-client-accessing-the-device) beschriebenen Schritte aus.


## <a name="validate-certificates"></a>Überprüfen von Zertifikaten

Zuerst generieren Sie eine ordnungsgemäße Ordnerstruktur und platzieren die Zertifikate in den entsprechenden Ordnern. Erst dann überprüfen Sie die Zertifikate mit dem Tool.

1. Führen Sie PowerShell als Administrator aus.

2. Geben Sie Folgendes an der Eingabeaufforderung ein, um die entsprechende Ordnerstruktur zu generieren:

    `New-AzsCertificateFolder -CertificateType AzureStackEdge -OutputPath "$ENV:USERPROFILE\Documents\AzureStackCSR"`

3. Konvertieren Sie das PFX-Kennwort in eine sichere Zeichenfolge. Typ:       

    `$pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString` 

4. Überprüfen Sie als Nächstes die Zertifikate. Typ:

    `Invoke-AzsCertificateValidation -CertificateType AzureStackEdge -DeviceName mytea1 -NodeSerialNumber VM1500-00025 -externalFQDN azurestackedge.contoso.com -CertificatePath $ENV:USERPROFILE\Documents\AzureStackCSR\AzureStackEdge -pfxPassword $pfxPassword`

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen Ihres Azure Stack Edge Pro-Geräts](azure-stack-edge-gpu-deploy-prep.md)
