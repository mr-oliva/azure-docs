---
 title: include file
 description: include file
 services: virtual-machines
 author: roygara
 ms.service: virtual-machines
 ms.topic: include
 ms.date: 12/09/2022
 ms.author: rogarana
 ms.custom: include file
---    
- Only supported for data disks.
- If a disk is 4 TiB or less, you can't expand it beyond 4 TiB without deallocating the VM. If a disk is already greater than 4 TiB, you can expand it without deallocating the VM.
- Not supported for Ultra disks or Premium SSD v2 disks.
- Not supported for shared disks.
- Install and use either:
    - The [latest Azure CLI](/cli/azure/install-azure-cli)
    - The [latest Azure PowerShell module](/powershell/azure/install-az-ps)
    - The [Azure portal](https://portal.azure.com/)
    - Or an Azure Resource Manager template with an API version that's `2021-04-01` or newer.
- Not available on some classic VMs. Use [this script](#expanding-without-downtime-classic-vm-sku-support) to get a list of classic VM SKUs that support expanding without downtime.