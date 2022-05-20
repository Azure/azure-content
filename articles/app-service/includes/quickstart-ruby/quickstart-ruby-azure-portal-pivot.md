[Azure App Service](../../overview.md) provides a highly scalable, self-patching web hosting service. This quickstart tutorial shows how to deploy a Ruby app to Azure App Service on Linux using the Azure portal.

This quickstart configures an App Service app in the **Free** tier and incurs no cost for your Azure subscription.

This quickstart shows you how to make these changes within your browser, without having to install the development environment tools on your machine.

![Sample app running in Azure](../../media/quickstart-ruby/ruby-hello-world-in-browser.png)

You can follow the steps here using a Mac, Windows, or Linux machine. Once the prerequisites are installed, it takes about five minutes to complete the steps.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]

## Prerequisites

- Have an Azure account with an active subscription. [Create an account for free](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-app-service-extension&mktingSource=vscode-tutorial-app-service-extension).
- Have a GitHub account to fork a repository.

## Fork the sample repository

1. In your browser, navigate to the repository containing [the sample code](https://github.com/Azure-Samples/ruby-docs-hello-world).

2. In the upper right corner, select **Fork**.

    ![In GitHub, in the Azure-Samples/ruby-docs-hello-world repo, select Fork.](../../media/quickstart-ruby/fork-ruby-docs-hello-world-repo.png)

3. On the **Create a new fork** screen, confirm the **Owner** and **Repository name** fields. Select **Create fork**.

    ![In GitHub, in the Azure-Samples/ruby-docs-hello-world repo, on the Create a new fork screen, confirm the details for the Owner and Repository name fields.](../../media/quickstart-ruby/fork-details-ruby-docs-hello-world-repo.png)

    >[!NOTE]
    > This should take you to the new fork. Your fork URL will look something like this: https://github.com/YOUR_GITHUB_ACCOUNT_NAME/ruby-docs-hello-world

### Update the fork's default branch

1. From the fork repository, select **Settings**.

 ![In GitHub, in the forked ruby-docs-hello-world repo, select Settings.](../../media/quickstart-ruby/forked-ruby-docs-hello-world-repo-settings.png)

2. Under **Code and automation**, select **Branches**.

 ![In GitHub, in the forked ruby-docs-hello-world repo's Settings, select Branches.](../../media/quickstart-ruby/forked-ruby-docs-hello-world-repo-settings-branches.png)

3. Under **Default branch**, to the right of `master`, select the **Rename branch** button represented by a pencil icon.

 ![On the Branches for the forked ruby-docs-hello-world repo, select the Rename branch button after master.](../../media/quickstart-ruby/forked-ruby-docs-hello-world-repo-settings-branches-edit.png)

4. In the **Rename this branch** pop-up, enter `main`.

 ![In the Rename this branch dialog, enter 'main'.](../../media/quickstart-ruby/forked-ruby-docs-hello-world-repo-rename-this-branch.png)

5. Select **Rename branch**.

> [!TIP]
> The branch name change isn't required by App Service. However, since many repositories are changing their default branch to `main`, this quickstart also shows you how to deploy a repository from `main`.

## Deploy to Azure

### Sign in to Azure portal

Sign in to the Azure portal at https://portal.azure.com.

### Create Azure resources

1. Type **app services** in the search. Under **Services**, select **App Services**.

 ![In the Azure portal, search for app services. Then select App Services.](../../media/quickstart-ruby/azure-portal-search-for-app-services.png)


2. In the **App Services** page, select **Create**.

    ![In the Azure portal, on the App Services page, select Create from the actions bar.](../../media/quickstart-ruby/azure-portal-create-app-service.png)


3. In the **Basics** tab, under **Project details**, ensure the correct subscription is selected and then select to **Create new** resource group. Type *myResourceGroup* for the name.

    ![On the Create Web App screen, on the Basics tab, in the Project Details section, for Resource group, select the 'Create new' link and add a group named 'myResourceGroup'.](../../media/quickstart-ruby/azure-portal-create-app-service-create-resource-group.png)

4. Under **Instance details**, set the following settings:
   - **Name**: Type a globally unique name for your web app. 
   - **Publish**: Select *Code*.
   - **Runtime stack**: Select *Ruby 2.7*. 
   - **Operating system**: Select *Linux*.
   - **Region**: Select the region you want to serve your app from.

    ![On the Create Web App screen, on the Basics tab, in the Instance Details section, use a globally unique name. Select the options for Publish, Runtime stack, Operating system, and region.](../../media/quickstart-ruby/azure-portal-create-app-service-instance-details.png)

5. Under **App Service Plan**, select **Create new** App Service Plan. Type *myAppServicePlan* for the name. Then select **OK**.

    ![On the Create Web App screen, on the Basics tab, in the App Service Plan section, select the 'Create new' link. Enter myAppServicePlan in the textbox.](../../media/quickstart-ruby/azure-portal-create-app-service-create-app-service-plan.png)   

6.  To change to the Free tier, next to **Sku and size**, select **Change size**. 
   
7.  In the Spec Picker, select **Dev/Test** tab, select **F1**, and select the **Apply** button at the bottom of the page.

    ![In the Spec Picker, select Dev/Test, then select F1. Finally select the Apply.](../../media/quickstart-ruby/azure-portal-create-app-service-select-free-tier.png)   

8. Select the **Review + create** button at the bottom of the page.

    ![At the bottom of the Create Web App screen, select 'Review + create'.](../../media/quickstart-ruby/azure-portal-create-app-service-review-create.png)   

9. After validation runs, select the **Create** button at the bottom of the page.

10. After deployment is complete, select **Go to resource**.

### Set up continuous deployment

This step will set up continuous deployment using GitHub actions.

1. In the Azure portal, navigate to the app service.
   
2. From the navigation, select **Deployment Center**.

    ![On the App Service resource, under the Deployment section of navigation, select Deployment Center.](../../media/quickstart-ruby/azure-portal-configure-app-service-deployment-center.png)  

3. Under **Settings**, select a **Source**. For this quickstart, select `GitHub`.

4. In the section under **GitHub**, select the following settings:
    - Organization: Select your organization.
    - Repository: Select `ruby-docs-hello-world`.
    - Branch: Select `main`.

5. Select **Save**.

    ![Verify organization, repository, and branch settings. Then select Save from the actions bar.](../../media/quickstart-ruby/azure-portal-configure-app-service-github-integration.png)  

> [!TIP]
> This quickstart uses GitHub. Additional continuous deployment sources include Bitbucket, Local Git, Azure Repos, and External Git. FTPS is also a supported deployment method.
  
### Browse to the app

Browse to the deployed application using your web browser. 

1. From the Deployment Center screen, navigate to **Overview**.

    ![On the App Service resource, on the Deployment Center, Overview is highlighted in the navigation window.](../../media/quickstart-ruby/azure-portal-navigate-to-overview-from-deployment-center.png)  

2. On the Overview, select the link under **URL**.

    ![On the App Service resource, on the Overview, the URL is highlighted.](../../media/quickstart-ruby/azure-portal-app-service-url.png)  

The Ruby sample code is running in an Azure App Service Linux web app.

![Sample app running in Azure](../../media/quickstart-ruby/ruby-hello-world-in-browser.png)

**Congratulations!** You've deployed your first Ruby app to App Service using the Azure portal.

## Update in GitHub and redeploy the code

1. Browse to your GitHub fork of ruby-docs-hello-world.

2. On your repo page, press `.` to start Visual Studio code within your browser.

    > [!NOTE]
    > The URL will change from GitHub.com to GitHub.dev. This feature only works with repos that have files. This does not work on empty repos.

3. Navigate to **app/controllers/application_controller.rb**.

![Visual Studio Code in the browser, highlighting app/controllers/application_controller.rb in the Explorer pane](../../media/quickstart-ruby/vscode-in-browser-navigate-to-application-controller.png)

4. Edit the *ApplicationController* class so that it shows "Hello world from Azure App Service on Linux!" instead of "Hello from Azure App Service on Linux!"

    ```ruby
    class ApplicationController < ActionController::Base
        def hello
            render html: "Hello world from Azure App Service on Linux!"
        end
    end
    ```

5. From the **Source Control** menu, select the **Stage Changes** button to stage the change.

    ![Visual Studio Code in the browser, highlighting the Source Control navigation in the sidebar, then highlighting the Stage Changes button in the Source Control panel.](../../media/quickstart-ruby/vscode-in-browser-stage-changes.png)

6. Enter a commit message such as `"Hello world"`. Then, select Commit and Push.

    ![Visual Studio Code in the browser, Source Control panel with a commit message of 'Hello world' and the Commit and Push button highlighted ](../../media/quickstart-ruby/vscode-in-browser-commit-push.png)

7. Once deployment has completed, return to the browser window that opened during the **Browse to the app** step, and refresh the page.

![Updated sample app running in Azure](../../media/quickstart-ruby/ruby-hello-world-updated-in-browser.png)

## Manage your new Azure app

1. Go to the <a href="https://portal.azure.com" target="_blank">Azure portal</a> to manage the web app you created. Search for and select **App Services**.

    ![In the Azure portal, search for app services. Then select App Services.](../../media/quickstart-ruby/azure-portal-search-for-app-services.png)    

2. Select the name of your Azure app.

    ![Portal navigation to Azure app](../../media/quickstart-ruby/ruby-docs-hello-world-app-service-list.png)

Your web app's **Overview** page will be displayed. Here, you can perform basic management tasks like **Browse**, **Stop**, **Restart**, and **Delete**.

![App Service page in Azure portal](../../media/quickstart-ruby/ruby-docs-hello-world-app-service-detail.png)

The web app menu provides different options for configuring your app.

## Clean up resources

When you're finished with the sample app, you can remove all of the resources for the app from Azure. It will not incur extra charges and keep your Azure subscription uncluttered. Removing the resource group also removes all resources in the resource group and is the fastest way to remove all Azure resources for your app.

1. From your App Service **Overview** page, select the resource group you created in the [Create Azure resources](#create-azure-resources) step.

2. From the resource group page, select **Delete resource group**. Confirm the name of the resource group to finish deleting the resources.
