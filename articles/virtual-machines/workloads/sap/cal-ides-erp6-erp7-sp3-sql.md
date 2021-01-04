---
title: Bereitstellen von SAP IDES EHP7 SP3 für SAP ERP 6.0 in Azure | Microsoft-Dokumentation
description: Bereitstellen von SAP IDES EHP7 SP3 für SAP ERP 6.0 in Azure
author: hermanndms
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 09/16/2016
ms.author: hermannd
ms.reviewer: cynthn
ms.openlocfilehash: dc0c1744c2943d33d516de4ed9d28893541773ad
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94957876"
---
# <a name="deploy-sap-ides-ehp7-sp3-for-sap-erp-60-on-azure"></a>Bereitstellen von SAP IDES EHP7 SP3 für SAP ERP 6.0 in Azure
In diesem Artikel wird beschrieben, wie Sie ein SAP IDES-System mit SQL Server und einem Windows-Betriebssystem über die SAP Cloud Appliance Library (SAP CAL) 3.0 in Azure bereitstellen. In den Screenshots ist dieser Prozess Schritt für Schritt dargestellt. Führen Sie die gleichen Schritte aus, um eine andere Lösung bereitzustellen.

Informationen zu Ihren ersten Schritten mit der SAP Cloud Appliance Library finden Sie auf der Website [SAP Cloud Appliance Library](https://cal.sap.com/). SAP bietet auch einen Blog zur neuen [SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience). 

> [!NOTE]
> Ab dem 29. Mai 2017 können Sie das Azure Resource Manager-Bereitstellungsmodell zusätzlich zum weniger bevorzugten klassischen Bereitstellungsmodell für die Bereitstellung der SAP CAL nutzen. Wir empfehlen allerdings, das neue Resource Manager-Bereitstellungsmodell zu verwenden und das klassische Bereitstellungsmodell außer Acht zu lassen.

Wenn Sie bereits ein SAP CAL-Konto erstellt haben, das das klassische Modell verwendet, *müssen Sie ein anderes SAP CAL-Konto verwenden*. Bei diesem Konto muss die Bereitstellung ausschließlich in Azure mithilfe des Resource Manager-Modells erfolgen.

Nach der Anmeldung bei der SAP CAL gelangen Sie in der Regel über die erste Seite zur Seite **Solutions** (Lösungen). Die in der SAP CAL angebotenen Lösungen werden immer mehr, daher müssen Sie möglicherweise ein wenig scrollen, bis Sie die gewünschte Lösung gefunden haben. Die hervorgehobene Windows-basierte SAP IDES-Lösung, die ausschließlich in Azure verfügbar ist, zeigt den Bereitstellungsprozess:

![SAP CAL-Lösungen](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic1.jpg)

### <a name="create-an-account-in-the-sap-cal"></a>Erstellen eines Kontos in der SAP CAL
1. Um sich erstmals bei der SAP CAL anzumelden, verwenden Sie Ihren SAP S-Benutzer oder einen anderen bei SAP registrierten Benutzer. Anschließend definieren Sie ein SAP CAL-Konto, das von der SAP CAL zum Bereitstellen von Appliances in Azure verwendet wird. Bei der Definition des Kontos müssen Sie die folgenden Schritte ausführen:

    a. Wählen Sie das Bereitstellungsmodell in Azure aus (Resource Manager oder klassisch).

    b. Geben Sie Ihr Azure-Abonnement ein. Ein SAP CAL-Konto kann nur einem Abonnement zugewiesen werden. Wenn Sie mehrere Abonnements benötigen, müssen Sie ein weiteres SAP CAL-Konto erstellen.
    
    c. Erteilen Sie der SAP CAL die Berechtigung zur Bereitstellung in Ihrem Azure-Abonnement.

   > [!NOTE]
   >  Die nächsten Schritte zeigen, wie Sie ein SAP CAL-Konto für Resource Manager-Bereitstellungen erstellen. Wenn Sie bereits über ein SAP CAL-Konto verfügen, das mit dem klassischen Bereitstellungsmodell verknüpft ist, *müssen* Sie folgende Schritte zum Erstellen eines neuen SAP CAL-Kontos ausführen. Das neue SAP CAL-Konto muss im Resource Manager-Modell bereitgestellt werden.

1. Beim Erstellen eines neuen SAP CAL-Kontos zeigt die Seite **Accounts** (Konten) für Azure zwei Optionen an: 

    a. **Microsoft Azure (klassisch)** ist das klassische Bereitstellungsmodell, das nicht mehr empfohlen wird.

    b. **Microsoft Azure** ist das neue Bereitstellungsmodell mit dem Ressourcen-Manager.

    ![Der Screenshot zeigt SAP CAL-Konten mit hervorgehobenem Microsoft Azure.](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic-2a.PNG)

    Wählen Sie zum Bereitstellen im Resource Manager-Modell **Microsoft Azure** aus.

    ![Der Screenshot zeigt SAP CAL--Konten.](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Geben Sie die Azure-**Abonnement-ID** ein, die Sie im Azure-Portal finden. 

    ![SAP CAL-Abonnement-ID](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic3c.PNG)

1. Um die SAP CAL für die Bereitstellung im angegebenen Azure-Abonnement zu autorisieren, klicken Sie auf **Authorize** (Autorisieren). Die folgende Seite wird auf der Browserregisterkarte angezeigt:

    ![Anmeldung beim Clouddienst in Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic4c.PNG)

1. Wenn mehrere Benutzer aufgeführt sind, wählen Sie das Microsoft-Konto, das als Co-Administrator mit dem ausgewählten Azure-Abonnement verknüpft ist. Die folgende Seite wird auf der Browserregisterkarte angezeigt:

    ![Bestätigung für Clouddienste in Internet Explorer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic5a.PNG)

1. Klicken Sie auf **Annehmen**. Wenn die Autorisierung erfolgreich ist, wird die Definition des SAP CAL-Kontos erneut angezeigt. Nach kurzer Zeit bestätigt eine Meldung, dass die Autorisierung erfolgreich war.

1. Um Ihrem Benutzer das neu erstellte SAP CAL-Konto zuzuweisen, geben Sie Ihre **Benutzer-ID** in das Textfeld rechts ein, und klicken dann auf **Add** (Hinzufügen). 

    ![Zuordnen des Kontos zum Benutzer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic8a.PNG)

1. Um Ihr Konto dem Benutzer zuzuordnen, der sich bei der SAP CAL anmeldet, klicken Sie auf **Review** (Überprüfen). 

1. Um die Zuordnung zwischen dem Benutzer und dem neu erstellten SAP CAL-Konto zu erstellen, klicken Sie auf **Create** (Erstellen).

    ![Zuordnung des Kontos zum Benutzer](./media/cal-ides-erp6-ehp7-sp3-sql/s4h-pic9b.PNG)

Sie haben erfolgreich ein SAP CAL-Konto mit den folgenden Möglichkeiten erstellt:

- Verwendung des Resource Manager-Bereitstellungsmodells
- Bereitstellung von SAP-Systemen in Ihrem Azure-Abonnement

> [!NOTE]
> Bevor Sie Ihre auf Windows und SQL Server basierende SAP IDES-Lösung bereitstellen können, müssen Sie sich für ein SAP CAL-Abonnement registrieren. Andernfalls wird die Lösung auf der Übersichtsseite möglicherweise als **Locked** (Gesperrt) angezeigt.

### <a name="deploy-a-solution"></a>Bereitstellen einer Lösung
1. Wählen Sie nach dem Einrichten eines SAP CAL-Kontos die Lösung **The SAP IDES solution on Windows and SQL Server** (Die SAP IDES-Lösung unter Windows und SQL Server) aus. Klicken Sie auf **Create Instance** (Instanz erstellen), und bestätigen Sie die Nutzungsbedingungen. 

1. Auf der Seite **Basic Mode: Create Instance** (Einfacher Modus: Instanz erstellen) müssen Sie Folgendes ausführen:

    a. Geben Sie einen **Namen** für die Instanz ein.

    b. Wählen Sie eine Azure-**Region** aus. Unter Umständen benötigen Sie ein SAP CAL-Abonnement, damit Ihnen mehrere Azure-Regionen vorgeschlagen werden.

    c.  Geben Sie das **Masterkennwort** für die Lösung ein:

    ![Einfacher SAP CAL-Modus: Instanz erstellen](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic10a.png)

1. Klicken Sie auf **Erstellen**. Nach einiger Zeit wird je nach Größe und Komplexität der Lösung (die SAP CAL zeigt eine Schätzung an) der Status als aktiv und betriebsbereit angezeigt: 

    ![SAP CAL-Instanzen](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic12a.png)

1. Wechseln Sie zum Azure-Portal, um die Ressourcengruppe und die dazugehörigen Objekte zu suchen, die von der SAP CAL erstellt wurden. Der Name der virtuellen Computer beginnt jeweils mit dem gleichen Instanznamen, der in der SAP CAL vergeben wurde.

    ![Ressourcengruppenobjekte](./media/cal-ides-erp6-ehp7-sp3-sql/ides_resource_group.PNG)

1. Navigieren Sie im SAP CAL-Portal zu den bereitgestellten Instanzen, und klicken Sie auf **Connect** (Verbindung herstellen). Das folgende Popupfenster wird angezeigt: 

    ![Herstellen einer Verbindung mit der Instanz](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic14a.PNG)

1. Bevor Sie eine der Optionen zum Herstellen einer Verbindung mit den bereitgestellten Systemen nutzen können, klicken Sie auf **Getting Started Guide** (Leitfaden für erste Schritte). Die Dokumentation nennt die Benutzer für jede der Verbindungsmethoden. Die Kennwörter für diese Benutzer sind auf das Masterkennwort festgelegt, das Sie am Anfang des Bereitstellungsprozesses festgelegt haben. In der Dokumentation werden weitere Funktionsbenutzer mit ihren Kennwörtern aufgeführt, die Sie zum Anmelden beim bereitgestellten System verwenden können.

    ![SAP-Begrüßungsdokumentation](./media/cal-ides-erp6-ehp7-sp3-sql/ides-pic15.jpg)

Innerhalb weniger Stunden wird ein fehlerfreies SAP IDES-System in Azure bereitgestellt.

Wenn Sie ein SAP CAL-Abonnement erworben haben, werden Bereitstellungen über die SAP CAL in Azure vollständig von SAP unterstützt. Die Supportwarteschlange heißt BC-VCM-CAL.

