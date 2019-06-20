---
title: Ausführen von Aufgaben unter Benutzerkonten – Azure Batch | Microsoft-Dokumentation
description: Konfigurieren von Benutzerkonten zum Ausführen von Aufgaben in Azure Batch
services: batch
author: laurenhughes
manager: jeconnoc
editor: ''
tags: ''
ms.assetid: ''
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: ''
ms.workload: big-compute
ms.date: 05/22/2017
ms.author: lahugh
ms.custom: seodec18
ms.openlocfilehash: bd5c16d755ef9b71f36b3d499838b12e6099ba6d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: de-DE
ms.lasthandoff: 06/13/2019
ms.locfileid: "65595386"
---
> [!NOTE] 
> Die in diesem Artikel beschriebenen Benutzerkonten unterscheiden sich aus Sicherheitsgründen von den Benutzerkonten für RDP (Remote Desktop Protocol) oder SSH (Secure Shell). 
>
> Um eine Verbindung mit einem Knoten herzustellen, auf dem die Linux-VM-Konfiguration über SSH ausgeführt wird, lesen Sie den Artikel [Installieren und Konfigurieren von Remotedesktop zum Herstellen einer Verbindung mit einem virtuellen Linux-Computer in Azure](../virtual-machines/virtual-machines-linux-use-remote-desktop.md). Um eine Verbindung mit Knoten herzustellen, auf denen Windows über RDP ausgeführt wird, lesen Sie den Artikel [Gewusst wie: Herstellen einer Verbindung mit einem virtuellen Azure-Computer unter Windows und Anmelden auf diesem Computer](../virtual-machines/windows/connect-logon.md).<br /><br />
> Um eine Verbindung mit einem Knoten herzustellen, auf dem die Clouddienstkonfiguration über RDP ausgeführt wird, lesen Sie den Artikel [Aktivieren einer Remotedesktopverbindung für eine Rolle in Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).
>
>


# <a name="run-tasks-under-user-accounts-in-batch"></a>Ausführen von Aufgaben unter Benutzerkonten in Batch

Eine Aufgabe wird in Azure Batch immer unter einem Benutzerkonto ausgeführt. Standardmäßig werden Aufgaben unter Standardbenutzerkonten ohne Administratorberechtigungen ausgeführt. Diese standardmäßigen Benutzerkontoeinstellungen sind in der Regel ausreichend. Für bestimmte Szenarien ist es jedoch hilfreich, in der Lage zu sein, das Benutzerkonto zu konfigurieren, unter dem eine Aufgabe ausgeführt werden soll. Dieser Artikel beschreibt die Typen von Benutzerkonten, und wie Sie sie für Ihr Szenario konfigurieren können.

## <a name="types-of-user-accounts"></a>Typen von Benutzerkonten

Azure Batch bietet zwei Typen von Benutzerkonten zum Ausführen von Aufgaben an:

- **Automatische Benutzerkonten.** Automatische Benutzerkonten sind integrierte Benutzerkonten, die vom Batch-Dienst automatisch erstellt werden. Standardmäßig werden Aufgaben unter einem automatischen Benutzerkonto ausgeführt. Sie können die automatische Benutzerspezifikation für eine Aufgabe konfigurieren, um anzugeben, unter welchem automatischen Benutzerkonto eine Aufgabe ausgeführt werden soll. Mit der automatischen Benutzerspezifikation können Sie Rechteerweiterungsebene und Bereich des automatischen Benutzerkontos angeben, das die Aufgabe ausführt. 

- **Ein benanntes Benutzerkonto.** Sie können eine oder mehrere benannte Benutzerkonten für einen Pool angeben, wenn Sie den Pool erstellen. Jedes Benutzerkonto wird auf jedem Knoten des Pools erstellt. Geben Sie neben dem Kontonamen das Kennwort des Benutzerkontos, die Rechteerweiterungsebene und, für Linux-Pools, den privaten SSH-Schlüssel ein. Wenn Sie eine Aufgabe hinzufügen, können Sie das benannte Benutzerkonto angeben, unter dem diese Aufgabe ausgeführt werden soll.

