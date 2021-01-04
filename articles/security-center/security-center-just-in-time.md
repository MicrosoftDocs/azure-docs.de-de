---
title: Just-In-Time-VM-Zugriff in Azure Security Center | Microsoft-Dokumentation
description: In diesem Dokument wird beschrieben, wie Sie mit Just-In-Time-VM-Zugriff (JIT) in Azure Security Center den Zugriff auf die virtuellen Azure-Computer steuern können.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 07/12/2020
ms.author: memildin
ms.openlocfilehash: 55e9d2d1af863084b080c2de7833712413221050
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 12/01/2020
ms.locfileid: "96445651"
---
# <a name="secure-your-management-ports-with-just-in-time-access"></a>Sichern Ihrer Verwaltungsports mit Just-in-Time-Zugriff (JIT)

Sperren Sie den eingehenden Datenverkehr zu Ihren Azure Virtual Machines mit dem Azure Security Center-Feature für den Just-In-Time-Zugriff (JIT) auf virtuelle Computer (VM). Dies verringert die Angriffsfläche und bietet gleichzeitig einfachen Zugriff, wenn Sie eine Verbindung mit einer VM herstellen müssen.

Eine vollständige Erläuterung der Funktionsweise von JIT und der zugrunde liegenden Logik findet sich unter [Erläuterung von Just-In-Time](just-in-time-explained.md).

Auf dieser Seite erfahren Sie, wie Sie JIT in Ihr Sicherheitsprogramm einbinden. Sie lernen Folgendes: 

- **Aktivieren von JIT auf Ihren VMs**: Sie können JIT mit Ihren eigenen benutzerdefinierten Optionen für eine oder mehrere VMs mithilfe von Security Center, PowerShell oder der REST-API aktivieren. Alternativ können Sie JIT mit hartcodierten Standardparametern von virtuellen Azure-Computern aus aktivieren. Wenn JIT aktiviert ist, sperrt es den eingehenden Datenverkehr zu Ihren virtuellen Azure-Computern, indem es eine Regel in Ihrer Netzwerksicherheitsgruppe erstellt.
- **Anfordern des Zugriffs auf eine VM mit aktiviertem JIT**: Das Ziel von JIT ist es, sicherzustellen, dass Security Center auch bei gesperrtem eingehenden Datenverkehr einen einfachen Zugriff bietet, um bei Bedarf eine Verbindung mit virtuellen Computern herzustellen. Sie können Anforderungen für den Zugriff auf einen JIT-fähigen virtuellen Computer über Security Center, virtuelle Azure-Computer, PowerShell oder die REST-API stellen.
- **Überwachen der Aktivität**: Um sicherzustellen, dass Ihre VMs angemessen gesichert sind, überprüfen Sie die Zugriffe auf Ihre JIT-fähigen virtuellen Computer im Rahmen Ihrer regelmäßigen Sicherheitsüberprüfungen.   



## <a name="availability"></a>Verfügbarkeit

|Aspekt|Details|
|----|:----|
|Status des Release:|Allgemein verfügbar (Generally Available, GA)|
|Preise:|Erfordert [Azure Defender für Server](defender-for-servers-introduction.md)|
|Unterstützte VMs:|![Ja](./media/icons/yes-icon.png) Durch Azure Resource Manager bereitgestellte VMs.<br>![Nein](./media/icons/no-icon.png) Mit klassischen Bereitstellungsmodellen bereitgestellte VMs. [Erfahren Sie mehr über diese Bereitstellungsmodelle](../azure-resource-manager/management/deployment-models.md).<br>![Nein](./media/icons/no-icon.png) Durch Azure Firewall-Instanzen geschützte VMs, die von [Azure Firewall Manager](../firewall-manager/overview.md) gesteuert werden.|
|Erforderliche Rollen und Berechtigungen:|Die Rollen **Reader** und **SecurityReader** können sowohl den JIT-Status als auch die Parameter anzeigen.<br>Informationen zur Erstellung benutzerdefinierter Rollen, die mit JIT arbeiten können, finden Sie unter [Welche Berechtigungen sind erforderlich, um JIT zu konfigurieren und zu verwenden?](just-in-time-explained.md#what-permissions-are-needed-to-configure-and-use-jit).<br>Verwenden Sie das Skript [Set-JitLeastPrivilegedRole](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/JIT%20Custom%20Role) von den GitHub-Communityseiten des Security Center, um für Benutzer, die JIT-Zugriff auf eine VM anfordern und keine anderen JIT-Operationen durchführen müssen, eine Rolle mit den geringsten Berechtigungen zu erstellen.|
|Clouds:|![Ja](./media/icons/yes-icon.png) Kommerzielle Clouds<br>![Ja](./media/icons/yes-icon.png) National/Sovereign (US Gov, China Gov, andere Gov)|
|||


## <a name="enable-jit-vm-access"></a>Aktivieren des JIT-VM-Zugriffs <a name="jit-configure"></a>

Sie können den JIT-VM-Zugriff mit Ihren eigenen benutzerdefinierten Optionen für eine oder mehrere VMs über Security Center oder programmgesteuert aktivieren. 

Alternativ können Sie JIT mit hartcodierten Standardparametern von virtuellen Azure-Computern aus aktivieren.

Jede dieser Optionen wird auf einer separaten Registerkarte unten erläutert.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-config-asc)

