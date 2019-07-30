# Alarms in Atenea

## Permissions and certificates

In order to consume the API Alarms is necessary count with certificates and permissions, both of them are personal and non transferable.

[Gain the Work environment certificate](https://platform.bbva.com/en-us/profile/ether-credentials.html)

![Image of gain work certificates](./imagenes/1-certificate-work.png)

###Configure certificate

There are many ways to do a request to an API, **Postman** and **CURL**:

**Postman:**

Go to Settings>Certificates>Add Certificate. Add the CRT file, key file and passphrase in the 3 hosts.

![Image of Postman certificates](./imagenes/2-certificate-postman.png)

The hosts that you have to configure are (every country use the same link, just the environment chages [work, live]):

* https://mr-tls.work.mx.ether.igrupobbva
* https://sigma.work.mx.ether.igrupobbva
* https://mu.work.mx.ether.igrupobbva

**cURL:**

To use this command pass the next --cert, --key and --pass arguments to call the API SEMaaS:

```bash
curl -i -k --cert etherId_cert.pem --key etherId_key.pem --pass password-key https://sigma.work.mx.ether.igrupobbva/v0/ns/com.bbva.aso/alarms -X GET
```

###Get permissions

It is necessary to have permissions on the API in order to use it, this need to be requested to SEMaaS team or someone that have admin rol.

In case you have the admin role , you can give grant users as well. (It is pre-request that you have the certificate previously installed)

![Image of set permissions](./imagenes/3-set-permissions.png)

| Instructions |
| ------------ |
| **mr-url** | https://mr-tls.work.mx.ether.igrupobbva -> Specify as you wish work or live. |
| **namespace-aso** | Namespace which you will grant access to a user, example: ether.pe.aso. |
| **consumerId** | User that will be granted. |
| **roles** | Roles assign to a user. The minimum rol that the user should be have is "Writer". |

***IMPORTANT NOTE***: Assure that the bot sigma.invoker@ether.mu has the role "Writer" in the namespace. The bot update automatically the status of the alarm.

![Image of set permissions to bot](./imagenes/4-set-permissions-bot.png)

##Alarm Receiver

The alarms are resources that lived on a BD and it is consumed through and API (Sigma)

Previously to create the alarm as well, it should create another resources: Alarm Receiver and Alarm Type.

The Alarm Receivers defines the channel which the channel will receive the alarm, there are 2 types: **mail** and **slack**

Payload to create an Alarm Receiver:

```json
{
    "_id": "slack-receiver-prueba",
    "kind": "SLACK",
    "config": {
        "webhook": "https://hooks.slack.com/services/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"
    }
}
```

Examples:

![Image of Alarm receiver slack](./imagenes/5-alarm-receiver-slack.png)
![Image of Alarm receiver mail](./imagenes/6-alarm-receiver-mail.png)

| Input parameters |
| ---------------- |
| **endpoint** | The endpoint for the resource is **/alarm-receivers** |
| **sigma-url** | https://sigma.work.mx.ether.igrupobbva | Check the endpoint if it is Work or Live. |
| **namespace-aso** | Namespace on which the user can grant access, example: ether.pe.aso. |
| **kind** | It can be SLACK or MAIL. |
| **config** | It has an attribute and it can be mail for MAIL or webhook for SLACK. |

##Alarm Type

The Alarm Types defines the type of information necessary to show in SEMaaS in case this happends, i.e this structure define the type of data for (email and phone support). Also in this part we define the receivers.
Skeleton to create an Alarm Type:

```json
{
    "_id": "alarma-type-prueba",
    "propertiesSpec": {
        "actionIfCritical": "string",
        "actionIfStalled": "string",
        "actionIfWarning": "string",
        "description": "string",
        "documentation": "string",
        "group": "string",
        "serviceName": "string",
        "serviceOwner": "string",
        "supportEmail": "string",
        "supportPhone": "string"
    },
    "notificationPolicies": [
        {
            "stateChangesOnly": true,
            "alarmReceiver": "//sigma.work.mx/ns/com.bbva.aso/alarm-receivers/mail-receiver-prueba"
        },
        {
            "stateChangesOnly": true,
            "alarmReceiver": "//sigma.work.mx/ns/com.bbva.aso/alarm-receivers/slack-receiver-prueba"
        }
    ]
}
```

Example:

![Image of Alarm type](./imagenes/7-alarm-type.png)

| Parameters |
| ---------- |
| **endpoint** | Endpoint for this resource is /alarm-types. |
| **sigma-url** | https://sigma.work.mx.ether.igrupobbva change if it is live or work. |
| **namespace-aso** | Namespace which will be grant access, example: ether.pe.aso. |
| **propertiesSpec** | Object that receives the data in order to proceed with the Alarm. |
| **notificationPolicies** | Array to configure the different Alarms Receiver. |
| **stateChangesOnly** | Flag to indicate if you want to receive the alarm when the state changes or every time that the status alarm is verified. |
| **alarmReceiver** | [Locator](##locator) of alarm receiver. To know the locator for any resource we can request a GET to the endpoint and it appears as attribute inside the response. |

###On Working Hours Configuration

The alarm types can be configured to only alert when the change of status takes place within working hours, it is an optional object (**onWorkingHours**). This can apply when alarms are in work environment.

Example of the structure of the **onWorkingHours** object that is inside the **notificationPolicies** array of the alarm type:

```json
"onWorkingHours": {
    "regular": {
        "mon": [
            [
                "13:00",
                "23:00"
            ]
        ],
        "tue": [
            [
                "13:00",
                "23:00"
            ]
        ],
        "wed": [
            [
                "13:00",
                "23:00"
            ]
        ],
        "thu": [
            [
                "13:00",
                "23:00"
            ]
        ],
        "fri": [
            [
                "13:00",
                "23:00"
            ]
        ]
    }
}
```

Example:

![Image of On Working Hours 1](./imagenes/8-on-working-hours-1.png)
![Image of On Working Hours 2](./imagenes/9-on-working-hours-2.png)

| Parameters |
| ---------- |
| **onWorkingHours** | Object that contains the range of hours per day in which the alarm will alert. |
| **mon, tue, wed, thu, fri, sat, sun** | Days of the week on which you can alert. |
| **Range of hours** | Start and end time of alert. **Important**: The hours are in UTC standard, in the example above, the range is from 8:00 a.m. to 6:00 p.m. Mexico City (UTC-5). It is also possible to define several ranges in the same day, for more information consult: https://platform.bbva.com/en-us/developers/sigma/documentation/api/v0#content17 |

##Alarm

The alarm contains the information specified in the Alarm Type and this will be show in the Atenea Console.

Structure to create an Alarm:

```json
{
    "_id": "SMCMX1500025-createTicket-error-num",
    "monitoredResource": "//mr.work.mx/ns/com.bbva.aso/mrs/ASO",
    "enabled": true,
    "alarmType": "//sigma.work.mx/ns/com.bbva.aso/alarm-types/alarma-type-prueba",
    "properties": {
        "actionIfCritical": "Contact the Support Group immediately and send Remedy ticket (pruebas) to the Support Group",
        "actionIfStalled": "Contact the Support Group immediately and send Remedy ticket (pruebas1) to the Support Group",
        "actionIfWarning": "Send Remedy ticket (pruebas2) to the Support Group",
        "description": " Num Error in createTicket above or below the threshold",
        "documentation": "--",
        "group": "ASO",
        "serviceName": "createTicket",
        "serviceOwner": "mb67494",
        "supportEmail": "gesarqserv@bbva.com ",
        "supportPhone": "11034"
    }
}
```

Example:

![Image of Alarm](./imagenes/10-alarm.png)

| Parameters |
| ---------- |
| **endpoint** | The endpoint for this resource is **/alarms** |
| **sigma-url** | https://sigma.work.mx.ether.igrupobbva It can be work or live. |
| **namespace-aso** | Namespace to be granted for the user, example: ether.pe.aso. |
| **monitoredResource** | [Locator](##locator) of monitored resource. This value always is the same. |
| **enabled** | Enable or disable the alarm. |
| **alarmType** | [Locator](##locator) of alarm type. To know the locator of any resource, send a GET request to the endpoint to get the resource and it appears as an attribute inside the response. |
| **properties** | Object that specify the values of the differents attributes that previously it was defined in the Alarm Type. The information you defined will be showing in the Atenea Console. |

###Alarm status

The alarm can be in 4 states while her the life cycle:

Status | Description
------ | -----------
**STALLED** | It is the initial state when the alarm has been created, it means that the alarm is waiting for another change on the other status. This is only the initial stage when the alarm is created, once the status changed can not come back to this.
**OK** | It is the status by default when there is no condition to the alarm has WARNING or CRITICAL state.
**WARNING** | This status is used to advice in case something was wrong. Low level of dangerous.
**CRITICAL** | This status has a high danger level, it is used to notify that there is something very bad depends on the parameters defined in the Watcher.

###Manual Changes of the status of the Alarm

It is possible to change the status of the alarm manually through call the API, example: Structure to change the status:

```json
{
    "status": "OK",
    "reason": "Everything OK!"
}
```
Example:

![Image of Alarm change status](./imagenes/11-alarm-change-status.png)

| Parameters |
| ---------- |
| **endpoint** | The endpoint is **/alarms/{{alarmId}}:setStatus** |
| **namespace-aso** | Namespace to grant access to the user, example: ether.pe.aso. |
| **status** | Status to change the alarm. |
| **reason** | Info to show what happened. |

##Metric Watcher

So far we have created an alarm defining all his information necessary to work correctly. But it is necessary one automatic mechanism that updated the alarm status base on an input metric. The objective of Metric Watchers is update the alarm status.

Watchers are used on metrics to change the alarm status. So we detail what is a Metric:

###Metric

A metric is the measure unit that a request can have in Atenea, it means.

There are 5 kind of metrics on the API:

Metric | Description
------ | -----------
**span_duration** | Set the duration of the span in ms, data type float
**num_executions** | Set the number of request executed, data type int
**technical_error** | Set the number of executions with technical errors (http 500), data type int
**functional_error** | Set the number of executions with functional errors (4xx), data type int
**security_error** | Set the number of executions with security errors, maybe it can be necessary to specify the http codes for security.

Now we can see a Metric Watcher

Example of the structure for a metric watcher:

```json
{
    "_id": "SMCMX1500025-grantingTicket-error-num",
    "kind": "OPERATION",
    "window": {
        "period": "60",
        "every": "60"
    },
    "source": {
        "metricSetType": "//mu.work.mx/ns/com.bbva.aso/metric-set-types/span-metric-set-type",
        "metricSource": {
            "metricSetKey": "technical_error",
            "operation": "sum",
            "filters": {
                "serviceRegistryId": [
                    "SMCMX1500025"
                ]
            }
        }
    },
    "enabled": true,
    "alarm": "//sigma.work.mx/ns/com.bbva.aso/alarms/SMCMX1500025-createTicket-error-num",
    "actions": [
        {
            "conditionKind": ">=",
            "threshold": "10",
            "thresholdReset": "10",
            "statusCode": "CRITICAL",
            "statusDuration": "60",
            "resetStatusDuration": "60"
        },
        {
            "conditionKind": ">=",
            "threshold": "5",
            "thresholdReset": "5",
            "statusCode": "WARNING",
            "statusDuration": "60",
            "resetStatusDuration": "60"
        }
    ]
}
```

Example:

![Image of Metric Watcher 1](./imagenes/12-metric-watcher-1.png)
![Image of Metric Watcher 2](./imagenes/13-metric-watcher-2.png)

| Parameters |
| ---------- |
| **endpoint** | The endpoint for this resource is **/metric-watchers** |
| **mu-url** | https://mu.work.mx.ether.igrupobbva Depends on the environment: work or live |
| **namespace-aso** | Namespace which will grant access to the user, example: ether.pe.aso. |
| **kind** | OPERATION as the name said, it means an arithmetic operation (sum, min, max, mean and count). |
| **window** |  Object that define the space time and when the watcher will be monitoring the metric. |
| **period** | Value in seconds that the Watcher will be monitoring the metric. |
| **every** | Value in seconds that the Watcher will be waiting until it start to monitor again. |
| **metricSetType** | It is the [Locator](##locator) of metric set type. |
| **metricSource** | Object that specify the metric to watch and where you define the filter over you want to apply the monitoring. |
| **metricSetKey** | Name of the metric to watch. |
| **operation** | Operation that will be applied once the threshold of metric was reached. |
| **filters** | Object that contains the different filters that are necessary to get a concrete result from the monitoring. The values can be whatever field that appears on SEMaaS. |
| **enabled** | Enable or disable the watcher. |
| **alarm** | [Locator](##locator) of the alarm to inform when the conditions specified occurs. The Watcher update the status to the alarm. |
| **actions** | Array that defines the conditions and the status that involved the metric setted. |
| **conditionKind** | Logical Statement that is evaluated against the **metricSource** to determine what will be the actions to aplies. The values can be =, >=, <=, !=. |
| **threshold** | Value that define when the status will be changed based on the **metricSource** result. |
| **thresholdReset** | Value that need to be reached to reset the status of the alarm based on the result of **metricSource**. |
| **statusCode** | Status to be updated if the condition is accomplished. |
| **statusDuration** | Duration in seconds where the **threshold** can be reached. |
| **resetStatusDuration** | Duration in seconds where the **thresholdReset** can be reached. |

##Live environment Settings

To obtain the certificates and the necessary permissions to work in Live environment, it is necessary to open a ticket in ServiceDesk indicating the namespace and that the requirement is required in sigma:

https://globaldevtools.bbva.com/jira/servicedesk/customer/portal/26/create/332

The configuration of the certificates is done in the same way as in work, the only thing that changes are the endpoints where it is pointed:

* https://mr.live.mx.nextgen.igrupobbva
* https://sigma.live.mx.nextgen.igrupobbva
* https://mu.live.mx.nextgen.igrupobbva

***IMPORTANT NOTE***: Ensure that the sigma.invoker@ether.mu bot has a "Writer" role in the namespace. The bot automatically updates the status of an alarm.

##References

* https://platform.bbva.com/en-us/developers/sigma/documentation/01-what-is
* https://platform.bbva.com/en-us/developers/sigma/documentation/api/v0
* https://platform.bbva.com/en-us/developers/mu/documentation/01-what-is
* https://platform.bbva.com/en-us/developers/mu/documentation/api/v0

##Locator

To find the locator of any resource, it is enough to request a GET to the endpoint of the resource that we want to find, example:

Example endpoint: https://sigma.work.mx.ether.igrupobbva/v0/ns/com.bbva.aso/alarm-receivers

![Image of Locator](./imagenes/14-locator.png)

In the screenshot it show the request for all the **alarm-receivers**, per each of them it has a **_locator** related that works as a reference to it in another resource.