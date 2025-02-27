---
date: '2023-11-01'
title: "Running Your First CorDapp"
menu:
  corda5-tools:
    parent: corda5-develop-get-started
    identifier: corda5-run-first-cordapp
    weight: 3000
section_menu: corda5-tools
---
# Running Your First CorDapp

The CSDE includes {{< tooltip >}}flows{{< /tooltip >}} and tests for a very simple {{< tooltip >}}CorDapp{{< /tooltip >}}, which you can run out of the box. The code for the flow can be found in the `src/main/kotlin.com.r3.developers.csdetemplate.flowexample.workflows.MyFirstFlow.kt` file. This is also the code described in the [first flow section]({{< relref "../first-flow/_index.md" >}}). This section describes how to run, test, and deploy this flow. It contains the following:

* [Starting the Corda Combined Worker](#starting-the-corda-combined-worker)
* [Testing Liveness and Swagger](#testing-liveness-and-swagger)
* [Deploying a CorDapp](#deploying-a-cordapp)

## Starting the Corda Combined Worker

To run the flow, you must first start a local combined worker version of Corda. CSDE includes helper Gradle tasks to do this.
{{< figure src="starting-corda.png" width="40%" figcaption="CSDE startCorda task" alt="CSDE task to start the combined worker in IntelliJ" >}}

The `startCorda` task runs in an Intellij 'Run' window. After about one minute, it shows the following output:
{{< figure src="starting-corda-running.png" figcaption="CSDE startCorda task running" alt="CSDE task to start the combined worker in IntelliJ" >}}

The `startCorda` will continue to run whilst the Corda cluster is running. It will stop when the cluster is shut down.
Currently, we do not have a liveness detector for Corda in the CSDE so we check liveness by manually hitting an endpoint. You can use the [listVNodes helper]({{< relref "../overview/_index.md#csde-queries" >}}) to do this.

## Testing Liveness and Swagger

Corda exposes HTTP REST {{< tooltip >}}API{{< /tooltip >}} endpoints for interacting with itself and the CorDapps running on it. It also exposes a Swagger interface which is described in the following sections.

### Displaying the Swagger UI
To display the Swagger UI, use the following link:

[https://localhost:8888/api/v5_1/swagger#/](https://localhost:8888/api/v5_1/swagger#/)

If Corda has started, the Swagger UI displays:
{{< figure src="swagger-ui.png" width="80%" figcaption="Swagger UI showing Corda liveness" alt="Swagger UI showing Corda" >}}

If Corda has not started yet, the page will not load.
If the Swagger UI is already open whilst starting Corda, you must hit an endpoint to test liveness of Corda.

### Authorizing Swagger

To access the Corda cluster, you must authorize Swagger:

1. Click the green **Authorize** button.
{{< figure src="authorize-button.png" width="30%" figcaption="Swagger Authorize button" alt="Button in Swagger UI to authorize access to the Corda cluster" >}}
   The **Available authorizations** window is displayed.
2. If necessary, click **Logout**.
3. Enter the username and password. For the purposes of experimental development, the username for the combined worker is set to  `admin` and the password is `admin`.
{{< figure src="authorize.png" width="70%" figcaption="Swagger Authorize authorizations window" alt="Authorize authorizations window in Swagger UI to authorize access to the Corda cluster" >}}
4. Click **Authorise** and then **Close**.

### Hitting Endpoints

Once authorised, you can start hitting endpoints. The easiest one to try is `/cpi` because it is the first one on the Swagger page and requires no arguments:

1. Expand the `GET /cpi` row and click **Try it out**.
{{< figure src="get-cpi.png" figcaption="Try it out button for GET /cpi" alt="Expanded GET /cpi with Try it out button" >}}
2. Click the **Execute** button to hit the endpoint.
   If Corda has started you should see a response like this:
   {{< figure src="get-cpi-response.png" figcaption="Swagger showing a successful response to GET /cpi" alt="Swagger showing a successful response to GET /cpi" >}}
   As we have not uploaded any CPIs yet, the returned list of CPIs is empty.
   If Corda has not started yet, Swagger will return an error:
   {{< figure src="get-cpi-error.png" figcaption="Swagger showing an error response to GET /cpi" alt="Swagger showing an error response to GET /cpi" >}}
   If this occurs, you either have not started Corda, Corda has not finished starting, or something has gone wrong. If something has gone wrong, you should try again or [reset the environment]({{< relref "../reset-csde.md" >}}) and start again.
   {{< note >}}
   Each time you start Corda for the specific combined worker development environment, it is a fresh instance. There is no persistence of state between restarts.
   {{< /note >}}

## Deploying a CorDapp

You can use the `MyFirstFlow` flow to build a CorDapp, without any further work:

1. Click the `5-vNodeSetup` Gradle task:
{{< figure src="vnodesetup.png" width="40%" figcaption="CSDE vNodeSetup task" alt="CSDE task to set up the vNodes in IntelliJ" >}}
   This task runs a series of Gradle tasks to:
   * Build the {{< tooltip >}}CPB{{< /tooltip >}}
   * Create the GroupPolicy (Application Network definition)
   * Generate signing keys to sign the CPB and {{< tooltip >}}CPI{{< /tooltip >}}
   * Build the CPI
   * Sign the CPI
   * Upload the CPI to Corda
   * Create and register the virtual nodes with the CPI
   {{< note >}}
   Some of these tasks only need to run once and will not run again if not required.
   {{< /note >}}
    You should now be able to start `MyFirstFlow` from the Swagger UI.

### Starting Your First Flow

To run your first flow:

1. Find the `holdingidentityshorthash` for the {{< tooltip >}}virtual node{{< /tooltip >}} you want to trigger the flow on. You can do this by running the `listVnodes` Gradle task to display a list of the configured virtual nodes:
   {{< figure src="list-vnodes.png" width="100%" figcaption="Running the listVnodes gradle task" >}}
   The 12 digit hash is the `holdingidentityshorthash` that acts as the unique identifier for a virtual node.

2. Expand the `POST /flow/{holdingidentityshorthash}` endpoint in the Flow Management API section in Swagger and click **Try it out**.
{{< figure src="post-flow.png" figcaption="Try it out button for POST /flow/{holdingidentityshorthash}" alt="Expanded POST /flow/{holdingidentityshorthash} with Try it out button" >}}
3. Enter the hash and the `requestBody` and click **Execute**.
{{< figure src="post-flow-arguments.png" figcaption="Arguments for POST /flow/{holdingidentityshorthash}" >}}

requestBody code:
```kotlin
{
   "clientRequestId": "r1",
   "flowClassName": "com.r3.developers.csdetemplate.flowexample.workflows.MyFirstFlow",
   "requestBody": {
      "otherMember":"CN=Bob, OU=Test Dept, O=R3, L=London, C=GB"
   }
}
```

* `ClientRequestId` is a unique identifier for the request to start a flow.
* `flowClassName` is the fully qualified path to the flow class you want to run.
* `requestBody` is the set of arguments you pass to the flow.

   Swagger should display the following response:
   {{< figure src="post-flow-start-requested.png" figcaption="Successful response for POST /flow/{holdingidentityshorthash}" >}}

Note, if you forget to change the `ClientRequestId` on subsequent attempts to run the flow, the following error message is displayed:
{{< figure src="post-flow-already-started-error.png" figcaption="Error response for POST /flow/{holdingidentityshorthash" >}}

Because the API is asynchronous, at this stage you only receive the confirmation `START_REQUESTED`. There is no indication if the flow has been successful. To find out the status of the flow, you must check the flow status.

### Checking the Flow Status

To check the flow status:

1. Expand the `GET /flow/{holdingidentityshorthash}/{clientrequestid}` endpoint in Swagger and click **Try it out**.
2. Enter the hash and the `requestid` used when [starting the flow](#starting-your-first-flow) and click **Execute**.
{{< figure src="get-flow-arguments.png" figcaption="Arguments for GET /flow/{holdingidentityshorthash}/{clientrequestid}" >}}
   If the flow is successful, you will see the following response:
{{< figure src="get-flow-completed.png" figcaption="Successful response for GET /flow/{holdingidentityshorthash}/{clientrequestid}" >}}
   You will learn more about the flowResult of "Hello Alice best wishes from Bob" in [Your first flow]({{< relref "../first-flow/_index.md" >}}).

{{< note >}}
If you receive a response with a status of "RUNNING”, wait a short time and retry the status check.
{{< /note >}}
