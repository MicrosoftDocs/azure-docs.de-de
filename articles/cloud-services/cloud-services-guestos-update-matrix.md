---
title: Informationen zu den neuesten Azure-Gastbetriebssystemversionen | Microsoft-Dokumentation
description: Die neueste Releaseneuigkeiten und SDK-Kompatibilität für das Azure Cloud Services-Gastbetriebssystem.
services: cloud-services
documentationcenter: na
author: yohaddad
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/17/2020
ms.author: yohaddad
ms.openlocfilehash: a5cc0fdf0473cb8ceafd164a34df6f0bd66dd6d3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96013699"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure-Gastbetriebssystemversionen und SDK-Kompatibilitätsmatrix
Bietet Ihnen aktuelle Informationen zu den neuesten Azure-Gastbetriebssystemreleases für Cloud Services. Anhand dieser Informationen können Sie Ihren Upgradepfad planen, bevor ein Gastbetriebssystem abgekündigt wird. Wenn Sie die Rollen so konfigurieren, dass die *automatischen* Gast-BS-Updates, wie unter [Updateeinstellungen für Azure-Gast-BS][Azure Guest OS Update Settings]beschrieben, verwendet werden, müssen Sie diese Seite nicht unbedingt lesen.

> [!IMPORTANT]
> Diese Seite bezieht sich auf die Cloud Services-Web- und Workerrollen, die zusätzlich zu einem Gastbetriebssystem ausgeführt werden. Sie **gilt nicht** für IaaS Virtual Machines.
>
>


> [!TIP]
>  Abonnieren Sie den [RSS-Feed zu Gastbetriebssystemupdates], um möglichst schnell über alle Gastbetriebssystemänderungen informiert zu werden.
>
>

> [!IMPORTANT]
> Es werden nur noch die beiden neuesten Versionen des Gastbetriebssystems unterstützt, und im Azure-Portal sind nur noch diese beiden Versionen verfügbar.
>
>

Sie wissen nicht genau, wie Sie Ihr Gastbetriebssystem aktualisieren sollen? Entsprechende Informationen finden Sie [hier][cloud updates].

## <a name="news-updates"></a>Neuigkeiten

###### <a name="november-17-2020"></a>**17. November 2020**
Das Gastbetriebssystem für Oktober wurde veröffentlicht. 

###### <a name="october-10-2020"></a>**10. Oktober 2020**
Das Gastbetriebssystem für September wurde veröffentlicht. 

###### <a name="september-5-2020"></a>**5. September 2020**
Das Gastbetriebssystem für August wurde veröffentlicht. 

###### <a name="august-17-2020"></a>**17. August 2020**
Das Gastbetriebssystem für Juli wurde veröffentlicht. 

###### <a name="august-10-2020"></a>**10. August 2020**
Das Gastbetriebssystem für Juni wurde veröffentlicht. 

###### <a name="june-2-2020"></a>**2. Juni 2020**
Das Gastbetriebssystem für Mai wurde veröffentlicht. 

###### <a name="may-4-2020"></a>**4. Mai 2020**
Das Gastbetriebssystem für April wurde veröffentlicht. 

###### <a name="april-2-2020"></a>**2. April 2020**
Das Gastbetriebssystem für März wurde veröffentlicht. 

###### <a name="march-5-2020"></a>**5. März 2020**
Das Gast-BS für Februar wurde veröffentlicht. 

###### <a name="january-24-2020"></a>**24. Januar 2020**
Das Gastbetriebssystem für Januar wurde veröffentlicht. 

###### <a name="january-8-2020"></a>**8. Januar 2020**
Das Gastbetriebssystem für Dezember wurde veröffentlicht.

###### <a name="december-5-2019"></a>**5. Dezember 2019**
Das Gastbetriebssystem für November wurde veröffentlicht.

###### <a name="november-1-2019"></a>**1. November 2019**
Das Gastbetriebssystem für Oktober wurde veröffentlicht.

###### <a name="october-7-2019"></a>**7. Oktober 2019**
Das Gastbetriebssystem für September wurde veröffentlicht.

###### <a name="september-4-2019"></a>**4. September 2019**
Das Gastbetriebssystem für August wurde veröffentlicht.

