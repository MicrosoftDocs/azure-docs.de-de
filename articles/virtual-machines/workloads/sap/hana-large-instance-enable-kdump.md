---
title: Skript zum Aktivieren von Kdump in SAP HANA (große Instanzen) | Microsoft-Dokumentation
description: Skript zum Aktivieren von Kdump in SAP HANA (große Instanzen) HLI-Typ I und HLI-Typ II
services: virtual-machines-linux
documentationcenter: ''
author: prtyag
manager: hrushib
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/30/2020
ms.author: prtyag
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: b6afcfe7684102b451860d9916d849842489cace
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/20/2020
ms.locfileid: "94967702"
---
# <a name="kdump-for-sap-hana-on-azure-large-instances-hli"></a>Kdump für SAP HANA in Azure (große Instanzen) (HLI)

Das Konfigurieren und Aktivieren von Kdump ist ein Schritt, der erforderlich ist, um Probleme mit Systemabstürzen zu beheben, die keine klare Ursache haben.
Manchmal stürzt ein System unerwartet ab, ohne dass dies durch Hardware- oder Infrastrukturprobleme erklärt werden kann.
In diesen Fällen kann es sich um ein Betriebssystem- oder Anwendungsproblem handeln. Mit Kdump unter SUSE kann ermittelt werden, warum ein System abgestürzt ist.

## <a name="enable-kdump-service"></a>Aktivieren des Kdump-Diensts

In diesem Artikel wird das Aktivieren des Kdump-Diensts für große Azure HANA-Instanzen (**Typ I und Typ II**) behandelt.

## <a name="supported-skus"></a>Unterstützte SKUs

|  Typ der großen HANA-Instanz   |  Betriebssystemhersteller   |  Betriebssystem-Paketversion   |  SKU        |
|-----------------------------|--------------|-----------------------|-------------|
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S224m      |
|   Typ I                    |  SuSE        |   SLES 12 SP4         |  S224m      |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S72        |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S72m       |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S72m       |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S96        |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S96        |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S192       |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S192       |
|   Typ I                    |  SuSE        |   SLES 12 SP4         |  S192       |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S192m      |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S192m      |
|   Typ I                    |  SuSE        |   SLES 12 SP4         |  S192m      |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S144       |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S144       |
|   Typ I                    |  SuSE        |   SLES 12 SP2         |  S144m      |
|   Typ I                    |  SuSE        |   SLES 12 SP3         |  S144m      |
|   Typ II                   |  SuSE        |   SLES 12 SP2         |  S384       |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S384       |
|   Typ II                   |  SuSE        |   SLES 12 SP4         |  S384       |
|   Typ II                   |  SuSE        |   SLES 12 SP2         |  S384xm     |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S384xm     |
|   Typ II                   |  SuSE        |   SLES 12 SP4         |  S384xm     |
|   Typ II                   |  SuSE        |   SLES 12 SP2         |  S576m      |
|   Typ II                   |  SuSE        |   SLES 12 SP3         |  S576m      |
|   Typ II                   |  SuSE        |   SLES 12 SP4         |  S576m      |

## <a name="prerequisites"></a>Voraussetzungen

- Der Kdump-Dienst verwendet das `/var/crash`-Verzeichnis zum Schreiben von Absturzabbildern. Stellen Sie sicher, dass die Partition, die diesem Verzeichnis entspricht, über ausreichend Speicherplatz verfügt, um Absturzabbilder zu speichern.

## <a name="setup-details"></a>Details zur Konfiguration

- Das Skript zum Aktivieren von Kdump finden Sie [hier](https://github.com/Azure/sap-hana/blob/master/tools/enable-kdump.sh).
> [!NOTE]
> Dieses Skript wurde auf Grundlage unserer Lab-Einrichtung erstellt. Der Kunde muss sich zur weiteren Optimierung an den Betriebssystemhersteller wenden.
> Für die neuen und vorhandenen Server wird eine separate LUN zum Speichern der Abbilder bereitgestellt, und das Skript übernimmt die Konfiguration des Dateisystems aus der LUN.
> Microsoft ist nicht für die Analyse des Abbilds verantwortlich. Der Kunde muss ein Ticket beim Hersteller des Betriebssystems öffnen, um die Analyse durchführen zu lassen.

- Führen Sie dieses Skript mithilfe des folgenden Befehls in einer großen HANA-Instanz aus.

    > [!NOTE]
    > Zur Ausführung dieses Befehls ist die sudo-Berechtigung erforderlich.

    ```bash
    sudo bash enable-kdump.sh
    ```

- Wenn der Befehl ausgibt, dass Kdump erfolgreich aktiviert wurde, müssen Sie das System neu starten, damit die Änderungen übernommen werden.

- Wenn die Befehlsausgabe einen Fehlschlag angibt, wurde der Kdump-Dienst nicht aktiviert. In diesem Fall finden Sie weitere Informationen im Abschnitt [Supportprobleme](#support-issue).

## <a name="test-kdump"></a>Testen von Kdump

> [!NOTE]
>  Mit dem folgenden Vorgang werden ein Kernel-Absturz und ein Systemneustart ausgelöst.

- Lösen Sie einen Kernel-Absturz aus.

    ```bash
    echo c > /proc/sysrq-trigger
    ```

- Überprüfen Sie nach erfolgreichem Systemneustart, ob das `/var/crash`-Verzeichnis Kernel-Absturzprotokolle enthält.

- Wenn `/var/crash` ein Verzeichnis mit dem aktuellen Datum enthält, wurde Kdump erfolgreich aktiviert.

## <a name="support-issue"></a>Supportprobleme

Wenn das Skript mit einem Fehler fehlschlägt oder Kdump nicht aktiviert wurde, wenden Sie sich mit einem Service Request und den folgenden Informationen an das Microsoft-Supportteam.

* HLI-Abonnement-ID

* Servername

* Betriebssystemhersteller

* Betriebssystemversion

* Kernelversion

## <a name="related-documents"></a>Verwandte Dokumente
- Weitere Informationen zum [Konfigurieren von Kdump](https://www.suse.com/support/kb/doc/?id=3374462)
