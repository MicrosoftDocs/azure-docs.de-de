---
title: Verwenden von Tools, die in Device Provisioning-Dienst-SDKs von Azure IoT Hub zur Vereinfachung der Entwicklung bereitgestellt werden
description: In diesem Dokument werden die Tools behandelt, die in Device Provisioning-Dienst-SDKs von Azure IoT Hub für die Entwicklung bereitgestellt werden
author: yzhong94
ms.author: yizhon
ms.date: 04/09/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.openlocfilehash: dc8c29b1c7d4e5056cb6aeee6335e32687fd547f
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "60627320"
---
# <a name="how-to-use-tools-provided-in-the-sdks-to-simplify-development-for-provisioning"></a>Gewusst wie: Verwenden von Tools, die in den SDKs zur Vereinfachung der Entwicklung für die Bereitstellung zur Verfügung stehen
Der IoT Hub Device Provisioning-Dienst vereinfacht den Bereitstellungsprozess mit [automatischer Just-in-Time-Bereitstellung](concepts-auto-provisioning.md) ohne manuelles Eingreifen in sicherer und skalierbarer Weise.  Der Sicherheitsnachweis in Form eines X.509-Zertifikats oder von Trusted Platform Module (TPM) ist erforderlich.  Microsoft pflegt zur Verbesserung des Vertrauens in die Absicherung der IoT-Bereitstellung auch Partnerschaften mit [anderen Sicherheitshardwarepartnern](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/). Umfassende Kenntnisse über die Hardwaresicherheitsanforderungen zu besitzen kann für Entwickler eine große Herausforderung sein. Mit einem Satz von Provisioning-Dienst-SDKs von Azure IoT können Entwickler mühelos Clients erstellen, die mit dem Provisioning-Dienst kommunizieren. Die SDKs bieten auch Beispiele für häufige Szenarien sowie eine Reihe von Tools, die den Sicherheitsnachweis bei der Entwicklung vereinfachen.