### <a name="enable-jit-on-your-vms-from-azure-security-center"></a>Aktivieren von JIT auf Ihren VMs über Azure Security Center <a name="jit-asc"></a>

:::image type="content" source="./media/security-center-just-in-time/jit-config-security-center.gif" alt-text="Konfigurieren des JIT-VM-Zugriffs in Azure Security Center":::

Über Security Center können Sie den JIT-VM-Zugriff aktivieren und konfigurieren.

1. Öffnen Sie das Azure Defender-Dashboard, und wählen Sie im Bereich „Erweiterter Schutz“ die Option **JIT-VM-Zugriff** aus.

    Die Seite **Just-In-Time-VM-Zugriff** wird geöffnet, auf der Ihre VMs auf den folgenden Registerkarten gruppiert sind:

    - **Konfiguriert**: Virtuelle Computer, die bereits für die Unterstützung von Just-In-Time-Zugriff konfiguriert wurden. Für jede VM zeigt die konfigurierte Registerkarte Folgendes an:
        - Anzahl der genehmigten JIT-Anforderungen in den letzten sieben Tagen
        - Datum und Uhrzeit des letzten Zugriffs
        - Konfigurierte Verbindungsdetails
        - Letzter Benutzer
    - **Nicht konfiguriert**: VMs ohne aktiviertes JIT, die aber JIT unterstützen können. Es wird empfohlen, JIT für diese VMS zu aktivieren.
    - **Nicht unterstützt**: VMs ohne aktiviertes JIT, die das Feature auch nicht unterstützen. Ihre VM befindet sich aus folgenden Gründen möglicherweise auf dieser Registerkarte:
      - Fehlende Netzwerksicherheitsgruppe (NSG) – JIT erfordert die Konfiguration einer NSG
      - Klassische VM – JIT unterstützt VMs, die über Azure Resource Manager und nicht die „klassische Bereitstellung“ bereitgestellt werden. [Erfahren Sie mehr über klassische und Azure Resource Manager-Bereitstellungsmodelle](../azure-resource-manager/management/deployment-models.md).
      - Sonstige – Ihre VM befindet sich möglicherweise auf dieser Registerkarte, wenn die JIT-Lösung in der Sicherheitsrichtlinie des Abonnements oder der Ressourcengruppe deaktiviert ist.

1. Markieren Sie auf der Registerkarte **Nicht konfiguriert** die VMs, die mit JIT geschützt werden sollen, und wählen Sie **JIT auf VMs aktivieren** aus. 

    Die JIT-VM-Zugriffsseite wird geöffnet und listet die Ports auf, deren Schutz vom Security Center empfohlen wird:
    - 22 – SSH
    - 3389 – RDP
    - 5985 – WinRM 
    - 5986 – WinRM

    Wählen Sie **Speichern** aus, um die Standardeinstellungen zu akzeptieren.

