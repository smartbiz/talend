
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

[![IDE view](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-01.jpg)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-01.jpg)

### Understand Job Data Flow ###
On the **Repository**, open ProductJob 0.1. The design of ProjectJob 0.1 will be displayed.

[![Design of a Job](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-02.jpg)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-02.jpg)

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

[![Job Creation](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-03.jpg)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-03.jpg)

**Step 3**: Select the *tFileInputDelimited* on the design, then select the **Component** tab: At the **File name/Stream** combobox, browse to the csv file. Modify **CSV Row Separator**, **File Separator** as in the above image. <br/>
**Step 4**: Select item **rawData(Main)**, at the tab **Component**, click **Edit Schema**. <br/>

[![Sample Product Job Schema Selection](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-04.jpg)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-04.jpg)

Next, click **Import schema** -> **OK** <br/>
<center>![Image 7](../assets/images/TOS-Job-06.jpg "Import schema")</center>
**Step 5**: Select tJavaRow **CheckDefinition**, at the tab **Component**, click **Edit Schema** -> click icon *Copy all of the columns from the input schema to the output schema* -> **OK**. Input the validation code to the **Code** textbox. <br/>

[![Edit schema](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-05.jpg)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-05.jpg)

**Edit Schema** -> click icon *Copy all of the columns from the input schema to the output schema* -> **OK**.

[![Copy schema](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-07.jpg)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-07.jpg)

**Step 6**: Slect tfilterRow **filterFlag**, at the tab **Component** input the *Condition* to filter at the table **Condition** <br/>

[![Filter component](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-08.jpg)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-08.jpg)

**Step 7**: Write your code to log invalid data into the code textbox of the **Component**  of the component *LogRejectProduct* and write your code to process valid data into the code textbox of the **Component** tab of component *LogvalidatedProducts*.

[![Code to log result](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-09.jpg)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-09.jpg)

Job implementation
<a id="TOS-Job-04"></a>
### Execution and Debug ###
#### Run ####
On the tab **Run (Job ProductSampleJob)**, click **Run** to execute the job.

[![Run job](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-11.jpg)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-11.jpg)

#### Debug ####
Right click on the component that you want to debug, select **Add breakpoint**.
On the tab ** Run (Job ProductSampleJob)**, select sub-tab **Debug Run**. On the dropbox Debug: <br/>

- Select **Trace Debug** if you want to run Debug in Trace mode. <br/>

[![Trace Debug](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-13.jpg)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-13.jpg)

- Select **Java Debug** if you want to run in Java debug mode. <br/>

[![Java Debug](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-12.jpg)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-Job-12.jpg)

<a id="TOC-ESB1"></a>
# Enterprise Service Bus #
<a id="TOC-ESB2"></a>
## Routes ##
<a id="TOC-ESB3"></a>
### ActiveMQ Integration ###
Step 1: Start ActiveMQ server

