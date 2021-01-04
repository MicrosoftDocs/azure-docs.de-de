---
title: Azure Stack Edge Pro R-Sicherheit | Microsoft-Dokumentation
description: Enthält eine Beschreibung der Sicherheits- und Datenschutzfeatures, die Ihre Azure Stack Edge Pro R- und Azure Stack Edge Mini R-Geräte, Ihren Dienst und Ihre Daten sowohl lokal als auch in der Cloud schützen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 10/14/2020
ms.author: alkohli
ms.openlocfilehash: f7d81d14ca561e6d4d897994088b2fc01b2c7701
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96465736"
---
# <a name="security-and-data-protection-for-azure-stack-edge-pro-r-and-azure-stack-edge-mini-r"></a>Sicherheit und Schutz von Daten für Azure Stack Edge Pro R und Azure Stack Edge Mini R

[!INCLUDE [applies-to-r-skus](../../includes/azure-stack-edge-applies-to-r-sku.md)]

Sicherheit ist bei der Einführung neuer Technologien immer ein zentrales Anliegen. Das gilt insbesondere, wenn die Technologie mit vertraulichen oder proprietären Daten verwendet wird. Mit Azure Stack Edge Pro R und Azure Stack Edge Mini R können Sie sicherstellen, dass Ihre Daten nur von autorisierten Entitäten angezeigt, geändert oder gelöscht werden können.

In diesem Artikel werden die Sicherheitsfeatures von Azure Stack Edge Pro R und Azure Stack Edge Mini R beschrieben, mit denen die einzelnen Komponenten der Lösung und die darin gespeicherten Daten geschützt werden.

Die Lösung besteht aus vier Hauptkomponenten, die untereinander interagieren:

- **Azure Stack Edge-Dienst, in der öffentlichen Azure- oder der Azure Government-Cloud gehostet:** Die Verwaltungsressource zum Erstellen des Geräteauftrags, zum Konfigurieren des Geräts sowie zum anschließenden Nachverfolgen des Auftrags bis zur Erfüllung
- **Widerstandsfähiges Azure Stack Edge-Gerät:** Das widerstandsfähige physische Gerät, das an Sie geschickt wird, damit Sie Ihre lokalen Daten in die öffentliche Azure- oder die Azure Government-Cloud importieren können. Hierbei kann es sich um ein Azure Stack Edge Pro R- oder Azure Stack Edge Mini R-Gerät handeln.
- **Mit dem Gerät verbundene Clients/Hosts:** Die Clients in Ihrer Infrastruktur, die mit dem Gerät verbunden werden und zu schützende Daten enthalten.
- **Cloudspeicher:** Der Speicherort auf der Azure-Cloudplattform, an dem die Daten gespeichert werden. Hierbei handelt es sich in der Regel um das Speicherkonto, das mit der von Ihnen erstellten Azure Stack Edge-Ressource verknüpft ist.

## <a name="service-protection"></a>Dienstschutz

Der Azure Stack Edge-Dienst ist ein in Azure gehosteter Verwaltungsdienst. Der Dienst wird zum Konfigurieren und Verwalten des Geräts verwendet.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-service-protection.md)]

## <a name="device-protection"></a>Geräteschutz

Hierbei handelt es sich um ein lokales Gerät, das Sie bei der Datentransformation unterstützt. Hierzu werden die Daten lokal verarbeitet und anschließend an Azure gesendet. Für Ihr Gerät gilt Folgendes:

- Es benötigt einen Aktivierungsschlüssel für den Zugriff auf den Azure Stack Edge-Dienst.
- Es ist jederzeit durch ein Gerätekennwort geschützt.
- Es ist ein gesperrtes Gerät. Der Baseboard-Verwaltungscontroller (Baseboard Management Controller, BMC) und das BIOS des Geräts sind kennwortgeschützt. Der BMS ist durch die Einschränkung des Benutzerzugriffs geschützt.
- „Secure Boot“ ist aktiviert, um sicherzustellen, dass das Gerät nur mit der vertrauenswürdigen Software von Microsoft gestartet wird.
- Die Windows Defender-Anwendungssteuerung (Windows Defender Application Control, WDAC) wird ausgeführt. Hiermit wird sichergestellt, dass nur vertrauenswürdige Anwendungen ausgeführt werden können, die Sie in Ihren Codeintegritätsrichtlinien definieren.
- Es ist ein Trusted Platform Module (TPM) für hardwarebasierte Sicherheitsfunktionen vorhanden. Mit dem TPM werden Geheimnisse und Daten verwaltet und geschützt, die dauerhaft auf dem Gerät gespeichert werden müssen.
- Auf dem Gerät werden nur die erforderlichen Ports geöffnet, und alle anderen Ports werden blockiert. Weitere Informationen finden Sie in der Liste mit den [Portanforderungen für Geräte](azure-stack-edge-pro-r-system-requirements.md).
- Der gesamte Zugriff auf die Gerätehardware und -software wird protokolliert. 
    - Für die Gerätesoftware werden Firewall-Standardprotokolle für den ein- und ausgehenden Datenverkehr des Geräts erfasst. Diese Protokolle sind im Supportpaket zusammengefasst.
    - Für die Gerätehardware werden alle Ereignisse für das Gerätegehäuse, z. B. Öffnen und Schließen, für das Gerät protokolliert.

    Weitere Informationen zu den spezifischen Protokollen, die die Informationen zu den erfolgten Hardware- und Softwareeingriffen enthalten, und zum Abrufen der Protokolle finden Sie unter [Sammeln erweiterter Sicherheitsprotokolle](azure-stack-edge-gpu-troubleshoot.md).


