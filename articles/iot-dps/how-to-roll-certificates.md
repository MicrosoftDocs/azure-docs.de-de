---
title: Rollen von X.509-Zertifikaten in Azure IoT Hub Device Provisioning Service
description: Rollen von X.509-Zertifikaten mit Ihrer Device Provisioning Service-Instanz (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 08/06/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: bf8b1e04e11dee4e636826430838a467fe034e3f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94951127"
---
# <a name="how-to-roll-x509-device-certificates"></a>Rollen von X.509-Gerätezertifikaten

Während des Lebenszyklus Ihrer IoT-Lösung müssen Sie Zertifikate „rollen“ (festlegen, dass ein neues Zertifikat ab einem bestimmten Zeitpunkt zum aktuellen Zertifikat wird). Zwei der wichtigsten Gründe für das Rollen von Zertifikaten sind Sicherheitsverletzungen und der Ablauf von Zertifikaten. 

Das Rollen von Zertifikaten ist eine bewährte Sicherheitsmethode, um Ihr System im Falle einer Sicherheitsverletzung zu schützen. Als Teil der [Assume Breach-Methodik](https://download.microsoft.com/download/C/1/9/C1990DBA-502F-4C2A-848D-392B93D9B9C3/Microsoft_Enterprise_Cloud_Red_Teaming.pdf) (von einer Sicherheitsverletzung ausgehen) betont Microsoft neben vorbeugenden Maßnahmen die Notwendigkeit von reaktiven Sicherheitsprozessen. Das Rollen Ihrer Gerätezertifikate sollte ein Bestandteil dieser Sicherheitsprozesse sein. Die Häufigkeit, mit der Sie die Zertifikate rollen, hängt von den Sicherheitsanforderungen Ihrer Lösung ab. Kunden, deren Lösungen sensible Daten beinhalten, rollen Zertifikate möglicherweise täglich, während andere ihre Zertifikate alle zwei Jahre rollen.

Beim Rollen von Gerätezertifikaten müssen das auf dem Gerät gespeicherte Zertifikat und der IoT Hub aktualisiert werden. Anschließend kann das Gerät sich selbst mit dem IoT Hub anhand der normalen [Bereitstellung](about-iot-dps.md#provisioning-process) mit Device Provisioning Service (DPS) erneut bereitstellen.


## <a name="obtain-new-certificates"></a>Abrufen neuer Zertifikate

Zum Abrufen neuer Zertifikate für Ihre IoT-Geräte stehen Ihnen viele Methoden zur Auswahl. Dazu zählen das Abrufen von Zertifikaten aus der Geräte-Factory, das Generieren Ihrer eigenen Zertifikate und die Verwaltung der Zertifikaterstellung durch einen Drittanbieter. 

Zertifikate werden voneinander signiert, um eine Vertrauenskette von einem Stammzertifizierungsstellenzertifikat zu einem [untergeordneten Zertifikat](concepts-x509-attestation.md#end-entity-leaf-certificate) zu bilden. Ein Signaturzertifikat ist das Zertifikat, das zum Signieren des untergeordneten Zertifikats am Ende der Vertrauenskette verwendet wird. Bei einem Signaturzertifikat kann es sich um ein Stammzertifizierungsstellenzertifikat oder ein Zwischenzertifikat in der Vertrauenskette handeln. Weitere Informationen finden Sie unter [X.509-Zertifikate](concepts-x509-attestation.md#x509-certificates).
 
Zum Abrufen eines Signaturzertifikats stehen zwei Methoden zur Auswahl. Die erste Methode, die für Produktionssysteme empfohlen wird, ist der Kauf eines Signaturzertifikats von einer Stammzertifizierungsstelle (ZS). Auf diese Weise wird die Sicherheit an eine vertrauenswürdige Quelle gebunden. 

Die zweite Methode ist die Erstellung eigener X.509-Zertifikate mit einem Tool wie OpenSSL. Dieser Ansatz eignet sich hervorragend zum Testen von X.509-Zertifikaten, bietet jedoch wenig Garantien im Hinblick auf die Sicherheit. Wir empfehlen, diesen Ansatz nur für Tests zu verwenden, es sei denn, Sie sind in der Lage, als Ihr eigener ZS-Anbieter zu fungieren.
 

## <a name="roll-the-certificate-on-the-device"></a>Rollen des Zertifikats auf dem Gerät

Zertifikate auf einem Gerät sollten immer an einem sicheren Ort gespeichert werden, beispielsweise in einem [Hardwaresicherheitsmodul (HSM)](concepts-service.md#hardware-security-module). Die Art und Weise, wie Sie Gerätezertifikate rollen, hängt in erster Linie davon ab, wie sie erstellt und auf den Geräten installiert wurden. 

Wenn Ihre Zertifikate von einem Drittanbieter stammen, müssen Sie überprüfen, wie dieser seine Zertifikate rollt. Der Prozess kann in Ihrer Vereinbarung mit dem Drittanbieter enthalten oder als separater Dienst verfügbar sein. 

Wenn Sie Ihre eigenen Gerätezertifikate verwalten, müssen Sie eine eigene Pipeline für die Aktualisierung von Zertifikaten erstellen. Stellen Sie sicher, dass sowohl alte als auch neue untergeordnete Zertifikate denselben allgemeinen Namen (Common Name, CN) aufweisen. Durch die Verwendung desselben allgemeinen Namens kann das Gerät sich selbst erneut bereitstellen, ohne einen doppelten Registrierungseintrag zu erstellen. 


## <a name="roll-the-certificate-in-the-iot-hub"></a>Rollen des Zertifikats im IoT Hub

Das Gerätezertifikat kann einem IoT Hub manuell hinzugefügt werden. Das Zertifikat kann auch mithilfe einer Device Provisioning Service-Instanz automatisiert werden. In diesem Artikel gehen wir davon aus, dass zur Unterstützung der automatischen Bereitstellung eine Device Provisioning Service-Instanz verwendet wird.

Wenn ein Gerät zunächst mittels automatischer Bereitstellung bereitgestellt wird, wird es gestartet und stellt eine Verbindung mit Provisioning Service her. Provisioning Service antwortet, indem er vor dem Erstellen einer Geräteidentität in einem IoT Hub eine Identitätsüberprüfung ausführt, bei der das untergeordnete Zertifikat des Geräts als Anmeldeinformation verwendet wird. Provisioning Service teilt dem Gerät anschließend mit, welchem IoT Hub es zugewiesen ist. Das Gerät verwendet dann sein untergeordnetes Zertifikat, um sich zu authentifizieren und eine Verbindung mit dem IoT Hub herzustellen. 

Sobald ein neues untergeordnetes Zertifikat auf das Gerät gerollt wurde, kann das Gerät keine Verbindung mit dem IoT Hub mehr herstellen, da es ein neues Zertifikat zur Verbindungsherstellung verwendet. Der IoT Hub erkennt nur das Gerät mit dem alten Zertifikat. Der Verbindungsversuch des Geräts führt zu einem Verbindungsfehler vom Typ „nicht autorisiert“. Um diesen Fehler zu beheben, müssen Sie den Registrierungseintrag für das Gerät entsprechend dem neuen untergeordneten Zertifikat des Geräts aktualisieren. Anschließend kann Provisioning Service bei der erneuten Bereitstellung des Geräts die IoT Hub-Geräteregistrierungsinformationen wie erforderlich aktualisieren. 

Eine mögliche Ausnahme, bei der dieser Verbindungsfehler nicht auftritt, wäre ein Szenario, in dem Sie eine [Registrierungsgruppe](concepts-service.md#enrollment-group) für Ihr Gerät in Provisioning Service erstellt haben. Rollen Sie in diesem Fall die Stamm- oder Zwischenzertifikate in der Vertrauenskette des Geräts nicht, wird das Gerät erkannt, sofern das neue Zertifikat Teil der in der Registrierungsgruppe definierten Vertrauenskette ist. Falls dieses Szenario infolge einer Sicherheitsverletzung auftritt, sollten Sie zumindest die von der Sicherheitsverletzung betroffenen Gerätezertifikate in der Gruppe nicht zulassen. Weitere Informationen finden Sie unter [Verweigern bestimmter Geräte in einer Registrierungsgruppe](./how-to-revoke-device-access-portal.md#disallow-specific-devices-in-an-enrollment-group).

Die Registrierungseinträge für gerollte Zertifikate werden auf der Seite **Registrierungen verwalten** aktualisiert. Gehen Sie wie folgt vor, um auf diese Seite zuzugreifen:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und navigieren Sie zu der IoT Hub Device Provisioning Service-Instanz mit dem Registrierungseintrag für Ihr Gerät.

2. Klicken Sie auf **Registrierungen verwalten**.

    ![Verwalten von Registrierungen](./media/how-to-roll-certificates/manage-enrollments-portal.png)


Wie Sie den Registrierungseintrag aktualisieren, hängt davon ab, ob Sie individuelle Registrierungen oder Gruppenregistrierungen verwenden. Zudem unterscheiden sich die empfohlenen Verfahren abhängig davon, ob Sie Zertifikate aufgrund einer Sicherheitsverletzung oder aufgrund des Zertifikatablaufs rollen. In den folgenden Abschnitten wird beschrieben, wie Sie diese Aktualisierungen vornehmen.


## <a name="individual-enrollments-and-security-breaches"></a>Individuelle Registrierungen und Sicherheitsverletzungen

Wenn Sie Zertifikate als Reaktion auf eine Sicherheitsverletzung rollen, sollten Sie die folgende Methode verwenden, bei der das aktuelle Zertifikat sofort gelöscht wird:

1. Klicken Sie auf **Individuelle Registrierungen**, und klicken Sie dann in der Liste auf den Eintrag für die Registrierungs-ID. 

2. Klicken Sie auf die Schaltfläche **Delete current certificate** (Aktuelles Zertifikat löschen) und anschließend auf das Ordnersymbol, um das neue Zertifikat auszuwählen, das für den Registrierungseintrag hochgeladen werden soll. Klicken Sie abschließend auf **Speichern**.

    Diese Schritte müssen für das primäre und das sekundäre Zertifikat ausgeführt werden, sofern beide kompromittiert wurden.

    ![Verwalten einzelner Registrierungen bei einer Sicherheitsverletzung](./media/how-to-roll-certificates/manage-individual-enrollments-portal.png)

3. Nachdem das kompromittierte Zertifikat aus dem Provisioning-Dienst entfernt wurde, kann es weiterhin verwendet werden, um Geräteverbindungen mit dem IoT-Hub herzustellen – sofern dort eine Geräteregistrierung dafür vorhanden ist. Es gibt zwei Vorgehensweisen: 

    Die erste Möglichkeit ist das manuelle Navigieren zu Ihrem IoT-Hub und das sofortige Entfernen der Geräteregistrierung, die dem kompromittierten Zertifikat zugeordnet ist. Wenn das Gerät dann mit einem aktualisierten Zertifikat erneut bereitgestellt wird, wird eine neue Geräteregistrierung erstellt.     

    ![Entfernen der IoT Hub-Geräteregistrierung](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Die zweite Möglichkeit ist die Verwendung der Unterstützung für die erneute Bereitstellung, um das Gerät auf demselben IoT-Hub erneut bereitzustellen. Dieser Ansatz kann genutzt werden, um das Zertifikat für die Geräteregistrierung auf dem IoT-Hub zu ersetzen. Weitere Informationen finden Sie unter [How to reprovision devices](how-to-reprovision.md) (Erneutes Bereitstellen von Geräten).

## <a name="individual-enrollments-and-certificate-expiration"></a>Individuelle Registrierungen und Zertifikatablauf

Wenn Sie Zertifikate rollen, weil Zertifikate ablaufen, sollten Sie die Konfiguration des sekundären Zertifikats wie folgt verwenden, um die Downtime für Geräte zu reduzieren, die sich bereitzustellen versuchen.

Wenn später auch das sekundäre Zertifikat bald abläuft und gerollt werden muss, können Sie zur Verwendung der primären Konfiguration wechseln. Ein solcher Wechsel zwischen primärem und sekundärem Zertifikat reduziert die Downtime für Geräte, die sich bereitzustellen versuchen.


1. Klicken Sie auf **Individuelle Registrierungen**, und klicken Sie dann in der Liste auf den Eintrag für die Registrierungs-ID. 

2. Klicken Sie auf die Schaltfläche **Sekundäres Zertifikat** und anschließend auf das Ordnersymbol, um das neue Zertifikat auszuwählen, das für den Registrierungseintrag hochgeladen werden soll. Klicken Sie auf **Speichern**.

    ![Verwalten individueller Registrierungen mit dem Ablauf des sekundären Zertifikats](./media/how-to-roll-certificates/manage-individual-enrollments-secondary-portal.png)

3. Später, wenn das primäre Zertifikat abgelaufen ist, kehren Sie zurück, und löschen Sie das primäre Zertifikat, indem Sie auf die Schaltfläche **Delete current certificate** (Aktuelles Zertifikat löschen) klicken.

## <a name="enrollment-groups-and-security-breaches"></a>Registrierungsgruppen und Sicherheitsverletzungen

Um eine Gruppenregistrierung als Reaktion auf eine Sicherheitsverletzung zu aktualisieren, sollten Sie einen der folgenden Ansätze verwenden, bei denen das aktuelle Stammzertifizierungsstellen- oder Zwischenzertifikat sofort gelöscht wird.

#### <a name="update-compromised-root-ca-certificates"></a>Aktualisieren kompromittierter Stammzertifizierungsstellenzertifikate

1. Klicken Sie auf die Registerkarte **Zertifikate** für Ihre Device Provisioning Service-Instanz.

2. Klicken Sie in der Liste auf das kompromittierte Zertifikat und anschließend auf die Schaltfläche **Löschen**. Bestätigen Sie den Löschvorgang, indem Sie den Namen des Zertifikats eingeben und auf **OK** klicken. Wiederholen Sie diesen Vorgang für alle kompromittierten Zertifikate.

    ![Löschen des Stammzertifizierungsstellenzertifikats](./media/how-to-roll-certificates/delete-root-cert.png)

3. Führen Sie die unter [Konfigurieren überprüfter Zertifizierungsstellenzertifikate](how-to-verify-certificates.md) beschriebenen Schritte aus, um neue Stammzertifizierungsstellenzertifikate hinzuzufügen und zu überprüfen.

4. Klicken Sie auf die Registerkarte **Registrierungen verwalten** für Ihre Device Provisioning Service-Instanz, und klicken Sie dann auf die Liste **Registrierungsgruppen**. Klicken Sie in der Liste auf den Namen Ihrer Registrierungsgruppe.

5. Klicken Sie auf **Zertifizierungsstellenzertifikat**, und wählen Sie das neue Stammzertifizierungsstellenzertifikat aus. Klicken Sie anschließend auf **Speichern**. 

    ![Auswählen des neuen Stammzertifizierungsstellenzertifikats für ein kompromittiertes Zertifikat](./media/how-to-roll-certificates/select-new-root-cert.png)

6. Nachdem das kompromittierte Zertifikat aus dem Provisioning-Dienst entfernt wurde, kann es weiterhin verwendet werden, um Geräteverbindungen mit dem IoT-Hub herzustellen – sofern dort Geräteregistrierungen dafür vorhanden sind. Es gibt zwei Vorgehensweisen: 

    Die erste Möglichkeit ist das manuelle Navigieren zu Ihrem IoT-Hub und das sofortige Entfernen der Geräteregistrierung, die dem kompromittierten Zertifikat zugeordnet ist. Wenn Ihre Geräte dann mit aktualisierten Zertifikaten erneut bereitgestellt wurden, wird jeweils eine neue Geräteregistrierung erstellt.     

    ![Entfernen der IoT Hub-Geräteregistrierung](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Die zweite Möglichkeit ist die Verwendung der Unterstützung für die erneute Bereitstellung, um Ihre Geräte auf demselben IoT-Hub erneut bereitzustellen. Dieser Ansatz kann verwendet werden, um Zertifikate für Geräteregistrierungen auf dem IoT-Hub zu ersetzen. Weitere Informationen finden Sie unter [How to reprovision devices](how-to-reprovision.md) (Erneutes Bereitstellen von Geräten).



#### <a name="update-compromised-intermediate-certificates"></a>Aktualisieren kompromittierter Zwischenzertifikate

1. Klicken Sie auf **Registrierungsgruppen**, und klicken Sie dann in der Liste auf den Gruppennamen. 

2. Klicken Sie auf **Intermediate Certificate** (Zwischenzertifikat) und anschließend auf **Delete current certificate** (Aktuelles Zertifikat löschen). Klicken Sie auf das Ordnersymbol, um zu dem neuen Zwischenzertifikat zu navigieren, das für die Registrierungsgruppe hochgeladen werden soll. Klicken Sie abschließend auf **Speichern**. Diese Schritte müssen sowohl für das primäre als auch das sekundäre Zertifikat ausgeführt werden, sofern beide kompromittiert wurden.

    Das neue Zwischenzertifikat muss von einem überprüften Stammzertifizierungsstellenzertifikat signiert sein, das bereits in Provisioning Service hinzugefügt wurde. Weitere Informationen finden Sie unter [X.509-Zertifikate](concepts-x509-attestation.md#x509-certificates).

    ![Verwalten individueller Registrierungen für ein kompromittiertes Zwischenzertifikat](./media/how-to-roll-certificates/enrollment-group-delete-intermediate-cert.png)


3. Nachdem das kompromittierte Zertifikat aus dem Provisioning-Dienst entfernt wurde, kann es weiterhin verwendet werden, um Geräteverbindungen mit dem IoT-Hub herzustellen – sofern dort Geräteregistrierungen dafür vorhanden sind. Es gibt zwei Vorgehensweisen: 

    Die erste Möglichkeit ist das manuelle Navigieren zu Ihrem IoT-Hub und das sofortige Entfernen der Geräteregistrierung, die dem kompromittierten Zertifikat zugeordnet ist. Wenn Ihre Geräte dann mit aktualisierten Zertifikaten erneut bereitgestellt wurden, wird jeweils eine neue Geräteregistrierung erstellt.     

    ![Entfernen der IoT Hub-Geräteregistrierung](./media/how-to-roll-certificates/remove-hub-device-registration.png)

    Die zweite Möglichkeit ist die Verwendung der Unterstützung für die erneute Bereitstellung, um Ihre Geräte auf demselben IoT-Hub erneut bereitzustellen. Dieser Ansatz kann verwendet werden, um Zertifikate für Geräteregistrierungen auf dem IoT-Hub zu ersetzen. Weitere Informationen finden Sie unter [How to reprovision devices](how-to-reprovision.md) (Erneutes Bereitstellen von Geräten).


## <a name="enrollment-groups-and-certificate-expiration"></a>Registrierungsgruppen und Zertifikatablauf

Wenn Sie Zertifikate rollen, weil Zertifikate ablaufen, sollten Sie die Konfiguration des sekundären Zertifikats wie folgt verwenden, um sicherzustellen, dass keine Downtime für Geräte auftritt, die sich bereitzustellen versuchen.

Wenn später auch das sekundäre Zertifikat bald abläuft und gerollt werden muss, können Sie zur Verwendung der primären Konfiguration wechseln. Ein solcher Wechsel zwischen primärem und sekundärem Zertifikat stellt sicher, dass keine Downtime für Geräte auftritt, die sich bereitzustellen versuchen. 

#### <a name="update-expiring-root-ca-certificates"></a>Aktualisieren ablaufender Stammzertifizierungsstellenzertifikate

1. Führen Sie die unter [Konfigurieren überprüfter Zertifizierungsstellenzertifikate](how-to-verify-certificates.md) beschriebenen Schritte aus, um neue Stammzertifizierungsstellenzertifikate hinzuzufügen und zu überprüfen.

2. Klicken Sie auf die Registerkarte **Registrierungen verwalten** für Ihre Device Provisioning Service-Instanz, und klicken Sie dann auf die Liste **Registrierungsgruppen**. Klicken Sie in der Liste auf den Namen Ihrer Registrierungsgruppe.

3. Klicken Sie auf **Zertifizierungsstellenzertifikat**, und wählen Sie das neue Stammzertifizierungsstellenzertifikat unter der Konfiguration **Sekundäres Zertifikat** aus. Klicken Sie anschließend auf **Speichern**. 

    ![Auswählen des neuen Stammzertifizierungsstellenzertifikats zum Ablauf](./media/how-to-roll-certificates/select-new-root-secondary-cert.png)

4. Später, wenn das primäre Zertifikat abgelaufen ist, klicken Sie auf die Registerkarte **Zertifikate** für Ihre Device Provisioning Service-Instanz. Klicken Sie in der Liste auf das abgelaufene Zertifikat und anschließend auf die Schaltfläche **Löschen**. Bestätigen Sie den Löschvorgang, indem Sie den Namen des Zertifikats eingeben und auf **OK** klicken.

    ![Löschen des Stammzertifizierungsstellenzertifikats](./media/how-to-roll-certificates/delete-root-cert.png)



#### <a name="update-expiring-intermediate-certificates"></a>Aktualisieren ablaufender Zwischenzertifikate


1. Klicken Sie auf **Registrierungsgruppen**, und klicken Sie dann in der Liste auf den Gruppennamen. 

2. Klicken Sie auf die Schaltfläche **Sekundäres Zertifikat** und anschließend auf das Ordnersymbol, um das neue Zertifikat auszuwählen, das für den Registrierungseintrag hochgeladen werden soll. Klicken Sie auf **Speichern**.

    Das neue Zwischenzertifikat muss von einem überprüften Stammzertifizierungsstellenzertifikat signiert sein, das bereits in Provisioning Service hinzugefügt wurde. Weitere Informationen finden Sie unter [X.509-Zertifikate](concepts-x509-attestation.md#x509-certificates).

   ![Verwalten von Registrierungsgruppen mit dem ablaufenden sekundären Zertifikats](./media/how-to-roll-certificates/manage-enrollment-group-secondary-portal.png)

3. Später, wenn das primäre Zertifikat abgelaufen ist, kehren Sie zurück, und löschen Sie das primäre Zertifikat, indem Sie auf die Schaltfläche **Delete current certificate** (Aktuelles Zertifikat löschen) klicken.


## <a name="reprovision-the-device"></a>Erneutes Bereitstellen des Geräts

Nachdem das Zertifikat auf dem Gerät und in Device Provisioning Service gerollt wurde, kann das Gerät sich selbst erneut bereitstellen, indem es eine Verbindung mit Device Provisioning Service herstellt. 

Eine einfache Möglichkeit zum Programmieren von Geräten zur erneuten Bereitstellung besteht darin, dass Gerät so zu programmieren, dass es zum Durchführen des Bereitstellungsablaufs eine Verbindung mit Provisioning Service herstellt, wenn es beim Verbindungsversuch mit dem IoT Hub einen Fehler vom Typ „nicht autorisiert“ empfängt.

Eine weitere Möglichkeit besteht darin, sowohl das alte als auch das neue Zertifikat für eine kurze Überlappungsdauer als gültige Zertifikate zu verwenden und mit dem IoT Hub einen Befehl an Geräte zu senden, damit sie sich zum Aktualisieren ihrer IoT Hub-Verbindungsinformationen über Provisioning Service erneut registrieren. Da jedes Gerät Befehle unterschiedlich verarbeiten kann, müssen Sie Ihr Gerät so programmieren, dass es beim Aufrufen des Befehls die richtige Aktion ausführt. Zum Steuern des Geräts über IoT Hub stehen verschiedene Möglichkeiten zur Verfügung. Wir empfehlen die Verwendung von [direkten Methoden](../iot-hub/iot-hub-devguide-direct-methods.md) oder [Aufträgen](../iot-hub/iot-hub-devguide-jobs.md), um den Prozess zu initiieren.

Sobald die erneute Bereitstellung abgeschlossen ist, können Geräte mit ihren neuen Zertifikaten eine Verbindung mit IoT Hub herstellen.


## <a name="disallow-certificates"></a>Verweigern von Zertifikaten

Es kann vorkommen, dass Sie ein Gerätezertifikat als Reaktion auf eine Sicherheitsverletzung verweigern müssen. Um ein Gerätezertifikat nicht zuzulassen, deaktivieren Sie den Registrierungseintrag für das Zielgerät/-zertifikat. Weitere Informationen zum Verweigern von Geräten finden Sie im Artikel [Verwalten der Registrierungsaufhebung](how-to-revoke-device-access-portal.md).

Sobald ein Zertifikat in einem deaktivierten Registrierungseintrag enthalten ist, schlagen alle mit diesem Zertifikat ausgeführten Versuche zur Registrierung bei einem IoT Hub fehl. Dies gilt selbst dann, wenn das Zertifikat in einem anderen Registrierungseintrag aktiviert ist.
 



## <a name="next-steps"></a>Nächste Schritte

- Weitere Informationen zu X.509-Zertifikaten in Device Provisioning Service finden Sie unter [X.509-Zertifikatnachweis](concepts-x509-attestation.md). 
- Informationen zum Ausführen eines Eigentumsnachweises für X.509-Stammzertifizierungsstellenzertifikate mit Azure IoT Hub Device Provisioning Service finden Sie unter [Überprüfen von Zertifikaten](how-to-verify-certificates.md).
- Weitere Informationen zum Erstellen einer Registrierungsgruppe mit dem Portal finden Sie unter [Verwalten von Geräteregistrierungen mit dem Azure-Portal](how-to-manage-enrollments.md).