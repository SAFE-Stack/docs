# [Azure](https://azure.microsoft.com/en-gb/overview/what-is-azure/) in SAFE

## What is Azure?

Azure is a comprehensive set of cloud services that developers and IT professionals use to build, deploy and manage applications through a global network of data centres. Integrated tools, DevOps and a marketplace support you in efficiently building anything from simple mobile apps to Internet-scale solutions.

## How does Azure integrate with SAFE?

Azure provides a number of flexible services for SAFE applications, including (but not only):

### Hosting Services

Azure comes with several ready-made hosting services, including [App Service](https://azure.microsoft.com/en-us/services/app-service/), which enables seamless hosting of web applications, including ASP.NET Core applications (which [Saturn](component-saturn.md) is built on top of). In addition, Azure supports a number of managed [hosting services](https://azure.microsoft.com/en-us/services/container-instances/) for [Docker](https://azure.microsoft.com/en-us/services/app-service/containers/) and [Kubernetes](https://azure.microsoft.com/en-us/services/container-service/), which work fantastically well with SAFE.

### Platform Services

Azure comes with a large number of ready-made platform services that can **dramatically lower the cost** of developing bespoke systems, including:

* **Compute** services such as [Azure Functions](https://azure.microsoft.com/en-us/services/functions/), for hosting F# code that can dynamically scale based on load, as well as [Service Fabric](https://azure.microsoft.com/en-us/services/service-fabric/) or [Virtual Machines](https://azure.microsoft.com/en-us/services/virtual-machines/).
* **Storage** services such as [Azure Storage](https://azure.microsoft.com/en-us/services/storage/) and [Data Lake](https://azure.microsoft.com/en-us/services/data-lake-store/), for storing virtually limitless volumes of data in unstructured or structure form.
* **Database** services, including managed [SQL Server](https://azure.microsoft.com/en-us/services/sql-database/), [MySQL](https://azure.microsoft.com/en-us/services/mysql/) and [Postgres](https://azure.microsoft.com/en-us/services/postgresql/), as well as [CosmosDB](https://azure.microsoft.com/en-us/services/cosmos-db/) for document and graph stores, [Redis](https://azure.microsoft.com/en-us/services/cache/) and more.
* **Messaging** services including [Queues](https://azure.microsoft.com/en-us/services/storage/queues/), [Service Bus](https://azure.microsoft.com/en-us/services/service-bus/) and [Event Hub](https://azure.microsoft.com/en-us/services/event-hubs/).
* **Analytical** services such as [Stream Analytics](https://azure.microsoft.com/en-us/services/stream-analytics/), [Databricks](https://azure.microsoft.com/en-us/services/databricks/), [Machine Learning](https://azure.microsoft.com/en-us/overview/machine-learning/) and [Analysis Services](https://azure.microsoft.com/en-us/services/analysis-services/).
* **Security** services such as [Key Vault](https://azure.microsoft.com/en-us/services/key-vault/) and [Active Directory](https://azure.microsoft.com/en-us/services/active-directory/).

Many of the above services have ready-made SDKs that can be run on .NET and therefore from F#.
