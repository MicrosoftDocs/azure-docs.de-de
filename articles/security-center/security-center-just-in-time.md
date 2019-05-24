---
title: Just-In-Time-VM-Zugriff in Azure Security Center | Microsoft-Dokumentation
description: In diesem Dokument wird beschrieben, wie Sie mit Just-In-Time-VM-Zugriff in Azure Security Center den Zugriff auf die virtuellen Azure-Computer steuern können.
services: security-center
documentationcenter: na
author: monhaber
manager: barbkess
editor: ''
ms.assetid: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/28/2019
ms.author: v-mohabe
ms.openlocfilehash: b07a89491343220c5c3411b5fc525f9b43f54e30
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 05/21/2019
ms.locfileid: "65968508"
---
# <a name="manage-virtual-machine-access-using-just-in-time"></a>Verwalten des Zugriffs auf virtuelle Computer mithilfe des Just-In-Time-Features

Mit Just-In-Time-VM-Zugriff (JIT-VM-Zugriff) kann eingehender Datenverkehr auf den Azure-VMs gesperrt werden, um die Gefährdung durch Angriffe zu reduzieren und bei Bedarf einfachen Zugriff auf Verbindungen mit virtuellen Computern bereitzustellen.

> [!NOTE]
> Das Just-In-Time-Feature ist im Standard-Tarif von Security Center verfügbar.  Weitere Informationen zu den Tarifen von Security Center finden Sie unter [Preise](security-center-pricing.md).
>
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="attack-scenario"></a>Angriffsszenario

Bei Brute-Force-Angriffen wird im Allgemeinen versucht, über Verwaltungsports Zugriff auf einen virtuellen Computer zu erlangen. Im Erfolgsfall kann ein Angreifer die Kontrolle über den virtuellen Computer erlangen und in Ihrer Umgebung Fuß fassen.