1. So passen Sie die JIT-Optionen an

    - Fügen Sie benutzerdefinierte Ports mit der Schaltfläche **Hinzufügen** hinzu. 
    - Ändern Sie einen der Standardports, indem Sie ihn aus der Liste auswählen.

    Für jeden (benutzerdefinierten oder standardmäßigen) Port bietet der Bereich **Portkonfiguration hinzufügen** die folgenden Optionen:

    - **Protokoll**: Das Protokoll, das auf diesem Port zulässig ist, wenn eine Anforderung genehmigt wird.
    - **Zulässige Quell-IP-Adressen**: Die IP-Bereiche, die für diesen Port zulässig sind, wenn eine Anforderung genehmigt wird.
    - **Maximale Anforderungsdauer**: Das maximale Zeitfenster, in dem ein bestimmter Port geöffnet werden kann.

     1. Legen Sie die Portsicherheit gemäß Ihren Anforderungen fest.

     1. Klicken Sie auf **OK**.

1. Wählen Sie **Speichern** aus.



### <a name="edit-the-jit-configuration-on-a-jit-enabled-vm-using-security-center"></a>Bearbeiten der JIT-Konfiguration auf einem JIT-fähigen virtuellen Computer mit Security Center <a name="jit-modify"></a>

Sie können die Just-In-Time-Konfiguration eines virtuellen Computers ändern, indem Sie einen neuen Port, der für diesen virtuellen Computer geschützt werden soll, hinzufügen und konfigurieren oder eine beliebige andere Einstellung eines bereits geschützten Ports ändern.

So bearbeiten Sie die vorhandenen JIT-Regeln für eine VM

1. Öffnen Sie das Azure Defender-Dashboard, und wählen Sie im Bereich „Erweiterter Schutz“ die Option **Adaptive Anwendungssteuerungen** aus.