###### <a name="july-26-2019"></a>**26. Juli 2019**
Das Gastbetriebssystem für Juli wurde veröffentlicht.

###### <a name="july-8-2019"></a>**8. Juli 2019**
Das Gastbetriebssystem für Juni wurde veröffentlicht.

###### <a name="june-6-2019"></a>**6. Juni 2019**
Das Gastbetriebssystem für Mai wurde veröffentlicht.

###### <a name="may-7-2019"></a>**7. Mai 2019**
Das Gastbetriebssystem für April wurde veröffentlicht.

###### <a name="march-26-2019"></a>**26. März 2019**
Das Gastbetriebssystem für März wurde veröffentlicht.

###### <a name="march-12-2019"></a>**12. März 2019**
Das Gast-BS für Februar wurde veröffentlicht.

###### <a name="february-5-2019"></a>**5. Februar 2019**
Das Gastbetriebssystem für Januar wurde veröffentlicht.

###### <a name="january-24-2019"></a>**24. Januar 2019**
Das Gastbetriebssystem der Familie 6 (Windows Server 2019) wurde veröffentlicht.

###### <a name="january-7-2019"></a>**7. Januar 2019**
Das Gastbetriebssystem für Dezember wurde veröffentlicht.

###### <a name="december-14-2018"></a>**14. Dezember 2018**
Das Gastbetriebssystem für November wurde veröffentlicht.

###### <a name="november-8-2018"></a>**8. November 2018**
Das Gastbetriebssystem für Oktober wurde veröffentlicht.

###### <a name="october-12-2018"></a>**12. Oktober 2018**
Das Gastbetriebssystem für September wurde veröffentlicht.

## <a name="releases"></a>Releases

## <a name="family-6-releases"></a>Releases von Familie 6
**Windows Server 2019**

Installierte .NET Framework-Versionen: 3.5, 4.7.2

> [!NOTE]
> Das Windows Azure SDK 3.0 für .NET kann [hier][Windows Azure SDK] heruntergeladen werden.
>
>Installationsschritte:
>1. Deinstallieren Sie ältere Versionen der MicrosoftAzureAuthoringTools „*.msi“
>2. Installieren Sie das [Azure SDK für .NET – 3.0][Windows Azure SDK].
>3. Starten Sie Ihren Computer neu
>4. Erstellen Sie ein neues Clouddienstprojekt, und fügen Sie eine einzelne Workerrolle hinzu
>5. Ändern Sie die Betriebssystemfamilie in 6, und erstellen Sie ein Paket
>6. Stellen Sie das Paket mithilfe des Azure-Portals oder von Visual Studio in Azure bereit
>
>Das Release „Guest OS Family 6“ (Gastbetriebssystemfamilie) erzwingt TLS 1.2 durch explizite Deaktivierung von TLS 1.0 und 1.1 und die Definition einer bestimmten Reihe von Verschlüsselungssammlungen. [Weitere Informationen].


