## Compute > Cloud Functions > Console User Guide
This document describes how to create and manage functions in NHN Cloud Functions console.

## Manage functions
You can create, edit, delete, and copy functions.

### Create functions
After setting up the function and writing the code, click the Create button to build the code and create the function.

![console-guide-03](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-07-29/console-guide-03.png)
#### Function settings
<table class="it">
    <tr>
        <th>Category</th>
        <th>Item</th>
        <th>Description</th>
    </tr>
    <tr>
        <td rowspan="2">Basic Information</td>
        <td>Name</td>
        <td>Name of the function<br> Duplicate function names are not allowed.<br> Used as the function endpoint URL.</td>
    </tr>
    <tr>
        <td>Description</td>
        <td>Description of the function, up to 250 characters</td>
    </tr>
    <tr>
        <td rowspan="7">Function settings</td>
        <td>Endpoint URL</td>
        <td>Function call HTTP endpoint URL<br>It changes automatically as you enter the function name.</td>
    </tr>
    <tr>
        <td>Type</td>
        <td>Pool Manager<br> New Deployment</td>
    </tr>
    <tr>
        <td>Resource</td>
        <td>Select function behavior environment resources<br>If it’s Pool Manager type, only the specified resources can be selected<br>New Deployment type allows you to enter custom resources</td>
    </tr>
    <tr>
        <td>Execution timeout</td>
        <td>Specify how long the function will run (Time out). If the function runs and exceeds the specified time, the function is forced to abort and responds with an error</td>
    </tr>
    <tr>
        <td>(Pool Manager) Concurrent execution settings</td>
        <td>Specify the number of concurrent executions of the function. When concurrent function calls occur, instances are executed concurrently as many times as the number of calls, and the maximum number of instances is limited.</td>
    </tr>
    <tr>
        <td>(New Deployment) Minimum number of instances</td>
        <td>Specify the minimum number of instances to retain.</td>
    </tr>
    <tr>
        <td>(New Deployment) Maximum number of instances</td>
        <td>Specify the maximum number of instances that can scale up automatically.</td>
    </tr>
    <tr>
        <td>Log settings</td>
        <td>Integrate with log service</td>
        <td>Choose to integrate using Log &amp; Crash Search service<br>The logs of the function are available directly from Log &amp; Crash Search service.</td>
    </tr>
</table>

> **[Note]** <br>
> **(Pool Manager) Concurrent execution settings**


![console-guide-05](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-07-29/console-guide-05.png)

<br>

> **[Note]** <br>
> **(New Deployment) Number of instances** - Depending on resource usage, the specified number of instances might not be created.

<br>


#### Code writing

![console-guide-04](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-07-29/console-guide-04.png)

<table class="it">
    <tr>
        <th>Category</th>
        <th>Item</th>
        <th>Description</th>
    </tr>
    <tr>
        <td rowspan="2">Source code</td>
        <td>Runtime Environment</td>
        <td>Select the function's runtime environment</td>
    </tr>
    <tr>
        <td>Entry Point</td>
        <td>Specify the entry point for the function, such as function name <br>It is automatically completed to match the template in the runtime environment.<br>It must match the entry point in the source code that you wrote when you modified randomly.</td>
    </tr>
    <tr>
        <td rowspan="3">Code</td>
        <td>Code editor</td>
        <td>The template files in the runtime environment are loaded, and you modify them to write the function.</td>
    </tr>
    <tr>
        <td>User’s local environment</td>
        <td>Write function code in the local environment and upload as a ZIP file</td>
    </tr>
    <tr>
        <td>Deployment</td>
        <td>Used to deploy user-written or uploaded code to verify that the build is normal.<br>Note: Functions are rebuilt with the latest updated source code at the time of creation, rather than using their distribution version.</td>
    </tr>
    <tr>
        <td rowspan="2">Test</td>
        <td>Test events</td>
        <td>Create a JSON Body to send to the function</td>
    </tr>
    <tr>
        <td>Test</td>
        <td>Test the function by sending the JSON Body you created to the event. (This must be deployed first.) <br>You can verify function behavior with logs.</td>
    </tr>
    <tr>
        <td></td>
        <td>Creation</td>
        <td>Use the Generate button to generate a function based on the function settings and the function you created.</td>
    </tr>
</table>

<br>

> **[Note]** <br> The Deploy button is only used to check the build of the source code you created, and when you create a function, it will be rebuilt with the latest source code, not the build you tested with Deploy.

### Modify functions
To modify the function settings and code of an existing function, use the Edit button to modify the function.
#### Non-modifiable item
- Name, runtime environment
    - You can edit everything except the item.
#### Source code
- If you're using the code editor, the existing code is loaded.
- If you created a function by uploading a ZIP file from the local environment, when you switch to the code editor, it loads the default template code without showing the ZIP file.

### Delete a function
Select an existing function to delete it. You can delete multiple functions at once.

### Copy a function
Copy a function that is identical to an existing function. The name cannot be duplicated, so you can rewrite the name before copying.

## About functions
### List of functions
![console-guide-01](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-07-29/console-guide-01.png)
- You can see a list of functions that users have created.
- The build status is automatically updated to confirm that the function is available.
### Basic information of functions
![console-guide-02](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-07-29/console-guide-02.png)
- You can see basic information about the function.
- You can check the logs by going to Log & Crash Search service via the Log & Crash Search button in the log management topic.
- You can check the build log by clicking the “Check build log” button in the build status item.
- You can download the code you wrote as a ZIP file.
### Manage function triggers
You can manage triggers that can perform functions. HTTP triggers are built-in.

- You can execute the function you created via a given HTTP trigger.
    - Example: `https://{userdomain}/{function name}`
    - Method : GET, POST

#### Create/edit triggers
- Timer
    - Value: Enter the cycle as a Cron string.
#### Delete triggers
- You can select multiple triggers to delete. You can't delete the HTTP trigger, which is the default trigger.

> **[Note]**
> <br>Currently, we only support Timer triggers.
### Monitor functions
![console-guide-06](https://kr1-api-object-storage.nhncloudservice.com/v1/AUTH_2acdfabf4efe4efc8a04c00b348110c9/cdn_origin/prod_cloud_functions/2025-07-29/console-guide-06.png)
- You can check the usage of a function.
- Provide metrics for the number of function calls, number of call rejections, number of errors, success rate, and function execution time.
- Provide metrics within a set time.
- If logs are integrated, you can navigate to the Log & Crash Search service using the Log & Crash Search button.

| Item | Description |
| --- | --- |
| Number of function calls | Total number of function calls (per second) |
| Number of call rejections | Number of function calls that failed as instances were not created (per second) |
| Number of errors | Number of responses with a non-200 response code (per second) |
| Success rate | Ratio of successful calls to the total number of function calls |
| Function execution time | Response time for function calls |

> **[Note]**
> <br>The number of call rejections is only relevant if the type is Pool Manager.
