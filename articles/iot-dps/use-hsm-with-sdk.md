---
title: 'Azure-Anleitung: Verwenden verschiedener Nachweismechanismen mit dem Client-SDK des Device Provisioning-Diensts in Azure'
description: 'Azure-Anleitung: Verwenden verschiedener Nachweismechanismen mit dem Client-SDK des Device Provisioning-Diensts in Azure'
author: yzhong94
ms.author: yizhon
ms.date: 03/30/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: arjmands
ms.custom: mvc
ms.openlocfilehash: af59ccc6d14dce49d06e178aac3ecafc29bd982c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "61248129"
---
# <a name="how-to-use-different-attestation-mechanisms-with-device-provisioning-service-client-sdk-for-c"></a>Verwenden verschiedener Nachweismechanismen mit dem Client-SDK des Device Provisioning-Diensts für C

In diesem Artikel erfahren Sie, wie Sie verschiedene [Nachweismechanismen](concepts-security.md#attestation-mechanism) mit dem Client-SDK des Device Provisioning-Diensts für C verwenden – entweder mit einem physischen Gerät oder mit einem Emulator. Der Bereitstellungsdienst unterstützt die Authentifizierung für zwei Arten von Nachweismechanismen: X.509 und Trusted Platform Module (TPM).

## <a name="prerequisites"></a>Voraussetzungen

Bereiten Sie Ihre Entwicklungsumgebung gemäß dem Abschnitt „Vorbereiten der Entwicklungsumgebung“ aus dem [Leitfaden zum Erstellen und Bereitstellen eines simulierten Geräts](./quick-create-simulated-device.md) vor.

### <a name="choose-an-attestation-mechanism"></a>Auswählen eines Nachweismechanismus

Als Gerätehersteller müssen Sie zuerst basierend auf einem Supporttyp einen Nachweismechanismus auswählen. Das [Client-SDK des Device Provisioning-Diensts für C](https://github.com/Azure/azure-iot-sdk-c/tree/master/provisioning_client) unterstützt derzeit die folgenden Nachweismechanismen: 

- [Trusted Platform Module (TPM)](https://en.wikipedia.org/wiki/Trusted_Platform_Module): TPM ist ein etablierter Standard für die meisten Windows-basierten Geräteplattformen sowie für einige Linux/Ubuntu-basierte Geräte. Gerätehersteller können sich für diesen Nachweismechanismus entscheiden, wenn auf ihren Geräten eines der folgenden Betriebssysteme ausgeführt wird und sie nach einem etablierten Standard suchen. Bei TPM-Chips können Sie nur jedes Gerät einzeln beim Device Provisioning-Dienst registrieren. Für Entwicklungszwecke können Sie den TPM-Simulator auf dem Windows- oder Linux-Entwicklungscomputer verwenden.

- [X.509](https://cryptography.io/en/latest/x509/): X.509-Zertifikate können auf relativ neuen Chips gespeichert werden, die als [Hardwaresicherheitsmodule (HSMs)](concepts-security.md#hardware-security-module) bezeichnet werden. Bei Microsoft wird auch an RIoT- oder DICE-Chips gearbeitet, die die X.509-Zertifikate implementieren. Mit X.509-Chips können Sie Massengeräteregistrierungen im Portal durchführen. Darüber hinaus werden bestimmte Windows-fremde Betriebssysteme wie mbed OS unterstützt. Für Entwicklungszwecke unterstützt das Client-SDK des Device Provisioning-Diensts einen X.509-Gerätesimulator. 

Weitere Informationen finden Sie unter [Sicherheitskonzepte beim IoT Hub Device Provisioning-Dienst](concepts-security.md) und [Konzepte für die automatische Bereitstellung](/azure/iot-dps/concepts-auto-provisioning).

## <a name="enable-authentication-for-supported-attestation-mechanisms"></a>Aktivieren der Authentifizierung für unterstützte Nachweismechanismen

Der SDK-Authentifizierungsmodus (X **.** 509 oder TPM) muss für das physische Gerät oder den Simulator aktiviert werden, damit es bzw. er im Azure-Portal registriert werden kann. Navigieren Sie zum Stammordner für „azure-iot-sdk-c“. Führen Sie den entsprechenden Befehl für den gewünschten Authentifizierungsmodus aus:

### <a name="use-x509-with-simulator"></a>Verwenden von X **.** 509 mit Simulator

Der Bereitstellungsdienst verfügt standardmäßig über einen DICE-Emulator (Device Identity Composition Engine), der ein X **.** 509-Zertifikat für die Geräteauthentifizierung generiert. Führen Sie den folgenden Befehl aus, um die X **.** 509-Authentifizierung zu aktivieren: 

```
cmake -Ddps_auth_type=x509 ..
```

Informationen zu Hardware mit DICE finden Sie [hier](https://azure.microsoft.com/blog/azure-iot-supports-new-security-hardware-to-strengthen-iot-security/).

### <a name="use-x509-with-hardware"></a>Verwenden von X **.** 509 mit Hardware

Der Bereitstellungsdienst kann mit X **.** 509 für andere Hardware verwendet werden. Für die Verbindungsherstellung wird eine Schnittstelle zwischen der Hardware und dem SDK benötigt. Informationen zur Schnittstelle erhalten Sie vom HSM-Hersteller.

### <a name="use-tpm"></a>Verwenden von TPM

Der Bereitstellungsdienst kann eine Verbindung mit Windows- und Linux-basierten TPM-Hardwarechips mit SAS-Token herstellen. Führen Sie den folgenden Befehl aus, um die TPM-Authentifizierung zu aktivieren:

```
cmake -Ddps_auth_type=tpm ..
```

### <a name="use-tpm-with-simulator"></a>Verwenden von TPM mit Simulator

Wenn Sie über kein Gerät mit TPM-Chips verfügen, können Sie bei der Entwicklung unter dem Windows-Betriebssystem einen Simulator verwenden. Führen Sie den folgenden Befehl aus, um die TPM-Authentifizierung zu aktivieren und den TPM-Simulator auszuführen:

```
cmake -Ddps_auth_type=tpm_simulator ..
```

## <a name="build-the-sdk"></a>Erstellen des SDKs
Das SDK muss vor dem Erstellen der Geräteregistrierung erstellt werden.

### <a name="linux"></a>Linux
- So erstellen Sie das SDK unter Linux:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake ..
    cmake --build .  # append '-- -j <n>' to run <n> jobs in parallel
    ```
- Fügen Sie zum Erstellen von Debugbinärdateien dem Projektgenerierungsbefehl die entsprechende CMake-Option hinzu. Beispiel:
    ```
    cmake -DCMAKE_BUILD_TYPE=Debug ..
    ```

- Für die SDK-Erstellung stehen zahlreiche [CMake-Konfigurationsoptionen](https://cmake.org/cmake/help/v3.6/manual/cmake.1.html) zur Verfügung. So können Sie dem CMake-Projektgenerierungsbefehl beispielsweise ein Argument hinzufügen, um einen der verfügbaren Protokollstapel zu deaktivieren:
    ```
    cmake -Duse_amqp=OFF ..
    ```
- Sie können auch einen Komponententest erstellen und ausführen:
    ```
    cmake -Drun_unittests=ON ..
    cmake --build .
    ctest -C "debug" -V
    ```

### <a name="windows"></a>Windows
- Wenn Sie das SDK unter Windows erstellen möchten, führen Sie die folgenden Schritte aus, um Projektdateien zu generieren:
  - Öffnen Sie eine Developer-Eingabeaufforderung für VS2015.
  - Führen Sie im Stammverzeichnis des Repositorys die folgenden CMake-Befehle aus:
    ```
    cd azure-iot-sdk-c
    mkdir cmake
    cd cmake
    cmake -G "Visual Studio 14 2015" ..
    ```
    Dieser Befehl erstellt x86-Bibliotheken. Wenn Sie Bibliotheken für x64 erstellen möchten, passen Sie das cmake-Generatorargument an: 
    ```
    cmake .. -G "Visual Studio 14 2015 Win64"
    ```

- Nach erfolgreicher Projekterstellung enthält der Ordner `cmake` eine Visual Studio-Projektmappendatei (SLN-Datei). So erstellen Sie das SDK:
    - Öffnen Sie **cmake\azure_iot_sdks.sln** in Visual Studio, und erstellen Sie die Projektmappe. **ODER:**
    - Führen Sie an der Eingabeaufforderung, die Sie zum Generieren der Projektdateien verwendet haben, den folgenden Befehl aus:
      ```
      cmake --build . -- /m /p:Configuration=Release
      ```
- Verwenden Sie zum Erstellen von Debugbinärdateien das entsprechende MSBuild-Argument: 
    ```
    cmake --build . -- /m /p:Configuration=Debug`
    ```
- Für die SDK-Erstellung stehen zahlreiche CMake-Konfigurationsoptionen zur Verfügung. So können Sie dem CMake-Projektgenerierungsbefehl beispielsweise ein Argument hinzufügen, um einen der verfügbaren Protokollstapel zu deaktivieren:
    ```
    cmake -G "Visual Studio 14 2015" -Duse_amqp=OFF ..
    ```
- Sie können auch Komponententests erstellen und ausführen:
    ```
    cmake -G "Visual Studio 14 2015" -Drun_unittests=ON ..
    cmake --build . -- /m /p:Configuration=Debug
    ctest -C "debug" -V
    ```
  
### <a name="libraries-to-include"></a>Einzubeziehende Bibliotheken
- Die folgenden Bibliotheken sollten in Ihrem SDK enthalten sein:
    - Der Bereitstellungsdienst: dps_http_transport, dps_client, dps_security_client
    - IoT Hub-Sicherheit: iothub_security_client

## <a name="create-a-device-enrollment-entry-in-device-provisioning-services"></a>Erstellen eines Geräteregistrierungseintrags in Device Provisioning-Diensten

### <a name="tpm"></a>TPM
Gehen Sie bei Verwendung von TPM gemäß der Anleitung unter [Erstellen und Bereitstellen eines simulierten TPM-Geräts mithilfe des C-Geräte-SDKs für den IoT Hub Device Provisioning-Dienst](./quick-create-simulated-device.md) vor, um in Ihrem Device Provisioning-Dienst einen Geräteregistrierungseintrag zu erstellen und den ersten Start zu simulieren.

### <a name="x509"></a>X **.** 509

1. Wenn Sie ein Gerät beim Bereitstellungsdienst registrieren möchten, müssen Sie sich den Endorsement Key und die Registrierungs-ID des jeweiligen Geräts notieren. Die entsprechenden Angaben finden Sie im Bereitstellungstool des Client-SDKs. Führen Sie den folgenden Befehl aus, um das Zertifikat der Stammzertifizierungsstelle (für Registrierungsgruppen) und das untergeordnete Zertifikat (für die individuelle Registrierung) auszugeben:
      ```
      ./azure-iot-sdk-c/dps_client/tools/x509_device_provision/x509_device_provision.exe
      ```
2. Melden Sie sich beim Azure-Portal an, klicken Sie im Menü auf der linken Seite auf die Schaltfläche **Alle Ressourcen**, und öffnen Sie Ihren Device Provisioning-Dienst.
   - Individuelle Registrierung mit X **.** 509: Wählen Sie auf dem Blatt mit der Zusammenfassung des Bereitstellungsdiensts die Option **Registrierungen verwalten** aus. Klicken Sie auf der Registerkarte **Individual Enrollments** (Individuelle Registrierungen) im oberen Bereich auf die Schaltfläche **Hinzufügen**. Wählen Sie als *Mechanismus* für den Identitätsnachweis die Option **X**.**509** aus, und laden Sie das untergeordnete Zertifikat gemäß den Anweisungen auf dem Blatt hoch. Klicken Sie abschließend auf die Schaltfläche **Speichern**. 
   - Gruppenregistrierung mit X **.** 509: Wählen Sie auf dem Blatt mit der Zusammenfassung des Bereitstellungsdiensts die Option **Registrierungen verwalten** aus. Klicken Sie auf der Registerkarte **Group Enrollments** (Gruppenregistrierungen) im oberen Bereich auf die Schaltfläche **Hinzufügen**. Wählen Sie als *Mechanismus* für den Identitätsnachweis die Option **X**.**509** aus, geben Sie einen Gruppen- und einen Zertifizierungsnamen ein, und laden Sie das Zertifikat der Stammzertifizierungsstelle bzw. das Zwischenzertifikat gemäß den Anweisungen auf dem Blatt hoch. Klicken Sie abschließend auf die Schaltfläche **Speichern**. 

## <a name="enable-authentication-for-devices-using-a-custom-attestation-mechanism-optional"></a>Aktivieren der Authentifizierung für Geräte mit einem benutzerdefinierten Nachweismechanismus (optional)

> [!NOTE]
> Dieser Abschnitt gilt nur für Geräte, die Unterstützung für eine benutzerdefinierte Plattform oder Nachweismechanismen benötigen, für die derzeit keine Unterstützung durch das Client-SDK des Device Provisioning-Diensts für C vorhanden ist. Beachten Sie auch, dass beim SDK häufig der Begriff „HSM“ als generischer Ersatz anstelle von „Nachweismechanismus“ verwendet wird.

Zuerst müssen Sie ein Repository und eine Bibliothek für Ihren benutzerdefinierten Nachweismechanismus entwickeln:

1. Entwickeln Sie eine Bibliothek für den Zugriff auf den Nachweismechanismus. Bei diesem Projekt muss eine statische Bibliothek zur Verwendung für das Device Provisioning-Dienst-SDK erzeugt werden.

2. Implementieren Sie in Ihrer Bibliothek die in der folgenden Headerdatei definierten Funktionen: 

    - Benutzerdefiniertes TPM: Implementieren Sie die Funktionen, die unter [HSM TPM API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-tpm-api) definiert sind.  
    - Benutzerdefiniertes X.509: Implementieren Sie die Funktionen, die unter [HSM X509 API](https://github.com/Azure/azure-iot-sdk-c/blob/master/provisioning_client/devdoc/using_custom_hsm.md#hsm-x509-api) definiert sind. 

Nach der erfolgreichen eigenständigen Erstellung Ihrer Bibliothek muss sie per Verknüpfung in das Client-SDK des Device Provisioning-Diensts integriert werden. :

1. Geben Sie das benutzerdefinierte GitHub-Repository und die Bibliothek im folgenden `cmake`-Befehl an:
    ```cmd/sh
    cmake -Duse_prov_client:BOOL=ON -Dhsm_custom_lib=<path_and_name_of_library> <PATH_TO_AZURE_IOT_SDK>
    ```
   
2. Öffnen Sie die mit „cmake“ erstellte Visual Studio-Projektmappendatei (`\azure-iot-sdk-c\cmake\azure_iot_sdks.sln`), und erstellen Sie sie. 

    - Im Rahmen des Buildprozesses wird die SDK-Bibliothek kompiliert.
    - Das SDK versucht, eine Verknüpfung mit der benutzerdefinierten Bibliothek zu erstellen, die im `cmake`-Befehl definiert ist.

3. Führen Sie die Beispiel-App „prov_dev_client_ll_sample“ unter „Provision_Samples“ aus (unter `\azure-iot-sdk-c\cmake\provisioning_client\samples\prov_dev_client_ll_sample`), um zu überprüfen, ob Ihr benutzerdefinierter Nachweismechanismus richtig implementiert wurde.

## <a name="connecting-to-iot-hub-after-provisioning"></a>Herstellen einer Verbindung mit IoT Hub nach der Bereitstellung

Nachdem das Gerät mit dem Bereitstellungsdienst bereitgestellt wurde, verwendet diese API den angegebenen Authentifizierungsmodus (X **.** 509 oder TPM), um eine Verbindung mit IoT Hub herzustellen: 
  ```
  IOTHUB_CLIENT_LL_HANDLE handle = IoTHubClient_LL_CreateFromDeviceAuth(iothub_uri, device_id, iothub_transport);
  ```