| Konfigurationszeichenfolge | Veröffentlichungsdatum | Deaktivierungsdatum |
| --- | --- | --- |
|  WA-GUEST-OS-6.24_202010-02 |  17. November 2020  |  Nach 6.26  |  
|  WA-GUEST-OS-6.23_202009-01  |  10. Oktober 2020  |  Nach 6.25  |  
|~~WA-GUEST-OS-6.22_202008-02~~|  5\. September 2020  |  17. November 2020  |  
|~~WA-GUEST-OS-6.21_202007-01~~|  17. August 2020  |  10. Oktober 2020  |  
|~~WA-GUEST-OS-6.20_202006-02~~|  10. August 2020  |  5\. September 2020  |  
|~~WA-GUEST-OS-6.19_202005-02~~|  2\. Juni 2020  |  17. August 2020  |  
|~~WA-GUEST-OS-6.18_202004-01~~|  4\. Mai 2020  |  10. August 2020  |  
|~~WA-GUEST-OS-6.17_202003-01~~|  2\. April 2020  |  2\. Juni 2020  |  
|~~WA-GUEST-OS-6.16_202002-01~~|  5\. März 2020  |  4\. Mai 2020  |  
|~~WA-GUEST-OS-6.15_202001-01~~|  24. Januar 2020  |  2\. April 2020  |  
|~~WA-GUEST-OS-6.14_201912-01~~| 8\. Januar 2020 | 5\. März 2020 |  
|~~WA-GUEST-OS-6.13_201911-01~~| 5\. Dezember 2019 | 24. Januar 2020 |  
|~~WA-GUEST-OS-6.12_201910-01~~| 1\. November 2019 | 8\. Januar 2020 |  
|~~WA-GUEST-OS-6.11_201909-01~~| 7\. Oktober 2019 | 5\. Dezember 2019 |  
|~~WA-GUEST-OS-6.10_201908-01~~| 4\. August 2019 | 1\. November 2019  |  
|~~WA-GUEST-OS-6.9_201907-0~~|26. Juli 2019 | 7\. Oktober 2019 |
|~~WA-GUEST-OS-6.8_201906-01~~|8\. Juli 2019 |4\. August 2019 |
|~~WA-GUEST-OS-6.7_201905-01~~ |6\. Juni 2019 |26. Juli 2019 |
|~~WA-GUEST-OS-6.6_201904-01~~ |7\. Mai 2019 |8\. Juli 2019 |
|~~WA-GUEST-OS-6.5_201903-01~~ |26. März 2019 |6\. Juni 2019 |
|~~WA-GUEST-OS-6.4_201902-01~~ |12. März 2019 |7\. Mai 2019 |
|~~WA-GUEST-OS-6.3_201901-01~~ |5\. Februar 2019 |26. März 2019 |
|~~WA-GUEST-OS-6.2_201812-01~~ |24. Januar 2019 |12. März 2019 |
|~~WA-GUEST-OS-6.1_201811-01~~ |24. Januar 2019 |5\. Februar 2019 |

## <a name="family-5-releases"></a>Releases von Familie 5
**Windows Server 2016**

Installierte .NET Framework-Versionen: 3.5, 4.6.2

> [!NOTE]
> Das RDP-Kennwort für Betriebssystemfamilie 5 muss mindestens zehn Zeichen umfassen.
>


| Konfigurationszeichenfolge | Veröffentlichungsdatum | Deaktivierungsdatum |
| --- | --- | --- |
|  WA-GUEST-OS-5.48_202010-02  |  17. November 2020  |  Nach 5.50  | 
|  WA-GUEST-OS-5.47_202009-01  |  10. Oktober 2020  |  Nach 5.49  | 
|~~WA-GUEST-OS-5.46_202008-02~~|  5\. September 2020  |  17. November 2020  |  
|~~WA-GUEST-OS-5.45_202007-01~~|  17. August 2020  |  10. Oktober 2020  |  
|~~WA-GUEST-OS-5.44_202006-02~~|  10. August 2020  |  5\. September 2020  |  
|~~WA-GUEST-OS-5.43_202005-02~~|  2\. Juni 2020  |  17. August 2020  |  
|~~WA-GUEST-OS-5.42_202004-01~~|  4\. Mai 2020  |  10. August 2020  |  
|~~WA-GUEST-OS-5.41_202003-01~~|  2\. April 2020  |  2\. Juni 2020  |  
|~~WA-GUEST-OS-5.40_202002-01~~|  5\. März 2020  |  4\. Mai 2020  |  
|~~WA-GUEST-OS-5.39_202001-01~~|  24. Januar 2020  |  2\. April 2020  |  
|~~WA-GUEST-OS-5.38_201912-01~~| 8\. Januar 2020 | 5\. März 2020 |  
|~~WA-GUEST-OS-5.37_201911-01~~| 5\. Dezember 2019 | 24. Januar 2020 |  
|~~WA-GUEST-OS-5.36_201910-01~~| 1\. November 2019 | 8\. Januar 2020 |  
|~~WA-GUEST-OS-5.35_201909-01~~| 7\. Oktober 2019 | 5\. Dezember 2019 |  
|~~WA-GUEST-OS-5.34_201908-01~~|  4\. August 2019  | 1\. November 2019 |  
|~~WA-GUEST-OS-5.33_201907-01~~| 26. Juli 2019 | 7\. Oktober 2019 |  
|~~WA-GUEST-OS-5.32_201906-01~~|8\. Juli 2019 |4\. August 2019 |
|~~WA-GUEST-OS-5.31_201905-01~~ |6\. Juni 2019 |26. Juli 2019 |
|~~WA-GUEST-OS-5.30_201904-01~~ |7\. Mai 2019 |8\. Juli 2019 |
|~~WA-GUEST-OS-5.29_201903-01~~ |26. März 2019 |6\. Juni 2019 |
|~~WA-GUEST-OS-5.28_201902-01~~ |12. März 2019 |7\. Mai 2019 |
|~~WA-GUEST-OS-5.27_201901-01~~ |5\. Februar 2019 |26. März 2019 |
|~~WA-GUEST-OS-5.26_201812-01~~ |7\. Januar 2019 |12. März 2019 |
|~~WA-GUEST-OS-5.25_201811-01~~ |14. Dezember 2018 |5\. Februar 2019 |
|~~WA-GUEST-OS-5.24_201810-01~~ |8\. November 2018 |7\. Januar 2019 |
|~~WA-GUEST-OS-5.23_201809-01~~ |12. Oktober 2018 |14. Dezember 2018 |

