---
title: include file
description: include file
services: virtual-machines
author: axayjo
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: akjosh; cynthn
ms.custom: include file
ms.openlocfilehash: 1476830313296615591a69a2cadd04bcc56b22bc
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178028"
---
Wenn beim Ausführen von Vorgängen an Katalogen mit geteilten Images, Imagedefinitionen und Imageversionen Probleme auftreten, führen Sie den fehlgeschlagenen Befehl erneut im Debugmodus aus. Der Debugmodus wird aktiviert, indem Sie in der CLI den Parameter **-debug** übergeben und bei der PowerShell den Parameter **-Debug**. Nachdem Sie den Fehler lokalisiert haben, befolgen Sie dieses Dokument, um die Fehler zu behandeln.


## <a name="unable-to-create-a-shared-image-gallery"></a>Erstellen eines Katalogs mit geteilten Images nicht möglich

Mögliche Ursachen:

*Der Katalogname ist ungültig.*

Zulässige Zeichen für Katalognamen sind Groß- und Kleinbuchstaben, Zahlen und Punkte. Der Katalogname kann keine Bindestriche enthalten. Ändern Sie den Katalognamen, und versuchen Sie es noch mal. 

*Der Katalogname ist innerhalb Ihres Abonnements nicht eindeutig.*

Wählen Sie einen anderen Katalognamen aus, und versuchen Sie es noch mal.


## <a name="unable-to-create-an-image-definition"></a>Erstellen einer Imagedefinition nicht möglich 

Mögliche Ursachen:

*Die Imagedefinition ist ungültig.*

Zulässige Zeichen für Imagedefinitionen sind Groß- und Kleinbuchstaben, Zahlen, Punkte und Bindestriche. Ändern Sie den Namen der Imagedefinition, und versuchen Sie es noch mal.

*Die obligatorischen Eigenschaften zum Erstellen einer Imagedefinition wurden nicht aufgefüllt.*

Die Eigenschaften wie Name, Herausgeber, Angebot, SKU und BS-Typ sind obligatorisch. Überprüfen Sie, ob alle Eigenschaften übergeben wurden.

Stellen Sie sicher, dass der **OSType** der Imagedefinition, entweder Linux oder Windows, derselbe ist wie der des verwalteten Quellimages, das Sie verwenden, um die Imageversion zu erstellen. 


## <a name="unable-to-create-an-image-version"></a>Erstellen einer Imageversion nicht möglich 

Mögliche Ursachen:

*Der Name der Imageversion ist ungültig.*

Zulässige Zeichen für die Imageversion sind Zahlen und Punkte. Zahlen müssen im Bereich einer ganzen 32-Bit-Zahl liegen. Format: *MajorVersion.MinorVersion.Patch*. Ändern Sie den Namen der Imageversion, und versuchen Sie es noch mal.

*Das verwaltete Quellimage, aus dem die Imageversion erstellt wird, wurde nicht gefunden.* 

Überprüfen Sie, ob das Quellimage vorhanden ist und sich in derselben Region wie die Imageversion befindet.

*Die Bereitstellung des verwalteten Images ist noch nicht abgeschlossen.*

Stellen Sie sicher, dass der Bereitstellungsstatus des verwalteten Quellimages **Erfolgreich** ist.

*Die Liste der Zielregionen enthält nicht die Quellregion.*

Die Liste der Zielregionen muss die Quellregion der Imageversion enthalten. Stellen Sie sicher, dass Sie die Quellregion in die Liste der Zielregionen aufgenommen haben, in die Azure Ihre Imageversion replizieren soll.

*Die Replikation in alle Zielregionen ist noch nicht abgeschlossen.*

Verwenden Sie den Parameter **--expand ReplicationStatus**, um zu überprüfen, ob die Replikation in alle angegebenen Zielregionen abgeschlossen wurde. Wenn nicht, warten Sie bis zu 6 Stunden auf den Abschluss des Auftrags. Falls dies fehlschlägt, führen Sie den Befehl erneut aus, um die Imageversion zu erstellen und zu replizieren. Wenn es viele Zielregionen gibt, in die die Imageversion repliziert wird, erwägen Sie, die Replikation in mehreren Phasen durchzuführen.

## <a name="unable-to-create-a-vm-or-a-scale-set"></a>Erstellen eines virtuellen Computers oder einer Skalierungsgruppe nicht möglich 

Mögliche Ursachen:

*Der Benutzer, der versucht, eine VM oder eine VM-Skalierungsgruppe zu erstellen, hat keinen Lesezugriff auf die Imageversion.*

Wenden Sie sich an den Abonnementbesitzer und bitten Sie ihn,mittels [rollenbasierter Zugriffssteuerung](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (RBAC) den Lesezugriff auf die Imageversion oder die übergeordneten Ressourcen (z. B. den Katalog mit geteilten Images oder die Imagedefinition) zu gewähren. 

*Die Imageversion wurde nicht gefunden.*

Überprüfen Sie, ob die Region, in der Sie versuchen, eine VM oder VM-Skalierungsgruppe zu erstellen, in der Liste der Zielregionen der Imageversion enthalten ist. Wenn sich die Region bereits in der Liste der Zielregionen befindet, überprüfen Sie, ob der Replikationsauftrag abgeschlossen wurde. Sie können den Parameter **-ReplicationStatus** verwenden, um zu überprüfen, ob die Replikation in alle angegebenen Zielregionen abgeschlossen wurde. 

*Das Erstellen der VM oder VM-Skalierungsgruppe dauert lange.*

Überprüfen Sie, ob der **OSType** der Imageversion, aus der Sie die VM oder VM-Skalierungsgruppe erstellen möchten, derselbe **OSType** wie der des verwalteten Quellimages ist, das Sie verwendet haben, um die Imageversion zu erstellen. 

## <a name="unable-to-share-resources"></a>Teilen von Ressourcen nicht möglich

Das Teilen von Ressourcen aus Katalogen mit freigegebenen Images, Imagedefinitionen und Imageversionen zwischen Abonnements wird mithilfe der [rollenbasierten Zugriffssteuerung](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles) (Role-Based Access Control, RBAC) aktiviert. 

## <a name="replication-is-slow"></a>Replikation ist langsam

Verwenden Sie den Parameter **--expand ReplicationStatus**, um zu überprüfen, ob die Replikation in alle angegebenen Zielregionen abgeschlossen wurde. Wenn nicht, warten Sie bis zu 6 Stunden auf den Abschluss des Auftrags. Falls dies fehlschlägt, lösen Sie den Befehl erneut aus, um die Imageversion zu erstellen und zu replizieren. Wenn es viele Zielregionen gibt, in die die Imageversion repliziert wird, erwägen Sie, die Replikation in mehreren Phasen durchzuführen.

## <a name="azure-limits-and-quotas"></a>Azure-Grenzwerte und -Kontingente 

[Azure-Grenzwerte und -Kontingente](https://docs.microsoft.com/azure/azure-subscription-service-limits) gelten für alle Katalog mit geteilten Images-, Imagedefinitions- und Imageversionsressourcen. Stellen Sie sicher, dass Sie die Grenzwerte für Ihre Abonnements einhalten. 



