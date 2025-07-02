# USER GUIDE FOR EZDRP LINUXPOLSKA

# Table of Contents
* [Prerequisites](#prerequisites)
   * [K8S platform -Rancher](#k8s-platform--rancher)
   * [Create project in rancher.](#create-project-in-rancher)
   * [Create work space in rancher.](#create-work-space-in-rancher)
   * [Adding repository with charts to Rancher](#adding-repository-with-charts-to-rancher)
* [Installation applications](#installation-applications)
   * [EZDRP BACKEND-CRD](#ezdrp-backend-crd)
   * [EZDRP BACKEND-DB](#ezdrp-backend-db)
   * [EZD FRONTEND](#ezd-frontend)

# Prerequisites
----------------------

## K8S platform -Rancher

1. Login to Rancher 

![login_screen](docs/images/login_screen.png)

2. Choose desired cluster/context.

## Create project in rancher.

1. This can be done by pressing create:

![project_1](docs/images/project_1.png)

2. Please fill in all necessary informations:

![project_2](docs/images/project_2.png)

3. If you would like to parameterize project please go thru settings:
![project_3](docs/images/project_3.png)
![project_4](docs/images/project_4.png)
![project_5](docs/images/project_5.png)

4. When everything will be ready press create.

## Create namespace space in rancher.

1. This can be done by pressing create:

![namespace_1](docs/images/namespace_1.png)

2. Please fill in a name field for instance `ezd-rp`:

![namespace_2](docs/images/namespace_2.png)
![namespace_3](docs/images/namespace_3.png)

3. When everything will be ready press create.

## Adding repository with charts to Rancher

1. After choosing cluster/context on left hand side you will have repositories:

![menu_apps](docs/images/menu_apps.png)

2. In menu of repositories you will have possibility to add new one by pressing Create:

![repository_add](docs/images/repository_add.png)

3. On the next screen you will have more detailed option of adding repository to tanchech. Please provide either git repository with helm charts or url to and index or cluster template definition:

![repository_add_1](docs/images/repository_add_1.png)

In this case please add following repositories:

* ezd-lp: https://linuxpolska.github.io/ezd-rp
* ezd-nask: https://hub.eadministracja.nask.pl/chartrepo/ezdrp 

4. When finished press create. 


# Installation applications
----------------------

## EZDRP CRDs and operators (install once per cluster)

1. Choose from left side menu Apps/Charts like on below screen:

You will be provided with Charts(please use filters to pick linuxpolska EZD RP, and EZD to choose NASK application)

2. Choose `EZD RP Backend (1/2) - Operators` chart.

![apps_chart](docs/images/chart_crd.png)

3. On next screen you will get instructions regarding requirments and instruction how to manually install and uninstall chart(Chart Info). Components version and requirments:

![lp_crd](docs/images/lp_crd.png)

Press **install** to proceed.

4. Next screens are giving you option to customize helm options before installation.

![lp_crd_1](docs/images/lp_crd_1.png)

![lp_crd_2](docs/images/lp_crd_2.png)

![lp_crd_3](docs/images/lp_crd_3.png)

Press **install** when customization is finished.

5. If installation succesed, you should see:

![lp_crd_4](docs/images/lp_crd_4.png)

## EZDRP BACKEND-DB

1. Choose from left side menu Apps/Charts like on below screen:

You will be provided with Charts(please use filters to pick linuxpolska EZD RP, and EZD to choose NASK application)

2. Choose `LP Backend for EZD RP` chart.

![apps_chart](docs/images/chart_backend.png)

3. On next screen you will get instructions regarding requirments and instruction how to manually install and uninstall chart(Chart Info). Components version and requirments:

![lp_backend](docs/images/lp_backend.png)

Press **install** to proceed.

4. Next screen is allowing you to choose desired namespace and it is giving you option to customize helm options before installation.

![lp_backend_1](docs/images/lp_backend_1.png)

5. If you pick to customize, you can choose which components you want to install and you need to provide details:

![lp_backend_2](docs/images/lp_backend_2.png)

6. Please provode passwords and storage classes in each group:

![lp_backend_3_1](docs/images/lp_backend_3.1.png)
![lp_backend_3_2](docs/images/lp_backend_3.2.png)
![lp_backend_3_3](docs/images/lp_backend_3.3.png)
![lp_backend_3_4](docs/images/lp_backend_3.4.png)

Press **install**.

8. On the last screen you will see progress in CLi. When installation will be completed you will be provided with all necessary infromations to install NASK frontend application (save it):

![lp_backend_5](docs/images/lp_backend_5.png)


## EZD FRONTEND

1. Choose from left side menu Apps/Charts like on below screen:

You will be provided with Charts (please use filters to pick linuxpolska EZD RP, and EZD to choose NASK application)

2. Choose **nask-ezdrp-ha**.

![apps_chart](docs/images/chart_frontend.png)

3. On next screen you will get instructions regarding requirments and information regarding chart (Chart Info).

![nask_installation](docs/images/nask_installation.png)

Choose desired version on the right side of the page (please mind that version 1.17.11 and above requires ReadWriteMany access which means it not work on vsphere-csi-rwo storageclass)

Press **install**.

4. Next page will allow you to choose namespace and unique name for installation of application.

Press **next**.

![nask_installation_1](docs/images/nask_installation_1.png)

5. Please provide all neccesary values for installation NASK application (use values from NOTES printed after *EZD RP Backend (2/2) Apps* installation)
   
   1. Group: **Domain info**

![nask_installation_2](docs/images/nask_installation_2.png)

   2. Group: **Persistance Storage**

![nask_installation_3](docs/images/nask_installation_3.png)

   3. Group: **Database Configuration**

![nask_installation_4](docs/images/nask_installation_4.png)

   Here is an example:

![nask_installation_4](docs/images/nask_installation_4.1.png)

   4. Group: **Redis Database Configuration**

![nask_installation_5](docs/images/nask_installation_5.png)

   Here is an example:

![nask_installation_5](docs/images/nask_installation_5.1.png)

   5. Group: **Redis-append Database Configuration**

![nask_installation_6](docs/images/nask_installation_6.png)

   Here is an example:

![nask_installation_6](docs/images/nask_installation_6.1.png)

   6. Group: **Rabbit Database Configuration**

![nask_installation_7](docs/images/nask_installation_7.png)

   Here is an example:

![nask_installation_7](docs/images/nask_installation_7.1.png)

   7. Group: **Email Configuration**

![nask_installation_8](docs/images/nask_installation_8.png)

7. Press **install**.

8. After when deployment will be completed you will be provided with website URL that you can access to start using NASK EZD-RP application.

![nask_installation_9](docs/images/nask_installation_9.png)
