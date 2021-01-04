---
title: Bereitstellen von StorSimple Virtual Array für das Cloud Solution Provider-Programm
description: Erfahren Sie, wie ein CSP-Partner einen neuen Kunden oder einem Bestandskunden ein neues Abonnement hinzufügen und dann einen Dienst zum Bereitstellen eines StorSimple Virtual Array im CSP-Programm erstellen kann.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 02/08/2017
ms.author: alkohli
ms.openlocfilehash: dc0cf718ee51e23fb749bdf57d5344977de009d8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96021859"
---
# <a name="deploy-storsimple-virtual-array-for-cloud-solution-provider-program"></a>Bereitstellen von StorSimple Virtual Array für das Cloud Solution Provider-Programm

## <a name="overview"></a>Übersicht

Partner im Cloudlösungsanbieter-Programm (Cloud Solution Provider, CSP) können StorSimple Virtual Array ihren Kunden bereitstellen. Ein CSP-Partner kann einen StorSimple-Geräte-Manager-Dienst erstellen. Dieser Dienst kann anschließend zum Bereitstellen und Verwalten von StorSimple Virtual Array und den zugehörigen Freigaben, Volumes und Sicherungen verwendet werden.

Dieser Artikel beschreibt, wie ein CSP-Partner einen neuen Kunden oder einem Bestandskunden ein neues Abonnement hinzufügen und dann einen Dienst zum Bereitstellen eines StorSimple Virtual Array im CSP-Programm erstellen kann.

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie beginnen, müssen diese Voraussetzungen erfüllt sein:

