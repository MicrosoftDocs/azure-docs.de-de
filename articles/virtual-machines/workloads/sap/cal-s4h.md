---
title: Bereitstellen von SAP S/4HANA oder BW/4HANA auf einem virtuellen Azure-Computer | Microsoft-Dokumentation
description: Bereitstellen von SAP S/4HANA oder BW/4HANA auf einem virtuellen Azure-Computer
author: hermanndms
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 09/15/2016
ms.author: hermannd
ms.reviewer: cynthn
ms.openlocfilehash: 6492e770479042a5a1b4da6f61917832adad4873
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021417"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Bereitstellen von SAP S/4HANA oder BW/4HANA in Azure
In diesem Artikel wird beschrieben, wie Sie S/4HANA über die SAP Cloud Appliance Library (SAP CAL) 3.0 in Azure bereitstellen. Zum Bereitstellen anderer SAP HANA-basierter Lösungen, z.B. BW/4HANA, befolgen Sie dieselben Schritte.

> [!NOTE]
> Weitere Informationen zur SAP Cloud Appliance Library finden Sie auf der Website [SAP Cloud Appliance Library](https://cal.sap.com/). SAP bietet auch einen Blog zur [SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> Ab dem 29. Mai 2017 können Sie das Azure Resource Manager-Bereitstellungsmodell zusätzlich zum weniger bevorzugten klassischen Bereitstellungsmodell für die Bereitstellung der SAP CAL nutzen. Wir empfehlen allerdings, das neue Resource Manager-Bereitstellungsmodell zu verwenden und das klassische Bereitstellungsmodell außer Acht zu lassen.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Vorgehensweise zum Bereitstellen der Lösung

Die folgenden Screenshots veranschaulichen, wie Sie S/4HANA mithilfe der SAP Cloud Appliance Library in Azure bereitstellen. Die Vorgehensweise für andere Lösungen wie BW/4HANA ist identisch.

Die Seite **Lösungen** zeigt einige der SAP CAL HANA-basierten Lösungen, die in Azure verfügbar sind. **SAP S/4HANA 1610 FPS01, Fully-Activated Appliance** befindet sich in der mittleren Zeile:

![SAP CAL-Lösungen](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Erstellen eines Kontos in der SAP CAL
1. Um sich erstmals bei der SAP CAL anzumelden, verwenden Sie Ihren SAP S-Benutzer oder einen anderen bei SAP registrierten Benutzer. Anschließend definieren Sie ein SAP CAL-Konto, das von der SAP CAL zum Bereitstellen von Appliances in Azure verwendet wird. Bei der Definition des Kontos müssen Sie die folgenden Schritte ausführen:

    a. Wählen Sie das Bereitstellungsmodell in Azure aus (Resource Manager oder klassisch).

    b. Geben Sie Ihr Azure-Abonnement ein. Ein SAP CAL-Konto kann nur einem Abonnement zugewiesen werden. Wenn Sie mehrere Abonnements benötigen, müssen Sie ein weiteres SAP CAL-Konto erstellen.

    c. Erteilen Sie der SAP CAL die Berechtigung zur Bereitstellung in Ihrem Azure-Abonnement.

   > [!NOTE]
   >  Die nächsten Schritte zeigen, wie Sie ein SAP CAL-Konto für Resource Manager-Bereitstellungen erstellen. Wenn Sie bereits über ein SAP CAL-Konto verfügen, das mit dem klassischen Bereitstellungsmodell verknüpft ist, *müssen* Sie folgende Schritte zum Erstellen eines neuen SAP CAL-Kontos ausführen. Das neue SAP CAL-Konto muss im Resource Manager-Modell bereitgestellt werden.

1. Erstellen Sie ein neues SAP CAL-Konto. Die Seite **Konten** zeigt drei Optionen für Azure: 

    a. **Microsoft Azure (klassisch)** ist das klassische Bereitstellungsmodell, das nicht mehr empfohlen wird.

    b. **Microsoft Azure** ist das neue Bereitstellungsmodell mit dem Ressourcen-Manager.

    c. **Windows Azure bei Betrieb über 21Vianet** ist eine Option in China, die das klassische Bereitstellungsmodell verwendet.

    Wählen Sie zum Bereitstellen im Resource Manager-Modell **Microsoft Azure** aus.

    ![SAP CAL-Kontodetails](./media/cal-s4h/s4h-pic-2a.png)

1. Geben Sie die Azure-**Abonnement-ID** ein, die Sie im Azure-Portal finden.

   ![SAP CAL-Konten](./media/cal-s4h/s4h-pic3c.png)

1. Um die SAP CAL für die Bereitstellung im angegebenen Azure-Abonnement zu autorisieren, klicken Sie auf **Authorize** (Autorisieren). Die folgende Seite wird auf der Browserregisterkarte angezeigt:

   ![Anmeldung beim Clouddienst in Internet Explorer](./media/cal-s4h/s4h-pic4c.png)

1. Wenn mehrere Benutzer aufgeführt sind, wählen Sie das Microsoft-Konto, das als Co-Administrator mit dem ausgewählten Azure-Abonnement verknüpft ist. Die folgende Seite wird auf der Browserregisterkarte angezeigt:

   ![Bestätigung für Clouddienste in Internet Explorer](./media/cal-s4h/s4h-pic5a.png)

1. Klicken Sie auf **Annehmen**. Wenn die Autorisierung erfolgreich ist, wird die Definition des SAP CAL-Kontos erneut angezeigt. Nach kurzer Zeit bestätigt eine Meldung, dass die Autorisierung erfolgreich war.

1. Um Ihrem Benutzer das neu erstellte SAP CAL-Konto zuzuweisen, geben Sie Ihre **Benutzer-ID** in das Textfeld rechts ein, und klicken dann auf **Add** (Hinzufügen).

   ![Zuordnen des Kontos zum Benutzer](./media/cal-s4h/s4h-pic8a.png)

1. Um Ihr Konto dem Benutzer zuzuordnen, der sich bei der SAP CAL anmeldet, klicken Sie auf **Review** (Überprüfen). 
 
1. Um die Zuordnung zwischen dem Benutzer und dem neu erstellten SAP CAL-Konto zu erstellen, klicken Sie auf **Create** (Erstellen).

   ![Zuordnung des SAP CAL-Kontos zum Benutzer](./media/cal-s4h/s4h-pic9b.png)

Sie haben erfolgreich ein SAP CAL-Konto mit den folgenden Möglichkeiten erstellt:

- Verwendung des Resource Manager-Bereitstellungsmodells
- Bereitstellung von SAP-Systemen in Ihrem Azure-Abonnement

Nun können Sie beginnen, S/4HANA in Ihrem Benutzerabonnement in Azure bereitzustellen.

> [!NOTE]
> Bevor Sie fortfahren, bestimmen Sie, ob Sie über Azure-vCPU-Kontingente für virtuelle Azure-Computer der H-Serie verfügen. Derzeit verwendet die SAP CAL VMs der H-Serie von Azure, um einige der SAP HANA-basierten Lösungen bereitzustellen. Ihr Azure-Abonnement verfügt möglicherweise nicht über vCPU-Kontingente für die H-Serie. Falls dem so ist, müssen Sie den Azure-Support kontaktieren, um ein Kontingent von mindestens 16 vCPUs der H-Serie anzufordern.
> 
> [!NOTE]
> Wenn Sie eine Lösung in Azure in der SAP CAL bereitstellen, stellen Sie möglicherweise fest, dass Sie nur eine Azure-Region auswählen können. Für eine Bereitstellung in Azure-Regionen, die nicht der von der SAP CAL vorgeschlagenen Region entsprechen, müssen Sie bei SAP ein CAL-Abonnement erwerben. Sie müssen ggf. auch in einer Nachricht SAP bitten, Ihre CAL so zu aktivieren, dass eine Bereitstellung in Azure-Regionen möglich wird, die sich von den anfänglich vorgeschlagenen unterscheidet.

### <a name="deploy-a-solution"></a>Bereitstellen einer Lösung

Lassen Sie uns auf der Seite **Lösungen** der SAP CAL eine Lösung bereitstellen. Die SAP CAL weist für die Bereitstellung zwei Sequenzen auf:

- Eine Standardsequenz, bei der eine Seite genutzt wird, um das bereitzustellende System zu definieren
- Eine erweiterte Sequenz, die Ihnen eine gewisse Auswahl bei VM-Größen bietet 

Wir veranschaulichen nun die grundlegende Vorgehensweise zur Bereitstellung.

1. Führen Sie auf der Seite **Kontodetails** die folgenden Schritte aus:

    a. Wählen Sie ein SAP CAL-Konto aus. (Wählen Sie ein Konto, das für eine Bereitstellung gemäß dem Ressourcen-Manager-Bereitstellungsmodell vorgesehen ist.)

    b. Geben Sie einen **Namen** für die Instanz ein.

    c. Wählen Sie eine Azure-**Region** aus. Die SAP CAL schlägt eine Region vor. Wenn Sie eine andere Azure-Region benötigen und über kein SAP CAL-Abonnement verfügen, müssen Sie bei SAP ein CAL-Abonnement bestellen.

    d. Geben Sie für die Lösung ein **Masterkennwort** mit acht oder neun Zeichen ein. Das Kennwort wird von den Administratoren der verschiedenen Komponenten verwendet.

   ![Einfacher SAP CAL-Modus: Instanz erstellen](./media/cal-s4h/s4h-pic10a.png)

1. Klicken Sie auf **Erstellen** und im angezeigten Meldungsfeld auf **OK**.

   ![SAP CAL: Unterstützte VM-Größen](./media/cal-s4h/s4h-pic10b.png)

1. Klicken Sie im Dialogfeld **Privater Schlüssel** auf **Speichern**, um den privaten Schlüssel in der SAP CAL zu speichern. Klicken Sie zur Verwendung des Kennwortschutzes für den privaten Schlüssel auf **Herunterladen**. 

   ![SAP CAL: Privater Schlüssel](./media/cal-s4h/s4h-pic10c.png)

1. Lesen die SAP CAL-**Warnmeldung**, und klicken Sie auf **OK**.

   ![SAP CAL-Warnung](./media/cal-s4h/s4h-pic10d.png)

    Nun kann die Bereitstellung erfolgen. Nach einiger Zeit wird je nach Größe und Komplexität der Lösung (die SAP CAL zeigt eine Schätzung an) der Status als aktiv und betriebsbereit angezeigt.

1. Um die virtuellen Computer zu finden, die mit den anderen zugehörigen Ressourcen in einer Ressourcengruppe gesammelt wurden, wechseln Sie zum Azure-Portal: 

   ![Im neuen Portal angezeigte SAP CAL-Objekte](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. Im SAP CAL-Portal wird der Status als **Aktiv** angezeigt. Klicken Sie zum Verbinden mit der Lösung auf **Verbinden**. Diese Lösung bietet verschiedene Optionen zum Herstellen von Verbindungen mit verschiedenen Komponenten.

   ![SAP CAL-Instanzen](./media/cal-s4h/active_solution.png)

1. Bevor Sie eine der Optionen zum Herstellen einer Verbindung mit den bereitgestellten Systemen nutzen können, klicken Sie auf **Getting Started Guide** (Leitfaden für erste Schritte). 

   ![Herstellen einer Verbindung mit der Instanz](./media/cal-s4h/connect_to_solution.png)

    Die Dokumentation nennt die Benutzer für jede der Verbindungsmethoden. Die Kennwörter für diese Benutzer sind auf das Masterkennwort festgelegt, das Sie am Anfang des Bereitstellungsprozesses festgelegt haben. In der Dokumentation werden weitere Funktionsbenutzer mit ihren Kennwörtern aufgeführt, die Sie zum Anmelden beim bereitgestellten System verwenden können. 

    Wenn Sie beispielsweise die SAP GUI verwenden, die auf dem Windows-Remotedesktop-Computer vorinstalliert ist, sieht das S/4-System in etwa so aus:

   ![SM50 auf der vorinstallierten SAP GUI](./media/cal-s4h/gui_sm50.png)

    Wenn Sie DBACockpit verwenden, sieht die Instanz wie folgt aus:

   ![SM50 auf der SAP GUI von DBACockpit](./media/cal-s4h/dbacockpit.png)

Innerhalb weniger Stunden wird eine fehlerfreie S/4 SAP-Appliance in Azure bereitgestellt.

Wenn Sie ein SAP CAL-Abonnement erworben haben, werden Bereitstellungen über die SAP CAL in Azure vollständig von SAP unterstützt. Die Supportwarteschlange heißt BC-VCM-CAL.







