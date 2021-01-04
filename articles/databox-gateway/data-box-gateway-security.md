---
title: Azure Data Box Gateway – Sicherheit | Microsoft-Dokumentation
description: Beschreibt die Sicherheits- und Datenschutzfeatures, die Ihr virtuelles Azure Data Box Gateway-Gerät, Ihren Dienst und Ihre Daten sowohl lokal als auch in der Cloud schützen.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 10/20/2020
ms.author: alkohli
ms.openlocfilehash: 11122b57a1971f328a8a83c3dea136813426c060
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/03/2020
ms.locfileid: "96581101"
---
# <a name="azure-data-box-gateway-security-and-data-protection"></a>Azure Data Box Gateway: Sicherheit und Schutz von Daten

Sicherheit ist bei der Einführung neuer Technologien immer ein zentrales Anliegen. Das gilt insbesondere, wenn die Technologie mit vertraulichen oder proprietären Daten verwendet wird. Azure Data Box Gateway trägt dazu bei, dass Ihre Daten nur von autorisierten Entitäten angezeigt, geändert oder gelöscht werden können.

In diesem Artikel werden die Sicherheitsmerkmale von Azure Data Box Gateway beschrieben, mit denen die einzelnen Komponenten der Lösung und die darin gespeicherten Daten geschützt werden.

Die Lösung Data Box Gateway besteht aus vier Hauptkomponenten, die miteinander interagieren:

- **In Azure gehosteter Data Box Gateway-Dienst:** Die Verwaltungsressource zum Erstellen des Geräteauftrags, zum Konfigurieren des Geräts sowie zum anschließenden Nachverfolgen des Auftrags bis zur Erfüllung
- **Data Box Gateway-Gerät:** Das virtuelle Gerät, das Sie im Hypervisor des angegebenen Systems bereitgestellt haben. Dieses virtuelle Gerät wird verwendet, um Ihre lokalen Daten in Azure zu importieren.
- **Mit dem Gerät verbundene Clients/Hosts:** Die Clients in Ihrer Infrastruktur, die mit dem Data Box Gateway-Gerät verbunden werden und zu schützende Daten enthalten
- **Cloudspeicher:** Der Speicherort auf der Azure-Cloudplattform, an dem die Daten gespeichert werden. Hierbei handelt es sich in der Regel um das Speicherkonto, das mit der erstellten Data Box Gateway-Ressource verknüpft ist.

## <a name="data-box-gateway-service-protection"></a>Schutz des Data Box Gateway-Diensts

Der Data Box Gateway-Dienst ist ein in Azure gehosteter Verwaltungsdienst. Der Dienst wird zum Konfigurieren und Verwalten des Geräts verwendet.

[!INCLUDE [data-box-gateway-service-protection](../../includes/data-box-gateway-service-protection.md)]

## <a name="data-box-gateway-device-protection"></a>Schutz des Data Box Gateway-Geräts

Das Data Box Gateway-Gerät ist ein virtuelles Gerät, das im Hypervisor eines angegebenen lokalen Systems bereitgestellt wird. Das Gerät hilft dabei, Daten an Azure senden. Für Ihr Gerät gilt Folgendes:

- Es benötigt einen Aktivierungsschlüssel für den Zugriff auf den Azure Stack Edge Pro-/Data Box Gateway-Dienst.
- Es ist jederzeit durch ein Gerätekennwort geschützt.
<!---  secure boot enabled.
- Runs Windows Defender Device Guard. Device Guard allows you to run only trusted applications that you define in your code integrity policies.-->

### <a name="protect-the-device-via-activation-key"></a>Schützen des Geräts mittels Aktivierungsschlüssel

Dem Data Box Gateway-Dienst, den Sie in Ihrem Azure-Abonnement erstellt haben, können nur autorisierte Data Box Gateway-Geräte hinzugefügt werden. Um ein Gerät zu autorisieren, müssen Sie es mithilfe eines Aktivierungsschlüssels für den Data Box Gateway-Dienst aktivieren.

[!INCLUDE [data-box-gateway-activation-key](../../includes/data-box-gateway-activation-key.md)]

