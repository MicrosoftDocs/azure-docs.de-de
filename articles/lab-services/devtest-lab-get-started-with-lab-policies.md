---
title: Verwalten von grundlegenden Labrichtlinien in Azure DevTest Labs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie in DevTest Labs einige grundlegende Richtlinien (Einstellungen) für ein Lab festlegen.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 8cc529fbf9b24335be1bec07f81c732ced7a2b72
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60773841"
---
# <a name="manage-basic-policies-for-a-lab-in-azure-devtest-labs"></a>Verwalten von grundlegenden Richtlinien für ein Lab in Azure DevTest Labs

Mit Azure DevTest Labs können Sie Kosten und unnötigen Aufwand in Ihren Labs minimieren, indem Sie Richtlinien (Einstellungen) für jedes Lab verwalten. Dieser Artikel dient als Einstieg in die Verwendung von Richtlinien. Sie erfahren, wie Sie zwei der wichtigsten Richtlinien festlegen: Begrenzen der Anzahl von virtuellen Computern, die von einem einzelnen Benutzer erstellt oder angefordert werden können, und Konfigurieren des automatischen Herunterfahrens. Informationen zum Einrichten der einzelnen Labrichtlinien finden Sie unter [Definieren von Labrichtlinien in Azure DevTest Labs](devtest-lab-set-lab-policy.md).  

## <a name="accessing-a-labs-policies-in-azure-devtest-labs"></a>Zugriff auf Labrichtlinien in Azure DevTest Labs
Die folgenden Schritte führen Sie durch das Einrichten von Richtlinien für ein Lab in Azure DevTest Labs:

Um die Richtlinien für ein Lab anzuzeigen (und zu ändern), gehen Sie folgendermaßen vor:

1. Melden Sie sich beim [Azure-Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040) an.

1. Wählen Sie **Alle Dienste** und dann in der Liste die Option **DevTest Labs**.

1. Wählen Sie in der Liste der Labs das gewünschte Lab aus.   

1. Wählen Sie **Konfiguration und Richtlinien** aus.

    ![Bereich „Richtlinieneinstellungen“](./media/devtest-lab-set-lab-policy/policies-menu.png)

1. Der Bereich **Konfiguration und Richtlinien** enthält ein Menü mit Einstellungen, die Sie angeben können. Dieser Artikel erläutert nur die Einstellungen für **Virtuelle Computer pro Benutzer**, **Automatisch herunterfahren** und **Automatisch starten**. Informationen zu den weiteren Einstellungen finden Sie unter [Verwalten aller Richtlinien für ein Lab in Azure DevTest Labs](./devtest-lab-set-lab-policy.md). 
   
## <a name="set-virtual-machines-per-user"></a>Festlegen der virtuellen Computer pro Benutzer
Mit der Richtlinie für **Virtual machines per user** (VMs pro Benutzer) können Sie die maximale Anzahl von virtuellen Computern angeben, die von einem einzelnen Benutzer erstellt werden können. Wenn ein Benutzer versucht, einen virtuellen Computer zu erstellen oder anzufordern, und das Benutzerlimit erreicht wurde, wird in einer Fehlermeldung darauf hingewiesen, dass der virtuelle Computer nicht erstellt/angefordert werden kann. 

1. Wählen Sie im Menü **Konfiguration und Richtlinien** des Labs die Option **Virtuelle Computer pro Benutzer**.
   
    ![Virtual machines per user](./media/devtest-lab-set-lab-policy/max-vms-per-user.png)

1. Wählen Sie **Ja**, um die Anzahl von virtuellen Computern pro Benutzer zu begrenzen. Wenn Sie die Anzahl von virtuellen Computern pro Benutzer nicht begrenzen möchten, wählen Sie **Nein**. Wenn Sie **Ja** auswählen, geben Sie einen numerischen Wert für die maximale Anzahl von virtuellen Computern ein, die von einem Benutzer erstellt oder angefordert werden können. 