## <a name="family-4-releases"></a>Releases von Familie 4
**Windows Server 2012 R2**

Installierte .NET Framework-Versionen: 3.5, 4.5.1, 4.5.2

| Konfigurationszeichenfolge | Veröffentlichungsdatum | Deaktivierungsdatum |
| --- | --- | --- |
|  WA-GUEST-OS-4.83_202010-02  |  17. November 2020  |  Nach 4.85  | 
|  WA-GUEST-OS-4.82_202009-01  |  10. Oktober 2020  |  Nach 4.84  | 
|~~WA-GUEST-OS-4.81_202008-02~~|  5\. September 2020  |  17. November 2020  | 
|~~WA-GUEST-OS-4.80_202007-01~~|  17. August 2020  |  10. Oktober 2020  | 
|~~WA-GUEST-OS-4.79_202006-02~~|  10. August 2020  |  5\. September 2020  | 
|~~WA-GUEST-OS-4.78_202005-02~~|  2\. Juni 2020  |  17. August 2020  |  
|~~WA-GUEST-OS-4.77_202004-01~~|  4\. Mai 2020  |  10. August 2020  |  
|~~WA-GUEST-OS-4.76_202003-01~~|  2\. April 2020  |  2\. Juni 2020  |  
|~~WA-GUEST-OS-4.75_202002-01~~|  5\. März 2020  |  4\. Mai 2020  |  
|~~WA-GUEST-OS-4.74_202001-01~~|  24. Januar 2020  |  2\. April 2020  |  
|~~WA-GUEST-OS-4.73_201912-01~~| 8\. Januar 2020 | 5\. März 2020 |  
|~~WA-GUEST-OS-4.72_201911-01~~| 5\. Dezember 2019 | 24. Januar 2020 |  
|~~WA-GUEST-OS-4.71_201910-01~~| 1\. November 2019 | 8\. Januar 2020 |  
|~~WA-GUEST-OS-4.70_201909-01~~| 7\. Oktober 2019 | 5\. Dezember 2019 |  
|~~WA-GUEST-OS-4.69_201908-01~~| 4\. August 2019 | 1\. November 2019 |  
|~~WA-GUEST-OS-4.68_201907-01~~| 26. Juli 2019  | 7\. Oktober 2019 |
|~~WA-GUEST-OS-4.67_201906-01~~| 8\. Juli 2019 |4\. August 2019 |
|~~WA-GUEST-OS-4.66_201905-01~~ |6\. Juni 2019 |26. Juli 2019 |
|~~WA-GUEST-OS-4.65_201904-01~~ |7\. Mai 2019 |8\. Juli 2019 |
|~~WA-GUEST-OS-4.64_201903-01~~ |26. März 2019 |6\. Juni 2019 |
|~~WA-GUEST-OS-4.63_201902-01~~ |12. März 2019 |7\. Mai 2019 |
|~~WA-GUEST-OS-4.62_201901-01~~ |5\. Februar 2019 |26. März 2019 |
|~~WA-GUEST-OS-4.61_201812-01~~ |7\. Januar 2019 |12. März 2019 |
|~~WA-GUEST-OS-4.60_201811-01~~ |14. Dezember 2018 |5\. Februar 2019 |
|~~WA-GUEST-OS-4.59_201810-01~~ |8\. November 2018 |7\. Januar 2019 |
|~~WA-GUEST-OS-4.58_201809-01~~ |12. Oktober 2018 |14. Dezember 2018 |

