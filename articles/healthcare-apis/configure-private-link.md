---
title: Private Link für Azure API for FHIR
description: In diesem Artikel wird beschrieben, wie Sie einen privaten Endpunkt für Azure API for FHIR-Dienste einrichten.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 10/12/2020
ms.author: matjazl
ms.openlocfilehash: bfbdb98e691312db5665261743f8ce698541d4cc
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398214"
---
# <a name="configure-private-link"></a>Konfigurieren von Private Link

> [!IMPORTANT]
> Diese Funktion befindet sich in der öffentlichen Vorschauphase, wird ohne Vereinbarung zum Servicelevel bereitgestellt und ist nicht für Produktionsworkloads vorgesehen. Manche Features werden möglicherweise nicht unterstützt oder sind nur eingeschränkt verwendbar. Weitere Informationen finden Sie unter [Zusätzliche Nutzungsbestimmungen für Microsoft Azure-Vorschauen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Private Link ermöglicht Ihnen den Zugriff auf Azure API for FHIR über einen privaten Endpunkt, eine Netzwerkschnittstelle, die Sie privat und sicher über eine private IP-Adresse aus Ihrem virtuellen Netzwerk verbindet. Mit Private Link können Sie sicher aus Ihrem VNet auf unsere Dienste als Erstanbieterdienst zugreifen, ohne ein öffentliches DNS passieren zu müssen. Dieser Artikel führt Sie durch die Schritte zum Erstellen, Testen und Verwalten Ihres privaten Endpunkts für die Azure API for FHIR.

## <a name="prerequisites"></a>Voraussetzungen

Vor dem Erstellen eines privaten Endpunkts müssen zunächst ein paar Azure-Ressourcen erstellen:

- Ressourcengruppe: Die Ressourcengruppe, die das virtuelle Netzwerk und den privaten Endpunkt enthalten soll.
- Azure API for FHIR: Die FHIR-Ressource, die Sie hinter einem privaten Endpunkt platzieren möchten.
- Virtuelles Netzwerk: Das VNet, mit dem Ihre Clientdienste und der private Endpunkt verbunden werden.

Weitere Informationen finden Sie in der [Dokumentation zu Private Link](../private-link/index.yml).

## <a name="disable-public-network-access"></a>Deaktivieren des Zugriffs auf das öffentliche Netzwerk

Wenn Sie einen privaten Endpunkt für Ihre FHRI-Ressource erstellen, wird der öffentliche Datenverkehr zu diesem nicht automatisch deaktiviert. Zu diesem Zweck müssen Sie Ihre FHIR-Ressource aktualisieren, um eine neue „Public Access“-Eigenschaft (Öffentlicher Zugriff) von „Aktiviert“ auf „Deaktiviert“ festzulegen. Gehen Sie bei der Deaktivierung des Zugriffs aus öffentlichen Netzwerken sorgfältig vor, da alle Anforderungen an Ihren FHIR-Dienst, die nicht von einem ordnungsgemäß konfigurierten privaten Endpunkt stammen, abgelehnt werden. Nur Datenverkehr von Ihren privaten Endpunkten ist zulässig.

![Deaktivieren des Zugriffs aus öffentlichen Netzwerken](media/private-link/private-link-disable.png)

## <a name="create-private-endpoint"></a>Erstellen eines privaten Endpunkts

Zum Erstellen eines privaten Endpunkts kann ein Entwickler mit RBAC-Berechtigungen für die FHIR-Ressource das Azure-Portal, die [Azure PowerShell](../private-link/create-private-endpoint-powershell.md) oder die [Azure CLI](../private-link/create-private-endpoint-cli.md) verwenden. Dieser Artikel führt Sie durch die Schritte zur Verwendung des Azure-Portals. Die Verwendung des Azure-Portals wird empfohlen, da es die Erstellung und Konfiguration der privates DNS-Zone automatisiert. Weitere Informationen finden Sie unter [Schnellstarthandbücher zu Private Link](../private-link/create-private-endpoint-portal.md).

Ein privater Endpunkt kann auf zwei Arten erstellt werden. Mit dem automatischen Genehmigungsflow kann ein Benutzer, der über RBAC-Berechtigungen für die FHIR-Ressource verfügt, einen privaten Endpunkt erstellen, ohne dass eine Genehmigung erforderlich ist. Mit dem manuellen Genehmigungsflow kann ein Benutzer ohne RBAC-Berechtigungen für die FHIR-Ressource die Genehmigung eines privaten Endpunkts bei Besitzern der FHIR-Ressource anfordern.

### <a name="auto-approval"></a>Automatische Genehmigung

Stellen Sie sicher, dass die Region für den neuen privaten Endpunkt mit der Region für Ihr virtuelles Netzwerk identisch ist. Die Region für Ihre FHIR-Ressource kann abweichen.

![Registerkarte „Grundlagen“ im Azure-Portal](media/private-link/private-link-portal2.png)

Suchen Sie für den „Ressourcentyp“ den Wert „Microsoft.HealthcareApis/services“, und wählen Sie ihn aus. Wählen Sie als „Ressource“ die FHIR-Ressource aus. Wählen Sie als „Zielunterressource“ den Wert „FHIR“ aus.

![Registerkarte „Ressource“ im Azure-Portal](media/private-link/private-link-portal1.png)

Wenn Sie noch nicht über eine eingerichtete private DNS-Zone verfügen, wählen Sie „(New)privatelink.azurehealthcareapis.com“ aus. Wenn Sie Ihre private DNS-Zone bereits konfiguriert haben, können Sie diese in der Liste auswählen. Sie muss das Format „privatelink.azurehealthcareapis.com“ haben.

![Registerkarte „Konfiguration“ im Azure-Portal](media/private-link/private-link-portal3.png)

Nachdem die Bereitstellung abgeschlossen wurde, können Sie zur Registerkarte „Private Endpunktverbindungen“ zurückkehren, auf der nun „Genehmigt“ als Verbindungsstatus angezeigt wird.

### <a name="manual-approval"></a>Manuelle Genehmigung

Wählen Sie für die manuelle Genehmigung die zweite Option unter „Ressource“ aus: „Stellen Sie über eine Ressourcen-ID oder einen Alias eine Verbindung mit einer Azure-Ressource her“. Geben Sie für „Zielunterressource“ den Wert „FHIR“ ein, wie bei der automatischen Genehmigung.

![Manuelle Genehmigung](media/private-link/private-link-manual.png)

Nachdem die Bereitstellung abgeschlossen wurde, können Sie zur Registerkarte „Private Endpunktverbindungen“ zurückkehren, auf der Sie Ihre Verbindung „Genehmigen“, „Ablehnen“ oder „Entfernen“ können.

![Optionen](media/private-link/private-link-options.png)

## <a name="test-private-endpoint"></a>Privaten Endpunkt testen

Um sicherzustellen, dass Ihr FHIR-Server nach dem Deaktivieren des öffentlichen Netzwerkzugriffs keinen öffentlichen Datenverkehr mehr empfängt, versuchen Sie, von Ihrem Computer aus, den Endpunkt „/metadata“ für Ihren Server zu erreichen. Sie sollten einen Fehler „403 Verboten“ erhalten. Beachten Sie, dass es nach der Aktualisierung des Flags für den öffentlichen Netzwerkzugriff bis zu 5 Minuten dauern kann, bis öffentlicher Datenverkehr blockiert wird.

So stellen Sie sicher, dass Ihr privater Endpunkt Datenverkehr an Ihren Server senden kann

1. Erstellen Sie einen virtuellen Computer, der mit dem virtuellen Netzwerk und Subnetz verbunden ist, in dem der private Endpunkt konfiguriert ist. Um sicherzustellen, dass Ihr Datenverkehr von dem virtuellen Computer nur das private Netzwerk verwendet, können Sie ausgehenden Internetdatenverkehr über eine Netzwerksicherheitsgruppen-Regel deaktivieren.
2. Greifen Sie per RDP auf die VM zu.
3. Versuchen Sie, von Ihrem virtuellen Computer aus auf den Endpunkt „/metadata“ Ihres FHIR-Servers zuzugreifen. Sie sollten die Funktionsbestätigung als Antwort erhalten.

## <a name="manage-private-endpoint"></a>Verwalten eines privaten Endpunkts

### <a name="view"></a>Sicht

Private Endpunkte und die zugeordnete NIC sind in Azure-Portal aus der Ressourcengruppe sichtbar, in der sie erstellt wurden.

![„Ansicht“ in „Ressourcen“](media/private-link/private-link-view.png)

### <a name="delete"></a>Löschen

Private Endpunkte können im Azure-Portal nur über das Blatt „Übersicht“ (wie unten) oder über die Option „Löschen“ auf der Registerkarte „Private Endpunktverbindungen“ von Netzwerk (Vorschau) gelöscht werden. Wenn Sie auf die Schaltfläche „Löschen“ klicken, werden der private Endpunkt und die zugehörige NIC gelöscht. Wenn Sie alle privaten Endpunkte zu der FHIR-Ressource löschen und der Zugriff aus dem öffentlichen Netzwerk deaktiviert ist, erreicht keine Anforderung mehr Ihren FHIR-Server. Alle privaten Endpunkte müssen aus der FHIR-Ressource gelöscht werden, bevor die FHIR-Ressource gelöscht oder verschoben werden kann.

![Löschen eines privaten Endpunkts](media/private-link/private-link-delete.png)