## Create Redshift Cluster through IaC (Infrastructure as Code)
###### Version 1.0 - November 2020

#### 1.0 Getting Started - What is IaC?

<p>Infrastructure as code (IaC) is the process of managing and provisioning computer data centers through machine-readable definition files, rather than physical hardware configuration or interactive configuration tools. The IT infrastructure managed by this process comprises both physical equipment, such as bare-metal servers, as well as virtual machines, and associated configuration resources. The definitions may be in a version control system. It can use either scripts or declarative definitions, rather than manual processes, but the term is more often used to promote declarative approaches. (Wikipedia: https://en.wikipedia.org/wiki/Infrastructure_as_code).</p>

<p>Now that we have the technical definition out of the way, in short: IaC allows people to spin up, pause, and delete machines to process a targeted set of operations without having to use the command line interface (CLI). This allows data engineers to save time, increase consistency, and even bid out use of elastic infrastructure... utimately saving money.</p>

#### 2.0 Files needed to Launch

###### 2.1 `dwh.cfg`

<p> The "dwh.cfg` file holds configurations information for setting up the cluster. Largely made up of credentials and settings, this file holds information that is used across systems and files to authenticate and direct the flow of information. More details can be found in the "Details of Code within Files" section.</p>

###### 2.2 `create_cluster.py`

<p> The 'create_cluster.py' file is the main file used to create the redshift cluster. This file contains the main portions of IaC that will establish a connection to redshift services, and create server instances. More details can be found in the "Details of Code within Files" section.</p>

###### 2.3 `delete_cluster.py`

<p> The 'delete_cluster.py` file is used to delete redshift instances after they have been used to perform a business action. It is important to shutdown servers and clusters that are not in use, particularly because users are charge for the amount of time and information being processed. More details can be found in the "Details of Code within Files" section.</p>

#### 3.0 Details of Code within Files

###### 3.1 `dwh.cfg` - Configuration File

###### 3.2 `create_cluster.py` - Launch Cluster File

#### 4.0 Executing the Files