[![TOS-ESB-16](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-16.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-16.JPG)

Step 2: Run MQMultiReceiver job and MQMultiSender in Talend
[![TOS-ESB-17](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-17.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-17.JPG)

The result id outputted on the console.
[![TOS-ESB-18](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-18.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-18.JPG)

<a id="TOC-ESB4"></a>
## Service Development ##
<a id="TOC-ESB5"></a>
### SOAP CRM Service###
Step 1: Create service using talend open studio
[![TOS-ESB-19](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-19.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-19.JPG)

Step 2: Create Job to expose SOAP

[![TOS-ESB-20](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-20.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-20.JPG)

Step 3: Run job by click run button on Run tab

[![TOS-ESB-21](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-21.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-21.JPG)

Step 4: Test SOAP using boomerang
Test case 1: type http://localhost:8090/services/CRMServiceProvider in boomerang and enter id equals 1.

[![TOS-ESB-22](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-22.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-22.JPG)

Test case 2: type http://localhost:8090/services/CRMServiceProvider in boomerang and enter id equals 2.

[![TOS-ESB-23](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-23.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-23.JPG)

Test case 3: Start getCRMStatus function by click run button on run tab and type http://localhost:8090/services/CRMServiceProvider in boomerang and enter id equals 1.

[![TOS-ESB-24](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-24.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-24.JPG)

Test case 4: Start getCRMStatus function by click run button on run tab and type http://localhost:8090/services/CRMServiceProvider in boomerang and enter id equals 2.

[![TOS-ESB-25](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-25.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-25.JPG)

Test case 5: Start updateCRMStatus function by click run button on run tab and type http://localhost:8090/services/CRMServiceProvider in boomerang and update id equals 1.

[![TOS-ESB-26](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-26.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-26.JPG)

The result is outputted on the console.
<a id="TOC-ESB6"></a>
### REST Customer service###
The REST API will be implemented in talend as the following

[![TOS-ESB-9](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-9.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-9.JPG)

Step 1: Create data to runing REST API. select tFixedFieldInput_1->create data in component tab.

[![TOS-ESB-10](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-10.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-10.JPG)

Step 2: Create request for REST API. select tRESTRequest_1->create request in component tab

[![TOS-ESB-11](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-11.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-11.JPG)

Step 3: The Test is implemented on talend (Select Run(job DemoREST)-> Run) and Advanced REST client for chrome. </br>
[![TOS-ESB-12](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-12.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-12.JPG)

Test case 1: type http://localhost:8090/services/customers in Advanced REST client for chrome.
[![TOS-ESB-13](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-13.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-13.JPG)

Test case 2: type http://localhost:8090/services/customers/1 in Advanced REST client for chrome.

[![TOS-ESB-14](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-14.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-14.JPG)

Test case 3: type http://localhost:8090/services/customers/2 in Advanced REST client for chrome.

[![TOS-ESB-15](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-15.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-15.JPG)
<a id="TOC-ESB7"></a>
## Service deployment ##
<a id="TOC-ESB8"></a>
### How to deploy service using ESBRuntime Container ###
We are ready to deploy Talend ESB RESTful Service DemoREST-0.1 job to Talend Runtime environment for that we must build job from studio as OSGI Bundle for ESB
[![TOS-ESB-2](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-2.jpg)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-2.jpg)
Select archive file path, Build type as OSGI Bundle For ESB and click Finish

[![TOS-ESB-3](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-3.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-3.JPG)

exported file must be copied in Runtime_ESBSE/container/deploy folder

[![TOS-ESB-4](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-4.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-4.JPG)

On Runtime container use list command to check status of your service.

[![TOS-ESB-5](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-5.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-5.JPG)

Now we are able to deploy ESB job as service in Talend Runtime container using apache karaf but to make it run as a operating system service we must install Karaf Wrapper feature.</br>
**Installing the wrapper**</br>
Step 1: Browse to the container/bin folder of the Talend Runtime installation directory, then launch the container by executing the trun file as a root user.</br>
Step 2: To install the wrapper feature, simply type:

[![TOS-ESB-6](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-6.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-6.JPG)

Once installed, wrapper feature will provide wrapper:install new command in the trun, which allows you to install Talend Runtime as a service.
Step 3: To register the container as a service in automatic start mode, simply type:

[![TOS-ESB-7](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-7.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-7.JPG)

Now, We will test service from chrome brower.
Step 1: To test the REST service, type http://10.10.10.1:8040/services/customers (you can replace 10.10.10.1:8040 by your deploy machine) in chrome brower.

[![TOS-ESB-8](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-8.JPG)](https://github.com/smartbiz/talend/blob/master/assets/images/TOS-ESB-8.JPG)

<a id="TOC-ESB9"></a>
### Deploy service using apache tomcat (**TODO**) ###
<a id="TOC-ESB10"></a>
# Management and Monitoring (**TODO**) #
<a id="TOC-ESB11"></a>
## Hawtio (**TODO**) ##
