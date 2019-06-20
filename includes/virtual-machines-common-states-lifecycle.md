---
title: include file
description: include file
services: virtual-machines
author: shandilvarun
ms.service: virtual-machines
ms.topic: include
ms.date: 08/09/2018
ms.author: vashan, cynthn, rajsqr
ms.custom: include file
ms.openlocfilehash: 57f557a812ec5e4eea75b76ca1394ca360a85d30
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178030"
---
Azure-VMs durchlaufen verschiedene Zustände, die sich in die Kategorien *Bereitstellungszustände* und *Betriebszustände* unterteilen lassen. Im vorliegenden Artikel werden diese Zustände beschrieben, und zudem enthält er genaue Angaben dazu, wann die Instanznutzung Kunden in Rechnung gestellt wird. 

## <a name="power-states"></a>Betriebszustände

Der Betriebszustand stellt den letzten bekannten Zustand der VM dar.

![Diagramm zum VM-Betriebszustand](./media/virtual-machines-common-states-lifecycle/vm-power-states.png)

<br>
Die folgende Tabelle enthält eine Beschreibung der einzelnen Instanzzustände und Angaben dazu, ob die Instanznutzung in Rechnung gestellt wird.

<table>
<tr>
<th>
Zustand
</th>
<th>
BESCHREIBUNG
</th>
<th>
Abrechnung der Instanznutzung
</th>
</tr>
<tr>
<td>
<p><b>Wird gestartet</b></p>
</td>
<td>
<p>Die VM wird gestartet.</p>
<code>"statuses": [<br>
   {<br>
      "code": "PowerState/starting",<br>
       "level": "Info",<br>
        "displayStatus": "VM starting"<br>
    }<br>
    ]</code><br>
</td>
<td>
<p><b>Nicht in Rechnung gestellt</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Wird ausgeführt</b></p>
</td>
<td>
<p>Dies ist der normale Ausführungszustand einer VM.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/running",<br>
 "level": "Info",<br>
 "displayStatus": "VM running"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>In Rechnung gestellt</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Wird beendet</b></p>
</td>
<td>
<p>Dies ist ein Übergangszustand. Nach Abschluss des Vorgangs wird für die VM **Beendet** angezeigt.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopping",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopping"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>In Rechnung gestellt</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Beendet</b></p>
</td>
<td>
<p>Die VM wurde über das Gastbetriebssystem oder mithilfe der PowerOff-APIs heruntergefahren.</p>
<p>Der VM ist weiterhin Hardware zugeordnet, und sie verbleibt auf dem Host. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/stopped",<br>
 "level": "Info",<br>
 "displayStatus": "VM stopped"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Billed&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Zuordnung wird aufgehoben</b></p>
</td>
<td>
<p>Dies ist ein Übergangszustand. Nach Abschluss des Vorgangs wird für die VM **Zuordnung aufgehoben** angezeigt.</p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocating",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocating"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Nicht in Rechnung gestellt&#42;</b></p>
</td>
</tr>
<tr>
<td>
<p><b>Zuordnung aufgehoben</b></p>
</td>
<td>
<p>Die VM wurde erfolgreich beendet und vom Host entfernt. </p>
<code>"statuses": [<br>
 {<br>
 "code": "PowerState/deallocated",<br>
 "level": "Info",<br>
 "displayStatus": "VM deallocated"<br>
 }<br>
 ]</code><br>
</td>
<td>
<p><b>Nicht in Rechnung gestellt</b></p>
</td>
</tr>
</tbody>
</table>


&#42;Für einige Azure-Ressourcen (z.B. Datenträger und Netzwerk) fallen Kosten an. Für Softwarelizenzen für die Instanz fallen keine Gebühren an.

## <a name="provisioning-states"></a>Bereitstellungszustände

Ein Bereitstellungzustand ist der Status eines vom Benutzer initiierten Vorgangs der Steuerungsebene auf der VM. Diese Zustände sind unabhängig vom Betriebszustand einer VM.

- **Erstellen** – Erstellung einer VM.

- **Aktualisieren** – Aktualisierung des Modells für eine vorhandene VM. Einige Änderungen an einer VM, die nicht das Modell betreffen (z.B. Start/Neustart), fallen ebenfalls unter „Aktualisieren“.

- **Löschen** – Löschen einer VM.

