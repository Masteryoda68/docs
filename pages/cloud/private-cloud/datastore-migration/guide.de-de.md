---
title: Datastore zwischen zwei PCCs kopieren
slug: datastore-copy
excerpt: Erfahren Sie hier, wie Sie über die OVHcloud API von einem PCC Dienst aus auf eine Datastore-Kopie eines nicht verfügbaren Dienstes zugreifen
section: OVHcloud Funktionen
hidden: true
---

> [!primary]
> Diese Übersetzung wurde durch unseren Partner SYSTRAN automatisch erstellt. In manchen Fällen können ungenaue Formulierungen verwendet worden sein, z.B. bei der Beschriftung von Schaltflächen oder technischen Details. Bitte ziehen Sie beim geringsten Zweifel die englische oder französische Fassung der Anleitung zu Rate. Möchten Sie mithelfen, diese Übersetzung zu verbessern? Dann nutzen Sie dazu bitte den Button «Mitmachen» auf dieser Seite.
>

**Letzte Aktualisierung am 29.03.2021**

## Ziel

Im Rahmen der Störung im Rechenzentrum SBG können Sie die Datastores eines betroffenen PCC Dienstes auf einen neuen PCC Dienst migrieren.

**Diese Anleitung erklärt, wie Sie über die OVHcloud API Datastores kopieren können.**

## Voraussetzungen

- Sie können sich in der [OVHcloud API-Konsole](https://api.ovh.com/) einloggen.
- Sie haben Zugriff auf das [vSphere Interface](../den_vsphere_client_installieren/).

> [!warning]
>
> Wenn Ihre Quell-PCC über ein [HDS](https://www.ovhcloud.com/de/enterprise/certification-conformity/hds/) oder [PCI-DSS](https://www.ovhcloud.com/de/enterprise/certification-conformity/pci-dss/) Zertifikat verfügt, muss Ihr Ziel-PCC über das gleiche aktive **Zertifikat verfügen,** um den Datastore abzurufen.
>
> Weitere Informationen finden Sie in unserer [Anleitung Aktivierung der Compliance Funktion Healthcare (HDS) oder Payment Services (PCI DSS)](../pci-dss-option-aktivieren/).
>

## In der praktischen Anwendung

Wenn Sie nicht mit der OVHcloud API vertraut sind, lesen Sie zunächst unsere Anleitung zur [OVHcloud API](https://docs.ovh.com/gb/en/api/first-steps-with-ovh-api/).

### Schritt 1: Die FilerID des Datastores abrufen

Sie müssen zuerst die zu kopierende `filerId` abrufen.

Loggen Sie sich auf [https://api.ovh.com/](https://api.ovh.com/) ein und verwenden Sie folgenden Aufruf:

> [!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/filer

Geben Sie diese Variablen ein:

- serviceName: Name des Quell-PCC in SBG (z.B. pcc-192-0-2-15).
- datacenterId: die ID des Quell-Rechenzentrums (z. B. 1337).

### Schritt 2: Datastore-Kopie starten

> [!warning]
>
> Das Ziel-PCC muss sich in einem der folgenden Bereiche befinden: RBX (Roubaix), LIM (Frankfurt) oder ERI (London).
>

Sobald Sie die FilerId identifiziert haben, verwenden Sie folgenden Aufruf, um den Datastore auf den Ziel-PCC zu kopieren:

> [!api]
>
> @api {POST} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/copyFiler

Geben Sie die Variablen ein:

- serviceName: Name des Ziel-PCC (z.B. pcc-192-0-2-50).
- datacenterId: die ID des Ziel-Rechenzentrums (z.B.: 1515).
- filerId: die im vorherigen Schritt abgerufene FilerId (z.B.: 001234).

Die Replikation der Daten kann mehrere Stunden dauern. Sobald die Kopie abgeschlossen ist, erhalten Sie eine E-Mail, in der der Erfolg des Kopiervorgangs bestätigt wird.

### Schritt 3: Status der Kopie abrufen

Um auf den Status der Kopie der Datastores zuzugreifen, nutzen Sie folgenden Call:

> [!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/copyFilerStatus

Geben Sie diese Variablen ein:

- serviceName: Name des Ziel-PCC (z.B. pcc-192-0-2-50).
- datacenterId: die ID des Ziel-Rechenzentrums (z.B.: 1515).

Wenn Sie eine Kopie angefordert haben, wird Ihnen die API alle Kopiervorgänge zurückgeben, die noch nicht abgeschlossen sind (prozentualer Fortschritt, Größe der übermittelten Daten, Task-Status usw.).

### Schritt 4: Zugriff auf die Kopie über vSphere

Gehen Sie in Ihrem [vSphere Interface](../den_vsphere_client_installieren/) in die Ansicht `Storage`{.action}.

![ds_restore](images/ds-restore.png){.thumbnail}

Die Replikation wird auf allen Hosts des Ziel-Rechenzentrums unter dem Namen `restore-XXXXXX` angezeigt (XXXXXX steht für die Nummer des Quell-Datastores).

> [!warning]
>
> Der wiederhergestellte Datastore ist read-only.
>

Virtuelle Maschinen müssen im [vSphere Inventar](../vsphere-register-vm-vmx/) gespeichert werden und dann in einen [Datastore geklont](../eine-vm-klonen/)  werden. Anschließend können sie gestartet werden.

## Weiterführende Informationen

Für den Austausch mit unserer User Community gehen Sie auf <https://community.ovh.com/en/>.
