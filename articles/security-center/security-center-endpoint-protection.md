---
title: Ermittlung von Endpoint Protection-Lösungen und Health Assessment in Azure Security Center | Microsoft-Dokumentation
description: Erläutert, wie die Endpoint Protection-Lösungen ermittelt und als fehlerfrei identifiziert werden.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
ms.assetid: 2730a2f5-20bc-4027-a1c2-db9ed0539532
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2019
ms.author: v-mohabe
ms.openlocfilehash: b17e5f16b988bfa562b00bc6f5b9dfd34be4ca43
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/27/2019
ms.locfileid: "66245573"
---
# <a name="endpoint-protection-assessment-and-recommendations-in-azure-security-center"></a>Endpoint Protection: Bewertung und Empfehlungen in Azure Security Center

Endpoint Protection-Bewertung und Empfehlungen in Azure Security Center ermittelt [unterstützte](https://docs.microsoft.com/azure/security-center/security-center-os-coverage#supported-platforms-for-windows-computers-and-vms) Versionen von Endpoint Protection-Lösungen und stellt Integritätsbewertungen von diesen bereit. In diesem Thema werden die Szenarien erläutert, bei denen die folgenden zwei Empfehlungen für Endpoint Protection-Lösungen über das Azure Security Center generiert werden.

* **Endpoint Protection-Lösungen auf Ihrem virtuellen Computer installieren**
* **Endpoint Protection-Integritätsprobleme auf Ihren Computern beheben**

## <a name="windows-defender"></a>Windows Defender

* Die Empfehlung **„Endpoint Protection-Lösungen auf Ihrem virtuellen Computer installieren“** wird generiert, wenn [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) ausgeführt wird, und das Ergebnis **AMServiceEnabled: False** lautet.

* Die Empfehlung **„Endpoint Protection-Integritätsprobleme auf Ihren Computern beheben“** wird generiert, wenn [Get-MpComputerStatus](https://docs.microsoft.com/powershell/module/defender/get-mpcomputerstatus?view=win10-ps) ausgeführt wird und mindestens eine der folgenden Situationen eintritt:

  * Mindestens eine der folgenden Eigenschaften ist „False“:

     **AMServiceEnabled**

     **AntispywareEnabled**

     **RealTimeProtectionEnabled**

     **BehaviorMonitorEnabled**

     **IoavProtectionEnabled**

     **OnAccessProtectionEnabled**

  * Mindestens eine oder beide folgenden Eigenschaften ist größer oder gleich 7.

     **AntispywareSignatureAge**

     **AntivirusSignatureAge**

## <a name="microsoft-system-center-endpoint-protection"></a>Microsoft System Center Endpoint Protection

* Die Empfehlung **„Endpoint Protection-Lösungen auf Ihrem virtuellen Computer installieren“** wird generiert, wen das Importieren von **SCEPMpModule  („$env:ProgramFiles\Microsoft Security Client\MpProvider\MpProvider.psd1“)** und Ausführen von **Get-MProtComputerStatus** zum Ergebnis **AMServiceEnabled = false** führt.

* Die Empfehlung **„Endpoint Protection-Integritätsprobleme auf Ihren Computern beheben“** wird generiert, wenn **Get-MprotComputerStatus** ausgeführt wird und mindestens eine der folgenden Situationen eintritt:

    * Mindestens eine der folgenden Eigenschaften ist „False“:

       **AMServiceEnabled**
    
       **AntispywareEnabled**
    
       **RealTimeProtectionEnabled**
    
       **BehaviorMonitorEnabled**
    
       **IoavProtectionEnabled**
    
       **OnAccessProtectionEnabled**
          
    * Mindestens eine oder beide folgenden Signaturaktualisierungen ist größer oder gleich 7. 

       **AntispywareSignatureAge**
    
       **AntivirusSignatureAge**

## <a name="trend-micro"></a>Trend Micro

* Die Empfehlung **„Endpoint Protection-Lösungen auf Ihrem virtuellen Computer installieren“** wird generiert, wenn eine oder mehrere der folgenden Überprüfungen nicht bestanden werden:
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent** ist vorhanden
    * **HKLM:\SOFTWARE\TrendMicro\Deep Security Agent\InstallationFolder** ist vorhanden
    * Die Datei **dsq_query.cmd** ist im Installationsordner vorhanden
    * Das Ausführen von **dsa_query.cmd** führt zum Ergebnis **Component.AM.mode: on – Trend Micro Deep Security-Agent erkannt**

## <a name="symantec-endpoint-protection"></a>Symantec Endpoint Protection
Die Empfehlung **„Endpoint Protection-Lösungen auf Ihrem virtuellen Computer installieren“** wird generiert, wenn eine der folgenden Überprüfungen nicht bestanden wird:

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

oder

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\PRODUCTNAME = "Symantec Endpoint Protection"**

* **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\ASRunningStatus = 1**

Die Empfehlung **„Endpoint Protection-Integritätsprobleme auf Ihren Computern beheben“** wird generiert, wenn eine der folgenden Überprüfungen nicht bestanden wird:  

* Überprüfen: Symantec-Version >= 12:  Registrierungspfad: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion" -Value "PRODUCTVERSION"**

* Echtzeitschutzstatus überprüfen: **HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection\AV\Storages\Filesystem\RealTimeScan\OnOff == 1**

* Status der Signaturaktualisierung überprüfen: **HKLM\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LatestVirusDefsDate <= 7 Tage**

* Status der vollständigen Überprüfung überprüfen: **HKLM:\Software\Symantec\Symantec Endpoint Protection\CurrentVersion\public-opstate\LastSuccessfulScanDateTime <= 7 Tage**

* Pfad der Signaturversionsnummer zu Signaturversion für Symantec 12 suchen: **Registry Paths+ "CurrentVersion\SharedDefs" -Value "SRTSP"** 

* Pfad zur Signaturversion für Symantec-14: **Registry Paths+ "CurrentVersion\SharedDefs\SDSDefs" -Value "SRTSP"**

Registrierungspfade:

**"HKLM:\Software\Symantec\Symantec Endpoint Protection" + $Path;** 
 **"HKLM:\Software\Wow6432Node\Symantec\Symantec Endpoint Protection" + $Path**

## <a name="mcafee-endpoint-protection-for-windows"></a>McAfee Endpoint Protection für Windows

Die Empfehlung **„Endpoint Protection-Lösungen auf Ihrem virtuellen Computer installieren“** wird generiert, wenn die folgenden Überprüfungen nicht bestanden werden:

* **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion** ist vorhanden

* **HKLM:\SOFTWARE\McAfee\AVSolution\MCSHIELDGLOBAL\GLOBAL\enableoas = 1**

Die Empfehlung **„Endpoint Protection-Integritätsprobleme auf Ihren Computern beheben“** wird generiert, wenn die folgenden Überprüfungen nicht bestanden werden:

* McAfee-Version: **HKLM:\SOFTWARE\McAfee\Endpoint\AV\ProductVersion >= 10**

* Signaturversion suchen: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "dwContentMajorVersion"**

* Signaturdatum suchen: **HKLM:\Software\McAfee\AVSolution\DS\DS -Value "szContentCreationDate" >= 7 Tage**

* Überprüfungsdatum suchen: **HKLM:\Software\McAfee\Endpoint\AV\ODS -Value "LastFullScanOdsRunTime" >= 7 Tage**

## <a name="troubleshoot-and-support"></a>Problembehandlung und Support

### <a name="troubleshoot"></a>Problembehandlung

Microsoft Antimalware-Erweiterungsprotokolle sind verfügbar unter:  
**%Systemdrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.Security.IaaSAntimalware(Or PaaSAntimalware)\1.5.5.x(version#)\CommandExecution.log**

### <a name="support"></a>Support

Sollten Sie beim Lesen dieses Artikels feststellen, dass Sie weitere Hilfe benötigen, können Sie sich über das [MSDN Azure-Forum oder über das Stack Overflow-Forum](https://azure.microsoft.com/support/forums/) mit Azure-Experten in Verbindung setzen. Sie können auch einen Azure-Supportfall erstellen. Rufen Sie die [Azure-Support-Website](https://azure.microsoft.com/support/options/) auf, und wählen Sie „Support erhalten“ aus. Informationen zur Nutzung von Azure-Support finden Sie unter [Microsoft Azure-Support-FAQ](https://azure.microsoft.com/support/faq/).
