---
title: Datensammlung in Azure Security Center | Microsoft-Dokumentation
description: " Hier erfahren Sie, wie Sie die Datensammlung in Azure Security Center aktivieren. "
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: 411d7bae-c9d4-4e83-be63-9f2f2312b075
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/02/2019
ms.author: v-mohabe
ms.openlocfilehash: 8fed3ce98b23c5ac1cc97b88a278c5946f06af8e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968707"
---
# <a name="data-collection-in-azure-security-center"></a>Datensammlung in Azure Security Center
Security Center sammelt Daten von Ihren virtuellen Azure-Computern (VMs), VM-Skalierungsgruppen, IaaS-Containern und Azure-fremden Computern (auch lokal), um sie auf Sicherheitslücken und Bedrohungen zu überwachen. Die Daten werden mithilfe von Microsoft Monitoring Agent gesammelt. Der Agent liest verschiedene sicherheitsrelevante Konfigurationen und Ereignisprotokolle auf dem Computer und kopiert die Daten zur Analyse in Ihren Arbeitsbereich. Beispiele für Daten dieser Art: Betriebssystemtyp und -version, Betriebssystemprotokolle (Windows-Ereignisprotokolle), ausgeführte Prozesse, Computername, IP-Adressen und angemeldeter Benutzer. Darüber hinaus kopiert der Microsoft Monitoring Agent Absturzabbilddateien in Ihren Arbeitsbereich.

Die Datensammlung ist erforderlich, um einen Einblick in fehlende Updates, falsch konfigurierte Sicherheitseinstellungen des Betriebssystems, Aktivierung des Endpunktschutzes sowie Integrität und Bedrohungserkennung bereitzustellen. 

Dieser Artikel enthält Anleitungen zum Installieren von Microsoft Monitoring Agent und zum Festlegen eines Log Analytics-Arbeitsbereichs, in dem die gesammelten Daten gespeichert werden. Beide Vorgänge sind erforderlich, um die Datensammlung aktivieren. 

> [!NOTE]
> - Die Datensammlung ist nur für Computeressourcen (virtuelle Computer, VM-Skalierungsgruppen, IaaS-Container und Azure-fremde Computer) erforderlich. Sie können auch von Azure Security Center profitieren, wenn Sie keine Agents bereitstellen. Allerdings ist dann die Sicherheit eingeschränkt, und die oben aufgeführten Funktionen werden nicht unterstützt.  
> - Die Liste der unterstützten Plattformen finden Sie unter [Unterstützte Plattformen in Azure Security Center](security-center-os-coverage.md).
> - Für VM-Skalierungsgruppen wird die Datensammlung derzeit nicht unterstützt.
> - Ganz gleich, ob Sie einen neuen oder vorhandenen Arbeitsbereich verwenden, fallen für das Speichern von Daten in Log Analytics möglicherweise zusätzliche Gebühren für die Datenspeicherung an. Details finden Sie in der Preisübersicht.

## Aktivieren der automatischen Bereitstellung von Microsoft Monitoring Agent <a name="auto-provision-mma"></a>

Zum Erfassen der Daten von den Computern müssen Sie Microsoft Monitoring Agent installiert haben.  Die Installation des Agents kann automatisch (empfohlen) oder manuell erfolgen.  

>[!NOTE]
> Die automatische Bereitstellung ist standardmäßig deaktiviert. Um die Installation der standardmäßigen automatischen Bereitstellung in Security Center festzulegen, legen Sie die Option auf **Ein** fest.
>

