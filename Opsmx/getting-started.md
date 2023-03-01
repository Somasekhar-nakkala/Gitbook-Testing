# Getting Started

## Introduction

Argo Rollout enhances the deployment capabilities of Kubernetes with features such as blue-green, canary, experimentation, and progressive delivery. The OpsMx Intelligent Software Delivery (ISD) platform integrates with Argo Rollout to automate the verification of deployments and reduce the risk of releasing faulty updates to production through progressive delivery.

ISD uses machine learning algorithms, Natural Language Processing (NLP), and statistical analysis to evaluate new releases and identify potential risks in comparison to the base releases. It integrates with the widely used log and metrics monitoring tools to perform analysis during the rollout process.

ISD integrates with the following monitoring tools:

* Amazon Cloudwatch
* AppDynamics
* DataDog
* Dynatrace
* ElasticSearch
* Graphite
* Graylog
* New Relic
* Prometheus
* Splunk
* Stackdriver
* SumoLogic
* VMware Tanzu Observability

### Prerequisites

* Argo CD (v2.4.8 or later) with Argo Rollouts (v1.3 or later) Installed.
* Application configured for deployments using Argo Rollouts.
* A log or metrics monitoring tool for observing applications (Refer to the above list of monitoring tools that ISD supports).
* Application configured for collecting metrics and logs through the supported monitoring tools.
* ISD platform installed. If ISD needs to be installed, follow the instructions [**here**](https://docs.opsmx.com/opsmx-intelligent-software-delivery-isd-platform-argo/operator-manual/isd-argo-standard-installation/isd-argo-platform-standard-installation).

### Overview of Performing Automated Analysis

1. Connect the ISD to the existing Argo CD instances.
2. To monitor data, we need to integrate the ISD system with appropriate monitoring tools. This integration will allow us to access the data source for application logs, such as Elastic, and metrics, such as Prometheus. These integration accounts will be utilized in subsequent data analysis processes.
3. Choose a Deployment for which you want to perform Rollouts. If you are unfamiliar with Rollouts, please refer to the [**Migrating to Rollout**](https://argoproj.github.io/argo-rollouts/migrating/) document to convert your existing Deployment into a Rollout object. A sample application with all the necessary supporting files can be found [**here**](https://github.com/OpsMx/isd-sample-apps/tree/main/autopilot-rollout).
4. Generate configMaps and secrets in the namespace where the Application Rollout will be executed. These should include the updated Metric/Log templates and any additional information related to your installation. A sample Application with all the required supporting files is available [**here**](https://github.com/OpsMx/isd-sample-apps/tree/main/rollout-for-your-apps).
5. Choose a Log Template that is specific to your data source from [**here**](https://github.com/OpsMx/isd-sample-apps/tree/main/rollout-for-your-apps/log-metric-templates)**.** The Log Template defines the application-specific log messages that are categorized. Follow the instructions provided in the file.
   * **OPTIONAL**: Adjust the log selection strings and topic type (CRITICAL, ERROR, WARNING, IGNORE) as necessary based on the specific needs of your application.
6. Choose a Metric Template that is specific to your metric monitoring tool from [**here**](https://github.com/OpsMx/isd-sample-apps/tree/main/rollout-for-your-apps/log-metric-templates). The Metric Template defines the metrics that you want to compare between the new release and the baseline. Follow the instructions in the file.
   * **OPTIONAL**: Adjust the metric selection as necessary based on the specific needs of your application
7. Modify the Argo Rollout AnalysisTemplate found [**here** ](https://github.com/OpsMx/isd-sample-apps/tree/main/rollout-for-your-apps/AnalysisTemplate)with your specific parameters, following the instructions in the file.
8. Modify the rollout.yaml to include automated analysis. Check [**here** ](https://github.com/OpsMx/isd-sample-apps/tree/main/rollout-for-your-apps/rollout-yaml)for samples.
9. Perform automated analysis during rollouts.
10. Evaluate analysis report.

### Setup for enabling Automated Analysis

To enable the Automated Analysis you need to perform a one-time platform setup and one-time setup for each application as stated below.

#### [One-time Platform Setup](getting-started.md#one-time-platform-setup):

The following is the sequence of activities for one-time platform setup.

1. Connect to existing Argo CD
2. Integrate with monitoring tools - Data Sources
3. Set up Kubernetes profile Secret for ISD
4. Create ServiceAccount for ISD Analysis

#### [Application Setup for Automated Rollout Analysis:](getting-started.md#overview-of-performing-automated-analysis)

Following are the sequence of activities for application setup for Automated Rollout analysis.

1. Create Log and Metric Template ConfigMaps
2. Create Analysis configuration
3. Setup Rollout AnalysisTemplate
4. Update rollout.yaml to enable Automated Analysis

### One-time Platform Setup:

#### **Step 1: Connect to Existing Argo CD**

To connect the ISD platform to one or more existing Argo CD installations, follow the steps below:

1. Access the CD Integration sub-menu under the top-level Setup menu in the ISD UI.
2. Deploy a secure agent in the same cluster as the Argo CD or in the same security domain as the Argo CD.
3.  Enter a name for the Argo CD connection in the ISD platform.\


    <figure><img src="broken-reference" alt=""><figcaption><p><br></p></figcaption></figure>

    {% hint style="info" %}
    **Note**: The secure agent ensures a secure connection between the ISD platform and the Argo CD installation. The name chosen for the Argo CD connection is used in later steps for identification purposes.
    {% endhint %}

#### **Step 2:** Integrate with Monitoring Tools - Data Sources

To integrate the ISD platform with the monitoring tools used to observe the applications during deployments, follow the steps below:

1. Access the Integrations sub-menu under the top-level Setup menu in the ISD UI.
2. Connect the ISD platform to the desired monitoring tools.
3. Create and configure "accounts" in the ISD platform to represent the connections to the monitoring tools.
4.  Use the account names in the log and metrics templates during application setup.\


    {% hint style="info" %}
    **Note**: The ISD platform pulls relevant data from the configured integrations (accounts) for analysis purposes during deployments. The account names are used in the log and metrics templates to identify the data source.
    {% endhint %}



    <figure><img src="https://lh4.googleusercontent.com/IGhzzDre7kbJWbN0mL-FKnfFm76jp1yGksZrs2ozxHSqYPUJVY1RGXrZqBfTmhg2bxEI4qeS_jBjI89IHhHJu51hJUVEelvpkGfNKB3cABak9LNN80sABoK9piNKjcvZzsjhJoiO168ltQ5SWC9837Q" alt=""><figcaption><p><br></p></figcaption></figure>

    <figure><img src="https://lh4.googleusercontent.com/4TMPkfC0qc940WvltxAer9HoPZG09KI7fa7jSwxcgkPuqolbMxH8yO-mf3P7j-q7ktEp78dTQNci3wmm75GK8eXaUdZvh9efvb_gdRdHIXQvCdkpgqzp48wg4HD6NtqbuIn25Hdotwo5-OxQSBKZkMM" alt=""><figcaption></figcaption></figure>

#### Step 3: Set up Kubernetes profile Secret for ISD

Create a Kubernetes profile secret in each Kubernetes target namespace as shown below. Sample kubernetes configuration file is available **** [**here**](https://github.com/OpsMx/isd-sample-apps/blob/main/rollout-for-your-apps/per-namespace/opsmx-profile.yaml)**.**  Customize the sample configuration file to match your specific scenario.

{% code overflow="wrap" %}
```
apiVersion: v1
kind: Secret
metadata:
  name: opsmx-profile                           # Default Name. No change necessary. It is referred in the AnalysisTemplate
stringData:
  opsmxIsdUrl: "https://ISD.REPLACE.THIS.WITH.YOURCOMPANY.COM"  # URL of ISD platform
  user: "admin"                                 # Default User. Change to an ISD platform User who has access to the data source (monitoring tools)
  sourceName: "argocd"                          # Argo CD name used in Setup->CDIntegration in ISD

  # Default is True.  Change to false if using Rollouts without Argo CD
  cdIntegration: "true"
```
{% endcode %}

{% hint style="info" %}
**Note**: The Kubernetes profile secret is used to securely store and access the credentials for your Kubernetes cluster. It is a crucial component in the process of deploying applications using Argo CD and ISD.
{% endhint %}

#### Step 4: Create ServiceAccount for ISD Analysis

The ISD analysis is carried out by a Kubernetes job, and a service account with the necessary permissions should be set up in each namespace where the rollout is performed. A sample file is available **** [**here**](https://github.com/OpsMx/isd-sample-apps/blob/main/rollout-for-your-apps/per-namespace/opsmx-provider-sa.yaml)**,** and usually, no modifications are needed.

### Application Setup for Automated Rollout Analysis

The following steps must be carried out for each application. Make sure that the application already has rollout enabled and that a canary or blue-green strategy is defined. If you need assistance with setting up the base rollout, refer to the [**Argo Rollout documentation**](https://argoproj.github.io/argo-rollouts/getting-started/).

#### **Step 1:** Create Log and Metric Template ConfigMaps

The Log and Metrics templates differ depending on the type of application and the metrics or log monitoring tool provider. It is recommended to find the appropriate log and metrics default template from the OpsMx [**public repository**](https://github.com/OpsMx/isd-sample-apps/tree/main/rollout-for-your-apps/log-metric-templates).

The following example demonstrates a typical configuration for an ElasticSearch log provider. Create the Log and Metrics template in the application's git folder and update the account names for the log and monitoring, as defined in Step 2 of the platform setup section.

Sample Log Template (for ElasticSearch):

{% code overflow="wrap" %}
```
Api Version: v1
kind: ConfigMap
metadata:
 name: elasticsearch-generic-minimal
data:
 elasticsearch-generic-minimal: |
   monitoringProvider: ELASTICSEARCH
   accountName: elastic-account-name # THIS is the account name given Step 2 where configured the data source
   index: kubernetes*
   filterKey: kubernetes.pod_name
   responseKeywords: log
```
{% endcode %}

For the detailed information on how to **create a Log Template in Git**, refer **** [**here**](https://docs.opsmx.com/opsmx-intelligent-software-delivery-isd-platform-argo/user-guide/delivery-verification/templates/create-log-template-in-git).\
For the detailed information on how to **create a Metric Template in Git**, refer [**here**](https://docs.opsmx.com/opsmx-intelligent-software-delivery-isd-platform-argo/user-guide/delivery-verification/templates/create-metric-template-in-git).

#### **Step 2:** Create Analysis configuration

This step involves defining the parameters for the rollout analysis such as the duration of the analysis and the minimum score required to pass. If the overall score falls below the defined pass score threshold, the rollout will automatically halt. The relevant log and metric templates defined in Step 1 should also be included in the analysis configuration. Sample analysis configurations for Prometheus and Elastic data sources are shown below and additional sample files are available on the [**OpsMx public repository.**](https://github.com/OpsMx/isd-sample-apps/tree/main/rollout-for-your-apps/provider-configs)****

```
apiVersion: v1
kind: ConfigMap
metadata:
 name: opsmx-provider-config        ## Default Name.  Change if multiple analysis is needed in the same namespace
data:
 providerConfig: |
   application: ARGO-CD-APPLICATION-NAME      ## Change this to your application name
   lifetimeMinutes: 05                        ## Change to Analysis Duration in minutes
   gitops: true
   passScore: 60   ## Change to Minimum Pass Score.  Any score above this score will continue Rollout.
   serviceList:
   - metricScopeVariables: "${app_key},${host_key}"
     baselineMetricScope: "{{env.STABLE_POD_HASH}}"
     canaryMetricScope: "{{env.LATEST_POD_HASH}}"
     metricTemplateName: "metrictemplate"
     logScopeVariables: "kubernetes.pod_name"
     baselineLogScope: ".*{{env.STABLE_POD_HASH}}.*"
     canaryLogScope: ".*{{env.LATEST_POD_HASH}}.*"
     logTemplateName: "logtemplate"
```

For the detailed information on how to **specify analysis configuration in OpsMx-Provider ConfigMap**, refer [**here**](https://docs.opsmx.com/opsmx-intelligent-software-delivery-isd-platform-argo/user-guide/delivery-verification/opsmx-provider-configmap).

#### **Step 3: Setup Rollout AnalysisTemplate**

The Argo Rollout AnalysisTemplate is used to specify the specifics of how to analyze the rollout data, using the provider-config and log and metrics templates from the previous steps. For general information about Analysis Templates in Argo Rollouts, please refer to the **** [**Argo documentation**](https://argoproj.github.io/argo-rollouts/features/analysis/?query=analysis+template#analysis-progressive-delivery).\
\
The ISD analysis is performed as a Kubernetes job and the AnalysisTemplate sample file can be found in the **** [**OpsMx public repository**](https://github.com/OpsMx/isd-sample-apps/tree/main/rollout-for-your-apps/AnalysisTemplate)**.**\
\
In most cases, there is no need to make any changes to the Analysis Template file, unless multiple applications and templates are being used in the same namespace. In that case, please follow the instructions provided in the sample file.

For the detailed information on how to **setup Rollout AnalysisTemplate**, refer [**here**](https://docs.opsmx.com/opsmx-intelligent-software-delivery-isd-platform-argo/user-guide/delivery-verification/analysis-template).

#### **Step 4: Update rollout.yaml to enable Automated Analysis** &#x20;

Now, we are ready to add the automated analysis as part of the rollout strategy.

The rollout.yaml file is the key configuration file for Argo Rollout and it specifies various details related to the rollout strategy. To enable automated analysis, update the rollout.yaml file with the following steps:

1. **Specify the Rollout Strategy:** Choose between Canary or Blue Green rollout strategy based on your requirements.
2. **Configure Traffic Shaping:** Specify the traffic shaping details such as the percentage of traffic to be redirected to the canary version.
3. **Progressive Delivery Steps (optional):** Define one or more progressive delivery steps if required.
4. **Automated AnalysisTemplate specification:** Add the automated analysis specification as defined in the previous steps in the AnalysisTemplate.

Sample rollout.yaml configuration with automated analysis specification is available below, which you can use as a reference. In this example, automated analysis is configured after 25% and 75% of traffic shaping to the canary version. More samples of rollout.yaml configuration for blue/green and canary is available [**here**](https://github.com/OpsMx/isd-sample-apps/tree/main/rollout-for-your-apps/rollout-yaml).

```
kind: Rollout
metadata:
 name: opsmx-demo
spec:
 replicas: 1
 revisionHistoryLimit: 2
 selector:
   matchLabels:
     app: opsmx-demo
 workloadRef:                                 
    apiVersion: apps/v1
    kind: Deployment
    name: opsmx-demo-deployment
 strategy:
   canary:
     canaryService: opsmx-demo-canary-svc
     stableService: opsmx-demo-stable-svc
     trafficRouting:
       nginx:
         stableIngress: opsmx-demo-ing
     steps:
     - setWeight: 25
     - pause: { duration: 60s }
     - analysis:
         templates:
           - templateName: opsmx-analysis-template
         args:
           - name: canary-hash
             valueFrom:
               podTemplateHashValue: Latest
           - name: baseline-hash
             valueFrom:
               podTemplateHashValue: Stable
     - setWeight: 75
     - pause: { duration: 60s }
     - analysis:
         templates:
           - templateName: opsmx-analysis-template
         args:
           - name: canary-hash
             valueFrom:
               podTemplateHashValue: Latest
           - name: baseline-hash
             valueFrom:
               podTemplateHashValue: Stable
```

### Final Checklist

Finally, ensure that you have completed all the necessary platform setup steps, including creating the Kubernetes secret for the cluster and the service account per namespace. This will allow you to see the applications in the ISD UI and the integrations created in the setup page.

Make sure that all the following relevant files are present in the same Git folder.

* Rollout and supporting objects for your application.
* AnalysisTemplate that refers to log and/or metric template, profile-secret, provider-config
* Log and/or metric templates
* Profile secret
* Provider config

To check that everything is in place, you can use the sample output as a reference. In this example, the Kubernetes secret and the service account definitions required for the analysis job are located in the same folder as the application, but this is not a strict requirement.\


<figure><img src="https://lh3.googleusercontent.com/r1f4nN0He8EmrBBjHaYO6S0tm3mTLeWotNyoEHsbcvA0wFXE2XG2aTqAOj27yiaahXekwvqPcd6JNg6nisMCl5LE4-PpDslKglLBOAGyNZkcX1yQ29VI-dxpbzBcNG-z4v91yfkhME--r9nSehDyY3A" alt=""><figcaption></figcaption></figure>

A fully functional sample application is provided **** [**here**](https://github.com/OpsMx/isd-sample-apps/tree/main/autopilot-rollout)**.**

### Perform Automated Analysis During Rollout

During a rollout, automated analysis will be triggered automatically as part of the progressive delivery process by updating the rollout.yaml file for your service. The status of the rollout can be monitored using the Argo Rollout extension or the Rollout dashboard. Refer to the image below.

<figure><img src="https://lh6.googleusercontent.com/45nqB9bQKmixkS5PJB67aGazuBhvux5dempPc_KD0t1yjBJfvCahPjH1oq0NfH3RBXyDMr6jtwHNTwDJ9C4Jrn0ZgMtFuy4vksXdHZBh-MVLq2m233cvJKG4G6s3RpFf4QUd-xybdoGPnmmGqADmU0lOAvKL9j3VRBZmNhUHLEi66cR91W3dbVAtsP4tZg" alt=""><figcaption></figcaption></figure>

The analysis will be carried out based on the configuration specified in the AnalysisTemplate and a report will be generated after each analysis performed during the progressive rollout. Usually, the analysis is conducted after each progressive rollout step. If the rollout is successful, the new version will be deployed, and users can review the report for any identified risks.

<figure><img src="https://lh3.googleusercontent.com/OAw6jX4M9ivG7jZMCEpNoQXoFMHI7rL3AyDPjqVsYCfsNOMYw4-_ck83YothTnuFPqHNh3EPmmxreLTwgJmEADdgJ-WHOQJVvgnx_BekY4OWEcO-chl0AsjeeuMhKPaUjaQPZjKHpBQpquPpyDVAvCX3VrXGv5zDmR_bXnHTug29q7Pt7mtSCDFx8ts3BA" alt=""><figcaption></figcaption></figure>

### Understanding the Automated Analysis Report

The ISD platform produces one or multiple analysis reports per rollout, as defined in the rollout.yaml. The overall score determines whether the rollout continues or stops.&#x20;

The report includes a detailed analysis of logs and metrics between the baseline and the new release. Refer to the images below. To understand the log and metric analysis and provide guided input, refer to the user guide [**here**](https://docs.opsmx.com/opsmx-intelligent-software-delivery-isd-platform-argo/getting-started/smarter-rollout-with-opsmx-automated-analysis-user-guide).&#x20;

<figure><img src="https://lh3.googleusercontent.com/kj0XBltU1hyhqRjIStmf1xkjLdH-R1oLTNOrPf9XNAdml2OVHIKn5oKxUE-lWAx9AEJMvv2qU2PxjA1ieaaHxl960D7bNbYDvo9rmT1K4oEEB-wlR_gXOCXWuQXTOPIu_PBejAVWlwOWipwlJ0pagOlbeFEsaNdzF4bzIOsMizQh1AnejBv5Lf6M95Buow" alt=""><figcaption></figcaption></figure>

<figure><img src="https://lh6.googleusercontent.com/fhUMN68lXdFr4B7p4qDXm51BVnOHYt91TUrS8UlmJSt2edRKFM4zzP_ImbIjKIAc3avf3ZZYwL-ie7bOEtGyJVbsFFi9H_VnKDAwNtnREOrnBL-vE_Td2uUjaFbFjjTJUyLMcXOIkjNXYTvVsfbWiDc" alt=""><figcaption></figcaption></figure>
