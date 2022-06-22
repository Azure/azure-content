---
title: 'Quickstart: Create a Ruby app'
description: Get started with Azure App Service by deploying your first Ruby app to a Linux container in App Service.
keywords: azure app service, linux, oss, ruby, rails
ms.assetid: 6d00c73c-13cb-446f-8926-923db4101afa
ms.topic: quickstart
ms.date: 04/27/2021
ms.devlang: ruby
ms.custom: mvc, cli-validate, seodec18, devx-track-azurecli, mode-other
---

# Create a Ruby on Rails App in App Service

[Azure App Service on Linux](overview.md#app-service-on-linux) provides a highly scalable, self-patching web hosting service using the Linux operating system.

> [!NOTE]
> The Ruby development stack only supports Ruby on Rails at this time. If you want to use a different platform, such as Sinatra, or if you want to use an unsupported Ruby version, you need to [run it in a custom container](./quickstart-custom-container.md?pivots=platform-linux%3fpivots%3dplatform-linux).

![Screenshot of the sample app running in Azure, showing 'Hello from Azure App Service on Linux!'.](media/quickstart-ruby/ruby-hello-world-in-browser.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

### [Azure CLI](#tab/cli)

If you are running the sample code locally, you will need:

* <a href="https://www.ruby-lang.org/en/documentation/installation/#rubyinstaller" target="_blank">Install Ruby 2.6 or higher</a>
* <a href="https://git-scm.com/" target="_blank">Install Git</a>

You will not need to install these if you are using the Cloud Shell.

[!INCLUDE [Try Cloud Shell](../../includes/cloud-shell-try-it.md)]
  
### [Portal](#tab/portal)

To complete this quickstart you need:

1. An Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
1. A GitHub account to fork a repository.

---

## 1 - Get the sample repository

### [Azure CLI](#tab/cli)

This quickstart tutorial shows how to deploy a Ruby on Rails app to App Service on Linux using the [Cloud Shell](../cloud-shell/overview.md).

1. In a terminal window, clone the sample application to your local machine, and navigate to the directory containing the sample code.

    ```bash
    git clone https://github.com/Azure-Samples/ruby-docs-hello-world
    cd ruby-docs-hello-world
    ```

1. Make sure the default branch is `main`.

    ```bash
    git branch -m main
    ```

    > [!TIP]
    > The branch name change isn't required by App Service. However, since many repositories are changing their default branch to `main`, this tutorial also shows you how to deploy a repository from `main`. For more information, see [Change deployment branch](deploy-local-git.md#change-deployment-branch).

### Run the application locally

If you want to run the application locally to see how it works, clone the repository locally and follow these steps.

> [!NOTE]
> These steps cannot be run in Azure Cloud Shell, due to version conflicts with Ruby.

1. Install the required gems. There's a `Gemfile` included in the sample, so just run the following command:

    ```bash
    bundle install
    ```

1. Once the gems are installed, start the app:

    ```bash
    bundle exec rails server
    ```

1. Using your web browser, navigate to `http://localhost:3000` to test the app locally.

    ![Hello World configured](media/quickstart-ruby/hello-world-updated.png)

### [Portal](#tab/portal)

This quickstart shows how to deploy a Ruby on Rails app to App Service on Linux within your browser, without having to install the development environment tools on your machine.

1. In your browser, navigate to the repository containing [the sample code](https://github.com/Azure-Samples/ruby-docs-hello-world).

1. In the upper right corner, select **Fork**.

    ![Screenshot of the Azure-Samples/ruby-docs-hello-world repo in GitHub, with the Fork option highlighted.](media/quickstart-ruby/fork-ruby-docs-hello-world-repo.png)

1. On the **Create a new fork** screen, confirm the **Owner** and **Repository name** fields. Select **Create fork**.

    ![Screenshot of the Create a new fork page in GitHub for creating a new fork of Azure-Samples/ruby-docs-hello-world.](media/quickstart-ruby/fork-details-ruby-docs-hello-world-repo.png)

    >[!NOTE]
    > This should take you to the new fork. Your fork URL will look something like this: https://github.com/YOUR_GITHUB_ACCOUNT_NAME/ruby-docs-hello-world

---

## 2 - Deploy your application code to Azure

### [Azure CLI](#tab/cli)

[!INCLUDE [Configure deployment user](../../includes/configure-deployment-user.md)]

[!INCLUDE [Create resource group](../../includes/app-service-web-create-resource-group-linux.md)]

[!INCLUDE [Create app service plan](../../includes/app-service-web-create-app-service-plan-linux.md)]


1. Create a [web app](overview.md#app-service-on-linux) in the `myAppServicePlan` App Service plan.

    In the Cloud Shell, you can use the [`az webapp create`](/cli/azure/webapp) command. In the following example, replace `<app-name>` with a globally unique app name (valid characters are `a-z`, `0-9`, and `-`). The runtime is set to `RUBY|2.7`. To see all supported runtimes, run [`az webapp list-runtimes --os linux`](/cli/azure/webapp).

    ```azurecli-interactive
    az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime 'RUBY|2.7' --deployment-local-git
    ```

    When the web app has been created, the Azure CLI shows output similar to the following example:

    <pre>
    Local git is configured with url of 'https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git'
    {
      "availabilityState": "Normal",
      "clientAffinityEnabled": true,
      "clientCertEnabled": false,
      "cloningInfo": null,
      "containerSize": 0,
      "dailyMemoryTimeQuota": 0,
      "defaultHostName": "&lt;app-name&gt;.azurewebsites.net",
      "deploymentLocalGitUrl": "https://&lt;username&gt;@&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git",
      "enabled": true,
      &lt; JSON data removed for brevity. &gt;
    }
    </pre>

    You've created an empty new web app, with git deployment enabled.

    > [!NOTE]
    > The URL of the Git remote is shown in the `deploymentLocalGitUrl` property, with the format `https://<username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Save this URL as you need it later.
    >

1. Browse to the app to see your newly created web app with built-in image. Replace _&lt;app-name>_ with your web app name.

    ```bash
    http://<app_name>.azurewebsites.net
    ```

    Here is what your new web app should look like:

    ![Splash page]media/quickstart-ruby/splash-page.png)

[!INCLUDE [Push to Azure](../../includes/app-service-web-git-push-to-azure-no-h.md)]

   <pre>
   remote: Using turbolinks 5.2.0
   remote: Using uglifier 4.1.20
   remote: Using web-console 3.7.0
   remote: Bundle complete! 18 Gemfile dependencies, 78 gems now installed.
   remote: Bundled gems are installed into `/tmp/bundle`
   remote: Zipping up bundle contents
   remote: .......
   remote: ~/site/repository
   remote: Finished successfully.
   remote: Running post deployment command(s)...
   remote: Deployment successful.
   remote: App container will begin restart within 10 seconds.
   To https://&lt;app-name&gt;.scm.azurewebsites.net/&lt;app-name&gt;.git
      a6e73a2..ae34be9  main -> main
   </pre>

1. Once the deployment has completed, wait about 10 seconds for the web app to restart, and then navigate to the web app and verify the results.

```bash
http://<app-name>.azurewebsites.net
```

![updated web app]media/quickstart-ruby/hello-world-configured.png)

> [!NOTE]
> While the app is restarting, you may observe the HTTP status code `Error 503 Server unavailable` in the browser, or the `Hey, Ruby developers!` default page. It may take a few minutes for the app to fully restart.
>

### [Portal](#tab/portal)

1. Sign into the Azure portal.

1. At the top of the portal, type **app services** in the search box. Under **Services**, select **App Services**.

    ![Screenshot of the Azure portal with 'app services' typed in the search text box. In the results, the App Services option under Services is highlighted.](media/quickstart-ruby/azure-portal-search-for-app-services.png)

1. In the **App Services** page, select **Create**.

    ![Screenshot of the App Services page in the Azure portal. The Create button in the action bar is highlighted.](media/quickstart-ruby/azure-portal-create-app-service.png)

1. Fill out the **Create Web App** page as follows.
  - **Resource Group**: Create a resource group named _myResourceGroup_.
  - **Name**: Type a globally unique name for your web app. 
  - **Publish**: Select _Code_.
  - **Runtime stack**: Select _Ruby 2.7_. 
  - **Operating system**: Select _Linux_.
  - **Region**: Select an Azure region close to you.
  - **App Service Plan**: Create an app service plan named _myAppServicePlan_.

2.  To change to the Free tier, next to **Sku and size**, select **Change size**. 
   
3.  In the Spec Picker, select **Dev/Test** tab, select **F1**, and select the **Apply** button at the bottom of the page.

    ![Screenshot of the Spec Picker for the App Service Plan pricing tiers in the Azure portal. Dev/Test, F1, and Apply are highlighted.](media/quickstart-ruby/azure-portal-create-app-service-select-free-tier.png)   

4. Select the **Review + create** button at the bottom of the page.

    ![Screenshot of the Create Web App screen. The Review + create button is highlighted.](media/quickstart-ruby/azure-portal-create-app-service-review-create.png)   

5. After validation runs, select the **Create** button at the bottom of the page. This will create an Azure resource group, app service plan, and app service.

6. After the Azure resources are created, select **Go to resource**.

7. From the left navigation, select **Deployment Center**.

    ![Screenshot of the App Service in the Azure Portal. The Deployment Center option in the Deployment section of the left navigation is highlighted.](media/quickstart-ruby/azure-portal-configure-app-service-deployment-center.png)  

8. Under **Settings**, select a **Source**. For this quickstart, select _GitHub_.

9. In the section under **GitHub**, select the following settings:
    - Organization: Select your organization.
    - Repository: Select _ruby-docs-hello-world_.
    - Branch: Select the default branch for your repository.

10. Select **Save**.

    ![Screenshot of the Deployment Center for the App Service, focusing on the GitHub integration settings. The Save button in the action bar is highlighted.](media/quickstart-ruby/azure-portal-configure-app-service-github-integration.png)  

    > [!TIP]
    > This quickstart uses GitHub. Additional continuous deployment sources include Bitbucket, Local Git, Azure Repos, and External Git. FTPS is also a supported deployment method.

11. Once the GitHub integration is saved, from the left navigation of your app, select **Overview** > **URL**. 

12. On the Overview, select the link under **URL**.

    ![Screenshot of the App Service resource's overview with the URL highlighted.](media/quickstart-ruby/azure-portal-app-service-url.png)  

---

The Ruby sample code is running in an Azure App Service Linux web app.

![Screenshot of the sample app running in Azure, showing 'Hello from Azure App Service on Linux!'.](media/quickstart-ruby/ruby-hello-world-in-browser.png)

**Congratulations!** You've deployed your first Ruby app to App Service using the Azure portal.

## 3 - Update and redeploy the app

### [Azure CLI](#tab/cli)

1. From Azure Cloud Shell, launch a text editor - such as `nano` or `vim` - to edit the file in `app/controllers/application_controller.rb`.
   
    ```bash
    nano app/controllers/application_controller.rb
    ```

1. Edit the _ApplicationController_ class so that it shows "Hello world from Azure App Service on Linux!" instead of "Hello from Azure App Service on Linux!".

    ```ruby
    class ApplicationController < ActionController::Base
        def hello
            render html: "Hello world from Azure App Service on Linux!"
        end
    end
    ```

1. Save and close the file.

1. Commit the change to Git with the following commands:

    ```bash
    git add .
    git commit -m "Hello world"
    git push azure main
    ```

> [!NOTE]
> Make sure your `git push` statement includes `azure main` so that you are pushing directly to the Azure Git deployment URL.
> You may also need to set your `user.name` and `user.email` Git config values when you commit the changes. You can do that with `git config user.name "YOUR NAME"` and `git config user.email "YOUR EMAIL"`.


### [Portal](#tab/portal)

1. Browse to your GitHub fork of ruby-docs-hello-world.

1. On your repo page, press `.` to start Visual Studio code within your browser.

    > [!NOTE]
    > The URL will change from GitHub.com to GitHub.dev. This feature only works with repos that have files. This does not work on empty repos.

1. Navigate to **app/controllers/application_controller.rb**.

    ![Screenshot of Visual Studio Code in the browser, highlighting app/controllers/application_controller.rb in the Explorer pane.](media/quickstart-ruby/visual-studio-code-in-browser-navigate-to-application-controller.png)

1. Edit the _ApplicationController_ class so that it shows "Hello world from Azure App Service on Linux!" instead of "Hello from Azure App Service on Linux!".

    ```ruby
    class ApplicationController < ActionController::Base
        def hello
            render html: "Hello world from Azure App Service on Linux!"
        end
    end
    ```

1. From the **Source Control** menu, select the **Stage Changes** button to stage the change.

    ![Screenshot of Visual Studio Code in the browser, highlighting the Source Control navigation in the sidebar, then highlighting the Stage Changes button in the Source Control panel.](media/quickstart-ruby/visual-studio-code-in-browser-stage-changes.png)

1. Enter a commit message such as `Hello world`. Then, select Commit and Push.

    ![Screenshot of Visual Studio Code in the browser, Source Control panel with a commit message of 'Hello Azure' and the Commit and Push button highlighted.](media/quickstart-ruby/visual-studio-code-in-browser-commit-push.png)

1. Once deployment has completed, return to the browser window that opened during the **Browse to the app** step, and refresh the page.

    ![Screenshot of the updated sample app running in Azure, showing 'Hello world from Azure App Service on Linux!'.](media/quickstart-ruby/ruby-hello-world-updated-in-browser.png)

---

## 4 - Manage your new Azure app

1. Go to the Azure portal to manage the web app you created. Search for and select **App Services**.

    ![Screenshot of the Azure portal with 'app services' typed in the search text box. In the results, the App Services option under Services is highlighted.](media/quickstart-ruby/azure-portal-search-for-app-services.png)    

1. Select the name of your Azure app.

    ![Screenshot of the App Services list in Azure. The name of the demo app service is highlighted.](media/quickstart-ruby/app-service-list.png)

Your web app's **Overview** page will be displayed. Here, you can perform basic management tasks like **Browse**, **Stop**, **Restart**, and **Delete**.

## 5 - Clean up resources

### [Azure CLI](#tab/cli)

[!INCLUDE [Clean-up section](../../includes/cli-script-clean-up.md)]

### [Portal](#tab/portal)

1. From your App Service **Overview** page, select the resource group you created.

1. From the resource group page, select **Delete resource group**. Confirm the name of the resource group to finish deleting the resources.

---

## Next steps

> [!div class="nextstepaction"]
> [Tutorial: Ruby on Rails with Postgres](tutorial-ruby-postgres-app.md)

> [!div class="nextstepaction"]
> [Configure Ruby app](configure-language-ruby.md)