> [!IMPORTANT] 
> Mit der Batch-Dienstversion 2017-01-01.4.0 wird eine entscheidende Änderung eingeführt, die erfordert, dass Sie den Code zum Aufrufen dieser Version aktualisieren. Wenn Sie Code von einer älteren Batch-Version migrieren, beachten Sie, dass die **runElevated**-Eigenschaft in den REST-API- oder Batch-Clientbibliotheken nicht mehr unterstützt wird. Verwenden Sie die neue **userIdentity**-Eigenschaft einer Aufgabe, um die Rechteerweiterungsebene anzugeben. Im Abschnitt [Aktualisieren Ihres Codes auf die aktuelle Batch-Clientbibliothek](#update-your-code-to-the-latest-batch-client-library) finden Sie kurze Richtlinien für die Aktualisierung des Batch-Codes, wenn Sie eine der Clientbibliotheken verwenden.
>
>

## <a name="user-account-access-to-files-and-directories"></a>Benutzerkontenzugriff auf Dateien und Verzeichnisse

Sowohl automatische als auch benannte Benutzerkonten haben Lese-/Schreibzugriff auf das Arbeitsverzeichnis, das freigegebene Verzeichnis und das Mehrinstanzen-Aufgabenverzeichnis der Aufgabe. Beide Kontotypen haben Lesezugriff auf die Start- und Auftragsvorbereitungsverzeichnisse.

Wenn eine Aufgabe unter dem gleichen Konto ausgeführt wird, das für die Ausführung einer Startaufgabe verwendet wurde, verfügt die Aufgabe über Lese-/Schreibzugriff auf das Startverzeichnis der Aufgabe. Ebenso verfügt eine Aufgabe über Lese-/Schreibzugriff auf das Verzeichnis der Auftragsvorbereitungsaufgabe, wenn die Aufgabe unter dem gleichen Konto ausgeführt wird, das für die Ausführung einer Auftragsvorbereitungsaufgabe verwendet wurde. Wenn eine Aufgabe unter einem anderen Konto als die Startaufgabe oder Auftragsvorbereitungsaufgabe ausgeführt wird, verfügt die Aufgabe nur über Lesezugriff auf das entsprechende Verzeichnis.

Weitere Informationen zum Zugriff auf Dateien und Verzeichnisse aus einer Aufgabe heraus finden Sie unter [Entwickeln von parallelen Computelösungen in größerem Umfang mit Batch](batch-api-basics.md#files-and-directories).

## <a name="elevated-access-for-tasks"></a>Erhöhte Zugriffsrechte für Aufgaben 

Die Rechteerweiterungsebene des Benutzerkontos lässt erkennen, ob eine Aufgabe mit erhöhten Zugriffsrechten ausgeführt wird. Sowohl ein automatisches Benutzerkonto als auch ein benanntes Benutzerkonto kann mit erhöhten Zugriffsrechten ausgeführt werden. Die zwei Optionen für die Rechteerweiterungsebene sind:

- **NonAdmin:** Die Aufgabe wird unter dem Standardbenutzer ohne erhöhte Zugriffsrechte ausgeführt. Die standardmäßige Rechteerweiterungsebene für ein Batch-Benutzerkonto ist immer **NonAdmin**.
- **Admin:** Die Aufgabe wird unter einem Benutzer mit erhöhten Zugriffsrechten und vollständigen Administratorberechtigungen ausgeführt. 

## <a name="auto-user-accounts"></a>Automatische Benutzerkonten

Standardmäßig werden Aufgaben in Batch unter einem automatischen Benutzerkonto als Standardbenutzer ohne erhöhte Zugriffsrechte und mit Aufgabenbereich ausgeführt. Wenn die automatische Benutzerspezifikation für den Aufgabenbereich konfiguriert ist, erstellt der Batch-Dienst ausschließlich für diese Aufgabe ein automatisches Benutzerkonto.

Die Alternative zum Aufgabenbereich ist der Poolbereich. Wenn die automatische Benutzerspezifikation für eine Aufgabe für den Poolbereich konfiguriert ist, wird die Aufgabe unter einem automatischen Benutzerkonto ausgeführt, das für jede Aufgabe im Pool verfügbar ist. Weitere Informationen zum Poolbereich finden Sie im Abschnitt „Ausführen einer Aufgabe als automatischer Benutzer mit Poolbereich“.   

Der Standardbereich ist auf Windows- und Linux-Knoten unterschiedlich:

- Auf Windows-Knoten werden Aufgaben standardmäßig unter dem Aufgabenbereich ausgeführt.
- Auf Linux-Knoten werden Aufgaben immer unter dem Poolbereich ausgeführt.

Es gibt vier mögliche Konfigurationen für die automatische Benutzerspezifikation, von denen jede einem eindeutigen automatischen Benutzerkonto entspricht:

- Zugriff ohne Administratorrechte mit Aufgabenbereich (die standardmäßige automatische Benutzerspezifikation)
- Administratorzugriff (erhöht) mit Aufgabenbereich
- Zugriff ohne Administratorrechte mit Poolbereich
- Administratorzugriff mit Poolbereich

> [!IMPORTANT] 
> Aufgaben, die unter dem Aufgabenbereich ausgeführt werden, haben de facto keinen Zugriff auf andere Aufgaben auf einem Knoten. Allerdings könnte ein böswilliger Benutzer mit Zugriff auf das Konto diese Einschränkung durch Übermitteln einer Aufgabe umgehen, die mit Administratorrechten ausgeführt wird und auf andere Aufgabenverzeichnisse zugreift. Ein böswilliger Benutzer könnte auch RDP oder SSH verwenden, um eine Verbindung mit einem Knoten herzustellen. Sie müssen Ihre Batch-Kontoschlüssel vor Zugriff schützen, um solch ein Szenario zu verhindern. Wenn Sie den Verdacht haben, dass Ihr Konto manipuliert wurde, generieren Sie Ihre Schlüssel unbedingt neu.
>
>

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Ausführen einer Aufgabe als automatischer Benutzer mit erhöhten Zugriffsrechten

Sie können die automatische Benutzerspezifikation für die Administratorrechte konfigurieren, wenn Sie eine Aufgabe mit erhöhten Zugriffsrechten ausführen müssen. Beispielsweise kann eine Startaufgabe erhöhte Zugriffsrechte zum Installieren von Software auf dem Knoten benötigen.

> [!NOTE] 
> Im Allgemeinen sollten erhöhte Zugriffsrechte nur bei Bedarf verwendet werden. Es hat sich als Methode bewährt, die Mindestberechtigungen zu erteilen, mit denen das gewünschte Ergebnis erzielt werden kann. Wenn eine Startaufgabe z.B. Software für den aktuellen Benutzer anstatt für alle Benutzer installiert, können Sie vermeiden, erhöhte Zugriffsrechte für Aufgaben zu gewähren. Sie können die automatische Benutzerspezifikation für den Poolbereich und den Zugriff ohne Administratorrechte für alle Aufgaben konfigurieren, die unter dem gleichen Konto ausgeführt werden müssen, inklusive der Startaufgabe. 
>
>

Die folgenden Codeausschnitte veranschaulichen das Konfigurieren der automatischen Benutzerspezifikation. In den Beispielen ist die Rechteerweiterungsebene auf `Admin` und der Bereich auf `Task` festgelegt. Der Aufgabenbereich ist die Standardeinstellung, wird hier jedoch zu Beispielzwecken einbezogen.

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```
#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");                        
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Ausführen einer Aufgabe als automatischer Benutzer mit Poolbereich

Wenn ein Knoten bereitgestellt wird, werden zwei poolweite automatische Benutzerkonten auf jedem Knoten im Pool erstellt, eines mit erhöhten Zugriffsrechten und das andere ohne. Bei Festlegung des Bereichs des automatischen Benutzers für eine bestimmte Aufgabe auf den Poolbereich wird die Aufgabe unter einem dieser beiden poolweiten automatischen Benutzerkonten ausgeführt. 

Wenn Sie den Poolbereich für den automatischen Benutzer angeben, werden alle mit Administratorzugriff auszuführenden Aufgaben unter dem gleichen poolweiten automatischen Benutzerkonto ausgeführt. Auf ähnliche Weise werden ohne Administratorberechtigungen auszuführende Aufgaben auch unter einem einzelnen poolweiten automatischen Benutzerkonto ausgeführt. 

> [!NOTE] 
> Die zwei poolweiten automatischen Benutzerkonten sind separate Konten. Aufgaben, die unter dem poolweiten Administratorkonto ausgeführt werden, können keine Daten mit Aufgaben gemeinsam nutzen, die unter dem Standardkonto ausgeführt werden (und umgekehrt). 
>
>

Der Vorteil der Ausführung unter dem gleichen automatischen Benutzerkonto ist, dass Aufgaben, die auf dem gleichen Knoten ausgeführt werden, Daten gemeinsam nutzen können.

Die Freigabe geheimer Schlüssel zwischen Aufgaben ist ein Szenario, in dem das Ausführen von Aufgaben unter einem der beiden poolweiten automatischen Benutzerkonten nützlich ist. Nehmen Sie beispielsweise an, dass eine Startaufgabe auf dem Knoten einen geheimen Schlüssel bereitstellen muss, den andere Aufgaben verwenden können. Sie könnten die Windows-Datenschutz-API (Windows Data Protection API, DPAPI) verwenden, aber sie erfordert Administratorrechte. Stattdessen können Sie den geheimen Schlüssel auf Benutzerebene schützen. Aufgaben, die unter dem gleichen Benutzerkonto ausgeführt werden, können ohne erhöhte Zugriffsrechte auf den geheimen Schlüssel zugreifen.

Ein weiteres Szenario, in dem Sie möglicherweise Aufgaben unter einem automatischen Benutzerkonto mit Poolbereich ausführen sollten, ist eine Message Passing Interface-Dateifreigabe (MPI). Eine MPI-Dateifreigabe ist nützlich, wenn die Knoten in der MPI-Aufgabe mit den gleichen Dateidaten arbeiten müssen. Der Hauptknoten erstellt eine Dateifreigabe, auf die die untergeordneten Knoten zugreifen können, wenn sie unter dem gleichen automatischen Benutzerkonto ausgeführt werden. 

Der folgende Codeausschnitt legt den Bereich des automatischen Benutzers für eine Aufgabe in „Batch .NET“ auf den Poolbereich fest. Die Rechteerweiterungsebene wird weggelassen, damit die Aufgabe unter dem standardmäßigen, poolweiten automatischen Benutzerkonto ausgeführt wird.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Benannte Benutzerkonten

Sie können benannte Benutzerkonten bei der Erstellung eines Pools definieren. Ein benanntes Benutzerkonto verfügt über einen Namen und ein Kennwort, die Sie bereitstellen. Sie können die Rechteerweiterungsebene für ein benanntes Benutzerkonto festlegen. Für Linux-Knoten können Sie auch einen privaten SSH-Schlüssel angeben.

Ein benanntes Benutzerkonto ist auf allen Knoten im Pool vorhanden und steht für alle Aufgaben zur Verfügung, die auf diesen Knoten ausgeführt werden. Sie können eine beliebige Anzahl von benannten Benutzern für einen Pool definieren. Wenn Sie eine Aufgabe oder Aufgabensammlung hinzufügen, können Sie angeben, dass die Aufgabe unter einem der für den Pool definierten benannten Benutzerkonten ausgeführt wird.

Ein benanntes Benutzerkonto ist nützlich, wenn alle Aufgaben in einem Auftrag unter dem gleichen Benutzerkonto ausgeführt werden sollen, jedoch von Aufgaben isoliert werden sollen, die zur gleichen Zeit in anderen Aufträgen ausgeführt werden. Sie können beispielsweise für jeden Auftrag einen benannten Benutzer erstellen und die Aufgaben jedes Auftrags unter diesem benannten Benutzerkonto ausführen. Jeder Auftrag kann dann einen geheimen Schlüssel mit seinen eigenen Aufgaben gemeinsam nutzen, jedoch nicht mit Aufgaben, die in anderen Aufträgen ausgeführt werden.

Sie können ein benanntes Benutzerkonto auch verwenden, um eine Aufgabe auszuführen, die Berechtigungen für externe Ressourcen wie z.B. Dateifreigaben festlegt. Mit einem benannten Benutzerkonto steuern Sie die Identität des Benutzers und können mithilfe dieser Benutzeridentität Berechtigungen festlegen.  

Benannte Benutzerkonten aktivieren kennwortfreies SSH zwischen Linux-Knoten. Sie können ein benanntes Benutzerkonto mit Linux-Knoten verwenden, die Aufgaben mit mehreren Instanzen ausführen müssen. Jeder Knoten im Pool kann Aufgaben unter einem Benutzerkonto ausführen, das für den gesamten Pool definiert ist. Weitere Informationen zu Aufgaben mit mehreren Instanzen finden Sie unter [Verwenden von Tasks mit mehreren Instanzen zum Ausführen von MPI\-Anwendungen (Message Passing Interface) in Batch](batch-mpi.md).

### <a name="create-named-user-accounts"></a>Erstellen benannter Benutzerkonten

Um benannte Benutzerkonten in Batch zu erstellen, fügen Sie dem Pool eine Sammlung von Benutzerkonten hinzu. Die folgenden Codeausschnitte veranschaulichen das Erstellen von benannten Benutzerkonten in .NET, Java und Python. Diese Codeausschnitte veranschaulichen das Erstellen benannter Konten mit und ohne Administratorberechtigungen für einen Pool. In den Beispielen werden mit der Clouddienstkonfiguration Pools erstellt, aber Sie verwenden den gleichen Ansatz beim Erstellen eines Windows- oder Linux-Pools mit der VM-Konfiguration.

#### <a name="batch-net-example-windows"></a>.NET-Beispiel für Batch (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));   

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>.NET-Beispiel für Batch (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,                                             
    virtualMachineSize: "Standard_A1",                                      
    virtualMachineConfiguration: virtualMachineConfiguration);                  

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```


#### <a name="batch-java-example"></a>Beispiel für Batch Java

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withCloudServiceConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Beispiel für Batch Python

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.nonadmin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Ausführen einer Aufgabe unter einem benannten Benutzerkonto mit erhöhten Zugriffsrechten

Um eine Aufgabe als Benutzer mit erhöhten Rechten auszuführen, legen Sie die **UserIdentity**-Eigenschaft der Aufgabe auf ein benanntes Benutzerkonto fest, das mit Festlegung seines **ElevationLevel**-Eigenschaftensatzes auf `Admin` erstellt wurde.

Dieser Codeausschnitt gibt an, dass die Aufgabe unter einem benannten Benutzerkonto ausgeführt werden soll. Dieses benannte Benutzerkonto wurde für den Pool definiert, als der Pool erstellt wurde. In diesem Fall wurde das benannte Benutzerkonto mit Administratorberechtigungen erstellt:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Aktualisieren Ihres Codes auf die aktuelle Batch-Clientbibliothek

Mit der Batch-Dienstversion 2017-01-01.4.0 wird eine entscheidende Änderung eingeführt: Die in früheren Versionen verfügbare **runElevated**-Eigenschaft wird durch die **userIdentity**-Eigenschaft ersetzt. Die folgenden Tabellen enthalten eine einfache Zuordnung, die Sie zum Aktualisieren des Codes aus früheren Versionen der Clientbibliotheken verwenden können.

### <a name="batch-net"></a>Batch .NET

| Wenn Ihr Code Folgendes verwendet:                  | Aktualisieren Sie ihn mit:                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` nicht angegeben | Keine Aktualisierung erforderlich                                                                                               |

### <a name="batch-java"></a>Batch Java

| Wenn Ihr Code Folgendes verwendet:                      | Aktualisieren Sie ihn mit:                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` nicht angegeben | Keine Aktualisierung erforderlich                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Wenn Ihr Code Folgendes verwendet:                      | Aktualisieren Sie ihn mit:                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`, wo <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`, wo <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.nonadmin))`             |
| `run_elevated` nicht angegeben | Keine Aktualisierung erforderlich                                                                                                                                  |


## <a name="next-steps"></a>Nächste Schritte

* Eine detaillierte Übersicht über Batch finden Sie unter [Entwickeln von parallelen Computelösungen in größerem Umfang mit Batch](batch-api-basics.md).