## <a name="family-3-releases"></a>Releases von Familie 3
**Windows Server 2012**

Installierte .NET Framework-Versionen: 3.5, 4.5

| Konfigurationszeichenfolge | Veröffentlichungsdatum | Deaktivierungsdatum |
| --- | --- | --- |
|  WA-GUEST-OS-3.90_202010-02  |  17. November 2020  |  Nach 3.92  |  
|  WA-GUEST-OS-3.89_202009-01  |  10. Oktober 2020  |  Nach 3.91  |  
|~~WA-GUEST-OS-3.88_202008-02~~|  5\. September 2020  |  17. November 2020  |  
|~~WA-GUEST-OS-3.87_202007-01~~|  17. August 2020  |  10. Oktober 2020  |  
|~~WA-GUEST-OS-3.86_202006-02~~|  10. August 2020  |  5\. September 2020  |  
|~~WA-GUEST-OS-3.85_202005-02~~|  2\. Juni 2020  |  17. August 2020  |  
|~~WA-GUEST-OS-3.84_202004-01~~|  4\. Mai 2020  |  10. August 2020  |  
|~~WA-GUEST-OS-3.83_202003-01~~|  2\. April 2020  |  2\. Juni 2020  |  
|~~WA-GUEST-OS-3.82_202002-01~~|  5\. März 2020  |  4\. Mai 2020  |  
|~~WA-GUEST-OS-3.81_202001-01~~|  24. Januar 2020  |  2\. April 2020  |  
|~~WA-GUEST-OS-3.80_201912-01~~| 8\. Januar 2020 | 5\. März 2020 |  
|~~WA-GUEST-OS-3.79_201911-01~~| 5\. Dezember 2019 | 24. Januar 2020 |  
|~~WA-GUEST-OS-3.78_201910-01~~| 1\. November 2019 | 8\. Januar 2020 |  
|~~WA-GUEST-OS-3.77_201909-01~~| 7\. Oktober 2019 | 5\. Dezember 2019 |  
|~~WA-GUEST-OS-3.76_201908-01~~|  4\. August 2019  |  1\. November 2019  |  
|~~WA-GUEST-OS-3.75_201907-01~~| 26. Juli 2019 | 7\. Oktober 2019 |
|~~WA-GUEST-OS-3.74_201906-01~~| 8\. Juli 2019 |4\. August 2019 |
|~~WA-GUEST-OS-3.73_201905-01~~ |6\. Juni 2019 |26. Juli 2019 |
|~~WA-GUEST-OS-3.72_201904-01~~ |7\. Mai 2019 |8\. Juli 2019 |
|~~WA-GUEST-OS-3.71_201903-01~~ |26. März 2019 |6\. Juni 2019 |
|~~WA-GUEST-OS-3.70_201902-01~~ |12. März 2019 |7\. Mai 2019 |
|~~WA-GUEST-OS-3.69_201901-01~~ |5\. Februar 2019 |26. März 2019 |
|~~WA-GUEST-OS-3.68_201812-01~~ |7\. Januar 2019 |12. März 2019 |
|~~WA-GUEST-OS-3.67_201811-01~~ |14. Dezember 2018 |5\. Februar 2019 |
|~~WA-GUEST-OS-3.66_201810-01~~ |8\. November 2018 |7\. Januar 2019 |
|~~WA-GUEST-OS-3.65_201809-01~~ |12. Oktober 2018 |14. Dezember 2018 |

## <a name="family-2-releases"></a>Releases von Familie 2
**Windows Server 2008 R2 SP1**

