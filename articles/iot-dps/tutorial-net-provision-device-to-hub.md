---
title: 'Tutorial: Bereitstellen eines Geräts mithilfe von Azure IoT Hub Device Provisioning Service (.NET)'
description: In diesem Tutorial erfahren Sie, wie Sie Ihr Gerät mithilfe von Azure IoT Hub Device Provisioning Service (DPS) und .NET für einen einzelnen IoT-Hub bereitstellen.
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.devlang: csharp
ms.custom: mvc, devx-track-csharp
ms.openlocfilehash: f9a14ee6ee3e10b36d64ec11fc23807efe2bfaf2
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966563"
---
# <a name="tutorial-enroll-the-device-to-an-iot-hub-using-the-azure-iot-hub-provisioning-service-client-net"></a>Tutorial: Registrieren des Geräts für eine IoT Hub-Instanz mithilfe des Azure IoT Hub Device Provisioning-Diensts (.NET)

Im vorherigen Tutorial haben Sie erfahren, wie ein Gerät eingerichtet wird, um eine Verbindung mit Ihrem Device Provisioning-Dienst herzustellen. In diesem Tutorial wird beschrieben, wie Sie diesen Dienst zum Bereitstellen Ihres Geräts auf einem einzelnen IoT Hub verwenden, indem Sie sowohl die **_Individuelle Registrierung_** als auch **_Registrierungsgruppen_** nutzen. Dieses Tutorial veranschaulicht folgende Vorgehensweisen:

> [!div class="checklist"]
> * Registrieren des Geräts
> * Starten des Geräts
> * Sicherstellen, dass das Gerät registriert ist

## <a name="prerequisites"></a>Voraussetzungen

Bevor Sie fortfahren, konfigurieren Sie auf jeden Fall Ihr Gerät und das zugehörige *Hardwaresicherheitsmodul* (HSM), wie in dem Tutorial [Einrichten eines bereitzustellenden Geräts mithilfe des Azure IoT Hub Device Provisioning-Diensts](./tutorial-set-up-device.md) erläutert wird.

* Visual Studio

