---
title: Verwalten von Geräteregistrierungen mit dem Azure-Portal | Microsoft-Dokumentation
description: 'Gewusst wie: Verwalten der Geräteregistrierungen für Ihren Device Provisioning-Dienst im Azure-Portal'
author: wesmc7777
ms.author: wesmc
ms.date: 04/05/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.openlocfilehash: 51b072bfd0827528a5504133dff8c1cdd7a7ca86
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "62122764"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Verwalten von Geräteregistrierungen mit dem Azure-Portal

Eine *Geräteregistrierung* erstellt einen Datensatz eines einzelnen Geräts oder einer Gruppe von Geräten, die sich zu einem beliebigen Zeitpunkt beim Azure IoT Hub Device Provisioning-Dienst registrieren können. Der Registrierungsdatensatz enthält die erste gewünschte Konfiguration für die Geräte im Rahmen dieser Registrierung, einschließlich des gewünschten IoT Hubs. Dieser Artikel zeigt Ihnen die Verwaltung von Geräteregistrierungen für Ihren Bereitstellungsdienst.


## <a name="create-a-device-enrollment"></a>Erstellen einer Geräteregistrierung

Sie haben zwei Möglichkeiten, Ihre Geräte beim Bereitstellungsdienst zu registrieren:

* Eine **Registrierungsgruppe** ist ein Eintrag für eine Gruppe von Geräten, für die ein gemeinsamer Nachweismechanismus mit X.509-Zertifikaten verwendet wird, die durch dasselbe Signaturzertifikat signiert werden. Hierbei kann es sich um das [Stammzertifikat](https://docs.microsoft.com/azure/iot-dps/concepts-security#root-certificate) oder das [Zwischenzertifikat](https://docs.microsoft.com/azure/iot-dps/concepts-security#intermediate-certificate) handeln, das zum Erstellen des Gerätezertifikats auf einem physischen Gerät verwendet wird. Es empfiehlt sich, eine Registrierungsgruppe für eine große Anzahl von Geräten, die eine gewünschte Erstkonfiguration gemeinsam nutzen, oder für Geräte zu verwenden, die alle demselben Mandanten zugeordnet sind. Beachten Sie, dass Sie nur Geräte registrieren können, die den X. 509-Nachweismechanismus als *Registrierungsgruppen* verwenden. 

    Sie können mit den folgenden Schritten im Portal eine Registrierungsgruppe für eine Gruppe von Geräten erstellen:

  1. Melden Sie sich beim Azure-Portal an, und klicken Sie im linken Menü auf **Alle Ressourcen**.  
  1. Klicken Sie auf den Gerätebereitstellungsdienst, bei dem Sie Ihr Gerät aus der Liste der Ressourcen registrieren möchten.  
  1. Gehen Sie in Ihrem Bereitstellungsdienst so vor:  
     a. Klicken Sie auf **Registrierungen verwalten**, und wählen Sie dann die Registerkarte **Registrierungsgruppen** aus.  
     b. Klicken Sie ganz oben auf die Schaltfläche **Hinzufügen**.  
     c. Wenn der Bereich „Registrierungsgruppe hinzufügen“ angezeigt wird, geben Sie die Informationen für den Registrierungslisteneintrag ein.  Der **Gruppenname** ist erforderlich. Wählen Sie außerdem für **Zertifikattyp** entweder „Zertifizierungsstelle“ oder „Zwischenzertifizierungsstelle“ aus, und laden Sie das **primäre Stammzertifikat** für die Gerätegruppe hoch.  
     d. Klicken Sie auf **Speichern**. Bei der erfolgreichen Erstellung Ihrer Registrierungsgruppe wird der Gruppenname unter der Registerkarte **Registrierungsgruppen** angezeigt.  

     [![Registrierungsgruppe im Portal](./media/how-to-manage-enrollments/group-enrollment.png)](./media/how-to-manage-enrollments/group-enrollment.png#lightbox)
    

* Eine **individuelle Registrierung** ist ein Eintrag für ein einzelnes Gerät, das registriert werden kann. Individuelle Registrierungen verwenden als Nachweismechanismen entweder X.509-Zertifikate oder SAS-Token (in einem physischen oder virtuellen TPM). Individuelle Registrierungen sollten für Geräte, die besondere Erstkonfigurationen erfordern, oder Geräte verwendet werden, die nur SAS-Tokens über das TPM oder das virtuelle TPM als Nachweismechanismus verwenden können. Bei individuellen Registrierungen ist möglicherweise die gewünschte IoT Hub-Geräte-ID angegeben.

    Sie können mit den folgenden Schritten eine individuelle Registrierung im Portal erstellen:

    1. Melden Sie sich beim Azure-Portal an, und klicken Sie im linken Menü auf **Alle Ressourcen**.
    1. Klicken Sie auf den Gerätebereitstellungsdienst, bei dem Sie Ihr Gerät aus der Liste der Ressourcen registrieren möchten.
    1. Gehen Sie in Ihrem Bereitstellungsdienst so vor:  
       a. Klicken Sie auf **Registrierungen verwalten**, und wählen Sie die Registerkarte **Individuelle Registrierungen** aus.  
       b. Klicken Sie ganz oben auf die Schaltfläche **Hinzufügen**.   
       c. Wenn der Bereich „Registrierung hinzufügen“ angezeigt wird, geben Sie die Informationen für den Registrierungslisteneintrag ein. Wählen Sie zuerst den **Mechanismus** für den Nachweis des Geräts (X.509 oder TPM) aus. Der X.509-Nachweis erfordert, dass Sie **primäre Zertifikat** für das Gerät hochladen. TPM erfordert die Eingabe des **Nachweisschlüssels** und der **Registrierungs-ID** für das Gerät.  
       d. Klicken Sie auf **Speichern**. Bei der erfolgreichen Erstellung Ihrer Registrierungsgruppe sollte Ihr Gerät unter der Registerkarte **Individuelle Registrierungen** angezeigt werden.  

       [![Individuelle Registrierung im Portal](./media/how-to-manage-enrollments/individual-enrollment.png)](./media/how-to-manage-enrollments/individual-enrollment.png#lightbox)

## <a name="update-an-enrollment-entry"></a>Aktualisieren eines Registrierungseintrags
Sie können mit den folgenden Schritten einen vorhandenen Registrierungseintrag im Portal aktualisieren:

1. Öffnen Sie Ihren Gerätebereitstellungsdienst im Azure-Portal, und klicken Sie auf **Registrierungen verwalten**. 
1. Navigieren Sie zu dem Registrierungseintrag, den Sie ändern möchten. Klicken Sie auf den Eintrag, worauf zusammenfassende Informationen zu Ihrer Geräteregistrierung geöffnet werden. 
1. Auf dieser Seite können Sie Elemente außer Sicherheitstyp und Anmeldeinformationen ändern, z.B. den IoT Hub, mit dem das Gerät verknüpft werden soll, sowie die Geräte-ID. Sie können auch den anfänglichen Gerätezwillingsstatus ändern. 
1. Klicken Sie bei Abschluss auf **Speichern**, um die Geräteregistrierung zu aktualisieren. 

    ![Aktualisieren der Registrierung im Portal](./media/how-to-manage-enrollments/update-enrollment.png)

## <a name="remove-a-device-enrollment"></a>Entfernen einer Geräteregistrierung
In Fällen, in denen Ihre Geräte nicht in einem IoT Hub bereitgestellt werden müssen, können Sie mit den folgenden Schritten den zugehörigen Registrierungseintrag im Portal entfernen:

1. Öffnen Sie Ihren Gerätebereitstellungsdienst im Azure-Portal, und klicken Sie auf **Registrierungen verwalten**. 
1. Navigieren Sie zu dem Registrierungseintrag, den Sie entfernen möchten, und wählen Sie ihn aus. 
1. Klicken Sie ganz oben auf die Schaltfläche **Löschen**, und bestätigen Sie nach Aufforderung mit **Ja**. 
1. Sobald die Aktion abgeschlossen ist, sehen Sie, dass Ihr Eintrag aus der Geräteregistrierungsliste entfernt wurde. 
 
    ![Entfernen der Registrierung im Portal](./media/how-to-manage-enrollments/remove-enrollment.png)