Weitere Informationen finden Sie unter [Abrufen des Aktivierungsschlüssels](data-box-gateway-deploy-prep.md#get-the-activation-key).

### <a name="protect-the-device-via-password"></a>Schützen des Geräts mittels Kennwort

Mit Kennwörtern wird sichergestellt, dass nur autorisierte Benutzer Zugriff auf Ihre Daten haben. Data Box Gateway-Geräte sind nach dem Start gesperrt.

Ihre Möglichkeiten:

- Stellen Sie über einen Browser eine Verbindung mit der lokalen Webbenutzeroberfläche des Geräts her, und geben Sie ein Kennwort an, um sich bei dem Gerät anzumelden.
- Stellen Sie eine HTTP-Remoteverbindung mit der PowerShell-Schnittstelle des Geräts her. Die Remoteverwaltung ist standardmäßig aktiviert. Anschließend können Sie das Gerätekennwort angeben, um sich bei dem Gerät anzumelden. Weitere Informationen finden Sie unter [Herstellen einer Remoteverbindung mit Ihrem Data Box Gateway-Gerät](data-box-gateway-connect-powershell-interface.md#connect-to-the-powershell-interface).

[!INCLUDE [data-box-gateway-password-best-practices](../../includes/data-box-gateway-password-best-practices.md)]
- Verwenden Sie zum [Ändern des Kennworts](data-box-gateway-manage-access-power-connectivity-mode.md#manage-device-access) die lokale Webbenutzeroberfläche. Sollten Sie das Kennwort ändern, benachrichtigen Sie alle Benutzer mit Remotezugriff, damit bei ihnen keine Anmeldefehler auftreten.

## <a name="protect-your-data"></a>Schützen Ihrer Daten

In diesem Abschnitt werden die Data Box Gateway-Sicherheitsfeatures für Daten während der Übertragung und für gespeicherte Daten beschrieben.

### <a name="protect-data-at-rest"></a>Schutz gespeicherter Daten

[!INCLUDE [data-box-gateway-data-rest](../../includes/data-box-gateway-data-rest.md)]

### <a name="protect-data-in-flight"></a>Schützen von Daten während der Übertragung

[!INCLUDE [data-box-gateway-data-flight](../../includes/data-box-gateway-data-flight.md)]

### <a name="protect-data-using-storage-accounts"></a>Schützen von Daten mithilfe von Speicherkonten

[!INCLUDE [data-box-gateway-data-storage-accounts](../../includes/data-box-gateway-protect-data-storage-accounts.md)]

- Rotieren und [synchronisieren Sie Ihre Speicherkontoschlüssel](data-box-gateway-manage-shares.md#sync-storage-keys) regelmäßig, um zu vermeiden, dass nicht autorisierte Benutzer auf Ihr Speicherkonto zugreifen können.

### <a name="protect-the-device-data-using-bitlocker"></a>Schützen der Gerätedaten mithilfe von BitLocker

Zum Schutz virtueller Datenträger auf Ihrem virtuellen Data Box Gateway-Computer wird die Aktivierung von BitLocker empfohlen. Standardmäßig ist BitLocker nicht aktiviert. Weitere Informationen finden Sie unter

- [Einstellungen für die Verschlüsselungsunterstützung in Hyper-V-Manager](hhttps://docs.microsoft.com/windows-server/virtualization/hyper-v/learn-more/generation-2-virtual-machine-security-settings-for-hyper-v#encryption-support-settings-in-hyper-v-manager)
- [BitLocker-Unterstützung auf einem virtuellen Computer](https://kb.vmware.com/s/article/2036142)

## <a name="manage-personal-information"></a>Verwalten persönlicher Informationen

Der Data Box Gateway-Dienst erfasst persönliche Informationen in folgenden Szenarien:

[!INCLUDE [data-box-gateway-manage-personal-data](../../includes/data-box-gateway-manage-personal-data.md)]

Eine Anleitung zum Anzeigen der Liste mit den Benutzern, die auf eine Freigabe zugreifen oder sie löschen können, finden Sie unter [Verwalten von Freigaben auf Data Box Gateway](data-box-gateway-manage-shares.md).

Weitere Informationen finden Sie im [Trust Center in der Microsoft-Datenschutzrichtlinie](https://www.microsoft.com/trustcenter).

## <a name="next-steps"></a>Nächste Schritte

[Bereitstellen Ihres Data Box Gateway-Geräts](data-box-gateway-deploy-prep.md)