Installierte .NET Framework-Versionen: 3.5 (einschließlich 2.0 und 3.0), 4.5

| Konfigurationszeichenfolge | Veröffentlichungsdatum | Deaktivierungsdatum |
| --- | --- | --- |
|  WA-GUEST-OS-2.103_202010-02  |  17. November 2020  |  Nach 2.105  |  
|  WA-GUEST-OS-2.102_202009-01  |  10. Oktober 2020  |  Nach 2.104  |  
|~~WA-GUEST-OS-2.101_202008-02~~|  5\. September 2020  |  17. November 2020 |    
|~~WA-GUEST-OS-2.100_202007-01~~|  17. August 2020  |  10. Oktober 2020  |  
|~~WA-GUEST-OS-2.99_202006-02~~|  10. August 2020  | 5\. September 2020  |  
|~~WA-GUEST-OS-2.98_202005-02~~|  2\. Juni 2020  |  17. August 2020  |  
|~~WA-GUEST-OS-2.97_202004-01~~|  4\. Mai 2020  |  10. August 2020  |  
|~~WA-GUEST-OS-2.96_202003-01~~|  2\. April 2020  |  2\. Juni 2020  |  
|~~WA-GUEST-OS-2.95_202002-01~~|  5\. März 2020  |  4\. Mai 2020  |  
|~~WA-GUEST-OS-2.94_202001-01~~|  24. Januar 2020  |  2\. April 2020  |  
|~~WA-GUEST-OS-2.93_201912-01~~| 8\. Januar 2020 | 5\. März 2020 |  
|~~WA-GUEST-OS-2.92_201911-01~~| 5\. Dezember 2019 | 24. Januar 2020 |  
|~~WA-GUEST-OS-2.91_201910-01~~| 1\. November 2019 | 8\. Januar 2020 |  
|~~WA-GUEST-OS-2.90_201909-01~~| 7\. Oktober 2019 | 5\. Dezember 2019 |  
|~~WA-GUEST-OS-2.89_201908-01~~| 4\. August 2019 | 1\. November 2019 |  
|~~WA-GUEST-OS-2.88_201907-01~~| 26. Juli 2019 | 7\. Oktober 2019 |
|~~WA-GUEST-OS-2.87_201906-01~~|8\. Juli 2019 | 4\. August 2019 |
|~~WA-GUEST-OS-2.86_201905-01~~ |6\. Juni 2019 |26. Juli 2019 |
|~~WA-GUEST-OS-2.85_201904-01~~ |7\. Mai 2019 |8\. Juli 2019 |
|~~WA-GUEST-OS-2.84_201903-01~~ |26. März 2019 |6\. Juni 2019 |
|~~WA-GUEST-OS-2.83_201902-01~~ |12. März 2019 |7\. Mai 2019 |
|~~WA-GUEST-OS-2.82_201901-01~~ |5\. Februar 2019 |26. März 2019 |
|~~WA-GUEST-OS-2.81_201812-01~~ |7\. Januar 2019 |12. März 2019 |
|~~WA-GUEST-OS-2.80_201811-01~~ |14. Dezember 2018 |5\. Februar 2019 |
|~~WA-GUEST-OS-2.79_201810-01~~ |8\. November 2018 |7\. Januar 2019 |
|~~WA-GUEST-OS-2.78_201809-01~~ |12. Oktober 2018 |14. Dezember 2018 |

## <a name="msrc-patch-updates"></a>MSRC-Patch-Updates
Die Liste der Patches, die in den einzelnen monatlichen Gastbetriebssystemreleases enthalten sind, steht [hier][patches] zur Verfügung.

## <a name="sdk-support"></a>SDK-Unterstützung
Obwohl die [Deaktivierungsrichtlinie für das Azure SDK][retire policy sdk] angibt, dass nur Versionen ab 2.2 unterstützt werden, können bei bestimmten Gastbetriebssystem-Familien frühere Versionen verwendet werden. Sie sollten immer das neueste unterstützte SDK verwenden.

| Gastbetriebssystemfamilie | Kompatible SDK-Versionen |
| --- | --- |
| 6 |Version 2.9.6+ |
| 5 |Version 2.9.5.1+ |
| 4 |Version 2.1+ |
| 3 |Version 1.8+ |
| 2 |Version 1.3+ |
| 1 |Version 1.0+ |