Bei aktivierter automatischer Bereitstellung wird Microsoft Monitoring Agent von Security Center auf allen unterstützten virtuellen Azure-Computern sowie auf allen neu erstellten virtuellen Computern bereitgestellt. Die automatische Bereitstellung wird dringend empfohlen, die manuelle Agent-Installation ist jedoch ebenfalls verfügbar. [Erfahren Sie, wie Sie die Microsoft Monitoring Agent-Erweiterung installieren.](#manualagent)



So aktivieren Sie die automatische Bereitstellung von Microsoft Monitoring Agent:
1. Klicken Sie im Hauptmenü von Security Center auf **Sicherheitsrichtlinie**.
2. Klicken Sie in der Spalte „Einstellungen“ für das gewünschte Abonnement in der Liste auf **Einstellungen bearbeiten**.

   ![Wählen Sie das Abonnement aus.][7]

3. Klicken Sie unter **Sicherheitsrichtlinie** auf **Datensammlung**.
4. Wählen Sie unter **Automatische Bereitstellung** die Option **Ein** aus, um die automatische Bereitstellung zu aktivieren.
5. Wählen Sie **Speichern** aus.

   ![Aktivieren der automatischen Bereitstellung][1]

>[!NOTE]
> - Anweisungen zum Bereitstellen einer bereits vorhandenen Installation finden Sie unter [Automatische Bereitstellung bei einer bereits vorhandenen Agent-Installation](#preexisting).
> - Anweisungen zur manuellen Bereitstellung finden Sie unter [Manuelles Installieren der Microsoft Monitoring Agent-Erweiterung](#manualagent).
> - Anweisungen zum Deaktivieren der automatischen Bereitstellung, finden Sie unter [Deaktivieren der automatischen Bereitstellung](#offprovisioning).
> - Anweisungen für das Onboarding von Security Center mit PowerShell finden Sie unter [Automatisieren des Onboardings von Azure Security Center mit PowerShell](security-center-powershell-onboarding.md).
>

## <a name="workspace-configuration"></a>Arbeitsbereichskonfiguration
Von Security Center gesammelte Daten werden in Log Analytics-Arbeitsbereichen gespeichert.  Sie können festlegen, ob Daten von virtuellen Azure-Computern in von Security Center erstellten Arbeitsbereichen oder in einem vorhandenen Arbeitsbereich gespeichert werden sollen, den Sie selbst erstellt haben. 

Die Konfiguration von Arbeitsbereichen wird abonnementbasiert festgelegt, und der gleiche Arbeitsbereich kann für mehrere Abonnements verwendet werden.

### <a name="using-a-workspace-created-by-security-center"></a>Verwenden eines von Security Center erstellten Arbeitsbereichs

Security Center kann automatisch einen Standardarbeitsbereich zum Speichern der Daten erstellen. 

Auswählen eines von Security Center erstellten Arbeitsbereichs:

1. Wählen Sie unter **Standardmäßige Arbeitsbereichskonfiguration** die Option „Von Security Center erstellte Arbeitsbereiche verwenden“ aus.
   ![Tarif auswählen][10] 

1. Klicken Sie auf **Speichern**.<br>
    Security Center erstellt eine neue Ressourcengruppe und einen Standardarbeitsbereich an diesem Standort und verbindet den Agent mit diesem Arbeitsbereich. Für den Arbeitsbereich und die Ressourcengruppe gilt folgende Namenskonvention:<br>
   **Arbeitsbereich: DefaultWorkspace-[Abonnement-ID]-[Region]<br> Ressourcengruppe: DefaultResourceGroup-[Region]**

   Wenn ein Abonnement VMs aus mehreren Geolocations enthält, erstellt Security Center mehrere Arbeitsbereiche. Mehrere Arbeitsbereiche werden erstellt, um Datenschutzregeln zu verwalten.
1. Security Center aktiviert gemäß dem für das Abonnement festgelegten Tarif automatisch eine Security Center-Lösung im Arbeitsbereich. 

> [!NOTE]
> Der Log Analytics-Tarif von Arbeitsbereichen, die vom Security Center erstellt wurden, hat keine Auswirkungen auf die Security Center-Abrechnung. Die Abrechnung von Security Center basiert immer auf Ihrer Security Center-Sicherheitsrichtlinie und den installierten Lösungen in einem Arbeitsbereich. Für den Free-Tarif aktiviert Security Center die Lösung *SecurityCenterFree* im Standardarbeitsbereich. Für den Standard-Tarif aktiviert Security Center aktiviert die Lösung *Security* im Standardarbeitsbereich.
> Für das Speichern von Daten in Log Analytics fallen möglicherweise zusätzliche Gebühren für die Datenspeicherung an. Details finden Sie in der Preisübersicht.

Weitere Informationen zu Preisen finden Sie unter [Security Center – Preise](https://azure.microsoft.com/pricing/details/security-center/).

Weitere Informationen zu vorhandenen Log Analytics-Konten finden Sie unter [Log Analytics-Bestandskunden](security-center-faq.md#existingloganalyticscust).

### <a name="using-an-existing-workspace"></a>Verwenden eines vorhandenen Arbeitsbereichs

Wenn Sie bereits über einen vorhandenen Log Analytics-Arbeitsbereich verfügen, können Sie diesen verwenden.

Zum Verwenden Ihres vorhandenen Log Analytics-Arbeitsbereichs benötigen Sie Lese- und Schreibberechtigungen für den Arbeitsbereich.

> [!NOTE]
> Im vorhandenen Arbeitsbereich aktivierte Lösungen gelten für virtuelle Azure-Computer, die damit verbunden sind. Bei gebührenpflichtigen Lösungen kann dies mit zusätzlichen Kosten verbunden sein. Stellen Sie aus Datenschutzgründen sicher, dass sich der ausgewählte Arbeitsbereich in der richtigen geografischen Region befindet.
> Für das Speichern von Daten in Log Analytics fallen möglicherweise zusätzliche Gebühren für die Datenspeicherung an. Details finden Sie in der Preisübersicht.

So wählen Sie einen vorhandenen Log Analytics-Arbeitsbereich aus:

1. Wählen Sie unter **Standardmäßige Arbeitsbereichskonfiguration** die Option **Use another workspace** (Anderen Arbeitsbereich verwenden) aus.

   ![Auswählen eines vorhandenen Arbeitsbereichs][2]

2. Wählen Sie im Pulldownmenü einen Arbeitsbereich zum Speichern der gesammelten Daten aus.

   > [!NOTE]
   > Im Pulldownmenü sind alle Arbeitsbereiche in allen Ihren Abonnements verfügbar. Weitere Informationen finden Sie unter [Abonnementübergreifende Arbeitsbereichsauswahl](security-center-enable-data-collection.md#cross-subscription-workspace-selection). Sie benötigen die Berechtigung zum Zugriff auf den Arbeitsbereich.
   >
   >

3. Wählen Sie **Speichern** aus.
4. Nach der Auswahl von **Speichern** werden Sie gefragt, ob Sie überwachte virtuelle Computer, die zuvor mit einem Standardarbeitsbereich verbunden waren, erneut konfigurieren möchten.

   - Klicken Sie auf **Nein**, wenn die neuen Arbeitsbereichseinstellungen nur auf neue virtuelle Computer angewendet werden sollen. Die neuen Arbeitsbereichseinstellungen gelten nur für neue Agent-Installationen (neu ermittelte virtuelle Computer, auf denen Microsoft Monitoring Agent nicht installiert ist).
   - Klicken Sie auf **Ja**, wenn die neuen Arbeitsbereichseinstellungen auf alle virtuellen Computer angewendet werden sollen. Darüber hinaus wird jeder virtuelle Computer, der mit einem von Security Center erstellten Arbeitsbereich verbunden ist, nun mit dem neuen Zielarbeitsbereich verbunden.

   > [!NOTE]
   > Wenn Sie auf „Ja“ klicken, dürfen die von Security Center erstellten Arbeitsbereiche erst gelöscht werden, wenn alle virtuellen Computer mit dem neuen Zielarbeitsbereich verbunden sind. Dieser Vorgang ist nicht erfolgreich, wenn ein Arbeitsbereich zu früh gelöscht wird.
   >
   >

   - Klicken Sie auf **Abbrechen**, um den Vorgang abzubrechen.

     ![Auswählen eines vorhandenen Arbeitsbereichs][3]

5. Wählen Sie den Tarif für den gewünschten Arbeitsbereich aus, mit dem Sie Microsoft Monitoring Agent festlegen möchten. <br>Zur Verwendung eines vorhandenen Arbeitsbereichs legen Sie den Tarif für den Arbeitsbereich fest. Dadurch wird im Arbeitsbereich eine Security Center-Lösung installiert, falls noch keine vorhanden ist.

    a.  Wählen Sie im Hauptmenü von Security Center die Option **Sicherheitsrichtlinie** aus.
     
    b.  Wählen Sie den Arbeitsbereich aus, in dem Sie die Agent-Verbindung herstellen möchten. Klicken Sie hierzu in der Spalte „Einstellungen“ für das gewünschte Abonnement in der Liste auf **Einstellungen bearbeiten**.
        ![Arbeitsbereich auswählen][8] c. Legen Sie den Tarif fest.
        ![Tarif auswählen][9] 
   
   >[!NOTE]
   >Wenn für den Arbeitsbereich bereits eine **Security**- oder **SecurityCenterFree**-Lösung aktiviert ist, werden die Preise automatisch festgelegt. 

## <a name="cross-subscription-workspace-selection"></a>Abonnementübergreifende Arbeitsbereichsauswahl
Wenn Sie einen Arbeitsbereich zum Speichern Ihrer Daten auswählen, sind alle Arbeitsbereiche in allen Ihren Abonnements verfügbar. Über die abonnementübergreifende Arbeitsbereichsauswahl können Sie Daten von virtuellen Computern erfassen, die in verschiedenen Abonnements ausgeführt werden, und im Arbeitsbereich Ihrer Wahl speichern. Diese Auswahl wird empfohlen, wenn Sie einen zentralen Arbeitsbereich in Ihrer Organisation verwenden und diesen für die Sammlung von Sicherheitsdaten verwenden möchten. Weitere Informationen zum Verwalten von Arbeitsbereichen finden Sie unter [Verwalten von Arbeitsbereichen](https://docs.microsoft.com/azure/log-analytics/log-analytics-manage-access).


## <a name="data-collection-tier"></a>Datensammlungsebene
Das Auswählen einer Datensammlungsebene in Azure Security Center hat nur Auswirkung auf die Speicherung von Sicherheitsereignissen in Ihrem Log Analytics-Arbeitsbereich. Der Log Analytics-Agent sammelt und analysiert weiterhin die Sicherheitsereignisse, die für die Bedrohungserkennung von Azure Security Center erforderlich sind, unabhängig davon, auf welcher Ebene Sicherheitsereignisse ggf. in Ihrem Log Analytics-Arbeitsbereich gespeichert werden sollen. Wenn Sie Sicherheitsereignisse in Ihrem Arbeitsbereich speichern, können Sie diese Ereignisse in Ihrem Arbeitsbereich untersuchen, durchsuchen und überprüfen. 
> [!NOTE]
> Für das Speichern von Daten in Log Analytics fallen möglicherweise zusätzliche Gebühren für die Datenspeicherung an. Details finden Sie in der Preisübersicht.
> 
> Sie können die passende Filterrichtlinie für Ihre Abonnements und Arbeitsbereiche auswählen. Dabei haben Sie die Wahl zwischen vier Gruppen von Ereignissen, die in Ihrem Arbeitsbereich gespeichert werden sollen: 

- **Keine:** deaktiviert die Speicherung von Sicherheitsereignissen. Dies ist die Standardeinstellung.
- **Minimal:** Eine kleinere Gruppe von Ereignissen für Kunden, die das Ereignisvolumen minimieren möchten.
- **Allgemein:** eine Gruppe von Ereignissen, die die Anforderungen der meisten Kunden erfüllt und einen vollständigen Überwachungspfad bietet.
- **Alle Ereignisse:** für Kunden, die alle Ereignisse speichern möchten.


> [!NOTE]
> Diese Sicherheitsereignisgruppen sind nur im Standard-Tarif von Security Center verfügbar. Weitere Informationen zu den Tarifen von Security Center finden Sie unter [Preise](security-center-pricing.md).
Diese Gruppen wurden für typische Szenarien konzipiert. Überlegen Sie sich vor der Implementierung, welche Ihre Anforderungen erfüllt.
>
>

Bei der Bestimmung der Ereignisse für die Gruppen **Allgemein** und **Minimal** haben wir uns mit Kunden und Branchenstandards beschäftigt, um uns über die ungefilterte Häufigkeit der einzelnen Ereignisse und deren Verwendung zu informieren. Dabei haben wir uns an den folgenden Richtlinien orientiert:

- **Minimal:** Diese Gruppe deckt nur Ereignisse ab, die auf eine erfolgreiche Sicherheitsverletzung hindeuten, sowie wichtige Ereignisse, die nur sehr selten auftreten. So deckt diese Gruppe beispielsweise erfolgreiche und nicht erfolgreiche Benutzeranmeldungen (Ereignis-IDs 4624 und 4625), aber keine Abmeldungen ab. Diese sind zwar für die Überwachung wichtig, nicht aber für die Erkennung, und sie treten zudem relativ häufig auf. Der Großteil des Datenvolumens dieser Gruppe ist auf Anmeldeereignisse und auf das Prozesserstellungsereignis (Ereignis-ID 4688) zurückzuführen.
- **Allgemein:** Diese Gruppe bietet einen vollständigen Benutzerüberwachungspfad. So enthält die Gruppe beispielsweise sowohl Benutzeranmeldungen als auch Benutzerabmeldungen (Ereignis-ID 4634). Wir schließen Überwachungsaktionen wie Sicherheitsgruppenänderungen, wichtige Domänencontroller-Kerberos-Vorgänge und andere Ereignisse ein, die von Branchenorganisationen empfohlen werden.

Ereignisse mit sehr geringem Volumen wurden in die Gruppe „Allgemein“ aufgenommen, da das Hauptargument für ihre Verwendung (im Vergleich zu allen anderen Ereignissen) in der Verringerung des Volumens besteht (und nicht darin, bestimmte Ereignisse herauszufiltern).

Im Anschluss finden Sie eine vollständige Aufschlüsselung der Sicherheits- und AppLocker-Ereignis-IDs für die einzelnen Gruppen:

| Datenschicht | Indikatoren für gesammelte Ereignisse |
| --- | --- |
| Wenig | 1102,4624,4625,4657,4663,4688,4700,4702,4719,4720,4722,4723,4724,4727,4728,4732,4735,4737,4739,4740,4754,4755, |
| | 4756,4767,4799,4825,4946,4948,4956,5024,5033,8001,8002,8003,8004,8005,8006,8007,8222 |
| Common | 1,299,300,324,340,403,404,410,411,412,413,431,500,501,1100,1102,1107,1108,4608,4610,4611,4614,4622, |
| |  4624,4625,4634,4647,4648,4649,4657,4661,4662,4663,4665,4666,4667,4688,4670,4672,4673,4674,4675,4689,4697, |
| | 4700,4702,4704,4705,4716,4717,4718,4719,4720,4722,4723,4724,4725,4726,4727,4728,4729,4733,4732,4735,4737, |
| | 4738,4739,4740,4742,4744,4745,4746,4750,4751,4752,4754,4755,4756,4757,4760,4761,4762,4764,4767,4768,4771, |
| | 4774,4778,4779,4781,4793,4797,4798,4799,4800,4801,4802,4803,4825,4826,4870,4886,4887,4888,4893,4898,4902, |
| | 4904,4905,4907,4931,4932,4933,4946,4948,4956,4985,5024,5033,5059,5136,5137,5140,5145,5632,6144,6145,6272, |
| | 6273,6278,6416,6423,6424,8001,8002,8003,8004,8005,8006,8007,8222,26401,30004 |

> [!NOTE]
> - Wenn Sie ein Gruppenrichtlinienobjekt (Group Policy Object, GPO) verwenden, wird empfohlen, das Überwachungsrichtlinien-Prozesserstellungsereignis 4688 und das Feld *CommandLine* im Ereignis 4688 zu aktivieren. Weitere Informationen zum Prozesserstellungsereignis 4688 finden Sie in den [häufig gestellten Fragen](security-center-faq.md#what-happens-when-data-collection-is-enabled) zu Security Center. Weitere Informationen zu diesen Überwachungsrichtlinien finden Sie unter [Empfehlungen zur Überwachungsrichtlinie](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations).
> -  Um die Datensammlung für [Adaptive Anwendungssteuerungen](security-center-adaptive-application.md) zu aktivieren, konfiguriert Security Center eine lokale AppLocker-Richtlinie im Überwachungsmodus, um alle Anwendungen zuzulassen. Damit generiert AppLocker Ereignisse, die dann von Security Center gesammelt und genutzt werden können. Es ist wichtig zu beachten, dass diese Richtlinie nicht auf Computern konfiguriert wird, auf denen bereits eine AppLocker-Richtlinie konfiguriert ist. 
> - Zum Erfassen von [Ereignis-ID 5156](https://www.ultimatewindowssecurity.com/securitylog/encyclopedia/event.aspx?eventID=5156) der Windows-Filterplattform müssen Sie [Filterplattformverbindung überwachen](https://docs.microsoft.com/windows/security/threat-protection/auditing/audit-filtering-platform-connection) aktivieren (Auditpol /set /subcategory:"Filtering Platform Connection" /Success:Enable).
>

So wählen Sie Ihre Filterrichtlinie:
1. Wählen Sie auf dem Blatt **Sicherheitsrichtlinie – Datensammlung** unter **Sicherheitsereignisse** Ihre Filterrichtlinie aus.
2. Wählen Sie **Speichern** aus.

   ![Auswählen der Filterrichtlinie][5]

### Automatische Bereitstellung bei einer bereits vorhandenen Agent-Installation <a name="preexisting"></a> 

Die folgenden Anwendungsfälle geben an, wie die automatische Bereitstellung funktioniert, wenn bereits ein Agent oder eine Erweiterung installiert ist. 

- Microsoft Monitoring Agent ist auf dem Computer installiert, jedoch nicht als Erweiterung (direkter Agent).<br>
Wenn Microsoft Monitoring Agent direkt auf dem virtuellen Computer (also nicht als Azure-Erweiterung) installiert ist, installiert Security Center die Microsoft Monitoring Agent-Erweiterung und aktualisiert diesen ggf. auf die neueste Version.
Der installierte Agent erstellt seine Berichte weiterhin für die bereits konfigurierten Arbeitsbereiche und darüber hinaus für den in Security Center konfigurierten Arbeitsbereich (Multihosting wird unterstützt).
Wenn der konfigurierte Arbeitsbereich ein Benutzerarbeitsbereich ist (nicht der Standardarbeitsbereich von Security Center), müssen Sie darin die Lösung „security“ oder „securityFree“ installieren, damit Security Center mit der Verarbeitung von Ereignissen von VMs und Computern, die ihre Berichte in diesem Arbeitsbereich erstellen, beginnen kann.<br>
<br>
Wenn für vorhandene Computer in Abonnements, die vor dem 17.03.2019 in Security Center integriert wurden, ein vorhandener Agent erkannt wurde, wird die Microsoft Monitoring Agent-Erweiterung nicht installiert, und der Computer ist nicht betroffen. Für diese Computer wird die Empfehlung „Monitoring Agent-Integritätsprobleme auf Ihren Computern beheben“ angezeigt, damit Sie die Installationsprobleme des Agents auf diesen Computern beheben können.

  
- Der System Center Operations Manager-Agent ist auf dem Computer installiert.<br>
Security Center installiert die Microsoft Monitoring Agent-Erweiterung zusätzlich zum vorhandenen Operations Manager. Der vorhandene Operations Manager-Agent sendet weiterhin normal Berichte an den Operations Manager-Server. Beachten Sie, dass die vom Operations Manager-Agent und dem Microsoft Monitoring Agent gemeinsam genutzten Laufzeitbibliotheken bei diesem Vorgang auf die neueste Version aktualisiert werden.
Hinweis: Wenn Operations Manager-Agent Version 2012 installiert ist, aktivieren Sie die automatische Bereitstellung **nicht**.<br>

- Eine VM-Erweiterung ist bereits vorhanden.<br>
    - Wenn der Monitoring Agent als Erweiterung installiert ist, erlaubt die Konfiguration der Erweiterung auch Berichte an nur einen einzelnen Arbeitsbereich. Bereits vorhandene Verbindungen mit Benutzerarbeitsbereichen werden von Security Center nicht überschrieben. Security Center speichert Sicherheitsdaten des virtuellen Computers im bereits verbundenen Arbeitsbereich, sofern darin die Lösung „security“ oder „securityFree“ installiert wurde. Security Center kann die Version der Erweiterung während dieses Vorgangs auf die neueste Version aktualisieren.  
    - Um festzustellen, an welchen Arbeitsbereich die vorhandene Erweiterung Daten sendet, führen Sie den Test zum [Überprüfen der Konnektivität mit Azure Security Center](https://blogs.technet.microsoft.com/yuridiogenes/2017/10/13/validating-connectivity-with-azure-security-center/) aus. Alternativ können Sie Log Analytics-Arbeitsbereiche öffnen, einen Arbeitsbereich und den virtuellen Computer auswählen und sich die Log Analytics-Agent-Verbindung ansehen. 
    - Wenn Sie über eine Umgebung verfügen, in der der Log Analytics-Agent auf Clientarbeitsstationen installiert ist und an einen vorhandenen Log Analytics-Arbeitsbereich berichtet, überprüfen Sie die Liste der [von Azure Security Center unterstützten Betriebssysteme](security-center-os-coverage.md), um sicherzustellen, dass Ihr Betriebssystem unterstützt wird. Weitere Informationen finden Sie unter [Log Analytics-Bestandskunden](security-center-faq.md#existingloganalyticscust).
 
### Deaktivieren der automatischen Bereitstellung <a name="offprovisioning"></a>
Sie können die automatische Bereitstellung in Ressourcen jederzeit deaktivieren, indem Sie diese Einstellung in der Sicherheitsrichtlinie deaktivieren. 


1. Kehren Sie zum Hauptmenü von Security Center zurück, und klicken Sie auf die Sicherheitsrichtlinie.
2. Klicken Sie in der Zeile des Abonnements, für das Sie die automatische Bereitstellung deaktivieren möchten, **Einstellungen bearbeiten>** aus.
3. Wählen Sie auf dem Blatt **Sicherheitsrichtlinie – Datensammlung** unter **Automatische Bereitstellung** die Option **Aus** aus.
4. Wählen Sie **Speichern** aus.

   ![Deaktivieren der automatischen Bereitstellung][6]

Wenn die automatische Bereitstellung deaktiviert ist, wird der Abschnitt mit der standardmäßigen Arbeitsbereichskonfiguration nicht angezeigt.

Wenn Sie die automatische Bereitstellung deaktivieren, nachdem sie aktiviert war:
-   werden Agents auf neuen virtuellen Computern nicht bereitgestellt.
-   beendet Security Center die Sammlung von Daten im Standardarbeitsbereich.
 
> [!NOTE]
>  Wenn Sie die automatische Bereitstellung deaktivieren, wird Microsoft Monitoring Agent nicht von virtuellen Azure-Computern entfernt, auf denen der Agent bereitgestellt wurde. Informationen zum Entfernen der OMS-Erweiterungs finden Sie unter [Wie entferne ich durch Security Center installierte OMS-Erweiterungen?](security-center-faq.md#remove-oms).
>
    
## Manuelle Agent-Bereitstellung <a name="manualagent"></a>
 
Es gibt mehrere Möglichkeiten, Microsoft Monitoring Agent manuell zu installieren. Achten Sie bei der manuellen Installation darauf, die automatische Bereitstellung zu deaktivieren.

### <a name="operations-management-suite-vm-extension-deployment"></a>Bereitstellung der Operations Management Suite-Erweiterung für virtuelle Computer 

Damit Security Center Sicherheitsdaten auf Ihren virtuellen Computern erfassen und Empfehlungen und Warnungen bereitstellen kann, können Sie Microsoft Monitoring Agent manuell installieren.
1. Wählen Sie für die automatische Bereitstellung „AUS“.
2. Erstellen Sie einen Arbeitsbereich, und legen Sie den Tarif für den Arbeitsbereich fest, mit dem Sie Microsoft Monitoring Agent festlegen möchten:

   a.  Wählen Sie im Hauptmenü von Security Center die Option **Sicherheitsrichtlinie** aus.
     
   b.  Wählen Sie den Arbeitsbereich aus, in dem Sie den Agent verbinden möchten. Stellen Sie sicher, dass sich der Arbeitsbereich im gleichen Abonnement befindet, das Sie in Security Center verwenden, und dass Sie über Lese-/Schreibberechtigungen für den Arbeitsbereich verfügen.
       ![Arbeitsbereich auswählen][8]
3. Legen Sie den Tarif fest.
   ![Tarif auswählen][9] 
   >[!NOTE]
   >Wenn für den Arbeitsbereich bereits eine **Security**- oder **SecurityCenterFree**-Lösung aktiviert ist, werden die Preise automatisch festgelegt. 
   > 

4. Wenn Sie die Agents auf neuen virtuellen Computern mithilfe der Resource Manager-Vorlage bereitstellen möchten, installieren Sie die OMS-Erweiterung für virtuelle Computer:

   a.  [Installieren der OMS-Azure-VM-Erweiterung für Windows](../virtual-machines/extensions/oms-windows.md)
    
   b.  [Installieren der OMS-Azure-VM-Erweiterung für Linux](../virtual-machines/extensions/oms-linux.md)
5. Folgen Sie den Anweisungen in [Sammeln von Daten über virtuelle Azure-Computer](../azure-monitor/learn/quick-collect-azurevm.md), um die Erweiterungen auf vorhandenen virtuellen Computern bereitzustellen.

   > [!NOTE]
   > Der Abschnitt **Sammeln von Ereignis- und Leistungsdaten** ist optional.
   >
6. Wie Sie die Erweiterung mithilfe von PowerShell bereitstellen, können Sie dem folgenden PowerShell-Beispiel entnehmen:
   
   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
   
   1. Rufen Sie **Log Analytics** auf, und klicken Sie auf **Erweiterte Einstellungen**.
    
      ![Festlegen der Protokollanalyse][11]

   2. Kopieren Sie die Werte aus **WorkspaceID** und **Primärschlüssel**.
  
      ![Kopieren von Werten][12]

   3. Füllen Sie die öffentliche Konfiguration und die private Konfiguration mit diesen Werten aus:
     
           $PublicConf = '{
               "workspaceId": "WorkspaceID value"
           }' 
 
           $PrivateConf = '{
               "workspaceKey": "<Primary key value>”
           }' 

      - Bei der Installation auf einem virtuellen Windows-Computer:
        
            Set-AzVMExtension -ResourceGroupName $vm.ResourceGroupName -VMName $vm.Name -Name "MicrosoftMonitoringAgent" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "MicrosoftMonitoringAgent" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True 
    
      - Bei der Installation auf einem virtuellen Linux-Computer:
        
            Set-AzVMExtension -ResourceGroupName $vm1.ResourceGroupName -VMName $vm1.Name -Name "OmsAgentForLinux" -Publisher "Microsoft.EnterpriseCloud.Monitoring" -ExtensionType "OmsAgentForLinux" -TypeHandlerVersion '1.0' -Location $vm.Location -Settingstring $PublicConf -ProtectedSettingString $PrivateConf -ForceRerun True`

> [!NOTE]
> Anweisungen für das Onboarding von Security Center mit PowerShell finden Sie unter [Automatisieren des Onboardings von Azure Security Center mit PowerShell](security-center-powershell-onboarding.md).

## <a name="troubleshooting"></a>Problembehandlung

-   Informationen zum Identifizieren von Problemen bei der automatischen Bereitstellung finden Sie unter [Integritätsprobleme des Überwachungs-Agents](security-center-troubleshooting-guide.md#mon-agent).

-  Informationen zum Identifizieren von Netzwerkanforderungen für den Überwachungs-Agent finden Sie unter [Beheben von Problemen mit den Netzwerkanforderungen für den Überwachungs-Agent](security-center-troubleshooting-guide.md#mon-network-req).
-   Informationen zum Identifizieren von Problemen beim manuellen Onboarding finden Sie unter [Behandeln von Problemen beim Operations Management Suite-Onboarding](https://support.microsoft.com/help/3126513/how-to-troubleshoot-operations-management-suite-onboarding-issues).

- Informationen zum Identifizieren nicht überwachter VMs und Computer finden Sie unter [Nicht überwachte virtuelle und physische Computer](security-center-virtual-machine-protection.md#unmonitored-vms-and-computers).

## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurde die Funktionsweise der Datensammlung und der automatischen Bereitstellung in Security Center erläutert. Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

* [Azure Security Center – Häufig gestellte Fragen](security-center-faq.md): Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
* [Überwachen der Sicherheitsintegrität in Azure Security Center](security-center-monitoring.md): Hier erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.



<!--Image references-->
[1]: ./media/security-center-enable-data-collection/enable-automatic-provisioning.png
[2]: ./media/security-center-enable-data-collection/use-another-workspace.png
[3]: ./media/security-center-enable-data-collection/reconfigure-monitored-vm.png
[5]: ./media/security-center-enable-data-collection/data-collection-tiers.png
[6]: ./media/security-center-enable-data-collection/disable-data-collection.png
[7]: ./media/security-center-enable-data-collection/select-subscription.png
[8]: ./media/security-center-enable-data-collection/manual-provision.png
[9]: ./media/security-center-enable-data-collection/pricing-tier.png
[10]: ./media/security-center-enable-data-collection/workspace-selection.png
[11]: ./media/security-center-enable-data-collection/log-analytics.png
[12]: ./media/security-center-enable-data-collection/log-analytics2.png
