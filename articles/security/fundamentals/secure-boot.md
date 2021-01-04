---
title: Sicherer Start von Firmware – Azure Security
description: Technische Übersicht über den sicheren Start von Azure-Firmware.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: feb28b1d448d0146046ed789d1389a3a42f344de
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557379"
---
# <a name="secure-boot"></a>Sicherer Start

Secure Boot (sicherer Start) ist ein Feature des [Unified Extensible Firmware Interface](https://en.wikipedia.org/wiki/Unified_Extensible_Firmware_Interface) (UEFI), das erfordert, dass alle Low-Level-Firmware- und -Softwarekomponenten vor dem Laden verifiziert werden müssen. Während des Startvorgangs überprüft UEFI Secure Boot die Signatur jede Komponente der Startsoftware, einschließlich der UEFI-Firmwaretreiber (auch als Option-ROMs bezeichnet), der EFI-Anwendungen (Extensible Firmware Interface) sowie der Betriebssystemtreiber und -Binärdateien. Wenn die Signaturen gültig sind oder vom Original Equipment Manufacturer (OEM) als vertrauenswürdig eingestuft werden, wird der Computer gestartet, und die Firmware übergibt die Steuerung an das Betriebssystem.

## <a name="components-and-process"></a>Komponenten und Prozess

Secure Boot basiert auf diesen wichtigen Komponenten:

- Plattformschlüssel (PK): Richtet eine Vertrauensstellung zwischen dem Plattformbesitzer (Microsoft) und der Firmware ein. Die öffentliche Hälfte ist „PKpub“, und die private Hälfte ist „PKpriv“.
- Datenbank der Schlüsselregistrierungsschlüssel (KEK): Richtet eine Vertrauensstellung zwischen dem Betriebssystem und der Plattformfirmware ein. Die öffentliche Hälfte ist „KEKpub“, und die private Hälfte ist „KEKpriv“.
- Signaturdatenbank (db): Enthält die Digests für vertrauenswürdige Signaturgeber (öffentliche Schlüssel und Zertifikate) der Firmware- und Softwarecodemodule, die zur Interaktion mit der Plattformfirmware autorisiert sind.
- Datenbank für widerrufene Signaturen (dbx): Enthält Digests widerrufener Codemodule, die als schädlich, anfällig, kompromittiert oder nicht vertrauenswürdig eingestuft wurden. Befindet sich ein Hash in der Signaturdatenbank und gleichzeitig in der Datenbank für widerrufene Signaturen, hat die Datenbank für widerrufene Signaturen Vorrang.

Die folgende Abbildung und der folgende Prozess erläutern, wie diese Komponenten aktualisiert werden:

![Diagramm der Secure Boot-Komponenten.](./media/secure-boot/secure-boot.png)

Der OEM speichert den Secure Boot-Digests zum Zeitpunkt der Fertigung im nicht flüchtigen RAM (NV-RAM) des Computers.

1. Die Signaturdatenbank (db) wird mit den Signaturgebern oder Bildhashs von UEFI-Anwendungen, Betriebssystem-Ladeprogrammen (z. B. dem Ladeprogramm oder Start-Manager für das Microsoft-Betriebssystem) und UEFI-Treibern aufgefüllt, die als vertrauenswürdig eingestuft werden.
2. Die Datenbank für widerrufene Signaturen (dbx) wird mit Digests von Modulen aufgefüllt, die als nicht mehr vertrauenswürdig eingestuft sind.
3. Die Datenbank der Schlüsselregistrierungsschlüssel (KEK) wird mit Signaturschlüsseln aufgefüllt, die zum Aktualisieren der Signaturdatenbank und der Datenbank für widerrufene Signaturen verwendet werden können. Die Datenbanken können über Updates, die mit dem richtigen Schlüssel signiert sind, oder über Updates durch einen physisch anwesenden autorisierten Benutzer mithilfe von Firmwaremenüs bearbeitet werden.
4. Nachdem die db-, dbx- und KEK-Datenbanken hinzugefügt wurden und die abschließenden Überprüfungen und Tests der Firmware fertig gestellt sind, sperrt der OEM die Firmware, damit diese nicht mehr bearbeitet werden kann, und generiert einen Plattformschlüssel (PK). Die PK kann verwendet werden, um Aktualisierungen des KEK zu signieren oder um Secure Boot zu deaktivieren.

Während jeder Phase des Startprozesses werden die Digests von Firmware, Bootloader, Betriebssystem, Kerneltreibern und anderen Startzeichenfolgen-Artefakte berechnet und mit zulässigen Werten verglichen. Firmware und Software, die als nicht vertrauenswürdig erkannt werden, dürfen nicht geladen werden. Daher können Schadsoftwareeinschleusungen auf niedriger Ebene oder Schadsoftwareangriffe vor dem Start blockiert werden.

## <a name="secure-boot-on-the-azure-fleet"></a>Secure Boot in der Azure-Flotte
Heute wird jeder in die Azure-Computeflotte integrierte und zum Hosten von Kundenworkloads bereitgestellte Computer mit werkseitig aktiviertem Secure Boot ausgeliefert. In jeder Phase der Hardwareentwicklungs- und -integrationspipeline stehen spezialisierte Tools und Prozesse zur Verfügung, um sicherzustellen, dass die Aktivierung von Secure Boot weder versehentlich noch in böswilliger Absicht rückgängig gemacht wird.

Die Überprüfung, ob db- und dbx-Digests korrekt sind, gewährleistet Folgendes:

- Der Bootloader ist in einem der Datenbankeinträge vorhanden.
- Die Signatur des Bootloaders ist gültig.
- Der Host wird mit vertrauenswürdiger Software gestartet.

 Durch Überprüfen der Signaturen von „KEKpub“ und „PKpub“ kann sichergestellt werden, dass nur vertrauenswürdige Parteien die Berechtigung erhalten, die Definitionen der als vertrauenswürdig eingestuften Software zu ändern. Indem sichergestellt wird, dass Secure Boot aktiv ist, wird letztlich gewährleistet, dass diese Definitionen durchgesetzt werden.

## <a name="next-steps"></a>Nächste Schritte
Weitere Informationen darüber, was wir tun, um die Integrität und Sicherheit von Plattformen voranzutreiben, finden Sie unter:

- [Firmwaresicherheit](firmware.md)
- [Gemessener Start- und Hostnachweis](measured-boot-host-attestation.md)
- [Project Cerberus](project-cerberus.md)
- [Verschlüsselung ruhender Daten](encryption-atrest.md)
- [Hypervisor-Sicherheit](hypervisor.md)