1. Wählen Sie **Ja**, um die Anzahl von virtuellen Computern zu begrenzen, die eine SSD (Solid-State Disk) verwenden können. Wenn Sie die Anzahl von virtuellen Computern, die eine SSD verwenden können, nicht begrenzen möchten, wählen Sie **Nein**. Wenn Sie **Ja** auswählen, geben Sie einen Wert für die maximale Anzahl von virtuellen Computern ein, die mit einer SSD erstellt werden können. 

1. Wählen Sie **Speichern** aus.

## <a name="set-auto-shutdown"></a>Festlegen des automatischen Herunterfahrens
Die Auto-Shutdown-Richtlinie hilft dabei, unnötigen Aufwand im Lab zu minimieren, indem sie Ihnen ermöglicht, die Uhrzeit anzugeben, zu der die virtuellen Computer für dieses Lab heruntergefahren werden.

1. Wählen Sie im Bereich **Konfiguration und Richtlinien** des Labs die Option **Automatisch herunterfahren** aus.
   
    ![Auto-shutdown](./media/devtest-lab-set-lab-policy/auto-shutdown.png)

1. Wählen Sie **Ein**, um diese Richtlinie zu aktivieren, und **Aus**, um sie zu deaktivieren.

1. Wenn Sie diese Richtlinie aktivieren, geben Sie die Uhrzeit (und die Zeitzone) an, zu der alle virtuellen Computer im aktuellen Lab heruntergefahren werden sollen.

1. Geben Sie für die Option, mit der 15 Minuten vor der angegebenen Uhrzeit des automatischen Herunterfahrens eine Benachrichtigung gesendet wird, entweder **Ja** oder **Nein** an. Wenn Sie **Ja** auswählen, geben Sie einen Webhook-URL-Endpunkt oder eine E-Mail-Adresse ein, der bzw. die angibt, wo die Benachrichtigung veröffentlicht bzw. an wen diese gesendet wird. Der Benutzer erhält eine Benachrichtigung und hat die Möglichkeit, das Herunterfahren zu verzögern.

   Weitere Informationen zu Webhooks finden Sie unter [Erstellen eines Webhooks oder einer API-Azure-Funktion](../azure-functions/functions-create-a-web-hook-or-api-function.md). 

1. Wählen Sie **Speichern** aus.

Standardmäßig gilt diese Richtlinie nach der Aktivierung für alle virtuellen Computer im aktuellen Lab. Um diese Einstellung von einem bestimmten virtuellen Computer zu entfernen, öffnen Sie den Verwaltungsbereich des virtuellen Computers, und ändern Sie die Einstellung **Automatisch herunterfahren**.

## <a name="set-auto-start"></a>Festlegen des automatischen Startens
Mit der Autostart-Richtlinie können Sie angeben, wann die virtuellen Computer im aktuellen Lab gestartet werden sollen.  

1. Wählen Sie im Bereich **Konfiguration und Richtlinien** des Labs die Option **Automatisch starten** aus.
   
    ![Auto-start](./media/devtest-lab-set-lab-policy/auto-start.png)

2. Wählen Sie **Ein**, um diese Richtlinie zu aktivieren, und **Aus**, um sie zu deaktivieren.

3. Wenn Sie diese Richtlinie aktivieren, geben Sie die geplante Startzeit, die Zeitzone und die Wochentage an, an denen die Uhrzeit gelten soll. 

4. Wählen Sie **Speichern** aus.

Nach der Aktivierung wird diese Richtlinie nicht automatisch auf alle virtuellen Computer im aktuellen Lab angewendet. Um diese Einstellung auf einen vorhandenen virtuellen Computer anzuwenden, öffnen Sie den Verwaltungsbereich des virtuellen Computers, und ändern Sie die Einstellung **Automatisch starten**.

## <a name="next-steps"></a>Nächste Schritte

- [Definieren von Labrichtlinien in Azure DevTest Labs](devtest-lab-set-lab-policy.md): Informationen zum Ändern anderer Labrichtlinien.