Eine Möglichkeit, die Gefährdung durch Brute-Force-Angriffe zu verringern, ist das Beschränken der Zeitspanne, in der ein Port geöffnet ist. Verwaltungsports müssen nicht jederzeit geöffnet sein. Sie müssen nur geöffnet sein, während eine Verbindung mit dem virtuellen Computer besteht, z.B. zum Ausführen von Verwaltungs- oder Wartungsaufgaben. Wenn Just-In-Time aktiviert ist, verwendet das Security Center [Netzwerksicherheitsgruppen](../virtual-network/security-overview.md#security-rules)-Regeln (NSG), die den Zugriff auf Verwaltungsports beschränken, um sie vor Angriffen zu schützen.

![Just-In-Time-Szenario](./media/security-center-just-in-time/just-in-time-scenario.png)

## <a name="how-does-jit-access-work"></a>Wie funktioniert JIT-Zugriff?

Wenn Just-In-Time aktiviert ist, sperrt das Security Center durch das Erstellen einer NSG-Regel eingehenden Datenverkehr auf den Azure-VMs. Sie wählen die Ports auf dem virtuellen Computer aus, für die eingehender Datenverkehr gesperrt wird. Diese Ports werden durch die Just-In-Time-Lösung gesteuert.

Wenn ein Benutzer auf einen virtuellen Computer zugreift, überprüft das Security Center, ob der Benutzer über Berechtigungen der [rollenbasierten Zugriffssteuerung (Role-Based Access Control, RBAC)](../role-based-access-control/role-assignments-portal.md) verfügt, die es ihm ermöglichen, erfolgreich Zugriff auf einen virtuellen Computer anzufordern. Wird die Anforderung genehmigt, konfiguriert Security Center die Netzwerksicherheitsgruppen (NSGs) automatisch so, dass für die angegebene Zeitspanne eingehender Datenverkehr an die ausgewählten Ports und angeforderten Quell-IP-Adressen oder -Bereiche zugelassen wird. Nach Ablauf dieser Zeitspanne stellt das Security Center die vorherigen Status der NSGs wieder her. Die bereits eingerichteten Verbindungen werden jedoch nicht unterbrochen.

> [!NOTE]
> Der durch das Security Center gesteuerte Just-In-Time-Zugriff auf virtuelle Computer unterstützt derzeit nur virtuelle Computer, die über Azure Resource Manager bereitgestellt wurden. Weitere Informationen über das klassische Bereitstellungsmodell und das Resource Manager-Bereitstellungsmodell finden Sie unter [Azure Resource Manager-Bereitstellung im Vergleich zur klassischen Bereitstellung](../azure-resource-manager/resource-manager-deployment-model.md).
>
>

Sie können auf folgende Arten auf JIT zugreifen:
- [Verwenden von JIT-Zugriff in Azure Security Center](#jit-asc)
- [Verwenden von JIT-Zugriff auf dem Blatt einer Azure-VM](#jit-vm)

## Verwenden von JIT-Zugriff in Azure Security Center <a name="jit-asc"></a>

1. Öffnen Sie das Dashboard **Security Center**.

2. Wählen Sie im linken Bereich die Option **JIT-VM-Zugriff** aus.

![Kachel „JIT-VM-Zugriff“](./media/security-center-just-in-time/just-in-time.png)

Das Fenster **JIT-VM-Zugriff** wird geöffnet.

![Kachel „JIT-VM-Zugriff“](./media/security-center-just-in-time/just-in-time-access.png)

**JIT-VM-Zugriff** enthält Informationen zum Status Ihrer virtuellen Computer:

- **Konfiguriert**: Virtuelle Computer, die für die Unterstützung von Just-In-Time-Zugriff konfiguriert wurden. Die dargestellten Daten betreffen die letzte Woche und enthalten für jeden virtuellen Computer die Anzahl der genehmigten Anforderungen, Datum und Uhrzeit des letzten Zugriffs sowie den letzten Benutzer.
- **Empfohlen**: Virtuelle Computer, die Just-In-Time-VM-Zugriff unterstützen, jedoch nicht entsprechend konfiguriert wurden. Es wird empfohlen, die Just-In-Time-VM-Zugriffssteuerung für diese virtuellen Computer zu aktivieren. Weitere Informationen finden Sie unter [Konfigurieren einer Just-In-Time-Zugriffsrichtlinie](#jit-config).
- **Keine Empfehlung** – Mögliche Gründe, dass ein virtueller Computer nicht empfohlen wird:
  - Fehlende NSG: Die Just-In-Time-Lösung erfordert, dass eine NSG festgelegt ist.
  - Klassischer virtueller Computer: Der durch das Security Center gesteuerte Just-In-Time-VM-Zugriff unterstützt derzeit nur virtuelle Computer, die über Azure Resource Manager bereitgestellt wurden. Klassische Bereitstellungen werden durch die Just-In Time-Lösung nicht unterstützt.
  - Andere: Ein virtueller Computer fällt in diese Kategorie, wenn die Just-In-Time-Lösung in der Sicherheitsrichtlinie des Abonnements oder der Ressourcengruppe deaktiviert ist oder wenn der virtuelle Computer über keine öffentliche IP-Adresse und über keine NSG verfügt.

### Konfigurieren einer JIT-Zugriffsrichtlinie<a name="jit-config"></a>

So wählen Sie den virtuellen Computer aus, den Sie aktivieren möchten:

1. Wählen Sie unter **JIT-VM-Zugriff** die Registerkarte**Empfohlen** aus.

   ![Aktivieren des Just-in-Time-Zugriffs](./media/security-center-just-in-time/enable-just-in-time-access.png)

2. Wählen Sie unter **Virtueller Computer** die virtuellen Computer aus, die Sie aktivieren möchten. Hierdurch wird der virtuelle Computer mit einem Häkchen markiert.
3. Wählen Sie **JIT auf VMs aktivieren** aus.
   1. Auf diesem Blatt werden die von Azure Security Center empfohlenen Standardports angezeigt:
      - 22 – SSH
      - 3389 – RDP
      - 5985 – WinRM 
      - 5986 – WinRM
   2. Sie können auch benutzerdefinierte Ports konfigurieren. Wählen Sie dazu **Hinzufügen** aus. 
   3. In **Portkonfiguration hinzufügen** können Sie für jeden Port (sowohl Standard- als auch benutzerdefinierter Port), der konfiguriert werden soll, die folgenden Einstellungen anpassen:
      - **Protokolltyp**: Das Protokoll, das auf diesem Port zulässig ist, wenn eine Anforderung genehmigt wird.
      - **Zulässige Quell-IP-Adressen**: Die IP-Bereiche, die für diesen Port zulässig sind, wenn eine Anforderung genehmigt wird.
      - **Maximale Anforderungsdauer**: Das maximale Zeitfenster, in dem ein bestimmter Port geöffnet werden kann.

4. Wählen Sie **Speichern** aus.


> [!NOTE]
>Wenn der JIT-VM-Zugriff für eine VM aktiviert ist, erstellt das Azure Security Center Regeln zum Ablehnen von sämtlichem eingehenden Datenverkehr für die ausgewählten Ports in den Netzwerksicherheitsgruppen, die ihr zugeordnet sind. Wurden für die ausgewählten Ports andere Regeln erstellt, so haben die bestehenden Regeln Vorrang vor den neuen Regeln zum Ablehnen von sämtlichem eingehenden Datenverkehr. Wenn es für die ausgewählten Ports keine Regeln gibt, dann haben die neuen Regeln zum Ablehnen von sämtlichem eingehenden Datenverkehr in den Netzwerksicherheitsgruppen höchste Priorität.
>

### <a name="request-jit-access-to-a-vm"></a>Anfordern von JIT-Zugriff auf einen virtuellen Computer

So fordern Sie Zugriff auf einen virtuellen Computer an:
1.  Wählen Sie unter **JIT-VM-Zugriff** die Option **Konfiguriert** aus.
2.  Überprüfen Sie unter **VMs** die virtuellen Computer, für die Sie Just-In-Time-Zugriff aktivieren möchten.
3.  Wählen Sie **Zugriff anfordern** aus. 
  ![Zugriff auf einen virtuellen Computer anfordern](./media/security-center-just-in-time/request-access-to-a-vm.png)
4.  Konfigurieren Sie unter **Zugriff anfordern** für jeden virtuellen Computer die zu öffnenden Ports und die Quell-IP-Adressen, für die der jeweilige Port geöffnet wird, und das Zeitfenster, in dem der Port geöffnet wird. Es kann nur Zugriff auf die Ports angefordert werden, die in der Just-In-Time-Richtlinie konfiguriert sind. Für jeden Port gilt eine maximal zulässige Zeitspanne, die aus der Just-In-Time-Richtlinie abgeleitet ist.
5.  Wählen Sie **Ports öffnen** aus.

> [!NOTE]
> Wenn sich ein Benutzer, der Zugriff anfordert, hinter einem Proxy befindet, funktioniert die Option **Meine IP-Adresse** möglicherweise nicht. Möglicherweise müssen Sie den vollständigen IP-Adressenbereich der Organisation definieren.

### <a name="editing-a-jit-access-policy"></a>Bearbeiten einer JIT-Zugriffsrichtlinie

Sie können die vorhandene Just-In-Time-Richtlinie eines virtuellen Computers ändern, indem Sie einen neuen Port, der für diesen virtuellen Computer geschützt werden soll, hinzufügen und konfigurieren oder eine beliebige andere Einstellung eines bereits geschützten Ports ändern.

So bearbeiten Sie eine vorhandene Just-In-Time-Richtlinie eines virtuellen Computers:
1. Wählen Sie auf der Registerkarte **Konfiguriert** unter **VMs** einen virtuellen Computer aus, dem ein Port hinzugefügt werden soll, indem Sie auf die drei Punkte in der Zeile für den betreffenden virtuellen Computer klicken. 
2. Wählen Sie **Bearbeiten** aus.
3. Unter **JIT-VM-Zugriffskonfiguration** können Sie die vorhandenen Einstellungen eines bereits geschützten Ports bearbeiten oder einen neuen benutzerdefinierten Port hinzufügen. Weitere Informationen finden Sie unter [Konfigurieren einer Just-In-Time-Zugriffsrichtlinie](#jit-config). 
  ![JIT-VM-Zugriff](./media/security-center-just-in-time/edit-policy.png)

## Verwenden von JIT-Zugriff auf dem Blatt einer Azure-VM<a name="jit-vm"></a>

Zur Vereinfachung können Sie direkt vom Blatt eines virtuellen Computers in Azure über JIT eine Verbindung mit diesem virtuellen Computer herstellen.

### <a name="configuring-a-just-in-time-access-policy"></a>Konfigurieren einer Just-In-Time-Zugriffsrichtlinie 
Um den Rollout des Just-In-Time-Zugriffs auf Ihre virtuellen Computer zu vereinfachen, können Sie einen virtuellen Computer so einrichten, dass er nur den direkten Just-In-Time-Zugriff innerhalb des virtuellen Computers gestattet.

1. Wählen Sie im Azure-Portal die Option für **virtuelle Computer** aus.
2. Klicken Sie auf den virtuellen Computer, den Sie auf den Just-In-Time-Zugriff beschränken möchten.
3. Klicken Sie im Menü auf **Konfiguration**.
4. Klicken Sie unter **Just-In-Time-Zugriff** auf **Just-In-Time-Richtlinie aktivieren**. 

Dies ermöglicht den Just-In-Time-Zugriff auf den virtuellen Computer mit den folgenden Einstellungen:

- Windows-Server:
    - RDP-Port 3389
    - Zugriff bis zu maximal drei Stunden
    - Zulässige Quell-IP-Adressen auf „Beliebige“ festgelegt
- Linux-Server:
    - SSH-Port 22
    - Zugriff bis zu maximal drei Stunden
    - Zulässige Quell-IP-Adressen auf „Beliebige“ festgelegt
     
Wenn für einen virtuellen Computer bereits Just-In-Time aktiviert ist, können Sie auf der Konfigurationsseite sehen, dass Just-In-Time aktiviert ist. Zudem können Sie über den Link die Richtlinie im Azure Security Center öffnen, um die Einstellungen anzuzeigen und zu ändern.

![JIT-Konfiguration im virtuellen Computer](./media/security-center-just-in-time/jit-vm-config.png)

### <a name="requesting-jit-access-to-a-vm"></a>Anfordern von JIT-Zugriff auf einen virtuellen Computer

Wenn Sie im Azure-Portal versuchen, eine Verbindung mit einem virtuellen Computer herzustellen, überprüft Azure, ob Sie eine Just-In-Time-Zugriffsrichtlinie auf diesem virtuellen Computer konfiguriert haben.

- Wenn Sie keine JIT-Zugriffsrichtlinie auf einem virtuellen Computer konfiguriert haben, werden Sie aufgefordert, dies zu tun.

  ![JIT-Eingabeaufforderung](./media/security-center-just-in-time/jit-prompt.png)

- Wenn Sie eine JIT-Richtlinie auf dem virtuellen Computer konfiguriert haben, können Sie auf **Zugriff anfordern** klicken, damit Sie entsprechend der JIT-Richtlinie, die für den virtuellen Computer festgelegt ist, Zugriff auf diesen erhalten. Der Zugriff wird mit den folgenden Standardparametern angefordert:
    - **Quell-IP**: „Beliebige“ (*) (kann nicht geändert werden)
    - **Zeitbereich**: 3 Stunden (kann nicht geändert werden)
    - **Portnummer** RDP-Port 3389 für Windows bzw. Port 22 für Linux (Sie können die Portnummer im Dialogfeld **Verbindung mit virtuellem Computer herstellen** ändern.)


  >![JIT-Zugriff anfordern](./media/security-center-just-in-time/jit-request-access.png)

## <a name="auditing-jit-access-activity"></a>Überwachen von JIT-Zugriffsaktivität

Mit der Protokollsuche erhalten Sie Einblicke in VM-Aktivitäten. So zeigen Sie Protokolle an:

1. Wählen Sie unter **JIT-VM-Zugriff** die Registerkarte**Konfiguriert** aus.
2. Wählen Sie unter **VMs** einen virtuellen Computer aus, zu dem Informationen angezeigt werden sollen, indem Sie auf die drei Punkte in der Zeile für den betreffenden virtuellen Computer klicken. Dadurch wird ein Menü geöffnet.
3. Wählen Sie im Menü **Aktivitätsprotokoll** aus. Daraufhin wird **Aktivitätsprotokoll** geöffnet.

   ![Aktivitätsprotokoll auswählen](./media/security-center-just-in-time/select-activity-log.png)

   **Aktivitätsprotokoll** enthält eine gefilterte Ansicht der früheren Vorgänge für diesen virtuellen Computer zusammen mit der Uhrzeit, dem Datum und dem Abonnement.

Sie können die Protokollinformationen herunterladen, indem Sie **Klicken Sie hier, um alle Elemente als CSV herunterzuladen** auswählen.

Ändern Sie die Filter, und wählen Sie **Anwenden** aus, um eine Suche und ein Protokoll zu erstellen.


## <a name="permissions-needed-to-configure-and-use-jit"></a>Berechtigungen, die zum Konfigurieren und Verwenden von JIT erforderlich sind

Legen Sie diese erforderlichen Berechtigungen fest, damit ein Benutzer eine JIT-Richtlinie für einen virtuellen Computer konfigurieren oder bearbeiten kann.

Weisen Sie der Rolle diese *Aktionen* zu: 
- Im Bereich eines Abonnements oder einer Ressourcengruppe, das oder die dem virtuellen Computer zugeordnet ist:
  - Microsoft.Security/locations/jitNetworkAccessPolicies/write
- Im Bereich eines Abonnements, einer Ressourcengruppe oder eines virtuellen Computers:
  - Microsoft.Compute/virtualMachines/write 

Legen Sie diese Berechtigungen fest, damit ein Benutzers erfolgreich JIT-Zugriff auf einen virtuellen Computer anfordern kann: Weisen Sie dem Benutzer diese *Aktionen* zu:
- Im Bereich eines Abonnements oder einer Ressourcengruppe, das oder die dem virtuellen Computer zugeordnet ist:
  - Microsoft.Security/locations/{der_Speicherort_der_VM}/jitNetworkAccessPolicies/ initiate/action
- Im Bereich eines Abonnements, einer Ressourcengruppe oder eines virtuellen Computers:
  - Microsoft.Compute/virtualMachines/read



## <a name="use-jit-programmatically"></a>Programmgesteuertes Verwenden von JIT
Sie können Just-In-Time über REST-APIs und über PowerShell einrichten.

### <a name="using-just-in-time-vm-access-via-rest-apis"></a>Verwenden von Just-In-Time-VM-Zugriff über REST-APIs

Das Feature Just-In-Time-VM-Zugriff kann über die Azure Security Center-API verwendet werden. Sie können über diese API u.a. Informationen über konfigurierte virtuelle Computer erhalten, neue hinzufügen und Zugriff auf einen virtuellen Computer anfordern. Unter [Richtlinien für den Jit-Netzwerkzugriff](https://docs.microsoft.com/rest/api/securitycenter/jitnetworkaccesspolicies) finden Sie weitere Informationen zur Just-In-Time-REST-API.

### <a name="using-jit-vm-access-via-powershell"></a>Verwenden von JIT-VM-Zugriff über PowerShell 

Um die Lösung für den Just-In-Time-VM-Zugriff mit PowerShell zu verwenden, verwenden Sie die offiziellen Azure Security Center-PowerShell-Cmdlets und insbesondere `Set-AzJitNetworkAccessPolicy`.

Im folgenden Beispiel werden auf einer bestimmten VM eine Richtlinie für den Just-In-Time-VM-Zugriff sowie die folgenden Einstellungen festgelegt:
1.  Schließen Sie die Ports 22 und 3389.
2.  Legen Sie für die Ports ein maximales Zeitfenster von drei Stunden fest, damit sie bei genehmigter Anforderung geöffnet werden können.
3.  Damit wird dem Benutzer, der Zugriff anfordert, ermöglicht, den Zugriff auf die Quell-IP-Adressen zu steuern und bei genehmigter Just-In-Time-Zugriffsanforderung eine Sitzung herzustellen.

Führen Sie hierfür Folgendes in PowerShell aus:

1.  Weisen Sie eine Variable zu, die die Just-In-Time-VM-Zugriffsrichtlinie für eine VM enthält:

        $JitPolicy = (@{
         id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
             number=22;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"},
             @{
             number=3389;
             protocol="*";
             allowedSourceAddressPrefix=@("*");
             maxRequestAccessDuration="PT3H"})})

2.  Fügen Sie einem Array die Just-In-Time-VM-Zugriffsrichtlinie hinzu:
    
        $JitPolicyArr=@($JitPolicy)

3.  Konfigurieren Sie die Just-In-Time-VM-Zugriffsrichtlinie auf der ausgewählten VM:
    
        Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr 

#### <a name="requesting-access-to-a-vm"></a>Anfordern des Zugriffs auf einen virtuellen Computer

Im folgenden Beispiel können Sie eine Just-In-Time-VM-Zugriffsanforderung für eine bestimmte VM sehen, in der das Öffnen von Port 22 für eine bestimmte IP-Adresse und für einen bestimmten Zeitraum angefordert wird:

Führen Sie die folgenden Schritte in PowerShell aus:
1.  Konfigurieren Sie die Eigenschaften für die VM-Zugriffsanforderung.

        $JitPolicyVm1 = (@{
          id="/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME"
        ports=(@{
           number=22;
           endTimeUtc="2018-09-17T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
2.  Fügen Sie die Parameter für die VM-Zugriffsanforderung zu einem Array hinzu:

        $JitPolicyArr=@($JitPolicyVm1)
3.  Senden Sie die Zugriffsanforderung (mit der Ressourcen-ID, die Sie in Schritt 1 abgerufen haben).

        Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr

Weitere Informationen finden Sie in der Dokumentation zu PowerShell-Cmdlets.


## <a name="next-steps"></a>Nächste Schritte
In diesem Artikel wurde Ihnen vermittelt, wie Sie mit Just-In-Time-VM-Zugriff im Security Center den Zugriff auf die virtuellen Azure-Computer steuern können.

Weitere Informationen zu Security Center finden Sie in den folgenden Quellen:

- [Festlegen von Sicherheitsrichtlinien:](tutorial-security-policy.md) Erfahren Sie, wie Sie Sicherheitsrichtlinien für Ihre Azure-Abonnements und -Ressourcengruppen konfigurieren.
- [Verwalten von Sicherheitsempfehlungen:](security-center-recommendations.md) Erfahren Sie, wie Empfehlungen Ihnen beim Schutz Ihrer Azure-Ressourcen helfen.
- [Überwachen der Sicherheitsintegrität:](security-center-monitoring.md) Erfahren Sie, wie Sie die Integrität Ihrer Azure-Ressourcen überwachen.
- [Verwalten von und Reagieren auf Sicherheitswarnungen:](security-center-managing-and-responding-alerts.md) Erfahren Sie, wie Sie Sicherheitswarnungen verwalten und darauf reagieren.
- [Überwachen von Partnerlösungen:](security-center-partner-solutions.md) Erfahren Sie, wie der Integritätsstatus Ihrer Partnerlösungen überwacht wird.
- [Security Center – Häufig gestellte Fragen:](security-center-faq.md) Hier finden Sie häufig gestellte Fragen zur Verwendung des Diensts.
- [Azure Security Blog](https://blogs.msdn.microsoft.com/azuresecurity/) (Blog zur Azure-Sicherheit): Hier finden Sie Blogbeiträge zur Azure-Sicherheit und -Compliance.