- Sie sind beim CSP-Programm registriert.
- Sie verfügen über gültige [Partner Center](https://partnercenter.microsoft.com/)-Anmeldeinformationen. Die Anmeldeinformationen ermöglichen Ihnen das Anmelden beim Partnerportal, um Neukunden hinzuzufügen, Kunden zu suchen oder im Partnerdashboard zu einem Kundenkonto zu wechseln. Der CSP kann im Auftrag des Kunden im Azure-Portal als StorSimple-Administrator fungieren.
                             
## <a name="add-a-customer"></a>Hinzufügen eines Kunden

Wenn Sie einen Kunden hinzufügen, wird automatisch ein Abonnement erstellt. Um einen Kunden hinzuzufügen (und automatisch ein Abonnement zu erstellen), führen Sie im Partnerportal die folgenden Schritte aus.

1. Wechseln Sie zum [Partner Center](https://partnercenter.microsoft.com/), und melden Sie sich mit Ihren CSP-Anmeldeinformationen an. Klicken Sie auf **Dashboard**.

     ![Dashboard im Partner Center](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. Klicken Sie im linken Bereich auf **Kunden**. Klicken Sie im rechten Bereich auf **Kunden hinzufügen**. Geben Sie die Details des Kunden ein. Klicken Sie auf **Weiter: Abonnements**, um ein Abonnement für den Kunden zu erstellen.

    ![Kunden hinzufügen](./media/storsimple-partner-csp-deploy/image2.png)

3.  Wählen Sie das Angebot **Microsoft Azure** aus. Scrollen Sie auf der Seite nach unten, und klicken Sie auf **Überprüfen**.

    ![Überprüfen der Abonnementinformationen](./media/storsimple-partner-csp-deploy/image3.png)
                              
4. Prüfen Sie die Informationen, und klicken Sie auf **Absenden**.

    ![Absenden des Abonnements](./media/storsimple-partner-csp-deploy/image4.png)

5. Speichern Sie die Bestätigungsinformationen zur künftigen Bezugnahme.

    ![Speichern der Bestätigung](./media/storsimple-partner-csp-deploy/image5.png)

6. Wechseln Sie zum Kunden, den Sie gerade hinzugefügt haben. Klicken Sie auf den **Firmennamen**, um die Details eingehender zu untersuchen.

    ![Wechseln zum Kunden](./media/storsimple-partner-csp-deploy/image6.png)  

7. Wählen Sie im linken Bereich **Dienstverwaltung** aus. Klicken Sie im rechten Bereich unter **Dienste verwalten** auf **Microsoft Azure-Verwaltungsportal**, um sich als Azure-Administrator Ihres Kunden anzumelden.

    ![Anmelden beim Azure-Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Um einen StorSimple-Geräte-Manager zu erstellen, klicken Sie auf **+ Neu** und wechseln zu **Virtuelle StorSimple-Geräteserie**. Weitere Informationen finden Sie unter [Bereitstellen eines StorSimple-Geräte-Manager-Diensts](storsimple-virtual-array-manage-service.md).

    ![Erstellen des StorSimple-Geräte-Manager-Diensts](./media/storsimple-partner-csp-deploy/image8.png)


## <a name="add-a-subscription"></a>Hinzufügen eines Abonnements

Möglicherweise gibt es schon einen Bestandskunden, dem Sie ein Abonnement hinzufügen müssen. Um einem Bestandskunden ein Abonnement hinzuzufügen, führen Sie im Partnerportal die folgenden Schritte aus.

1. Wechseln Sie zum [Partner Center](https://partnercenter.microsoft.com/), und melden Sie sich mit Ihren CSP-Anmeldeinformationen an. Klicken Sie auf **Dashboard**.

     ![Dashboard im Partner Center](./media/storsimple-partner-csp-deploy/image1.png)
                              
2. Klicken Sie im linken Bereich auf **Kunden**. Wechseln Sie zum Kunden, dem Sie ein Abonnement hinzufügen möchten. Klicken Sie auf das Symbol ![„Erweitern“](./media/storsimple-partner-csp-deploy/expand_pane_icon.png), um die Zeile mit dem Namen des Unternehmen Ihres Kunden zu erweitern. Klicken Sie in den Details auf **Abonnements hinzufügen**.

    ![Kunden](./media/storsimple-partner-csp-deploy/image10.png)

3. Versehen Sie im Abonnement **Microsoft Azure** bei **Topangebote** mit einem Häkchen, und klicken Sie auf **Absenden**. Dadurch wird ein neues Abonnement erstellt.

    ![Hinzufügen eines neuen Abonnements](./media/storsimple-partner-csp-deploy/image11.png)

6. Nachdem ein neues Abonnement erstellt wurde, klicken Sie im linken Bereich auf **<-- Kunden**, um zur Seite **Kunden** zurückzukehren. Suchen Sie den Kunden, für den Sie soeben ein Abonnement erstellt haben. Klicken Sie auf den **Firmennamen**, um die Details eingehender zu untersuchen.

    ![Wechseln zum Kunden](./media/storsimple-partner-csp-deploy/image6.png)  

7. Wählen Sie im linken Bereich **Dienstverwaltung** aus. Klicken Sie im rechten Bereich unter **Dienste verwalten** auf **Microsoft Azure-Verwaltungsportal**, um sich als Azure-Administrator Ihres Kunden anzumelden.

    ![Anmelden beim Azure-Portal](./media/storsimple-partner-csp-deploy/image9.png)

8. Um einen StorSimple-Geräte-Manager zu erstellen, klicken Sie auf **+ Neu** und wechseln zu **Virtuelle StorSimple-Geräteserie**. Weitere Informationen finden Sie unter [Bereitstellen eines StorSimple-Geräte-Manager-Diensts](storsimple-virtual-array-manage-service.md).

    ![Erstellen des StorSimple-Geräte-Manager-Diensts](./media/storsimple-partner-csp-deploy/image8.png)

## <a name="next-steps"></a>Nächste Schritte

- Wenn Sie weitere Fragen zu StorSimple im CSP-Programm haben, fahren Sie mit [StorSimple im CSP-Programm: Häufig gestellte Fragen](storsimple-partner-csp-faq.md) fort.
- Wenn Sie zur Bereitstellung Ihrer StorSimple-Lösung bereit sind, lesen Sie unter [Deploy StorSimple Virtual Array for Cloud Solution Provider Program](storsimple-partner-csp-deploy.md) (Bereitstellen von StorSimple Virtual Array für das Programm für Cloudlösungsanbieter) weiter.
