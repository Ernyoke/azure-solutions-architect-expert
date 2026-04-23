# Storage Queueu

- It is part of Azure Storage Account
- It is the simples queue implementation in Azure
- There is not special pricing for Storage Queue, pricing is included in the Storage Account
- It has the same availability as blob storage

## Performance

- For reequests of 1 KB per message we get:
    - 20K messages / sec / storage account
    - 2K messages / sec/ single queueu
- Max message size in a Storage Queue is 64 KB

## Development

- Recommneded to use client libraries for many development lanugages
- Client libraries have a simple object model for using storage queues