- **Zuordnung aufheben** – Beenden einer VM und Entfernen der VM vom Host. Das Aufheben der Zuordnung einer VM gilt als Aktualisierung. Daher werden Bereitstellungszustände im Zusammenhang mit dem Aktualisieren angezeigt.



Die folgenden Übergangszustände treten auf, nachdem die Plattform eine vom Benutzer initiierte Aktion akzeptiert hat:

<br>

<table>
<tbody>
<tr>
<td width="162">
<p><b>Zustände</b></p>
</td>
<td width="366">
<p>BESCHREIBUNG</p>
</td>
</tr>
<tr>
<td width="162">
<p><b>Wird erstellt</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating",<br>
 "level": "Info",<br>
 "displayStatus": "Creating"<br>
 }</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Wird aktualisiert</b></p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/updating",<br>
 "level": "Info",<br>
 "displayStatus": "Updating"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Wird gelöscht</b>.</p>
</td>
<td width="366">
<code>"statuses": [<br>
 {<br>
 "code": "ProvisioningState/deleting",<br>
 "level": "Info",<br>
 "displayStatus": "Deleting"<br>
 }<br>
 ]</code><br>
</td>
</tr>
<tr>
<td width="162">
<p><b>Bereitstellungszustände des Betriebssystems</b></p>
</td>
<td width="366">
<p>Wenn eine VM mit einem Betriebssystemimage und nicht mit einem speziellen Image erstellt wird, können folgende Unterzustände auftreten:</p>
<p>1. <b>OSProvisioningInprogress</b> &ndash; Die VM wird ausgeführt, und das Gastbetriebssystem wird installiert. <p /> 
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningInprogress",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning In progress"<br>
 }<br>
]</code><br>
<p>2. <b>OSProvisioningComplete</b> &ndash; Dies ist ein Zustand von kurzer Dauer. Die VM wechselt schnell in den Zustand **Erfolgreich**, sofern keine Erweiterungen installiert werden müssen. Die Installation von Erweiterungen kann einige Zeit in Anspruch nehmen. <br />
<code> "statuses": [<br>
 {<br>
 "code": "ProvisioningState/creating/OSProvisioningComplete",<br>
 "level": "Info",<br>
 "displayStatus": "OS Provisioning Complete"<br>
 }<br>
]</code><br>
<p><b>Hinweis</b>: Die Bereitstellung des Betriebssystems kann in den Zustand **Fehler** wechseln, wenn ein Fehler des Betriebssystems vorliegt oder das Betriebssystem nicht in der vorgesehenen Zeit installiert wird. Kunden wird die auf der Infrastruktur bereitgestellte VM in Rechnung gestellt.</p>
</td>
</tr>
</table>


Sobald der Vorgang abgeschlossen ist, wechselt die VM in einen der folgenden Zustände:

- **Erfolgreich** – Die vom Benutzer initiierten Aktionen wurden abgeschlossen.

    ```
  "statuses": [ 
  {
     "code": "ProvisioningState/succeeded",
     "level": "Info",
     "displayStatus": "Provisioning succeeded",
     "time": "time"
  }
  ]
    ```

 

- **Fehler** – Stellt einen fehlgeschlagenen Vorgang dar. Weitere Informationen und mögliche Lösungen können Sie den Fehlercodes entnehmen.

    ```
  "statuses": [
    {
      "code": "ProvisioningState/failed/InternalOperationError",
      "level": "Error",
      "displayStatus": "Provisioning failed",
      "message": "Operation abandoned due to internal error. Please try again later.",
      "time": "time"
    }
    ]
    ```



## <a name="vm-instance-view"></a>VM-Instanzenansicht

Die Instanzenansicht-API bietet Informationen zum Ausführungszustand der VM. Weitere Informationen finden Sie in der API-Dokumentation unter [Virtual Machines – Instance View](https://docs.microsoft.com/rest/api/compute/virtualmachines/instanceview) (VMs – Instanzenansicht).

Der Azure-Ressourcen-Explorer bietet eine einfache Benutzeroberfläche zum Anzeigen des Ausführungszustands des virtuellen Computers: [Ressourcen-Explorer](https://resources.azure.com/).

Bereitstellungszustände werden in den VM-Eigenschaften und in der Instanzenansicht angezeigt. Betriebszustände sind in der Instanzenansicht der VM verfügbar. 

