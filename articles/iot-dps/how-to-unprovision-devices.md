---
title: Aufheben der Bereitstellung von Geräten, die mit Azure IoT Hub Device Provisioning Service bereitgestellt wurden
description: Aufheben der Bereitstellung von Geräten, die mit Azure IoT Hub Device Provisioning Service (DPS) bereitgestellt wurden
author: wesmc7777
ms.author: wesmc
ms.date: 05/11/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 37099b551e004ebfc702dce37c2a1499aa46ff10
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950722"
---
# <a name="how-to-deprovision-devices-that-were-previously-auto-provisioned"></a>Aufheben der Bereitstellung von Geräten, die zuvor automatisch bereitgestellt wurden 

Manchmal muss unter Umständen die Bereitstellung von Geräten aufgehoben werden, die zuvor automatisch über den Device Provisioning-Dienst bereitgestellt wurden. Dies kann beispielsweise erforderlich sein, wenn ein Gerät verkauft oder in eine andere IoT Hub-Instanz verschoben wird oder wenn es verloren gegangen ist, gestohlen wurde oder anderweitig gefährdet ist. 

Die Aufhebung der Bereitstellung eines Geräts umfasst im Allgemeinen zwei Schritte:

1. Heben Sie die Registrierung des Geräts bei Ihrem Bereitstellungsdienst auf, um weiter automatische Bereitstellungen zu verhindern. Ein Registrierungseintrag kann deaktiviert oder gelöscht werden – je nachdem, ob Sie den Zugriff vorübergehend oder dauerhaft widerrufen möchten. Für Geräte mit X.509-Nachweis empfiehlt es sich gegebenenfalls, einen Eintrag in der Hierarchie Ihrer vorhandenen Registrierungsgruppen zu deaktivieren bzw. zu löschen.  
 
   - Informationen zum Aufheben der Registrierung eines Geräts finden Sie unter [Aufheben der Registrierung eines Geräts bei IoT Hub Device Provisioning Service](how-to-revoke-device-access-portal.md).
   - Unter [Gewusst wie: Verwalten von Geräteregistrierungen mit Azure Device Provisioning Service-SDKs](how-to-manage-enrollments-sdks.md) erfahren Sie, wie Sie die Registrierung eines Geräts programmgesteuert mithilfe eines der Bereitstellungsdienst-SDKs aufheben.

