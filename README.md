---
page_type: sample
languages:
  - azdeveloper
  - javascript
  - typescript
  - nodejs
  - bicep
products:
  - azure
  - ai-services
  - azure-openai
urlFragment: trabajo_Tech_MBA
name: Trabajo Tech MBA
description: Un proyecto de ejemplo para utilizar las capacidades de Generative AI en sus proyectos del MBA.
---

<!-- YAML front-matter schema: https://review.learn.microsoft.com/en-us/help/contribute/samples/process/onboarding?branch=main#supported-metadata-fields-for-readmemd -->

# Trabajo final: Tecnologías Digitales

## Tabla de Contenidos

- [Funcionalidades](#funcionalidades)
- [Requerimientos de Azure](#prerequisitos)
- [Usando tus propios datos](#usando-tus-propios-datos)
- [Deployment en Azure](#deployment-en-Azure)
  - [Setup del proyecto](#setup-del-proyecto)
    - [Requerimientos del ambiente local](#requerimientos-del-ambiente-local)
  - [Instalando desde 0](#instalando-desde-0)
  - [Instalando con recursos existentes](#instalando-con-recursos-existentes)
- [Usando la aplicacion web](#usando-la-aplicacion-web)
- [Adaptando la interfaz grafica](#adaptando-la-interfaz-grafica)
- [Limpieza](#limpieza)
- [Recursos](#recursos)
  - [Nota](#nota)
  - [FAQ](#faq)
  - [Troubleshooting](#troubleshooting)

Este ejemplo demuestra algunos enfoques para crear experiencias similares a ChatGPT a partir de sus propios datos utilizando el patrón de generación aumentada de recuperación (RAG en inglés). Utiliza el servicio Azure OpenAI para acceder al modelo ChatGPT (gpt-35-turbo) y Azure AI Search para la indexación y recuperación de datos.

![Arquitectura RAG](docs/rag-architecture.png)

El repositorio incluye datos de muestra, por lo que está listo para probarse de principio a fin. En esta aplicación de ejemplo utilizamos una empresa ficticia llamada Contoso Real Estate y la experiencia permite a sus clientes hacer preguntas de soporte sobre el uso de sus productos. Los datos de muestra incluyen un conjunto de documentos que describen sus términos de servicio, política de privacidad y una guía de soporte.

La aplicación está hecha de múltiples componentes, que incluyen:

- **Search service**: el servicio backend que proporciona las capacidades de búsqueda y recuperación.
- **Indexer service**: el servicio que indexa los datos y crea los índices de búsqueda.
- **Web app**: la aplicación web frontend que proporciona la interfaz de usuario y organiza la interacción entre el usuario y los servicios backend.

![App Architecture](docs/app-architecture.drawio.png)

## Funcionalidades

- Interfaces de chat y preguntas y respuestas.
- Explora varias opciones para ayudar a los usuarios a evaluar la confiabilidad de las respuestas con citas, seguimiento del contenido fuente, etc.
- Muestra posibles enfoques para la preparación de datos, la construcción rápida y la orquestación de la interacción entre el modelo (ChatGPT) y el recuperador (Azure AI Search).
- Configuraciones directamente en la UX para modificar el comportamiento y experimentar con opciones

![Chat screen](docs/chat-screenshot.png)

[📺 Mirar un video sobre la aplicación (en inglés)](https://youtu.be/uckVTuS36H0)

## Prerequisitos

- **Azure**. Acceso a la subscripción en Azure que vimos en las clases de Diciembre 2023. Recueden ingresear al [Portal](https://portal.azure.com) para revisar los componentes
- **Azure OpenAI**. Ocuparemos los modelos actualmente disponibles en nuestra subscripción de Azure en el grupo de recursos Azure llamado "Clase-Viernes-15". Pueden experimentar con los modelos disponibles a través de [Azure AI Studio](https://oai.azure.com/)

## Usando tus propios datos

La aplicación está diseñada para trabajar con cualquier archivo PDF. Estos se deben de agegar a la carpeta data del proyecto antes de comenzar a hacer el proceso de Deploy en Azure (a continuación)

## Deployment en Azure

### Setup del proyecto

#### Requerimientos del ambiente local

- [Azure Developer CLI](https://aka.ms/azure-dev/install)
- [Node.js LTS](https://nodejs.org/en/download/)
- [Docker for Desktop](https://www.docker.com/products/docker-desktop/)
- [Git](https://git-scm.com/downloads)
- [Powershell 7+ (pwsh)](https://github.com/powershell/powershell) - Sólo para usuarios de Windows.
  - **Importante**: Asegúrate de que puedes ejecutar `pwsh.exe` desde un comando en PowerShell. Si esto falla, probablemente se necesite actualizar PowerShell.

### Instalando desde 0

Ejecute el siguiente comando si no tiene ningún servicio de Azure preexistente y desea comenzar desde una implementación nueva.

1. Ejecutar `azd up` - Esto aprovisionará recursos de Azure e implementará esta muestra en esos recursos, incluida la creación del índice de búsqueda basado en los archivos que se encuentran en la carpeta `./data`.
   - Se le pedirá que seleccione una ubicación para la mayoría de los recursos, excepto los recursos de OpenAI y Static Web App.
   - De forma predeterminada, el recurso OpenAI se implementará en `eastus2`. Puede establecer una ubicación diferente con `azd env set AZURE_OPENAI_RESOURCE_GROUP_LOCATION {ubicacion}`. Actualmente sólo se acepta una breve lista de ubicaciones. Esa lista de ubicaciones se basa en la [tabla de disponibiliadd de modelos de OpenAI](https://azure.microsoft.com/explore/global-infrastructure/products-by-region/?products=search) y puede quedar obsoleto a medida que cambia la disponibilidad.
     -De forma predeterminada, el recurso de la aplicación web estática se implementará en `eastus2`. Puede establecer una ubicación diferente con `azd env set AZURE_WEBAPP_LOCATION {ubicacion}`. Actualmente sólo se acepta una breve lista de ubicaciones. Tenga en cuenta que la aplicación web estática es un servicio global y la ubicación que elija solo afectará a la aplicación de funciones administrada que no se utiliza en este ejemplo.
2. Una vez que la aplicación se haya implementado correctamente, verá una URL impresa en la consola. Haga clic en esa URL para interactuar con la aplicación en su navegador.
   Se verá como esto:

!['Salida luego de ejecutar azd up'](docs/deployment.png)

> NOTA: La aplicación puede tardar más de 15 minutos en implementarse por completo.

### Instalando con recursos existentes

Si ya tiene recursos de Azure existentes, puede reutilizarlos estableciendo valores de entorno `azd`.

#### Grupo de Recursos existente

1. Ejecutar `azd env set AZURE_RESOURCE_GROUP {Nombre del grupo de recursos existente}`
1. Ejecutar `azd env set AZURE_LOCATION {Ubicación del grupo de recursos existente}`

#### Azure OpenAI ya existente

1. Ejecutar `azd env set AZURE_OPENAI_SERVICE {Nombre del servicio OpenAI existente}`
1. Ejecutar `azd env set AZURE_OPENAI_RESOURCE_GROUP {Nombre del grupo de recursos donde OpenAI fue aprovisionado}`
1. Ejecutar `azd env set AZURE_OPENAI_CHATGPT_DEPLOYMENT {Nombre de la implementación de ChatGPT}`. Solo es necesario si su implementación de ChatGPT no es el 'chat' predeterminado.
1. Ejecutar `azd env set AZURE_OPENAI_EMBEDDING_DEPLOYMENT {Nombre de la implementación de Embedding}`. Solo es necesario si su implementación de embeddings no es 'embedding'.

#### Azure AI Search existente

1. Ejecutar `azd env set AZURE_SEARCH_SERVICE {Nombre del servicio Azure AI Search existente}`
1. Ejecutar `azd env set AZURE_SEARCH_SERVICE_RESOURCE_GROUP {Nombre del grupo de recursos existente con servicio ACS}`
1. Si ese grupo de recursos está en una ubicación diferente a la que elegirá para el paso de `azd up`,
   entonces ejecuta `azd env set AZURE_SEARCH_SERVICE_LOCATION {Ubicación del servicio existente}`
1. Si el SKU del servicio de búsqueda no es estándar, ejecute `azd env set AZURE_SEARCH_SERVICE_SKU {Nombre del SKU}`. La capa gratuita no funcionará porque no admite identidades administradas. ([Ver otros posibles valores](https://learn.microsoft.com/azure/templates/microsoft.search/searchservices?pivots=deployment-language-bicep#sku))

#### Otros recursos de Azure existentes

También puede utilizar Form Recognizer y Storage Accounts existentes. Ver `./infra/main.parameters.json` para obtener una lista de variables de entorno a las que pasar a `azd env set` para configurar esos recursos existentes.

#### Aprovisionar los recursos restantes

Ahora puedes ejecutar `azd up`, siguiendo los pasos desde [Instalando desde 0](#Instalando desde 0).
Eso aprovisionará recursos e implementará el código.

### Implementando nuevamente

Si solo ha cambiado el código backend/frontend en la carpeta `app`, entonces no es necesario volver a aprovisionar los recursos de Azure. Puedes simplemente ejecutar:

`azd deploy`

Si ha cambiado los archivos de infraestructura (carpeta `infra` o `azure.yaml`), entonces deberá volver a aprovisionar los recursos de Azure. Puedes hacerlo ejecutando:

`azd up`

## Usando la aplicacion web

- En Azure: navegue hasta la aplicación web estática de Azure implementada por azd. La URL se imprime cuando se completa azd (como "Endpoint"), o puedes encontrarla en el Portal de Azure, dentro del grupo de recursos que se ha creado.

Una vez en la aplicación web:

- Pruebe diferentes temas en el chat o en el contexto de preguntas y respuestas. Para chatear, intente hacer preguntas de seguimiento, aclaraciones, pedir que simplifiquen o desarrollen la respuesta, etc.
- Explorar citas y fuentes.
- Haga clic en "configuración" para probar diferentes opciones, modificar indicaciones (prompts), etc.

## Adaptando la interfaz grafica

La interfáz gráfica (frontend) está construida usando [React](https://reactjs.org/) y [componentes Fluent UI](https://react.fluentui.dev/). Los componentes de la interfáz gráfica están en la carpeta `app/frontend/src`. Los típicos componentes a adaptar son

- `packages/webapp/index.html`: Título de la página
- `packages/webapp/src/pages/layout/Layout.tsx`: Cambios de encabezado y logo
- `packages/webapp/src/pages/chat/Chat.tsx`: Cambios en la forma del chat
- `packages/chat-component/src/config/global-config.js`: Configuraciones generales del chat. También están las preguntas ahí.

## Recursos

- [Generative AI For Beginners](https://github.com/microsoft/generative-ai-for-beginners)
- [Revolutionize your Enterprise Data with ChatGPT: Next-gen Apps w/ Azure OpenAI and AI Search](https://aka.ms/entgptsearchblog)
- [Azure AI Search](https://learn.microsoft.com/azure/search/search-what-is-azure-search)
- [Azure OpenAI Service](https://learn.microsoft.com/azure/cognitive-services/openai/overview)
- [Building ChatGPT-Like Experiences with Azure: A Guide to Retrieval Augmented Generation for JavaScript applications](https://devblogs.microsoft.com/azure-sdk/building-chatgpt-like-experiences-with-azure-a-guide-to-retrieval-augmented-generation-for-javascript-applications/)

## Limpieza

Para limpiar todos los recursos creados por este ejemplo:

1. Ejecutar `azd down --purge`
2. Cuando se le pregunte si está seguro de querer continuar, ingrese `y`
3. Cuando se le pregunte si desea eliminar permanentemente los recursos, ingrese `y`

Se eliminarán el grupo de recursos y todos los recursos.

### Nota

> Nota: Los documentos utilizados en esta demostración contienen información generada mediante un modelo de lenguaje (servicio Azure OpenAI). La información contenida en estos documentos tiene únicamente fines de demostración y no refleja las opiniones o creencias de Microsoft. Microsoft no ofrece ninguna declaración ni garantía de ningún tipo, expresa o implícita, sobre la integridad, exactitud, confiabilidad, idoneidad o disponibilidad con respecto a la información contenida en este documento. Todos los derechos reservados a Microsoft.

### FAQ

<details><a id="ingestion-why-chunk"></a>
<summary>Why do we need to break up the documents into chunks when Azure AI Search supports searching large documents?</summary>

Chunking allows us to limit the amount of information we send to OpenAI due to token limits. By breaking up the content, it allows us to easily find potential chunks of text that we can inject into OpenAI. The method of chunking we use leverages a sliding window of text such that sentences that end one chunk will start the next. This allows us to reduce the chance of losing the context of the text.

</details>

<details><a id="ingestion-more-pdfs"></a>
<summary>How can we upload additional documents without redeploying everything?</summary>

To upload more documents, put them in the data/ folder and run `./scripts/index-data.sh` or `./scripts/index-data.ps1`.

</details>

<details><a id="compare-samples"></a>
<summary>How does this sample compare to other Chat with Your Data samples?</summary>

Another popular repository for this use case is here:
https://github.com/Microsoft/sample-app-aoai-chatGPT/

That repository is designed for use by customers using Azure OpenAI studio and Azure Portal for setup. It also includes `azd` support for folks who want to deploy it completely from scratch.

The primary differences:

- This repository includes multiple RAG (retrieval-augmented generation) approaches that chain the results of multiple API calls (to Azure OpenAI and ACS) together in different ways. The other repository uses only the built-in data sources option for the ChatCompletions API, which uses a RAG approach on the specified ACS index. That should work for most uses, but if you needed more flexibility, this sample may be a better option.
- This repository is also a bit more experimental in other ways, since it's not tied to the Azure OpenAI Studio like the other repository.

Feature comparison:

| Feature                 | azure-search-openai-javascript | sample-app-aoai-chatGPT                  |
| ----------------------- | ------------------------------ | ---------------------------------------- |
| RAG approach            | Multiple approaches            | Only via ChatCompletion API data_sources |
| Vector support          | ✅ Yes                         | ✅ Yes                                   |
| Data ingestion          | ✅ Yes (MD)                    | ✅ Yes (PDF, TXT, MD, HTML)              |
| Persistent chat history | ❌ No (browser tab only)       | ✅ Yes, in CosmosDB                      |

Technology comparison:

| Tech       | azure-search-openai-javascript | sample-app-aoai-chatGPT |
| ---------- | ------------------------------ | ----------------------- |
| Frontend   | React/Lit                      | React                   |
| Backend    | Node.js (Fastify)              | Python (Flask)          |
| Vector DB  | Azure AI Search                | Azure AI Search         |
| Deployment | Azure Developer CLI (azd)      | Azure Portal, az, azd   |

</details>

<details><a id="switch-gpt4"></a>
<summary>How do you use GPT-4 with this sample?</summary>

Run these commands:

```bash
azd env set AZURE_OPENAI_CHATGPT_MODEL gpt-4
```

You may also need to adjust the capacity in `infra/main.bicep` file, depending on how much TPM your account is allowed.

</details>
<details><a id="chat-ask-diff"></a>
<summary>What is the difference between the Chat and Ask tabs?</summary>

The chat tab uses the approach programmed in [chat-read-retrieve-read.ts](https://github.com/Azure-Samples/azure-search-openai-javascript/blob/main/packages/search/src/lib/approaches/chat-read-retrieve-read.ts).
The ask tab uses the approach programmed in [ask-retrieve-then-read.ts](https://github.com/Azure-Samples/azure-search-openai-javascript/blob/main/packages/search/src/lib/approaches/ask-retrieve-then-read.ts).
There is also another one /ask approach available, [using an agent](https://github.com/Azure-Samples/azure-search-openai-javascript/blob/main/packages/search/src/lib/approaches/ask-read-retrieve-read.ts.

</details>

<details><a id="azd-up-explanation"></a>
<summary>What does the `azd up` command do?</summary>

The `azd up` command comes from the [Azure Developer CLI](https://learn.microsoft.com/azure/developer/azure-developer-cli/overview), and takes care of both provisioning the Azure resources and deploying code to the selected Azure hosts.

The `azd up` command uses the `azure.yaml` file combined with the infrastructure-as-code `.bicep` files in the `infra/` folder. The `azure.yaml` file for this project declares several "hooks" for the prepackage step and postprovision steps. The `up` command first runs the `prepackage` hook which installs Node dependencies and builds the React.JS-based JavaScript files. It then packages all the code (both frontend and backend services) into a zip file which it will deploy later.

Next, it provisions the resources based on `main.bicep` and `main.parameters.json`. At that point, since there is no default value for the OpenAI resource location, it asks you to pick a location from a short list of available regions. Then it will send requests to Azure to provision all the required resources. With everything provisioned, it runs the `postprovision` hook to process the local data and add it to an Azure AI Search index.

Finally, it looks at `azure.yaml` to determine the Azure host (Container Apps and Static Web Apps, in this case) and uploads the zip to Azure App Service. The `azd up` command is now complete, but it may take a few minutes for the app to be fully available and working after the initial deploy.

Related commands are `azd provision` for just provisioning (if infra files change) and `azd deploy` for just deploying updated app code.

</details>

### Troubleshooting

Here are the most common failure scenarios and solutions:

1. The subscription (`AZURE_SUBSCRIPTION_ID`) doesn't have access to the Azure OpenAI service. Please ensure `AZURE_SUBSCRIPTION_ID` matches the ID specified in the [OpenAI access request process](https://aka.ms/oai/access).

1. You're attempting to create resources in regions not enabled for Azure OpenAI (e.g. East US 2 instead of East US), or where the model you're trying to use isn't enabled. See [this matrix of model availability](https://aka.ms/oai/models).

1. You've exceeded a quota, most often number of resources per region. See [this article on quotas and limits](https://aka.ms/oai/quotas).

1. You're getting "same resource name not allowed" conflicts. That's likely because you've run the sample multiple times and deleted the resources you've been creating each time, but are forgetting to purge them. Azure keeps resources for 48 hours unless you purge from soft delete. See [this article on purging resources](https://learn.microsoft.com/azure/cognitive-services/manage-resources?tabs=azure-portal#purge-a-deleted-resource).

1. After running `azd up` and visiting the website, you see a '404 Not Found' in the browser. Wait 10 minutes and try again, as it might be still starting up. Then try running `azd deploy` and wait again. If you still encounter errors with the deployed app, consult these [tips for debugging App Service app deployments](http://blog.pamelafox.org/2023/06/tips-for-debugging-flask-deployments-to.html) and file an issue if the error logs don't help you resolve the issue.

1. You're getting an error `401 Principal does not have access to API/Operation` while running the project locally or trying to deploy. That's likely because your environment variables include `AZURE_TENANT_ID`, `AZURE_CLIENT_ID` and `AZURE_CLIENT_SECRET`. You should either grant permissions to the related _Service Principal_ or remove these variables from your environment to ensure normal access. For more details, please refer to [Azure identity SDK](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/identity/identity/README.md#defaultazurecredential).
