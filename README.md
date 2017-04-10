
<h1> <Center> Talend Developer Guide </Center> </h1>
This Developer Guide will introduce you how to use Talend Open Studio for ESB to develop Data Integration, develop and deploy Enterprise Service Bus and finally manage and monitor the deployed services.
#Table of Contents#
1. [Eviroment Setup](#TOS-evn-01)
1. [Data Integration](#TOS-Job-01)</br>
	2.1. [Job Data Flow](#TOS-Job-02)</br>
	2.2. [Write your own Job](#TOS-Job-03)</br>
	2.3. [Execute and Debug](#TOS-Job-04)
2. [Enterprise Service Bus](#TOC-ESB1)</br>
	3.1. [Routes](#TOC-ESB2)</br>
	3.1.1. [ActiveMQ Integration](#TOC-ESB3)</br>
	3.2. [Service Development](#TOC-ESB4)</br>
	3.2.1. [SOAP CRM service](#TOC-ESB5)</br>
	3.2.2. [REST Customers service](#TOC-ESB6)</br>
	3.3. [Service deployment](#TOC-ESB7)</br>
	3.3.1 [How to deploy service using ESBRuntime Container](#TOC-ESB8)</br>
	3.3.2. [deploy service using apache tomcat](#TOC-ESB9)
3. [Management and Monitoring](#TOC-ESB10)</br>
	4.1.1. [Hawtio](#TOC-ESB11)

<a id="TOS-evn-01"></a>
# EnvironmentSetup #
<a id="TOS-evn-02"></a>
## <a id="TOS-evn-02">JDK installation</a> ##
- Download Java SE Development Kit 8 from [Oracle Website](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
- Install the downloaded package into local directory. The installation path must NOT contain white space.
- Configure path for JAVA_HOME.
<a id="TOS-evn-03"></a>
## Talend Package Installation ##
- Download Talend Open Studio for ESB [here](https://www.talend.com/download/talend-open-studio/#t3)
- Extract the downloaded package into your local directory. Ex: *E:\Talend\01.DevTools*
- Go into folder Studio [Ex: *E:\Talend\01.DevTools\Studio*] and run [TOS_ESB-win-x86_64.exe] in case of Win64bit or [TOS_ESB-win32-x86] in case of Win32bit to start Talend Open Studio for ESB.

[![Initial View of Talend Open Studio](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Env-1.jpg)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Env-1.jpg)

<center>*Initial View of Talend Open Studio for ESB*</center>
<a id="TOS-Job-01"></a>
# Data Integration #
In this part, we will use Initial Data Load module to introduce how to implement Data Integration using Talend Open Studio.
<a id="TOS-Job-02"></a>
## Job Data Flow ##
Talend Open Studio allow you to develop job to implement Data Integration. The purpose of a job is to parse an input file (ex: csv file, xml file, excel file...), then perform validate the raw data, filter the invalid data and finally send the  valid data to the server. In order to understand the flow clearly, we will analyze the existing project INITIAL\_DATA\_LOAD.
### Import existing project ###
At the initial screen of Talend Open Studio, select option **Import an existing project**, click **Finish** and then browse to the INITIAL\_DATA\_LOAD] project (Please extract [INITIAL\_DATA\_LOAD.zip] in directory ../sources).

[![Import Project View](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Env-2.jpg)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Env-2.jpg)

After import successfully, the development view will be displayed as below:
<center>![Image 3](./images/TOS-Job-01.jpg "IDE view")</center>
### Understand Job Data Flow ###
On the **Repository**, open ProductJob 0.1. The design of ProjectJob 0.1 will be displayed.
<center>![Image 4](./images/TOS-Job-02.jpg "Design of a Job")</center>
1. **parseInputCSVfile**: this component parses the input csv file. </br>
2. **Default value and Validation**: At this component, each csv record will be validated the data definition such as data length, data format, data type.etc... The invalid data will be marked for filtering in the next step. Default value is also assigned to the record at this step.  </br>
3. **Filter flag**: At this step, the valid data will be transferred to the *Reference check* step. The invalid data will be moved to *Log error* component.<br/>
4. **References check**: This step makes sure the input data having the valid reference. For example, when importing a product, the price list must be existed. The data having invalid reference will be marked. <br/>
5. **Filter data**: The data with invalid reference will go to step *Log error*. The valid data will go to the final step *Finish*. </br>
6. **Finish**: At this component, all the valid data will be send to ERP server for importing purpose. <br/>
7. **Log Error**: this component at step 7 and step 8 will log the invalid data.
<a id="TOS-Job-03"></a>
## Write your own Job ##
This part will guide you steps to write your own simple job. To implement the job, you need the following palette *tFileInputDelimited*, *tJavaRow*, *tFilterRow*. 

**Step 1**: At the **Repository**, Right Click on **Job Design** -> **Create Job**. Input the information for the new Job. Example: *name = ProductSampleJob*. <br/>

**Step 2**: Using the palette to draw the design as the following image. <br/>
<center>![Image 5](../assets/images/TOS-Job-03.jpg "Job Creation")</center>

**Step 3**: Select the *tFileInputDelimited* on the design, then select the **Component** tab: At the **File name/Stream** combobox, browse to the csv file. Modify **CSV Row Separator**, **File Separator** as in the above image. <br/>
**Step 4**: Select item **rawData(Main)**, at the tab **Component**, click **Edit Schema**. <br/>
<center>![Image 6](../assets/images/TOS-Job-04.jpg "Sample Product Job Schema Selection")</center>

Next, click **Import schema** -> **OK** <br/>
<center>![Image 7](../assets/images/TOS-Job-06.jpg "Import schema")</center>
**Step 5**: Select tJavaRow **CheckDefinition**, at the tab **Component**, click **Edit Schema** -> click icon *Copy all of the columns from the input schema to the output schema* -> **OK**. Input the validation code to the **Code** textbox. <br/>
<center>![Image 8](../assets/images/TOS-Job-05.jpg "Edit schema")</center>

**Edit Schema** -> click icon *Copy all of the columns from the input schema to the output schema* -> **OK**.
<center>![Image 9](../assets/images/TOS-Job-07.jpg "Copy schema")</center>

**Step 6**: Slect tfilterRow **filterFlag**, at the tab **Component** input the *Condition* to filter at the table **Condition** <br/>
<center>![Image 10](../assets/images/TOS-Job-08.jpg "Filter component")</center>

**Step 7**: Write your code to log invalid data into the code textbox of the **Component**  of the component *LogRejectProduct* and write your code to process valid data into the code textbox of the **Component** tab of component *LogvalidatedProducts*.
<center>![Image 11](../assets/images/TOS-Job-09.jpg "Code to log result")</center>
Job implementation
<a id="TOS-Job-04"></a>
### Execution and Debug ###
#### Run ####
On the tab **Run (Job ProductSampleJob)**, click **Run** to execute the job.
<center>![Image 12](../assets/images/TOS-Job-11.jpg "Run job")</center>
#### Debug ####
Right click on the component that you want to debug, select **Add breakpoint**.
On the tab ** Run (Job ProductSampleJob)**, select sub-tab **Debug Run**. On the dropbox Debug: <br/>

- Select **Trace Debug** if you want to run Debug in Trace mode. <br/>
<center>![Image 13](../assets/images/TOS-Job-13.jpg "Trace Debug")</center>

- Select **Java Debug** if you want to run in Java debug mode. <br/>
<center>![Image 14](../assets/images/TOS-Job-12.jpg "Java Debug")</center>
<a id="TOC-ESB1"></a>
# Enterprise Service Bus #
<a id="TOC-ESB2"></a>
## Routes ##
<a id="TOC-ESB3"></a>
### ActiveMQ Integration ###
Step 1: Start ActiveMQ server
<center> ![TOS-ESB-16](../assets/images/TOS-ESB-16.jpg)</br></center>
Step 2: Run MQMultiReceiver job and MQMultiSender in Talend
<center> ![TOS-ESB-17](../assets/images/TOS-ESB-17.jpg)</br></center>
The result id outputted on the console.
<center> ![TOS-ESB-18](../assets/images/TOS-ESB-18.jpg)</br></center>
<a id="TOC-ESB4"></a>
## Service Development ##
<a id="TOC-ESB5"></a>
### SOAP CRM Service###
Step 1: Create service using talend open studio
<center> ![TOS-ESB-19](../assets/images/TOS-ESB-19.jpg)</br></center>
Step 2: Create Job to expose SOAP
<center> ![TOS-ESB-20](../assets/images/TOS-ESB-20.jpg)</br></center>
Step 3: Run job by click run button on Run tab
<center> ![TOS-ESB-21](../assets/images/TOS-ESB-21.jpg)</br></center>
Step 4: Test SOAP using boomerang
Test case 1: type http://localhost:8090/services/CRMServiceProvider in boomerang and enter id equals 1.
<center> ![TOS-ESB-22](../assets/images/TOS-ESB-22.jpg)</br></center>
Test case 2: type http://localhost:8090/services/CRMServiceProvider in boomerang and enter id equals 2.
<center> ![TOS-ESB-23](../assets/images/TOS-ESB-23.jpg)</br></center>
Test case 3: Start getCRMStatus function by click run button on run tab and type http://localhost:8090/services/CRMServiceProvider in boomerang and enter id equals 1.
<center> ![TOS-ESB-24](../assets/images/TOS-ESB-24.jpg)</br></center>
Test case 4: Start getCRMStatus function by click run button on run tab and type http://localhost:8090/services/CRMServiceProvider in boomerang and enter id equals 2.
<center> ![TOS-ESB-25](../assets/images/TOS-ESB-25.jpg)</br></center>
Test case 5: Start updateCRMStatus function by click run button on run tab and type http://localhost:8090/services/CRMServiceProvider in boomerang and update id equals 1.
<center> ![TOS-ESB-26](../assets/images/TOS-ESB-26.jpg)</br></center>
The result is outputted on the console.
<a id="TOC-ESB6"></a>
### REST Customer service###
The REST API will be implemented in talend as the following
<center> ![TOS-ESB-9](../assets/images/TOS-ESB-9.jpg)</br></center>
Step 1: Create data to runing REST API. select tFixedFieldInput_1->create data in component tab.
<center> ![TOS-ESB-10](../assets/images/TOS-ESB-10.jpg)</br></center>
Step 2: Create request for REST API. select tRESTRequest_1->create request in component tab
<center> ![TOS-ESB-11](../assets/images/TOS-ESB-11.jpg)</br></center>
Step 3: The Test is implemented on talend (Select Run(job DemoREST)-> Run) and Advanced REST client for chrome. </br>
<center> ![TOS-ESB-12](../assets/images/TOS-ESB-12.jpg)</br></center>
Test case 1: type http://localhost:8090/services/customers in Advanced REST client for chrome.
<center> ![TOS-ESB-13](../assets/images/TOS-ESB-13.jpg)</br></center>
Test case 2: type http://localhost:8090/services/customers/1 in Advanced REST client for chrome.
<center> ![TOS-ESB-14](../assets/images/TOS-ESB-14.jpg)</br></center>
Test case 3: type http://localhost:8090/services/customers/2 in Advanced REST client for chrome.
<center> ![TOS-ESB-15](../assets/images/TOS-ESB-15.jpg)</br></center>
<a id="TOC-ESB7"></a>
## Service deployment ##
<a id="TOC-ESB8"></a>
### How to deploy service using ESBRuntime Container ###
We are ready to deploy Talend ESB RESTful Service DemoREST-0.1 job to Talend Runtime environment for that we must build job from studio as OSGI Bundle for ESB
<center> ![TOS-ESB-2](../assets/images/TOS-ESB-2.jpg)</br></center>
Select archive file path, Build type as OSGI Bundle For ESB and click Finish
<center> ![TOS-ESB-3](../assets/images/TOS-ESB-3.jpg)</br></center>
exported file must be copied in Runtime_ESBSE/container/deploy folder
<center> ![TOS-ESB-4](../assets/images/TOS-ESB-4.jpg)</br></center>
On Runtime container use list command to check status of your service.
<center> ![TOS-ESB-5](../assets/images/TOS-ESB-5.jpg)</br></center>
Now we are able to deploy ESB job as service in Talend Runtime container using apache karaf but to make it run as a operating system service we must install Karaf Wrapper feature.</br>
**Installing the wrapper**</br>
Step 1: Browse to the container/bin folder of the Talend Runtime installation directory, then launch the container by executing the trun file as a root user.</br>
Step 2: To install the wrapper feature, simply type:
<center> ![TOS-ESB-6](../assets/images/TOS-ESB-6.jpg)</br></center>
Once installed, wrapper feature will provide wrapper:install new command in the trun, which allows you to install Talend Runtime as a service.
Step 3: To register the container as a service in automatic start mode, simply type:
<center> ![TOS-ESB-7](../assets/images/TOS-ESB-7.jpg)</br></center>
Now, We will test service from chrome brower.
Step 1: To test the REST service, type http://10.10.10.1:8040/services/customers (you can replace 10.10.10.1:8040 by your deploy machine) in chrome brower.
<center> ![TOS-ESB-8](../assets/images/TOS-ESB-8.jpg)</br></center>
<a id="TOC-ESB9"></a>
### Deploy service using apache tomcat (**TODO**) ###
<a id="TOC-ESB10"></a>
# Management and Monitoring (**TODO**) #
<a id="TOC-ESB11"></a>
## Hawtio (**TODO**) ##