### <a name="protect-the-device-via-activation-key"></a>Schützen des Geräts mittels Aktivierungsschlüssel

Dem in Ihrem Azure-Abonnement erstellten Azure Stack Edge-Dienst kann nur ein autorisiertes Azure Stack Edge Pro R- oder Azure Stack Edge Mini R-Gerät hinzugefügt werden. Um ein Gerät zu autorisieren, müssen Sie es mithilfe eines Aktivierungsschlüssels für den Azure Stack Edge-Dienst aktivieren.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-activation-key.md)]

Weitere Informationen finden Sie unter [Abrufen des Aktivierungsschlüssels](azure-stack-edge-pro-r-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Schützen des Geräts mittels Kennwort

Mit Kennwörtern wird sichergestellt, dass nur autorisierte Benutzer Zugriff auf Ihre Daten haben. Azure Stack Edge Pro R-Geräte sind nach dem Start gesperrt.

Ihre Möglichkeiten:

- Stellen Sie über einen Browser eine Verbindung mit der lokalen Webbenutzeroberfläche des Geräts her, und geben Sie ein Kennwort an, um sich bei dem Gerät anzumelden.
- Stellen Sie eine HTTP-Remoteverbindung mit der PowerShell-Schnittstelle des Geräts her. Die Remoteverwaltung ist standardmäßig aktiviert. Darüber hinaus ist für die Remoteverwaltung auch die minimale Administration (Just Enough Administration, JEA) konfiguriert, um die Möglichkeiten für Benutzer einzuschränken. Anschließend können Sie das Gerätekennwort angeben, um sich bei dem Gerät anzumelden. Weitere Informationen finden Sie unter [Verwalten eines Azure Stack Edge Pro-GPU-Geräts mithilfe von Windows PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md).
- Der lokale Edge-Benutzer auf dem Gerät hat eingeschränkten Zugriff auf das Gerät, um die Erstkonfiguration und die Problembehandlung durchführen zu können. Auf die auf dem Gerät ausgeführten Computeworkloads, die Datenübertragung und den Speicher kann jeweils über das öffentliche Azure-Portal oder das Government-Portal für die Ressource in der Cloud zugegriffen werden.

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-password-best-practices.md)]
- Verwenden Sie zum [Ändern des Kennworts](azure-stack-edge-gpu-manage-access-power-connectivity-mode.md#change-device-password) die lokale Webbenutzeroberfläche. Sollten Sie das Kennwort ändern, benachrichtigen Sie alle Benutzer mit Remotezugriff, damit bei ihnen keine Anmeldefehler auftreten.

### <a name="establish-trust-with-the-device-via-certificates"></a>Einrichten der Vertrauensstellung mit dem Gerät über Zertifikate

Beim widerstandsfähigen Azure Stack Edge-Gerät können Sie Ihre eigenen Zertifikate nutzen und installieren, damit sie für alle öffentlichen Endpunkte verwendet werden können. Weitere Informationen finden Sie unter [Hochladen von Zertifikaten](azure-stack-edge-j-series-manage-certificates.md#upload-certificates). Eine Liste mit allen Zertifikaten, die auf Ihrem Gerät installiert werden können, finden Sie unter [Verwenden von Zertifikaten mit einem Azure Stack Edge Pro-GPU-Gerät](azure-stack-edge-j-series-manage-certificates.md).

- Wenn Sie Compute auf Ihrem Gerät konfigurieren, werden ein IoT-Gerät und ein IoT Edge-Gerät erstellt. Diesen Geräten werden automatisch symmetrische Zugriffsschlüssel zugewiesen. Diese Schlüssel werden aus Sicherheitsgründen regelmäßig über den IoT Hub-Dienst rotiert.

## <a name="protect-your-data"></a>Schützen Ihrer Daten

In diesem Abschnitt werden die Sicherheitsfeatures für Daten während der Übertragung sowie für gespeicherte Daten beschrieben.

### <a name="protect-data-at-rest"></a>Schutz gespeicherter Daten

Alle ruhenden Daten auf dem Gerät sind doppelt verschlüsselt, der Zugriff auf Daten wird gesteuert, und nachdem das Gerät deaktiviert wurde, werden die Daten auf sichere Weise von den Datenträgern gelöscht.

#### <a name="double-encryption-of-data"></a>Doppelte Verschlüsselung von Daten

Die Daten auf Ihren Datenträgern sind durch zwei Verschlüsselungsebenen geschützt:

- Die erste Verschlüsselungsebene ist eine Verschlüsselung vom Typ „BitLocker XTS-AES 256-Bit“ auf den Datenvolumes.
- Die zweite Ebene ist die integrierte Verschlüsselung der Festplatten.
- Für das Betriebssystemvolume wird BitLocker als alleinige Verschlüsselungsebene verwendet.

> [!NOTE]
> Für den Betriebssystemdatenträger wird für die Softwareverschlüsselung nur eine Ebene vom Typ „BitLocker XTS-AES-256“ genutzt.

Beim Aktivieren des Geräts werden Sie aufgefordert, eine Schlüsseldatei mit Wiederherstellungsschlüsseln zu speichern, mit denen die Daten auf dem Gerät wiederhergestellt werden können, wenn es nicht gestartet werden kann. Die Datei enthält zwei Schlüssel:

- Mit einem Schlüssel wird die Gerätekonfiguration auf den Betriebssystemvolumes wiederhergestellt.
<!-- - Second key is to unlock the BitLocker on the data disks. -->
- Mit dem zweiten Schlüssel wird die Hardwareverschlüsselung auf den Datenträgern entsperrt.

> [!IMPORTANT]
> Speichern Sie die Schlüsseldatei an einem sicheren Ort außerhalb des Geräts. Wenn das Gerät nicht gestartet werden kann und Sie nicht über den Schlüssel verfügen, kann dies unter Umständen zu Datenverlust führen.

- Bei bestimmten Wiederherstellungsszenarien werden Sie zur Eingabe der von Ihnen gespeicherten Schlüsseldatei aufgefordert. 
<!--- If a node isn't booting up, you will need to perform a node replacement. You will have the option to swap the data disks from the failed node to the new node. For a 4-node device, you won't need a key file. For a 1-node device, you will be prompted to provide a key file.-->

#### <a name="restricted-access-to-data"></a>Eingeschränkter Zugriff auf Daten

Der Zugriff auf die Daten, die auf Freigaben und in Speicherkonten gespeichert werden, ist eingeschränkt.

- SMB-Clients, die auf Freigabedaten zugreifen, benötigen Benutzeranmeldeinformationen, die der Freigabe zugeordnet sind. Diese Anmeldeinformationen werden definiert, wenn die Freigabe erstellt wird.
- Für NFS-Clients, die auf eine Freigabe zugreifen, muss die IP-Adresse während der Erstellung der Freigabe explizit hinzugefügt werden.
- Die Edge-Speicherkonten, die auf dem Gerät erstellt werden, sind lokale Konten und durch die Verschlüsselung auf den Datenträgern geschützt. Die Azure-Speicherkonten, denen diese Edge-Speicherkonten zugeordnet sind, sind nach Abonnement und mit zwei 512-Bit-Speicherzugriffsschlüsseln für das Edge-Speicherkonto geschützt (diese Schlüssel unterscheiden sich von den Schlüsseln, die Ihren Azure Storage-Konten zugeordnet sind). Weitere Informationen finden Sie unter [Schützen von Daten in Speicherkonten](#protect-data-in-storage-accounts).
- BitLocker XTS-AES-256-Bit-Verschlüsselung wird verwendet, um lokale Daten zu schützen.

#### <a name="secure-data-erasure"></a>Schützen vor Datenlöschung

Wenn für das Gerät ein Kaltstart erfolgt, wird auf dem Gerät ein geschützter Zurücksetzungsvorgang (Secure Wipe) durchgeführt. Hierbei werden die Daten auf den Datenträgern gelöscht, indem eine Bereinigung vom Typ „NIST SP 800-88r1“ erfolgt.

### <a name="protect-data-in-flight"></a>Schützen von Daten während der Übertragung

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-data-flight.md)]

### <a name="protect-data-in-storage-accounts"></a>Schützen von Daten in Speicherkonten

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-protect-data-storage-accounts.md)]

- Rotieren und [synchronisieren Sie Ihre Speicherkontoschlüssel](azure-stack-edge-j-series-manage-storage-accounts.md) regelmäßig, um zu vermeiden, dass nicht autorisierte Benutzer auf Ihr Speicherkonto zugreifen können.

## <a name="manage-personal-information"></a>Verwalten persönlicher Informationen

Der Azure Stack Edge-Dienst erfasst persönliche Informationen in folgenden Szenarien:

[!INCLUDE [azure-stack-edge-gateway-data-rest](../../includes/azure-stack-edge-gateway-manage-personal-data.md)]

Eine Anleitung zum Anzeigen der Liste mit den Benutzern, die auf eine Freigabe zugreifen oder sie löschen können, finden Sie unter [Verwalten von Freigaben auf der Azure Stack Edge-Ressource](azure-stack-edge-j-series-manage-shares.md).

Weitere Informationen finden Sie im [Trust Center in der Microsoft-Datenschutzrichtlinie](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen Ihres Azure Stack Edge Pro R-Geräts](azure-stack-edge-gpu-deploy-prep.md)
