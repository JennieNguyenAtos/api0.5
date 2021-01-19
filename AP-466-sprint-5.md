# Event Generation: GCP
2020 API Iteration 0.5
API-Abstraction / AP-466

## Story Description
The API Platform provides abstraction for inbound requests for provisioning, management, and access to Cloud resources across many disparate providers.   A corollary feature is also required to configure the cloud providers to send event data to the platform which can then be analyzed, formatted, acted upon and send back to our client applications in a standard manner. 

Research, Design, Test and Document event generation mechanisms for GCP Cloud provider and integration point in the platform. Obtain Performance, Status, Security, etc using Provider Native Tools. Document the process used and how it can be automated.

NOTE:  This is foundational work to identify the proper sources and techniques that can be used to obtain the event data and will enable creation of platform requirements for development.

## Objective
The objective of this ticket is to gather information about the different event generations mechanisms for the GCP Cloud provider. You might want to export logs for the following reasons: To store logs that are unlikely to be read but must be retained for compliance purposes, to use big-data analysis tools on your logs, or to stream your logs to other applications, other respositories, or third parties.

![Logs Router Image](https://cloud.google.com/logging/docs/images/logs-router-image.png)

Exporting involves writing a filter that selects the log entries you want to export, and choosing a destination (such and Pub/Sub: JSON messages delivered to Pub/Sub topics). The filter and destinations are held in an object called a sink. Sinks can be created in Google Cloud project, organizations, folders, and billing accounts. [Link to sink propertiies and terminology](https://cloud.google.com/logging/docs/export#sink-terms)

How sinks work: Every time a log entry arrives in a project, folder, billing account, or organization resource, Logging compares the log entry to the sinks in that resource. Each sink whose filter matches the log entry writes a copy of the log entry to the sink's export destination.

What is Pub/Sub: an asynchronous messaging service that decouples services that produce events from services that process events. We will be using it for event ingestion and delivery for streaming analytics pipelines. A publisher application creates and sends messages to a topic. In this scenario, the publisher application can be any application that can make HTTPS requests to pubsub.googleapis.com and subscriber is the client application. 

## Results
Results of research showed that there are multiple ways that event data can be exported back to the intended platform. You can export some or all of your logs to various sink destinations. The first option is to use GCP Cloud Logging API, and the second option is exporting logs with the Cloud Console and Pub/Sub.

### GCP Cloud Logging API Method

### GCP Cloud Console and Pub/Sub
Using the Cloud Console, you can do the following:
- View all of your sinks in one place.
- View which log entries are matched by your sink query before you create a sink
- Create and authorize export destinations for your sinks.
However, the Cloud Console can only create or view sinks in projects.

Before you can create a sink, verify the following:
* You have a Google Cloud Project with logs that you can see in the Logs Explorer.
* You have the Owner or the Logging/Logs Configuration Writer IAM roles in the Cloud project to create, delete, or modify a sink. 
* You have a destination service or have the ability to create a destination service.

[Link on steps to creating a sink in the GCP Console](https://cloud.google.com/logging/docs/export/configure_export_v2#creating_sink)
* When creating a sink, make sure that the sink destination is set to your desired Pub/Sub topic to receive the exported logs.

Next, configure your Pub/Sub topic
* In **push delivery**, Pub/Sub initiates requests to your subscriber application to deliver messages. The subscriber explicitly calls the acknowledge method, using the returned ack ID to acknowledge receipt. The endpoint acknowledges the message by returning an HTTP success status code. A non-success response indicates that the message should be resent. [How to recieve messages using Push](https://cloud.google.com/pubsub/docs/push)

## Challenges
**Pricing**: Cloud Logging doesn't charge to export logs, but destination charges might apply.

Pub/Sub pricing is based on:

* Message ingestion and delivery
* Seek-related message storage: snapshots and retained acknowledged messages
* [Pub Sub Pricing](https://cloud.google.com/pubsub/pricing)
* [Quotas and limits for PubSub](https://cloud.google.com/pubsub/quotas#quota_limits)

## Recommendations
Google recommmends using the Cloud Console and Pub/Sub to automate exporting logs.

For each resource in the Cloud Console, create a sink for the logs that you want to be pushed to the client application using a pub/sub topic. 
