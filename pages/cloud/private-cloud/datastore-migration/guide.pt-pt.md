---
title: Copiar um datastore entre dois PCC
slug: datastore-copy
excerpt: Saiba como aceder à cópia de um datastore de um PCC a partir de outro PCC através das API OVHcloud
section: Funcionalidades da OVHcloud
hidden: true
---

> [!primary]
> Esta tradução foi automaticamente gerada pelo nosso parceiro SYSTRAN. Em certos casos, poderão ocorrer formulações imprecisas, como por exemplo nomes de botões ou detalhes técnicos. Recomendamos que consulte a versão inglesa ou francesa do manual, caso tenha alguma dúvida. Se nos quiser ajudar a melhorar esta tradução, clique em "Contribuir" nesta página.
>

**Última atualização: 29/03/2021**

## Objetivo

No seguimento do incidente ocorrido no datacenter SBG, pode migrar os datastores de um PCC afetado pelo incidente para um PCC de destino.

**Saiba como aceder à cópia de um datastore de um PCC a partir de outro PCC através das API OVHcloud**

## Requisitos

- Estar ligado às [API OVHcloud](https://api.ovh.com/)
- Estar ligado à [interface vSphere](../instalar_o_vsphere_client/).

> [!warning]
>
> Se o seu PCC de origem dispõe de uma certificação [HDS](https://www.ovhcloud.com/pt/enterprise/certification-conformity/hds/) ou [PCI-DSS](https://www.ovhcloud.com/pt/enterprise/certification-conformity/pci-dss/), o seu PCC de destino deverá dispor da mesma certificação **ativa** para recuperar o datastore.
>
> Para mais informações, consulte o nosso guia [Ativação da conformidade Healthcare (HDS) ou Payment Services (PCI DSS)](../ativar-certificacao-pci-dss-no-private-cloud-ovh/).
>

## Instruções

Se não está habituado ao funcionamento das API OVHcloud, consulte o nosso guia [Primeiros passos com as API OVHcloud](https://docs.ovh.com/gb/en/api/first-steps-with-ovh-api/).

### Etapa 1: recuperar o filerId do datastore

Primeiro, tem de apontar para o filerId a copiar.

Aceda a [https://api.ovh.com/](https://api.ovh.com/) e utilize a seguinte chamada:

> [!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/filer

Introduza as variáveis:

- serviceName: o nome do PCC de origem situado em SBG (ex: pcc-192-0-2-15).
- datacenterId: ID do datacenter de origem (ex: 1337).

### Etapa 2: lançar a cópia do datastore

> [!warning]
>
> O PCC de destino deve estar situado numa das seguintes zonas: RBX (Roubaix), LIM (Frankfurt) ou ERI (Londres).
>

Depois de identificar o filerId, utilize a seguinte chamada para copiar o datastore para o PCC de destino:

> [!api]
>
> @api {POST} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/copyFiler

Introduza as variáveis:

- serviceName: o nome do PCC de destino (ex: pcc-192-0-2-50).
- datacenterId: ID do datacenter de destino (ex: 1515).
- filerId: o filerId recuperado na etapa anterior (ex: 001234).

A replicação dos dados pode durar várias horas. Uma vez terminada a cópia, receberá um e-mail a confirmar que a cópia foi bem-sucedida.

### Etapa 3: conhecer o estado de adiantamento da cópia

Para aceder ao estado de avanço da cópia dos datastores, efetue a seguinte chamada:

> [!api]
>
> @api {GET} /dedicatedCloud/{serviceName}/datacenter/{datacenterId}/copyFilerStatus

Introduza as variáveis:

- serviceName: o nome do PCC de destino (ex: pcc-192-0-2-50).
- datacenterId: ID do datacenter de destino (ex: 1515).

Se tiver sido pedida uma cópia, a API devolverá o conjunto das operações de cópias, pendentes, em curso ou concluídas (percentagem de progressão, tamanho dos dados transferidos, estado da tarefa, etc.).

### Etapa 4: aceder à cópia a partir do vSphere

Na sua [interface vSphere](../instalar_o_vsphere_client/), coloque-se na vista `Armazenamento`{.action}.

![ds_restore](images/ds-restore.png){.thumbnail}

A cópia é apresentada através de um datastore em todos os hosts do datacenter de destino, sob o nome `restore-XXXXXX` (XXXXXX, que designa o número do datastore de origem).

> [!warning]
>
> O datastore recuperado está apenas em leitura.
>

Para as máquinas virtuais, é necessário [registar estas no inventário vSphere](../vsphere-register-vm-vmx/) e [cloná](../clonar-uma-vm/)-las para um dos datastores antes de as poder iniciar.

## Saiba mais

Fale com a nossa comunidade de utilizadores em <https://community.ovh.com/en/>.