1. Klicken Sie auf der Registerkarte **Konfiguriert** mit der rechten Maustaste auf die VM, zu der Sie einen Port hinzufügen möchten, und wählen Sie „Bearbeiten“ aus. 

    ![Bearbeiten einer JIT-VM-Zugriffskonfiguration in Azure Security Center](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

1. Unter **JIT-VM-Zugriffskonfiguration** können Sie die vorhandenen Einstellungen eines bereits geschützten Ports bearbeiten oder einen neuen benutzerdefinierten Port hinzufügen.

1. Wenn Sie die Bearbeitung der Ports abgeschlossen haben, wählen Sie **Speichern** aus.
 


### <a name="azure-virtual-machines"></a>[**Virtuelle Azure-Computer**](#tab/jit-config-avm)

### <a name="enable-jit-on-your-vms-from-azure-virtual-machines"></a>Aktivieren von JIT auf Ihren VMs über virtuelle Azure-Computer

Sie können JIT auf einer VM von den Seiten für virtuelle Azure-Computer des Azure-Portals aus aktivieren.

![Konfigurieren des JIT-VM-Zugriffs auf virtuellen Azure-Computern](./media/security-center-just-in-time/jit-config-virtual-machines.gif)

> [!TIP]
> Wenn für einen virtuellen Computer bereits Just-In-Time aktiviert ist, können Sie auf der Konfigurationsseite sehen, dass Just-In-Time aktiviert ist. Zudem können Sie über den Link die Just-In-Time-VM-Zugriffsseite in Security Center öffnen, um die Einstellungen anzuzeigen und zu ändern.

1. Suchen Sie im [Azure-Portal](https://ms.portal.azure.com) nach **Virtuelle Computer**, und wählen Sie die Option aus. 

1. Wählen Sie den virtuellen Computer aus, den Sie mit JIT schützen möchten.

1. Wählen Sie im Menü die Option **Konfiguration** aus.

1. Wählen Sie unter **Just-in-Time-Zugriff** die Option **Just-in-Time aktivieren** aus. 

    Dies ermöglicht den Just-In-Time-Zugriff auf den virtuellen Computer mit den folgenden Standardeinstellungen:

    - Windows-Computer:
        - RDP-Port 3389
        - Zugriff bis zu maximal drei Stunden
        - Zulässige Quell-IP-Adressen auf „Beliebige“ festgelegt
    - Linux-Computer:
        - SSH-Port 22
        - Zugriff bis zu maximal drei Stunden
        - Zulässige Quell-IP-Adressen auf „Beliebige“ festgelegt

1. Verwenden Sie die Just-In-Time-Seite von Azure Security Center, um einen dieser Werte zu bearbeiten oder weitere Ports zu Ihrer JIT-Konfiguration hinzuzufügen:

    1. Wählen Sie im Security Center-Menü die Option **Just-In-Time-VM-Zugriff** aus.

    1. Klicken Sie auf der Registerkarte **Konfiguriert** mit der rechten Maustaste auf die VM, zu der Sie einen Port hinzufügen möchten, und wählen Sie „Bearbeiten“ aus. 

        ![Bearbeiten einer JIT-VM-Zugriffskonfiguration in Azure Security Center](./media/security-center-just-in-time/jit-policy-edit-security-center.png)

    1. Unter **JIT-VM-Zugriffskonfiguration** können Sie die vorhandenen Einstellungen eines bereits geschützten Ports bearbeiten oder einen neuen benutzerdefinierten Port hinzufügen.

    1. Wenn Sie die Bearbeitung der Ports abgeschlossen haben, wählen Sie **Speichern** aus.


### <a name="powershell"></a>[**PowerShell**](#tab/jit-config-powershell)

### <a name="enable-jit-on-your-vms-using-powershell"></a>Aktivieren von JIT auf Ihren VMs mithilfe von PowerShell

Verwenden Sie das offizielle PowerShell-Cmdlet `Set-AzJitNetworkAccessPolicy` von Azure Security Center, um den Just-In-Time-VM-Zugriff von PowerShell aus zu ermöglichen.

**Beispiel**: Aktivieren Sie den Just-In-Time-VM-Zugriff auf eine bestimmte VM mit den folgenden Regeln:

* Schließen Sie die Ports 22 und 3389.
* Legen Sie für die Ports ein maximales Zeitfenster von drei Stunden fest, damit sie bei genehmigter Anforderung geöffnet werden können.
* Erlauben Sie dem Zugriff anfordernden Benutzer die Steuerung der IP-Quelladressen.
* Erlauben Sie dem Zugriff anfordernden Benutzer die Einrichtung einer erfolgreichen Sitzung nach einer genehmigten Just-In-Time-Zugriffsanforderung

Die folgenden PowerShell-Befehle erstellen diese JIT-Konfiguration:

1. Weisen Sie eine Variable zu, die die Just-In-Time-VM-Zugriffsregeln für eine VM enthält:

    ```azurepowershell
    $JitPolicy = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
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
    ```

1. Fügen Sie einem Array die Just-In-Time-VM-Zugriffsregeln hinzu:
    
    ```azurepowershell
    $JitPolicyArr=@($JitPolicy)
    ```

1. Konfigurieren Sie die Just-In-Time-VM-Zugriffsregeln auf der ausgewählten VM:
    
    ```azurepowershell
    Set-AzJitNetworkAccessPolicy -Kind "Basic" -Location "LOCATION" -Name "default" -ResourceGroupName "RESOURCEGROUP" -VirtualMachine $JitPolicyArr
    ```

    Verwenden Sie den Parameter „-Name“, um eine VM anzugeben. Verwenden Sie z. B. zur Einrichtung der JIT-Konfiguration für zwei verschiedene VMs (VM1 und VM2): ```Set-AzJitNetworkAccessPolicy -Name VM1``` und ```Set-AzJitNetworkAccessPolicy -Name VM2```.


### <a name="rest-api"></a>[**REST-API**](#tab/jit-config-api)

### <a name="enable-jit-on-your-vms-using-the-rest-api"></a>Aktivieren von JIT auf Ihren VMs mithilfe der REST-API

Das Feature Just-In-Time-VM-Zugriff kann über die Azure Security Center-API verwendet werden. Verwenden Sie diese API, um Informationen über konfigurierte virtuelle Computer zu erhalten, neue virtuelle Computer hinzufügen, Zugriff auf einen virtuellen Computer anzufordern usw. 

Weitere Informationen finden Sie unter [Richtlinien für den JIT-Netzwerkzugriff](/rest/api/securitycenter/jitnetworkaccesspolicies).


--- 










## <a name="request-access-to-a-jit-enabled-vm"></a>Anfordern des Zugriffs auf einen JIT-fähigen virtuellen Computer

Sie können den Zugriff auf einen JIT-fähigen virtuellen Computer über das Azure-Portal (in Security Center oder auf virtuellen Azure-Computern) oder programmgesteuert anfordern.

Jede dieser Optionen wird auf einer separaten Registerkarte unten erläutert.

### <a name="azure-security-center"></a>[**Azure Security Center**](#tab/jit-request-asc)

### <a name="request-access-to-a-jit-enabled-vm-from-azure-security-center"></a>Anfordern des Zugriffs auf einen JIT-fähigen virtuellen Computer über Azure Security Center 

Wenn JIT für einen virtuellen Computer aktiviert ist, müssen Sie zum Herstellen der Verbindung entsprechend den Zugriff anfordern. Sie können Zugriff auf jede der unterstützten Arten anfordern, unabhängig davon, wie Sie JIT aktiviert haben.

:::image type="content" source="./media/security-center-just-in-time/jit-request-security-center.gif" alt-text="Anfordern des JIT-Zugriffs über Security Center":::

1. Wählen Sie auf der Seite **JIT-VM-Zugriff** die Registerkarte **Konfiguriert** aus.

1. Markieren Sie die virtuellen Computer, auf die Sie zugreifen möchten.

    - Das Symbol in der Spalte **Verbindungsdetails** gibt an, ob JIT in der Netzwerksicherheitsgruppe oder der Firewall aktiviert ist. Wenn JIT für beide aktiviert ist, wird nur das Firewallsymbol angezeigt.

    - Die Spalte **Verbindungsdetails** enthält die Informationen, die dazu erforderlich sind, den virtuellen Computer zu verbinden, und enthält dessen geöffnete Ports.

1. Wählen Sie **Zugriff anfordern** aus. Das Fenster **Zugriff anfordern** wird geöffnet.

1. Konfigurieren Sie unter **Zugriff anfordern** für jeden virtuellen Computer die zu öffnenden Ports und die Quell-IP-Adressen, für die der jeweilige Port geöffnet wird, und das Zeitfenster, in dem der Port geöffnet wird. Es ist nur möglich, Zugriff auf die konfigurierten Ports anzufordern. Jeder Port verfügt über eine maximal zulässige Zeit, die sich aus der von Ihnen erstellten JIT-Konfiguration ableitet.

1. Wählen Sie **Ports öffnen** aus.

> [!NOTE]
> Wenn sich ein Benutzer, der Zugriff anfordert, hinter einem Proxy befindet, funktioniert die Option **Meine IP-Adresse** möglicherweise nicht. Möglicherweise müssen Sie den vollständigen IP-Adressenbereich der Organisation definieren.



### <a name="azure-virtual-machines"></a>[**Virtuelle Azure-Computer**](#tab/jit-request-avm)

### <a name="request-access-to-a-jit-enabled-vm-from-the-azure-virtual-machines-connect-page"></a>Anfordern des Zugriffs auf eine JIT-fähige VM über die Seite „Verbinden“ des virtuellen Azure-Computers

Wenn JIT für einen virtuellen Computer aktiviert ist, müssen Sie zum Herstellen der Verbindung entsprechend den Zugriff anfordern. Sie können Zugriff auf jede der unterstützten Arten anfordern, unabhängig davon, wie Sie JIT aktiviert haben.

  >![JIT-Just-In-Time-Anforderung](./media/security-center-just-in-time/jit-request-vm.png)


So fordern Sie den Zugriff von virtuellen Azure-Computern an

1. Öffnen Sie im Azure-Portal die Seiten mit den virtuellen Computern.

1. Wählen Sie die VM aus, mit der Sie eine Verbindung herstellen möchten, und öffnen Sie die Seite **Verbinden**.

    Azure prüft, ob JIT auf diesem virtuellen Computer aktiviert ist.

    - Wenn JIT für den virtuellen Computer nicht aktiviert ist, werden Sie aufgefordert, es zu aktivieren.

    - Wenn JIT aktiviert ist, wählen Sie **Zugriff anfordern** aus, um eine Zugriffsanforderung mit der anfordernden IP-Adresse, dem Zeitbereich und den Ports zu übergeben, die für diese VM konfiguriert wurden.

> [!NOTE]
> Nachdem eine Anforderung für einen durch Azure Firewall geschützten virtuellen Computer genehmigt wurde, stellt Security Center dem Benutzer die richtigen Verbindungsinformationen (die Portzuordnung aus der DNAT-Tabelle) zum Herstellen einer Verbindung mit dem virtuellen Computer zur Verfügung.



### <a name="powershell"></a>[**PowerShell**](#tab/jit-request-powershell)

### <a name="request-access-to-a-jit-enabled-vm-using-powershell"></a>Anfordern des Zugriffs auf eine JIT-fähige VM mithilfe von PowerShell

Im folgenden Beispiel können Sie eine Just-In-Time-VM-Zugriffsanforderung für eine bestimmte VM sehen, in der das Öffnen von Port 22 für eine bestimmte IP-Adresse und für einen bestimmten Zeitraum angefordert wird:

Führen Sie die folgenden Schritte in PowerShell aus:

1. Konfigurieren Sie die Eigenschaften für die VM-Zugriffsanforderung:

    ```azurepowershell
    $JitPolicyVm1 = (@{
        id="/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/VMNAME";
        ports=(@{
           number=22;
           endTimeUtc="2020-07-15T17:00:00.3658798Z";
           allowedSourceAddressPrefix=@("IPV4ADDRESS")})})
    ```

1. Fügen Sie die Parameter für die VM-Zugriffsanforderung zu einem Array hinzu:

    ```azurepowershell
    $JitPolicyArr=@($JitPolicyVm1)
    ```
        
1. Senden Sie die Zugriffsanforderung (mit der Ressourcen-ID aus Schritt 1).

    ```azurepowershell
    Start-AzJitNetworkAccessPolicy -ResourceId "/subscriptions/SUBSCRIPTIONID/resourceGroups/RESOURCEGROUP/providers/Microsoft.Security/locations/LOCATION/jitNetworkAccessPolicies/default" -VirtualMachine $JitPolicyArr
    ```

Weitere Informationen finden Sie in der [PowerShell-Cmdlet-Dokumentation](/powershell/scripting/developer/cmdlet/cmdlet-overview).



### <a name="rest-api"></a>[**REST-API**](#tab/jit-request-api)

### <a name="request-access-to-a-jit-enabled-vms-using-the-rest-api"></a>Anfordern des Zugriffs auf JIT-fähige VMs mithilfe der REST-API

Das Feature Just-In-Time-VM-Zugriff kann über die Azure Security Center-API verwendet werden. Verwenden Sie diese API, um Informationen über konfigurierte virtuelle Computer zu erhalten, neue virtuelle Computer hinzufügen, Zugriff auf einen virtuellen Computer anzufordern usw. 

Weitere Informationen finden Sie unter [Richtlinien für den JIT-Netzwerkzugriff](/rest/api/securitycenter/jitnetworkaccesspolicies).

---








## <a name="audit-jit-access-activity-in-security-center"></a>Überwachen einer JIT-Zugriffsaktivität über Security Center

Mit der Protokollsuche erhalten Sie Einblicke in VM-Aktivitäten. So zeigen Sie die Protokolle an:

1. Wählen Sie über **JIT-VM-Zugriff** die Registerkarte **Konfiguriert** aus.

1. Öffnen Sie für die zu überwachende VM das durch Auslassungspunkte dargestellte Menü am Ende der Zeile.
 
1. Wählen Sie im Menü **Aktivitätsprotokoll** aus.

   ![Auswählen des Just-In-Time-JIT-Aktivitätsprotokolls](./media/security-center-just-in-time/jit-select-activity-log.png)

   Das Aktivitätsprotokoll enthält eine gefilterte Ansicht der früheren Vorgänge für diesen virtuellen Computer zusammen mit der Uhrzeit, dem Datum und dem Abonnement.

1. Wählen Sie **Als CSV herunterladen** aus, um die Protokollinformationen herunterzuladen.








## <a name="next-steps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie den Just-In-Time-VM-Zugriff einrichten und nutzen. Lesen Sie den Konzeptartikel, in dem die von JIT abgewehrten Bedrohungen erläutert werden, um die Gründe für die Verwendung von JIT zu erfahren:

> [!div class="nextstepaction"]
> [Erläuterung von JIT](just-in-time-explained.md)