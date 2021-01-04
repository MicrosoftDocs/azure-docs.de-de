---
title: Fehler in der seriellen Azure-Konsole | Microsoft-Dokumentation
description: Häufige Fehler in der seriellen Azure-Konsole
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/20/2019
ms.author: alsin
ms.openlocfilehash: cad12a55332a6c7898f9709776c58d7dba8dd81a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022835"
---
# <a name="common-errors-within-the-azure-serial-console"></a>Häufige Fehler in der seriellen Azure-Konsole
In der seriellen Azure-Konsole können mehrere bekannte Fehler auftreten. Diese Fehler und die jeweils entsprechenden Lösungsschritte sind nachfolgend aufgeführt.

## <a name="common-errors"></a>Häufige Fehler

Fehler                             |   Minderung
:---------------------------------|:--------------------------------------------|
„Für die serielle Azure-Konsole muss die Startdiagnose aktiviert sein. Klicken Sie hier, um die Startdiagnose für Ihren virtuellen Computer zu konfigurieren.“ | Stellen Sie sicher, dass die [Startdiagnose](boot-diagnostics.md) für den virtuellen Computer oder die VM-Skalierungsgruppe aktiviert ist. Wenn Sie die serielle Konsole in einer VM-Skalierungsgruppeninstanz verwenden, vergewissern Sie sich, dass für die Instanz das neueste Modell verwendet wird.
„Für die serielle Azure-Konsole muss ein virtueller Computer ausgeführt werden. Verwenden Sie die Schaltfläche ‚Start‘ oben, um Ihren virtuellen Computer zu starten.“  | Der virtuelle Computer oder die VM-Skalierungsgruppeninstanz muss den Status „Gestartet“ aufweisen, um auf die serielle Konsole zugreifen zu können. (Der virtuelle Computer darf weder beendet noch freigegeben sein.) Stellen Sie sicher, dass der virtuelle Computer oder die VM-Skalierungsgruppeninstanz ausgeführt wird, und wiederholen Sie den Vorgang.
„Die serielle Azure-Konsole ist für dieses Abonnement nicht aktiviert. Wenden Sie sich an Ihren Abonnementadministrator, um sie zu aktivieren.“ | Die serielle Azure-Konsole kann auf Abonnementebene deaktiviert werden. Als Abonnementadministrator können Sie [die serielle Azure-Konsole aktivieren und deaktivieren](./serial-console-enable-disable.md). Wenn Sie kein Abonnementadministrator sind, wenden Sie sich für die nächsten Schritte an Ihren Abonnementadministrator.
Beim Zugriff auf das Speicherkonto für die Startdiagnose dieses virtuellen Computers wurde eine „Forbidden“-Antwort empfangen. | Stellen Sie sicher, dass die Startdiagnose nicht über eine Kontofirewall verfügt. Damit die serielle Konsole funktioniert, ist Zugriff auf ein Startdiagnose-Speicherkonto erforderlich. Die serielle Konsole kann programmbedingt nicht verwendet werden, wenn für das Speicherkonto mit Startdiagnose Speicherkontofirewalls aktiviert sind.
Sie verfügen nicht über die erforderlichen Berechtigungen, um diese VM mit der seriellen Konsole zu verwenden. Achten Sie darauf, dass Sie mindestens über die Berechtigungen der Rolle „Mitwirkender für virtuelle Computer“ verfügen.| Für den Zugriff auf die serielle Konsole müssen Sie über Berechtigungen auf der Zugriffsebene „Mitwirkender“ oder höher für den virtuellen Computer oder die VM-Skalierungsgruppe verfügen. Weitere Informationen finden Sie auf der Seite mit der [Übersicht](serial-console-overview.md).
Das Speicherkonto „{0}“ für die Startdiagnose auf dieser VM wurde nicht gefunden. Stellen Sie sicher, dass die Startdiagnose für diesen virtuellen Computer aktiviert ist, dass dieses Speicherkonto nicht gelöscht wurde und dass Sie Zugriff auf dieses Speicherkonto haben. | Vergewissern Sie sich, dass Sie das Startdiagnose-Speicherkonto für den virtuellen Computer oder die VM-Skalierungsgruppe nicht gelöscht haben.
Fehler bei der Verbindung der seriellen Konsole mit der VM: 'Bad Request' (400). (400 – Ungültige Anforderung.) | Dieser Fehler kann auftreten, wenn Ihr Startdiagnose-URI nicht korrekt ist. Manchmal wurde beispielsweise „http://“ verwendet anstatt „https://“. Der Startdiagnose-URI kann mithilfe des folgenden Befehls korrigiert werden: `az vm boot-diagnostics enable --name vmName --resource-group rgName --storage https://<storageAccountUri>.blob.core.windows.net/`
Sie haben nicht die erforderlichen Berechtigungen zum Schreiben in das Startdiagnose-Speicherkonto für diesen virtuellen Computer. Vergewissern Sie sich, dass Sie mindestens die Berechtigungen eines VM-Mitwirkenden besitzen. | Für den Zugriff auf die serielle Konsole sind Berechtigungen der Zugriffsebene „Mitwirkender“ für das Startdiagnose-Speicherkonto erforderlich. Weitere Informationen finden Sie auf der Seite mit der [Übersicht](serial-console-overview.md).
Die Ressourcengruppe für das Startdiagnose-Speicherkonto *&lt;STORAGEACCOUNTNAME&gt;* kann nicht ermittelt werden. Stellen Sie sicher, dass die Startdiagnose für diesen virtuellen Computer aktiviert ist und Sie über Zugriff auf dieses Speicherkonto verfügen. | Für den Zugriff auf die serielle Konsole sind Berechtigungen der Zugriffsebene „Mitwirkender“ für das Startdiagnose-Speicherkonto erforderlich. Weitere Informationen finden Sie auf der Seite mit der [Übersicht](serial-console-overview.md).
Die Bereitstellung für diesen virtuellen Computer war noch nicht erfolgreich. Stellen Sie sicher, dass der virtuelle Computer vollständig bereitgestellt wird, und versuchen Sie dann noch einmal, die Verbindung mit der seriellen Konsole herzustellen. | Die Bereitstellung des virtuellen Computers oder der VM-Skalierungsgruppe ist möglicherweise noch nicht abgeschlossen. Warten Sie einige Zeit, und versuchen Sie es erneut.
Das Websocket ist geschlossen oder konnte nicht geöffnet werden. | Möglicherweise müssen Sie Firewallzugriff auf `*.console.azure.com` hinzufügen. Ein detaillierterer, aber längerer Ansatz besteht darin, Firewallzugriff auf die [IP-Bereiche des Microsoft Azure-Rechenzentrums](https://www.microsoft.com/download/details.aspx?id=41653) zuzulassen, die sich jedoch relativ regelmäßig ändern.
Die serielle Konsole funktioniert nicht mit einem Speicherkonto, für das Azure Data Lake Storage Gen2 mit hierarchischen Namespaces verwendet wird. | Dies ist ein bekanntes Problem mit hierarchischen Namespaces. Stellen Sie zur Behebung des Problems sicher, dass das Startdiagnose-Speicherkonto Ihrer VM nicht per Azure Data Lake Storage Gen2 erstellt wird. Diese Option kann nur bei der Erstellung des Speicherkontos festgelegt werden. Unter Umständen müssen Sie ein separates Startdiagnose-Speicherkonto ohne Aktivierung von Azure Data Lake Storage Gen2 erstellen, um dieses Problem zu beheben.
Fehler bei der Verbindung der seriellen Konsole mit der VM: 'Verboten'(SubscriptionNotEnabled) - Abonnementname nicht definiert, ID \<subscription id> befindet sich in einem nicht aktivierten Zustand | Dieses Problem kann auftreten, wenn das Abonnement, für das ein Benutzer sein Cloud Shell-Speicherkonto in erstellt hat, deaktiviert wurde. Um dies zu verhindern, starten Sie Cloud Shell und [führen Sie die erforderlichen Schritte](../../cloud-shell/persisting-shell-storage.md#unmount-clouddrive-1) aus, um erneut ein unterstützendes Speicherkonto für Cloud Shell im aktuellen Abonnement bereitzustellen.

## <a name="next-steps"></a>Nächste Schritte
* Weitere Informationen zur [seriellen Azure-Konsole für virtuelle Linux-Computer](./serial-console-linux.md)
* Weitere Informationen zur [seriellen Azure-Konsole für virtuelle Windows-Computer](./serial-console-windows.md)