## <a name="guest-os-release-information"></a>Informationen zu den Gastbetriebssystemreleases
Es gibt drei wichtige Daten für Gastbetriebssystemreleases: **Freigabedatum**, **Deaktivierungsdatum** und **Ablaufdatum**. Ein Gast-BS wird als verfügbar betrachtet, wenn es im Portal verfügbar ist und als Zielgastbetriebssystem ausgewählt werden kann. Wenn das **Deaktivierungsdatum** für ein Gast-BS erreicht wird, wird es aus Azure entfernt. Jedoch funktioniert jeder Clouddienst, der auf dieses Gast-BS abzielt, wie gewohnt.

Das Fenster zwischen dem **Deaktivierungsdatum** und dem **Ablaufdatum** bietet Ihnen einen Puffer, um problemlos von einem Gast-BS zu einem neueren zu wechseln. Wenn Sie die Option *automatisch* für Ihr Gastbetriebssystem festgelegt haben, verwenden Sie immer die neueste Version. In diesem Fall müssen Sie sich keine Gedanken über das Ablaufdatum machen.

Wenn das **Ablaufdatum** überschritten ist, wird jeder Clouddienst, der dieses Gastbetriebssystem weiterhin verwendet, angehalten oder gelöscht bzw. es wird ein Upgrade erzwungen. [Hier][retirepolicy] erfahren Sie mehr über die Deaktivierungsrichtlinie.

## <a name="guest-os-family-version-explanation"></a>Erläuterung zu den Versionen der Gastbetriebssystemfamilie
Die Gastbetriebssystemfamilien basieren auf veröffentlichten Versionen von Microsoft Windows Server. Das Gastbetriebssystem ist das zugrunde liegende Betriebssystem, auf dem Azure Cloud Services ausgeführt wird. Jedes Gastbetriebssystem verfügt über eine Familien-, eine Versions- und eine Releasenummer.

* **Guest OS family**  
  entspricht einem Windows Server-Betriebssystemrelease, auf dem ein Gastbetriebssystem basiert. Die *Familie 3* basiert beispielsweise auf Windows Server 2012.
* **Gastbetriebssystem-Version**  
  Gilt für das Image einer Gastbetriebssystemfamilie sowie für relevante [Microsoft Security Response Center-Patches (MSRC)][msrc], die zum Zeitpunkt der Produktion der neuen Gastbetriebssystemversion verfügbar sind. Möglicherweise sind nicht alle Patches enthalten.

    Die Zahlen beginnen bei 0 und werden jedes Mal, wenn ein neuer Satz von Updates hinzugefügt wird, um 1 erhöht. Nachfolgende Nullen werden nur angezeigt, wenn sie von Bedeutung sind. Version 2.10 ist beispielsweise eine andere, viel höhere Version als Version 2.1.
* **Gastbetriebssystemrelease**  
  Eine Neuveröffentlichung einer Gastbetriebssystemversion. Eine Neuveröffentlichung kommt vor, wenn Microsoft beim Testen Probleme feststellt, die Änderungen erfordern. Das neueste Release ersetzt immer alle vorherigen Releases, unabhängig davon, ob diese veröffentlicht wurden oder nicht. Das Azure-Portal lässt Benutzer nur das neueste Release für eine bestimmte Version auswählen. Bereitstellungen, die auf einem früheren Release ausgeführt werden, werden normalerweise nicht zwangsweise aktualisiert. Dies ist abhängig vom Schweregrad des Fehlers.

Im folgenden Beispiel steht 2 für die Familie, 12 für die Version, und "rel2" für das Release.

**Gastbetriebssystemrelease** – 2.12 rel2

**Konfigurationszeichenfolge für dieses Release** – WA-GUEST-OS-2.12_201208-02