## <a name="trusted-platform-module-tpm-simulator"></a>Simulator für Trusted Platform Module (TPM)
[TPM](https://docs.microsoft.com/azure/iot-dps/concepts-security) kann sich auf einen Standard zum sicheren Speichern von Schlüsseln, mit denen die Plattform authentifiziert wird, oder auf die E/A-Schnittstelle beziehen, die für die Interaktion mit den Modulen zum Implementieren des Standards verwendet wird. TPMs können als diskrete Hardware, integrierte Hardware, firmwarebasiert oder softwarebasiert vorhanden sein.  In der Produktion befindet sich TPM auf dem Gerät, entweder als diskrete Hardware, integrierte Hardware oder firmwarebasiert. In der Testphase steht Entwicklern ein softwarebasierter TPM-Simulator zur Verfügung.  Dieser Simulator ist derzeit nur für die Entwicklung auf der Windows-Plattform verfügbar.

In folgenden Schritten wird der TPM-Simulator verwendet:
1. [Vorbereiten der Entwicklungsumgebung](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) und Klonen des GitHub-Repositorys:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Wechseln Sie zum TPM-Simulatorordner unter ```azure-iot-sdk-java/provisioning/provisioning-tool/tpm-simulator/```.
3. Führen Sie vor der Ausführung einer Clientanwendung zur Bereitstellung des Geräts „Simulator.exe“ aus.
4. Führen Sie den Simulator während des gesamten Bereitstellungsprozesses im Hintergrund aus, um Registrierungs-ID und Endorsement Key abzurufen.  Beide Werte sind nur für eine Instanz der Ausführung gültig.

## <a name="x509-certificate-generator"></a>X.509-Zertifikatgenerator
[X.509-Zertifikate](https://docs.microsoft.com/azure/iot-dps/concepts-security#x509-certificates) können als Nachweismechanismus zum Skalieren der Produktion und Vereinfachen der Gerätebereitstellung verwendet werden.  Ein X.509-Zertifikat kann auf [unterschiedliche Weise](https://docs.microsoft.com/azure/iot-hub/iot-hub-x509ca-overview#how-to-get-an-x509-ca-certificate) abgerufen werden:
* Für Produktionsumgebungen sollten Sie ein X.509-CA-Zertifikat von einer öffentlichen Stammzertifizierungsstelle erwerben.
* Für Testumgebungen können Sie ein X.509-Stammzertifikat oder eine X.509-Zertifikatkette generieren mithilfe von:
    * OpenSSL: Sie können Skripts für die Generierung von Zertifikaten verwenden:
        * [Node.js](https://github.com/Azure/azure-iot-sdk-node/tree/master/provisioning/tools)
        * [PowerShell oder Bash](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md)
        
    * DICE-Emulator (Device Identity Composition Engine): DICE kann für kryptografische Geräteidentität und Nachweis basierend auf TLS-Protokoll und X.509-Clientzertifikaten verwendet werden.  [Erfahren Sie](https://www.microsoft.com/research/publication/device-identity-dice-riot-keys-certificates/) mehr über Geräteidentität mit DICE.

### <a name="using-x509-certificate-generator-with-dice-emulator"></a>Verwenden des X.509-Zertifikatgenerators mit DICE
Die SDKs stellen einen X.509-Zertifikatgenerator mit DICE-Emulator bereit, der im [Java SDK](https://github.com/Azure/azure-iot-sdk-java/tree/master/provisioning/provisioning-tools/provisioning-x509-cert-generator) enthalten ist.  Dieser Generator arbeitet plattformübergreifend.  Das generierte Zertifikat kann für die Entwicklung in anderen Sprachen verwendet werden.

Derzeit gibt der DICE-Emulator ein Stammzertifikat, ein Zwischenzertifikat, ein untergeordnetes Zertifikat und den zugehörigen privaten Schlüssel aus.  Allerdings kann das Stammzertifikat oder Zwischenzertifikat nicht verwendet werden, um ein separates untergeordnetes Zertifikat zu signieren.  Wenn Sie beabsichtigen, ein Gruppenregistrierungsszenario zu testen, in dem ein Signaturzertifikat verwendet wird, um die untergeordneten Zertifikate von mehreren Geräten zu signieren, können Sie OpenSSL verwenden, um eine Kette von Zertifikaten zu erzeugen.

So generieren Sie mithilfe dieses Generators ein X.509-Zertifikat:
1. [Vorbereiten der Entwicklungsumgebung](https://docs.microsoft.com/azure/iot-dps/quick-enroll-device-x509-java) und Klonen des GitHub-Repositorys:
   ```
   git clone https://github.com/Azure/azure-iot-sdk-java.git
   ```
2. Ändern Sie den Stamm in „azure-iot-sdk-java“.
3. Führen Sie ```mvn install -DskipTests=true``` aus, um alle erforderlichen Pakete herunterzuladen und das SDK zu kompilieren.
4. Navigieren Sie zum Stamm für den X.509-Zertifikatgenerator in ```azure-iot-sdk-java/provisioning/provisioning-tools/provisioning-x509-cert-generator```.
5. Verwenden Sie zum Erstellen ```mvn clean install```.
6. Führen Sie das Tool mit den folgenden Befehlen aus:
   ```
   cd target
   java -jar ./provisioning-x509-cert-generator-{version}-with-deps.jar
   ```
7. Wenn die Aufforderung angezeigt wird, können Sie optional einen _allgemeinen Namen_ für Ihre Zertifikate eingeben.
8. Das Tool erstellt lokal ein **Clientzertifikat**, den **privaten Schlüssel für das Clientzertifikat**, das **Zwischenzertifikat** und das **Stammzertifikat**.

Das **Clientzertifikat** ist das untergeordnete Zertifikat des Geräts.  **Clientzertifikat** und zugehöriger **privater Schlüssel für das Clientzertifikat** sind im Geräteclient erforderlich. Je nach ausgewählter Sprache kann der Mechanismus, dies in die Clientanwendung einzufügen, unterschiedlich sein.  Weitere Informationen finden Sie unter den [Schnellstarts](https://docs.microsoft.com/azure/iot-dps/quick-create-simulated-device-x509) zum Erstellen eines simulierte Geräts mithilfe von X.509.

Das Stammzertifikat oder Zwischenzertifikat kann genutzt werden, um eine Registrierungsgruppe oder einzelne Registrierung [programmgesteuert](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments-sdks) oder über das [Portal](https://docs.microsoft.com/azure/iot-dps/how-to-manage-enrollments) zu erstellen.

## <a name="next-steps"></a>Nächste Schritte
* Entwickeln mithilfe des [Azure IoT SDK]( https://github.com/Azure/azure-iot-sdks) für Azure IoT Hub und des Device Provisioning-Diensts von Azure IoT Hub
