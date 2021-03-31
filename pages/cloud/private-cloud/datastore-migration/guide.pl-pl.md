---
title: Skopiuj datastore między dwoma PCC
slug: datastore-copy
excerpt: Dowiedz się, jak uzyskać dostęp do kopii zapasowej datastore usługi PCC z poziomu innej usługi PCC poprzez API OVHcloud
section: Funkcje OVHcloud
hidden: true
---

> [!primary]
> Tłumaczenie zostało wygenerowane automatycznie przez system naszego partnera SYSTRAN. W niektórych przypadkach mogą wystąpić nieprecyzyjne sformułowania, na przykład w tłumaczeniu nazw przycisków lub szczegółów technicznych. W przypadku jakichkolwiek wątpliwości zalecamy zapoznanie się z angielską/francuską wersją przewodnika. Jeśli chcesz przyczynić się do ulepszenia tłumaczenia, kliknij przycisk „Zaproponuj zmianę” na tej stronie.
> 

**Ostatnia aktualizacja z dnia 29-03-2021**

## Wprowadzenie

W związku z incydentem w centrum danych SBG, możesz przenieść przestrzeń dyskową na usługę CDN dotkniętą incydentem do docelowego PCC.

**Dowiedz się, jak uzyskać dostęp do kopii zapasowej datastore usługi PCC z poziomu innej usługi PCC poprzez API OVHcloud**

## Wymagania początkowe

- Dostęp do [API OVHcloud](https://api.ovh.com/)
- Dostęp do [interfejsu vSphere](../polaczenie-interfejs-vsphere/)

> [!warning]
>
> Jeśli Twoja źródłowa usługa PCC dysponuje certyfikatem [HDS](https://www.ovhcloud.com/pl/enterprise/certification-conformity/hds/) lub [PCI-DSS](https://www.ovhcloud.com/pl/enterprise/certification-conformity/pci-dss/), docelowa usługa PCC będzie musiała posiadać **aktywną** certyfikację, aby pobrać datastore.
>
> Więcej informacji znajdziesz w przewodniku [Aktywacja zgodności usługi Healthcare (HDS) lub Payment Services (PCI DSS)](../aktywuj-opcje-hds-hipp-lub-pci-dss/).
>

## W praktyce

Jeśli nie jesteś przyzwyczajony do działania API OVHcloud, zapoznaj się z naszym przewodnikiem [Pierwsze kroki z API OVHcloud](https://docs.ovh.com/gb/en/api/first-steps-with-ovh-api/).

### Etap 1: pobierz filerId z datastore

Najpierw należy wybrać filerId do skopiowania.

Zaloguj się na stronie [https://api.ovh.com/](https://api.ovh.com/) i skorzystaj z następującego połączenia:

> [!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/filer

Wpisz zmienne:

- serviceName: nazwa oryginalnego PCC zlokalizowanego w SBG (np.: pcc-192-0-2-15).
- datacenterId: ID źródłowego centrum danych (np. 1337).

### Etap 2: uruchom kopię datastore

> [!warning]
>
> Docelowa PCC musi być zlokalizowana w jednym z następujących obszarów: RBX (Roubaix), LIM (Frankfurt) lub ERI (Londyn).
>

Po zidentyfikowaniu filerId zastosuj następujące połączenie, aby skopiować datastore na docelowy PCC:

> [!api]
>
> @api {POST} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/copyFiler

Wpisz zmienne:

- serviceName: nazwa docelowego PCC (np.: pcc-192-0-2-50).
- datacenterId: ID docelowego centrum danych (np.: 1515).
- filerId: filerId odzyskany w poprzednim etapie (np.: 001234).

Replikacja danych może trwać kilka godzin. Po zakończeniu operacji otrzymasz e-mail z potwierdzeniem sukcesu kopii.

### Etap 3: znać status kopii

Aby uzyskać dostęp do aktualnego stanu kopii zapasowej datastore, wywołaj następujące połączenie:

> [!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/copyFilerStatus

Wpisz zmienne:

- serviceName: nazwa docelowego PCC (np.: pcc-192-0-2-50).
- datacenterId: ID docelowego centrum danych (np.: 1515).

Jeśli żądana jest kopia, API zwróci Ci wszystkie oczekujące, bieżące lub zakończone operacje kopiowania (procent postępu, rozmiar przesłanych danych, stan zadania, itp.).

### Etap 4: dostęp do kopii w vSphere

W [interfejsie vSphere](../polaczenie-interfejs-vsphere/) umieść widok `Storage`{.action}.

![ds_restore](images/ds-restore.png){.thumbnail}

Kopię przekazuje się za pomocą datastore na wszystkich hostach w docelowym centrum danych, pod nazwą `restore-XXXX` (XXXX określający numer datastore źródłowego).

> [!warning]
>
> Odzyskany datastore jest w trybie do odczytu.
>

W przypadku wirtualnych maszyn [należy je zapisać w wykazie vSphere](../vsphere-register-vm-vmx), a następnie [sklonować](../klonowanie-wiertualnej-maszyny/) do jednego z datastores przed ich uruchomieniem.

## Sprawdź również

Dołącz do społeczności naszych użytkowników na stronie<https://community.ovh.com/en/>.