In der Konfigurationszeichenfolge für ein Gastbetriebssystem sind dieselben Informationen eingebettet sowie ein Datum, das zeigt, welche MSRC-Patches in diesem Release berücksichtigt wurden. In diesem Beispiel wurden MSRC-Patches für Windows Server 2008 R2 bis einschließlich August 2012 integriert. Es werden nur Patches einbezogen, die ausdrücklich für diese Version von Windows Server gelten. Wenn beispielsweise ein MSRC-Patch für Microsoft Office gilt, wird er nicht berücksichtigt, da dieses Produkt kein Bestandteil des Windows Server-Basisimage ist.

## <a name="guest-os-system-update-process"></a>Updateprozess des Gastbetriebssystems
Diese Seite enthält Informationen zu anstehenden Gastbetriebssystemreleases. Kunden haben uns mitgeteilt, dass sie wissen möchten, wann ein Release stattfindet, da ihre Clouddienstrollen neu gestartet werden, wenn sie auf "Automatisches Update" festgelegt sind. Gastbetriebssystemreleases werden in der Regel mindestens zwei bis drei Wochen nach dem MSRC-Updaterelease veröffentlicht, das am zweiten Dienstag jedes Monats stattfindet. Neue Releases enthalten alle relevanten MSRC-Patches für jede Gastbetriebssystemfamilie.

Für Microsoft Azure werden ständig Updates veröffentlicht. Das Gastbetriebssystem ist nur ein solches Update in der Pipeline. Ein Release kann durch viele Faktoren beeinflusst werden, die zu zahlreich sind, um hier aufgeführt zu werden. Darüber hinaus wird Azure auf Hunderttausenden von Computern ausgeführt. Daher ist es unmöglich, ein genaues Datum und eine Uhrzeit anzugeben, zu der Ihre Rollen neu gestartet werden. Wir arbeiten an einem Plan zur Begrenzung oder zeitlichen Planung von Neustarts.

Wenn eine neue Version des Gastbetriebssystems veröffentlicht wird, kann die vollständige Verteilung über Azure einige Zeit in Anspruch nehmen. Während Dienste auf das neue Gastbetriebssystem aktualisiert werden, werden sie den Updatedomänen entsprechend neu gestartet. Dienste, für die automatische Updates festgelegt wurden, erhalten zuerst ein Release. Nach dem Update wird die neue Gastbetriebssystemversion für Ihren Dienst im Azure-Portal aufgeführt. Erneute Releases können während dieses Zeitraums auftreten. Einige Versionen können über einen längeren Zeitraum bereitgestellt werden, und es finden möglicherweise für viele Wochen nach dem offiziellen Veröffentlichungsdatum keine automatischen Upgradeneustarts statt. Sobald ein Gastbetriebssystem verfügbar ist, können Sie diese Version explizit im Portal oder in der Konfigurationsdatei auswählen.

Viele wertvolle Informationen zu Neustarts und Verweise auf weitere technische Informationen zu Gast- und Hostbetriebssystemupdates finden Sie im MSDN-Blogbeitrag [Role Instance Restarts Due to OS Upgrades][restarts] (Neustarts von Rolleninstanzen aufgrund von Betriebssystemupgrades).

Wenn Sie Ihr Gastbetriebssystem manuell aktualisieren, finden Sie weitere Informationen in der [Deaktivierungsrichtlinie für Gastbetriebssysteme][retirepolicy].

## <a name="guest-os-supportability-and-retirement-policy"></a>Unterstützungs- und Deaktivierungsrichtlinie für Gastbetriebssysteme
Die Unterstützungs- und Deaktivierungsrichtlinie für Gastbetriebssysteme wird [hier][retirepolicy] erläutert.

[cloud updates]: ./cloud-services-update-azure-service.md
[RSS-Feed zu Gastbetriebssystemupdates]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: ./cloud-services-dotnet-install-dotnet.md?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: /security-updates/SecurityAdvisories/2015/3009008
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: /security-updates/SecurityBulletins/2014/ms14-066
[MS14-046]: /security-updates/SecurityBulletins/2014/ms14-046
[retire policy sdk]: /previous-versions/azure/reference/dn479282(v=azure.100)
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: /archive/blogs/kwill/role-instance-restarts-due-to-os-upgrades
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: /security-updates/SecurityBulletins/2017/ms17-010
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
[Weitere Informationen]: ./applications-dont-support-tls-1-2.md
