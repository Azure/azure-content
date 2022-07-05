---
author: cephalin
ms.service: app-service
ms.devlang: java
ms.topic: quickstart
ms.date: 06/30/2022
ms.author: cephalin
---

[Azure App Service](../../overview.md) on Linux provides pre-defined application stacks on Linux with support for languages such as .NET, PHP, Node.js and others. You can also use a custom Docker image to run your web app on an application stack that isn't already defined in Azure. This quickstart shows you how to deploy an image from Docker Hub to App Service.

To complete this quickstart, you need:

* An [Azure account](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)

## 1 - Deploy to Azure

### Sign in to Azure portal

Sign in to the Azure portal at https://portal.azure.com.


### Create Azure resources

1. Type **app services** in the search. Under **Services**, select **App Services**.

     :::image type="content" source="../../media/quickstart-custom-container/portal-search.png?text=Azure portal search details" alt-text="Screenshot of portal search.":::

1. In the **App Services** page, select **+ Create**.

1. In the **Basics** tab, under **Project details**, ensure the correct subscription is selected and then select to **Create new** resource group. Type *myResourceGroup* for the name.

    :::image type="content" source="../../media/quickstart-custom-container/project-details.png" alt-text="Screenshot of the Project details section showing where you select the Azure subscription and the resource group for the web app.":::

1. Under **Instance details**, type a globally unique name for your web app and select **Docker Container**. Select *Linux* for the **Operating System**. Select a **Region** you want to serve your app from.

    :::image type="content" source="../../media/quickstart-custom-container/instance-details-linux.png" alt-text="Screenshot of the Instance details section where you provide a name for the virtual machine and select its region, image and size.":::

1. Under **App Service Plan**, select **Create new** App Service Plan. Type *myAppServicePlan* for the name. To change to the Free tier, select **Change size**, select the **Dev/Test** tab, select **F1**, and select the **Apply** button at the bottom of the page.

    :::image type="content" source="../../media/quickstart-custom-container/app-service-plan-details-linux.png" alt-text="Screenshot of the Administrator account section where you provide the administrator username and password.":::

1. Select the **Next: Docker >** button at the bottom of the page.

1. In the **Docker** tab, ensure *Single Container* **option** is selected, and select *Docker Hub* **Image Source**.

    :::image type="content" source="../../media/quickstart-custom-container/docker-details-linux.png" alt-text="Screenshot showing the container deployment options.":::

1. Under **Docker hub options**, set the **Access Type** to *Public*. Set **Image and tag** to *mcr.microsoft.com/dotnet/samples:aspnetapp*.

    :::image type="content" source="../../media/quickstart-custom-container/docker-hub-options-linux.png" alt-text="Screenshot showing the docker hub options.":::

1. Select the **Review + create** button at the bottom of the page.

    :::image type="content" source="../../media/quickstart-custom-container/review-create.png" alt-text="Screenshot showing the Review and create button at the bottom of the page.":::

1. After validation runs, select the **Create** button at the bottom of the page.

1. After deployment is complete, select **Go to resource**.

    :::image type="content" source="../../media/quickstart-custom-container/next-steps.png" alt-text="Screenshot showing the next step of going to the resource.":::


## 2 - Browse to the app

1. Browse to the deployed application in your web browser at the URL `http://<app-name>.azurewebsites.net`.

    :::image type="content" source="../../media/quickstart-custom-container/browse-custom-container-linux.png" alt-text="Screenshot showing the deployed application.":::

## 3 - Clean up resources

[!INCLUDE [Clean-up Portal web app resources](../../../../includes/clean-up-section-portal-no-h.md)]


## Next steps

Congratulations, you've successfully completed this quickstart.

The App Service app pulls from the container registry every time it starts. If you rebuild your image, you just need to push it to your container registry, and the app pulls in the updated image when it restarts. To tell your app to pull in the updated image immediately, restart it.

> [!div class="nextstepaction"]
> [Configure custom container](../../configure-custom-container.md)

> [!div class="nextstepaction"]
> [Custom container tutorial](../../tutorial-custom-container.md)

> [!div class="nextstepaction"]
> [Multi-container app tutorial](../../tutorial-multi-container-app.md)