> [!NOTE]
> Visual Studio ist nicht erforderlich. Die Installation von [.NET](https://www.microsoft.com/net) ist ausreichend, und Entwickler können ihren bevorzugten Editor unter Windows oder Linux verwenden.  

In diesem Tutorial wird der Zeitraum während des bzw. direkt nach dem Hardwareherstellungsprozess beschrieben, in dem Geräteinformationen für den Provisioning-Dienst hinzugefügt werden. Dieser Code wird normalerweise auf einem PC oder einem Gerät in einem Werk ausgeführt, auf dem die Ausführung von .NET-Code möglich ist. Er sollte nicht den Geräten selbst hinzugefügt werden.


## <a name="enroll-the-device"></a>Registrieren des Geräts

Bei diesem Schritt werden die einzigartigen Sicherheitsartefakte des Geräts zum Device Provisioning-Dienst hinzugefügt. Zu diesen Sicherheitsartefakten gehören Folgende:

- Für TPM-basierte Geräte:
    - Der *Endorsement Key*, der für jeden TPM-Chip oder jede TPM-Simulation eindeutig ist. Weitere Informationen finden Sie im Artikel [Grundlegendes zum TPM Endorsement Key](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc770443(v=ws.11)).
    - Die *Registrierungs-ID*, die zur eindeutigen Identifizierung eines Geräts im Namespace/Bereich verwendet wird. Diese ist eventuell mit der Geräte-ID identisch. Die ID ist für jedes Gerät erforderlich. Bei TPM-basierten Geräten kann die Registrierungs-ID vom TMP selbst abgeleitet werden, z.B. ein SHA-256-Hash des TPM Endorsement Key.

- Für X.509-basierte Geräte:
    - Das [für das Gerät ausgestellte X.509-Zertifikat](/windows/win32/seccertenroll/about-x-509-public-key-certificates), entweder in Form einer *PEM*- oder einer *CER*-Datei. Für die individuelle Registrierung müssen Sie für Ihr X.509-System das *untergeordnete Zertifikat* verwenden, für Registrierungsgruppen dagegen das *Stammzertifikat* oder ein gleichwertiges *Signaturzertifikat*.
    - Die *Registrierungs-ID*, die zur eindeutigen Identifizierung eines Geräts im Namespace/Bereich verwendet wird. Diese ist eventuell mit der Geräte-ID identisch. Die ID ist für jedes Gerät erforderlich. Für X.509-basierte Geräte wird die Registrierungs-ID vom allgemeinen Namen des Zertifikats (Common Name, CN) abgeleitet. Weitere Informationen zu diesen Anforderungen finden Sie im Artikel mit den [Konzepten für Geräte](./concepts-service.md).

Es gibt zwei Möglichkeiten zum Registrieren des Geräts beim Device Provisioning-Dienst:

- **Individuelle Registrierungen** Diese stellen Einträge für ein einzelnes Gerät dar, das beim Device Provisioning-Dienst registriert werden kann. Individuelle Registrierungen verwenden X.509-Zertifikate oder SAS-Token (in einem physischen oder virtuellen TPM) als Nachweismechanismen. Individuelle Registrierungen sollten für Geräte, die besondere Erstkonfigurationen erfordern, oder für Geräte verwendet werden, die nur SAS-Token über das TPM als Nachweismechanismus verwenden können. Bei individuellen Registrierungen ist möglicherweise die gewünschte IoT Hub-Geräte-ID angegeben.

- **Registrierungsgruppen** Diese stellen Gruppen von Geräten dar, die einen bestimmten Nachweismechanismus gemeinsam nutzen. Es wird empfohlen, eine Registrierungsgruppe für eine große Anzahl von Geräten, die eine gewünschte Erstkonfiguration gemeinsam nutzen, oder für Geräte zu verwenden, die alle demselben Mandanten zugeordnet sind. Registrierungen sind auf X.509 beschränkt, und in der dazugehörigen X.509-Zertifikatkette wird ein gemeinsames Signaturzertifikat genutzt.

### <a name="enroll-the-device-using-individual-enrollments"></a>Registrieren des Geräts mit individuellen Registrierungen

1. Erstellen Sie in Visual Studio ein Visual C#-Konsolenanwendungsprojekt, indem Sie die Projektvorlage **Konsolen-App** verwenden. Geben Sie dem Projekt den Namen **DeviceProvisioning**.
    
1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **DeviceProvisioning**, und klicken Sie dann auf **NuGet-Pakete verwalten...** .

1. Wählen Sie im Fenster **NuGet-Paket-Manager** die Option **Durchsuchen**, und suchen Sie nach **microsoft.azure.devices.provisioning.service**. Wählen Sie den Eintrag aus, und klicken Sie auf **Installieren**, um das Paket **Microsoft.Azure.Devices.Provisioning.Service** zu installieren, und akzeptieren Sie die Nutzungsbedingungen. Bei diesem Verfahren wird das NuGet-Paket mit dem [Azure IoT Device Provisioning Service-SDK](https://www.nuget.org/packages/Microsoft.Azure.Devices.Provisioning.Service/) heruntergeladen und installiert und ein Verweis auf das Paket und seine Abhängigkeiten hinzugefügt.

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:
   
    ```csharp
    using Microsoft.Azure.Devices.Provisioning.Service;
    ```

1. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert durch die Device Provisioning Service-Verbindungszeichenfolge aus dem vorherigen Abschnitt.
   
    ```csharp
    static readonly string ServiceConnectionString = "{Device Provisioning Service connection string}";

    private const string SampleRegistrationId = "sample-individual-csharp";
    private const string SampleTpmEndorsementKey =
            "AToAAQALAAMAsgAgg3GXZ0SEs/gakMyNRqXXJP1S124GUgtk8qHaGzMUaaoABgCAAEMAEAgAAAAAAAEAxsj2gUS" +
            "cTk1UjuioeTlfGYZrrimExB+bScH75adUMRIi2UOMxG1kw4y+9RW/IVoMl4e620VxZad0ARX2gUqVjYO7KPVt3d" +
            "yKhZS3dkcvfBisBhP1XH9B33VqHG9SHnbnQXdBUaCgKAfxome8UmBKfe+naTsE5fkvjb/do3/dD6l4sGBwFCnKR" +
            "dln4XpM03zLpoHFao8zOwt8l/uP3qUIxmCYv9A7m69Ms+5/pCkTu/rK4mRDsfhZ0QLfbzVI6zQFOKF/rwsfBtFe" +
            "WlWtcuJMKlXdD8TXWElTzgh7JS4qhFzreL0c1mI0GCj+Aws0usZh7dLIVPnlgZcBhgy1SSDQMQ==";
    private const string OptionalDeviceId = "myCSharpDevice";
    private const ProvisioningStatus OptionalProvisioningStatus = ProvisioningStatus.Enabled;
    ```

1. Fügen Sie Folgendes hinzu, um die Registrierung für das Gerät zu implementieren:

    ```csharp
    static async Task SetRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetRegistrationData");

        Attestation attestation = new TpmAttestation(SampleTpmEndorsementKey);

        IndividualEnrollment individualEnrollment = new IndividualEnrollment(SampleRegistrationId, attestation);

        individualEnrollment.DeviceId = OptionalDeviceId;
        individualEnrollment.ProvisioningStatus = OptionalProvisioningStatus;

        Console.WriteLine("\nAdding new individualEnrollment...");
        var serviceClient = ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString);

        IndividualEnrollment individualEnrollmentResult =
            await serviceClient.CreateOrUpdateIndividualEnrollmentAsync(individualEnrollment).ConfigureAwait(false);

        Console.WriteLine("\nIndividualEnrollment created with success.");
        Console.WriteLine(individualEnrollmentResult);
    }
    ```

1. Fügen Sie abschließend der **Main**-Methode den folgenden Code hinzu, um die Verbindung mit dem IoT Hub zu öffnen und mit der Registrierung zu beginnen:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Provisioning example");

        SetRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    Console.ReadLine();
    ```
        
1. Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf Ihre Projektmappe, und klicken Sie dann auf **Startprojekte festlegen...** . Wählen Sie **Einzelnes Startprojekt** und dann im Dropdownmenü das Projekt **DeviceProvisioning** aus.  

1. Führen Sie die .NET-Geräte-App **DeviceProvisiong** aus. Die Bereitstellung für das Gerät wird eingerichtet: 

    ![Ausführung der individuellen Registrierung](./media/tutorial-net-provision-device-to-hub/individual.png)

Wenn das Gerät erfolgreich registriert ist, sollte es wie folgt im Portal angezeigt werden:

   ![Erfolgreiche Registrierung im Portal](./media/tutorial-net-provision-device-to-hub/individual-portal.png)

### <a name="enroll-the-device-using-enrollment-groups"></a>Registrieren des Geräts mit Registrierungsgruppen

> [!NOTE]
> Für das Registrierungsgruppenbeispiel ist ein X.509-Zertifikat erforderlich.

1. Öffnen Sie im Projektmappen-Explorer von Visual Studio das oben erstellte Projekt **DeviceProvisioning**. 

1. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:
    
    ```csharp
    using System.Security.Cryptography.X509Certificates;
    ```

1. Fügen Sie der **Program** -Klasse die folgenden Felder hinzu. Ersetzen Sie den Platzhalterwert durch den Speicherort des X509-Zertifikats.
   
    ```csharp
    private const string X509RootCertPathVar = "{X509 Certificate Location}";
    private const string SampleEnrollmentGroupId = "sample-group-csharp";
    ```

1. Fügen Sie der Datei **Program.cs** Folgendes hinzu, um die Registrierung für die Gruppe zu implementieren:

    ```csharp
    public static async Task SetGroupRegistrationDataAsync()
    {
        Console.WriteLine("Starting SetGroupRegistrationData");

        using (ProvisioningServiceClient provisioningServiceClient =
                ProvisioningServiceClient.CreateFromConnectionString(ServiceConnectionString))
        {
            Console.WriteLine("\nCreating a new enrollmentGroup...");

            var certificate = new X509Certificate2(X509RootCertPathVar);

            Attestation attestation = X509Attestation.CreateFromRootCertificates(certificate);

            EnrollmentGroup enrollmentGroup = new EnrollmentGroup(SampleEnrollmentGroupId, attestation);

            Console.WriteLine(enrollmentGroup);
            Console.WriteLine("\nAdding new enrollmentGroup...");

            EnrollmentGroup enrollmentGroupResult =
                await provisioningServiceClient.CreateOrUpdateEnrollmentGroupAsync(enrollmentGroup).ConfigureAwait(false);

            Console.WriteLine("\nEnrollmentGroup created with success.");
            Console.WriteLine(enrollmentGroupResult);
        }
    }
    ```

1. Fügen Sie abschließend den folgenden Code für die **Main**-Methode ein, um die Verbindung mit dem IoT Hub zu öffnen und mit der Gruppenregistrierung zu beginnen:
   
    ```csharp
    try
    {
        Console.WriteLine("IoT Device Group Provisioning example");

        SetGroupRegistrationDataAsync().GetAwaiter().GetResult();
            
        Console.WriteLine("Done, hit enter to exit.");
        Console.ReadLine();
    }
    catch (Exception ex)
    {
        Console.WriteLine();
        Console.WriteLine("Error in sample: {0}", ex.Message);
    }
    ```

1. Führen Sie die .NET-Geräte-App **DeviceProvisiong** aus. Die Gruppenbereitstellung für das Gerät wird eingerichtet: 

    ![Ausführung der Gruppenregistrierung](./media/tutorial-net-provision-device-to-hub/group.png)

    Wenn die Gerätegruppe erfolgreich registriert wurde, sollte sie im Portal wie folgt angezeigt werden:

   ![Erfolgreiche Gruppenregistrierung im Portal](./media/tutorial-net-provision-device-to-hub/group-portal.png)


## <a name="start-the-device"></a>Starten des Geräts

An dieser Stelle ist Folgendes für die Geräteregistrierung eingerichtet:

1. Ihr Gerät oder eine Gruppe von Geräten ist bei Ihrem Device Provisioning-Dienst registriert, und 
2. Für Ihr Gerät wurde die Sicherheit konfiguriert, und über die Anwendung kann mithilfe des Client-SDK des Device Provisioning-Diensts darauf zugegriffen werden.

Starten Sie das Gerät, damit Ihre Clientanwendung die Registrierung bei Ihrem Device Provisioning-Dienst starten kann.  


## <a name="verify-the-device-is-registered"></a>Sicherstellen, dass das Gerät registriert ist

Nachdem Ihr Gerät gestartet wurde, sollten folgende Aktionen durchgeführt werden. Weitere Details finden Sie im [Beispiel für die Bereitstellung eines Geräteclients](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/device). 

1. Das Gerät sendet eine Registrierungsanforderung an Ihren Device Provisioning-Dienst.
2. Bei TPM-Geräten sendet der Device Provisioning-Dienst eine Registrierungsherausforderung zurück, auf die Ihr Gerät antwortet. 
3. Nach erfolgreicher Registrierung sendet der Device Provisioning-Dienst den IoT Hub-URI, die Geräte-ID und den verschlüsselten Schlüssel an das Gerät zurück. 
4. Die IoT Hub-Clientanwendung auf dem Gerät stellt dann eine Verbindung mit Ihrem Hub her. 
5. Nachdem erfolgreich eine Verbindung mit dem Hub hergestellt wurde, sollte das Gerät im **Device Explorer** von IoT Hub angezeigt werden. 

    ![Erfolgreiche Herstellung einer Verbindung mit dem Hub im Portal](./media/tutorial-net-provision-device-to-hub/hub-connect-success.png)

## <a name="next-steps"></a>Nächste Schritte
In diesem Tutorial haben Sie Folgendes gelernt:

> [!div class="checklist"]
> * Registrieren des Geräts
> * Starten des Geräts
> * Sicherstellen, dass das Gerät registriert ist

Fahren Sie mit dem nächsten Tutorial fort, um zu erfahren, wie mehrere Geräte für Hubs mit Lastausgleich bereitgestellt werden. 

> [!div class="nextstepaction"]
> [Bereitstellen von Geräten für IoT Hubs mit Lastausgleich](./tutorial-provision-multiple-hubs.md)