2. Heben Sie die Registrierung des Geräts bei Ihrer IoT Hub-Instanz auf, um eine weitere Kommunikation sowie weitere Datenübertragungen zu verhindern. Auch hier können Sie den Eintrag des Geräts in der Identitätsregistrierung für die IoT Hub-Instanz, in der es bereitgestellt wurde, entweder vorübergehend deaktivieren oder dauerhaft löschen. Weitere Informationen zur Deaktivierung finden Sie unter [Deaktivieren von Geräten](../iot-hub/iot-hub-devguide-identity-registry.md#disable-devices). Ihre IoT Hub-Ressource finden Sie im [Azure-Portal](https://portal.azure.com) unter „Geräteverwaltung“ > „IoT-Geräte“.

Die genaue Vorgehensweise zum Aufheben der Bereitstellung eines Geräts hängt von dessen Nachweismechanismus und dem zugehörigen Registrierungseintrag in Ihrem Bereitstellungsdienst ab. Die folgenden Abschnitte geben einen Überblick über den Prozess auf der Grundlage von Registrierungs- und Nachweistyp.

## <a name="individual-enrollments"></a>Individuelle Registrierungen
Geräte mit TPM-Nachweis oder X.509-Nachweis und einem untergeordneten Zertifikat werden über einen individuellen Registrierungseintrag bereitgestellt. 

So heben Sie die Bereitstellung eines Geräts auf, das über eine individuelle Registrierung verfügt: 

1. Heben Sie die Registrierung des Geräts bei Ihrem Bereitstellungsdienst auf:

   - Bei Verwendung von Geräten mit TPM-Nachweis löschen Sie den individuellen Registrierungseintrag, um die Zugriffsberechtigung des Geräts auf den Bereitstellungsdienst dauerhaft zu widerrufen. Alternativ können Sie den Eintrag auch deaktivieren, um die Zugriffsberechtigung nur vorübergehend zu widerrufen. 
   - Für Geräte mit X.509-Nachweis können Sie den Eintrag entweder löschen oder deaktivieren. Bedenken Sie jedoch, dass das Gerät erneut registriert werden kann, wenn Sie eine individuelle Registrierung für ein Gerät mit X.509 löschen und eine aktivierte Registrierungsgruppe für ein Signaturzertifikat in der Vertrauenskette des Geräts vorhanden ist. Für solche Geräte kann es sicherer sein, den Registrierungseintrag zu deaktivieren. Dadurch wird eine erneute Registrierung des Geräts verhindert, unabhängig davon, ob eine aktivierte Registrierungsgruppe für eines der Signaturzertifikate vorhanden ist.

2. Deaktivieren oder löschen Sie das Gerät in der Identitätsregistrierung für die IoT Hub-Instanz, in der es bereitgestellt wurde. 


## <a name="enrollment-groups"></a>Registrierungsgruppen
Bei Verwendung des X.509-Nachweises können Geräte auch über eine Registrierungsgruppe bereitgestellt werden. Registrierungsgruppen werden mit einem Signaturzertifikat (Zwischenzertifikat oder Zertifikat der Stammzertifizierungsstelle) konfiguriert und steuern den Zugriff auf den Bereitstellungsdienst für Geräte mit diesem Zertifikat in ihrer Zertifikatkette. Weitere Informationen zu Registrierungsgruppen und X.509-Zertifikaten mit dem Bereitstellungsdienst finden Sie unter [X.509-Zertifikatnachweis](concepts-x509-attestation.md). 

Eine Liste mit Geräten, die über eine Registrierungsgruppe bereitgestellt wurden, finden Sie in den Details der Registrierungsgruppe. So können Sie ganz einfach nachvollziehen, für welche IoT Hub-Instanz die einzelnen Geräte bereitgestellt wurden. So zeigen Sie die Geräteliste an: 

1. Melden Sie sich beim Azure-Portal an, und klicken Sie im linken Menü auf **Alle Ressourcen**.
2. Klicken Sie in der Ressourcenliste auf Ihren Bereitstellungsdienst.
3. Klicken Sie in Ihrem Bereitstellungsdienst auf **Registrierungen verwalten**, und wählen Sie dann die Registerkarte **Registrierungsgruppen**.
4. Klicken Sie auf die Registrierungsgruppe, um sie zu öffnen.

   ![Anzeigen des Registrierungsgruppeneintrags über das Portal](./media/how-to-unprovision-devices/view-enrollment-group.png)

Mit Registrierungsgruppen sind zwei Szenarien denkbar:

- So heben Sie die Bereitstellung aller Geräte auf, die über eine Registrierungsgruppe bereitgestellt wurden:
  1. Deaktivieren Sie die Registrierungsgruppe, um die Zulassung ihres Signaturzertifikats aufzuheben. 
  2. Verwenden Sie die Liste bereitgestellter Geräte für diese Registrierungsgruppe, um jedes Gerät aus der Identitätsregistrierung der entsprechenden IoT Hub-Instanz zu deaktivieren oder zu löschen. 
  3. Nach dem Deaktivieren oder Löschen aller Geräte aus den entsprechenden IoT Hub-Instanzen können Sie optional die Registrierungsgruppe löschen. Hinweis: Wenn Sie die Registrierungsgruppe löschen und für ein übergeordnetes Signaturzertifikat in der Zertifikatkette mindestens eines der Geräte eine aktivierte Registrierungsgruppe vorhanden ist, können sich die entsprechenden Geräte erneut registrieren. 

- So heben Sie die Bereitstellung eines einzelnen Geräts aus einer Registrierungsgruppe auf:
  1. Erstellen Sie eine deaktivierte individuelle Registrierung für das untergeordnete Zertifikat (des Geräts). Dadurch wird er Zugriff auf den Bereitstellungsdienst für dieses Gerät aufgehoben. Anderen Geräten, deren Zertifikatkette das Signaturzertifikat der Registrierungsgruppe enthält, wird dagegen weiterhin Zugriff gewährt. Löschen Sie die deaktivierte individuelle Registrierung für das Gerät nicht. Andernfalls kann sich das Gerät über die Registrierungsgruppe erneut registrieren. 
  2. Ermitteln Sie anhand der Liste bereitgestellter Geräte für die Registrierungsgruppe die IoT Hub-Instanz, für die das Gerät bereitgestellt wurde. Anschließend deaktivieren Sie das Gerät oder löschen es aus der Identitätsregistrierung des Hubs.