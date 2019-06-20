---
title: Löschen und Exportieren personenbezogener Daten aus Azure DevTest Labs | Microsoft-Dokumentation
description: Erfahren Sie, wie Sie personenbezogene Daten aus dem Azure DevTest Labs-Dienst löschen und exportieren, um Ihren Verpflichtungen im Rahmen der allgemeinen Datenschutz-Grundverordnung (DSGVO) nachzukommen.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2018
ms.author: spelluru
ms.openlocfilehash: e681652c13e521bd33524e247db65088f47a794c
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60394984"
---
# <a name="export-or-delete-personal-data-from-azure-devtest-labs"></a>Exportieren oder Löschen personenbezogener Daten aus Azure DevTest Labs
Dieser Artikel enthält Schritte zum Löschen und Exportieren personenbezogener Daten aus dem Azure DevTest Labs-Dienst. 

## <a name="what-personal-data-does-devtest-labs-collect"></a>Welche personenbezogenen Daten werden durch DevTest Labs erfasst?
DevTest Labs erfasst im Wesentlichen zwei Bestandteile der personenbezogenen Daten vom Benutzer: die E-Mail-Adresse des Benutzers und die Benutzerobjekt-ID. Diese Informationen sind wichtig, damit der Dienst den Labadministratoren und Labbenutzern die im Dienst enthaltenen Features bereitstellen kann.

### <a name="user-email-address"></a>E-Mail-Adresse des Benutzers
DevTest Labs verwendet die E-Mail-Adresse des Benutzers, um beim automatischen Herunterfahren E-Mail-Benachrichtigungen an Labbenutzer zu senden. In der E-Mail werden Benutzer darüber benachrichtigt, dass ihr Computer heruntergefahren wird. Die Benutzer können das Herunterfahren nach Bedarf entweder verschieben oder überspringen. Die E-Mail-Adresse wird auf Lab- oder auf VM-Ebene konfiguriert.

**Festlegen der E-Mail-Adresse auf Labebene:**

![Festlegen der E-Mail-Adresse auf Labebene](./media/personal-data-delete-export/lab-user-email.png)

**Festlegen der E-Mail-Adresse auf VM-Ebene:**

![Festlegen der E-Mail-Adresse auf VM-Ebene](./media/personal-data-delete-export/vm-user-email.png)

### <a name="user-object-id"></a>Benutzerobjekt-ID
DevTest Labs verwendet die Benutzerobjekt-ID, um Kostentrends im Vergleich zum Vormonat sowie Informationen zu Kosten nach Ressourcen für Labadministratoren anzuzeigen. So können diese Kosten verfolgen und Schwellenwerte für ihr Lab verwalten. 

**Geschätzter Kostentrend für den aktuellen Kalendermonat:** 
![Geschätzter Kostentrend für den aktuellen Kalendermonat](./media/personal-data-delete-export/estimated-cost-trend-per-month.png)

**Geschätzte monatliche Kosten bis heute nach Ressource:** 
![Geschätzte monatliche Kosten bis heute nach Ressource](./media/personal-data-delete-export/estimated-month-to-date-cost-by-resource.png)


## <a name="why-do-we-need-this-personal-data"></a>Warum brauchen wir diese personenbezogenen Daten?
Der DevTest Labs-Dienst verwendet die personenbezogenen Daten zu operativen Zwecken. Diese Daten sind entscheidend, damit der Dienst wesentliche Features bereitstellen kann. Wenn Sie für die Benutzer-E-Mail-Adresse eine Aufbewahrungsrichtlinie festlegen, erhalten Labbenutzer keine rechtzeitigen E-Mail-Benachrichtigungen zum automatischen Herunterfahren, nachdem ihre E-Mail-Adresse aus unserem System gelöscht wurde. Ebenso wenig kann der Labadministrator Kostentrends im Vergleich zum Vormonat oder Kosten nach Ressource für die Computer in seinen Labs anzeigen, wenn die Benutzerobjekt-IDs aufgrund einer Aufbewahrungsrichtlinie gelöscht werden. Daher müssen diese Daten aufbewahrt werden, solange die Ressource des Benutzers im Lab aktiv ist.

## <a name="how-can-i-have-the-system-to-forget-my-personal-data"></a>Wie kann ich meine personenbezogenen Daten aus dem System entfernen?
Wenn Sie als Labbenutzer wünschen, dass diese personenbezogenen Daten gelöscht werden, können Sie die entsprechende Ressource im Lab löschen. Der DevTest Labs-Dienst anonymisiert die gelöschten persönlichen Daten 30 Tage nachdem sie vom Benutzer gelöscht wurden.

Wenn Sie beispielsweise Ihren virtuellen Computer löschen oder Ihre E-Mail-Adresse entfernt haben, werden diese Daten 30 Tage nach dem Löschen der Ressource vom DevTest Labs-Dienst anonymisiert. Die 30-Tage-Aufbewahrungsrichtlinie nach dem Löschvorgang stellt sicher, dass wir dem Labadministrator eine genaue Kostenprognose im Vergleich zum Vormonat bereitstellen.

## <a name="how-can-i-request-an-export-on-my-personal-data"></a>Wie kann ich einen Export meiner personenbezogenen Daten anfordern?
Als Labbenutzer können Sie einen Export Ihrer personenbezogenen Daten anfordern, die im DevTest Labs-Dienst gespeichert werden. Wechseln Sie zum Anfordern eines Exports in Ihrem Lab auf der Seite **Übersicht** zur Option **Personenbezogene Daten**. Durch Auswahl der Schaltfläche **Export anfordern** wird eine herunterladbare Excel-Datei im Speicherkonto Ihres Labadministrators erstellt. Sie können sich dann an Ihren Labadministrator wenden, um diese Daten einzusehen.

1. Wählen Sie im Menü links die Option **Personenbezogene Daten**. 

    ![Seite „Personenbezogene Daten“](./media/personal-data-delete-export/personal-data-page.png)
2. Wählen Sie die **Ressourcengruppe** aus, die das Lab enthält.

    ![Auswählen der Ressourcengruppe](./media/personal-data-delete-export/select-resource-group.png)
3. Wählen Sie das **Speicherkonto** in der Ressourcengruppe aus.
4. Klicken Sie auf der Seite **Speicherkonto** auf **Blobs**.

    ![Auswählen der Kachel „Blobs“](./media/personal-data-delete-export/select-blobs-tile.png)
5. Wählen Sie in der Liste der Container den Container mit dem Namen **labresourceusage** aus.

    ![Auswählen des Blobcontainers](./media/personal-data-delete-export/select-blob-container.png)
6. Wählen Sie den **Ordner** aus, der nach Ihrem Lab benannt ist. In diesem Ordner finden Sie **CSV**-Dateien für **Datenträger** und **virtuelle Computer** in Ihrem Lab. Sie können diese CSV-Dateien herunterladen und deren Inhalt für den Labbenutzer, der Zugriff angefordert hat, filtern und bereitstellen.

    ![Herunterladen der CSV-Datei](./media/personal-data-delete-export/download-csv-file.png)

## <a name="next-steps"></a>Nächste Schritte
Entsprechende Informationen finden Sie in den folgenden Artikeln: 

- [Festlegen von Richtlinien für ein Lab](devtest-lab-get-started-with-lab-policies.md)
- [Häufig gestellte Fragen](devtest-lab-faq.md)
