---
coding: utf-8

title: A YANG Data Model for Optical Resource Performance Monitoring

abbrev: Performance Monitoring YANG
docname: draft-yu-ccamp-optical-resource-pm-yang-02
workgroup: CCAMP Working Group
category: std
ipr: trust200902

stand_alone: yes
pi: [toc, sortrefs, symrefs, comments]

author:
  -
    name: Chaode Yu
    org: Huawei Technologies
    email: yuchaode@huawei.com
  -
    name: Fabio Peruzzini
    org: TIM
    email: fabio.peruzzini@telecomitalia.it
  -
    name: Yanlei Zheng
    org: China Unicom
    email: zhengyanlei@chinaunicom.cn
  -
    name: Italo Busi
    org: Huawei Technologies
    email: italo.busi@huawei.com
  -
    name: Aihua Guo
    org: Futurewei Technologies
    email: aihuaguo.ietf@gmail.com
  -
    name: Victor Lopez
    org: Nokia
    email: victor.lopez@nokia.com
  -
    name: Xing Zhao
    org: CAICT
    email: zhaoxing@caict.ac.cn

normative:
  TMF-518:
    title: Resource Performance Management
    author:
      org: TM Forum (TMF)
    date:  2011
    seriesinfo: TMF518_RPM
    target: https://www.tmforum.org/resources/collection/mtosi-4-0

  ITU-T_G.7710:
    title: Common equipment management function requirements
    author:
      org: International Telecommunication Union
    date:  October 2020
    seriesinfo: ITU-T Recommendation G.7710
    target:
https://www.itu.int/rec/T-REC-G.7710/en

  ITU-T_G.874:
    title: Management aspects of optical transport network elements
    author:
      org: International Telecommunication Union
    date:  October 2020
    seriesinfo: ITU-T Recommendation G.874
    target:
https://www.itu.int/rec/T-REC-G.874/en

--- abstract

This document defines a YANG data model for performance Monitoring in optical networks which provides the functionalities of performance monitoring task management, TCA (Threshold Crossing Alert) configuration and current or historic performance data retrieval. This data model should be used in the northbound of PNC.

--- middle

# Introduction

Performance monitoring is a basic function of optical networks management. With it, operators can proactively detect the running state of devices, identify major risks in advanced and avoid users' complaints.

As before, TMF has defined interfaces for performance management through traditional protocols, such as CORBA and MTOSI. With the development of SDN technologies and the using of RESTCONF interfaces, it becomes a widespread requirement to use RESTCONF protocol to support performance monitoring.

Using RESTCONF does not mean changing existing performance monitoring requirements or scenarios. On the contrary, since O&M is very important, many operators' O&M departments tend to be conservative. Mostly, they are fear of introducing issues into their network due to the protocol changes and O&M habits changes. Therefore, our document prefers to use the new protocol to support legacy functionality.

Traditional performance management involves control of performance monitoring, setting collectors on monitored objects, and obtaining performance data in different periods. The data can be current data on devices or processed by PNC, and historical performance data. TCA can be also configured by performance monitoring tasks.

ITU-T also has a lot of related recommendations for performance monitoring. Such as session 10 of {{ITU-T_G.7710}} has provided very detail description of performance management application and functions. {{ITU-T_G.7710}} doesn’t provide a UML model or YANG module to guide the implementation of PNC while this document does.

Section 10.2 of {{ITU-T_G.874}} provides a list of OM indicators which are on the NE level, which could help to reduce duplicated definition of performance indicators.

Currently, there are some existing documents related to performance monitoring in IETF, but there is no overlap with our current work. For example:

{{?I-D.ietf-teas-actn-pm-telemetry-autonomics}} provides a YANG data model that describes performance monitoring and scaling intent mechanisms for TE-Tunnels and Virtual Networks(VNs). VN is determinate to be used in CMI (CNC-MDSC Interface) level and TE tunnel is more service or connection related. Our data model is proposed to be used in MPI (MDSC-PNC Interface) level and this performance monitoring is performed on network resources, such as network element, board, fiber, port or TTP (Tunnel Termination Point), which are not included in {{?I-D.ietf-teas-actn-pm-telemetry-autonomics}}.

{{?I-D.ietf-opsawg-yang-vpn-service-pm}} defines a YANG data model for performance monitoring of both network topology layer and overlay VPN service topology layer. VPN service is more IP-specific and not adopted in Optical domain. And the data model in this document is augmenting network model. If the client wants to retrieve performance data of a link by RESTCONF, the URL would be probably same with the URL of topology retrieval. This may need some special mechanisms to make the client and server differentiate the using scenarios. This is not quite compliance with current O&M habits of Optical technology.

{{?I-D.zheng-ccamp-client-pm-yang}} provides a performance monitoring YANG data model on client signal level. It is also not operated on resource level which is not compliance with the existing O&M habits. This performance monitoring solution is more user-oriented and can be used for more automatic O&M scenarios in the future. However, there is not a complete closed-loop O&M solution at the service layer and it has not been accepted by all operators' O&M departments, resource-based performance monitoring is still required.

The YANG data model defined in this document conforms to the Network Management Datastore Architecture (NMDA) defined in {{!RFC8342}}.

## Terminology and Notations
Refer to {{!RFC7446}} and {{!RFC7581}} for the key terms used in this document.  The following terms are defined in {{!RFC7950}} and are not redefined here:
*  client
*  server
*  augment
*  data model
*  data node

The following terms are defined in {{!RFC6241}} and are not redefined here:
*  configuration data
*  state data

The following terms are defined in {{?RFC8454}} and are not redefined here:
*  CMI
*  MPI
*  MDSC
*  CNC
*  PNC

//To Be Added: some explanation of performance indicator

## Tree Diagram
A simplified graphical representation of the data model is used in Section 3 of this document.  The meaning of the symbols in these diagrams are defined in {{!RFC8340}}.


## Prefix in Data Node Names
In this document, names of data nodes and other data model objects are prefixed using the standard prefix associated with the corresponding YANG imported modules, as shown in the following table.

| Prefix | Yang Module                       | Reference |
| ------ | --------------------------------- | --------- |
| optrpm | ietf-optical-resource-pm          | RFC XXXX  |
|optrpm-types| ietf-optical-resource-pm-types    | RFC XXXX  |
| yang   | ietf-yang-types                   |{{!RFC6991}}|
{: #tab-prefixes title="Prefixes and corresponding YANG modules"}

RFC Editor Note:
Please replace XXXX with the RFC number assigned to this document.

# YANG Data Model for Optical Performance Monitoring
According to the business requirements stated in {{TMF-518}}, resource performance management requirements include:
* The Interface shall support the control of performance monitoring in the network. This includes PM control, e.g., the enabling and disabling of PM collection and Threshold Crossing Alerts (TCA) control, e.g., the enabling and disabling of TCA generation.
* The Interface shall support the retrieval of current and historical performance measurements for network resources.
* The Interface shall support the distribution of TCAs to subscribed OSs.

For these requirements of PM, there are three group of interfaces are defined in TMF, including PerformanceManagementControl, PerformanceManagementRetrieval and ThresholdCrossingAlertControl.

## Generic Resource
The definition of most performance monitoring interfaces in TMF is quite generic. And it is also similar that the functionalities of both alarm and performance monitoring can be defined with a generic structure, regardless of what kind of object is operated on. Therefore, our data model prefer to follow this approach. And when defining our data model, we get refer to {{?RFC8632}}, in which a generic alarm data model is provided. In {{?RFC8632}}, a union type of resource attributes is defined as the source of alarm. So we also reference this structure in our data model.

Additionally, we extend a resource-type attribute to indicate what exact kind of resource is. Firstly, this attribute can help to improve interaction efficiency between PNC and MDSC. The MDSC may quickly identify the resource type from this attribute, without parsing the resource identifier or searching the resource id in the whole resource. On the other hand, it will be unified with the existing IETF topology and inventory objects and form a complete interface system.

~~~~ ascii-art
module: ietf-optical-resource-pm
   +--rw performance-monitoring
      +--rw resources
         +--rw resource-list* [resource]
            +--rw resource             union
            +--ro resource-type?       identityref
~~~~

## PerformanceManagementControl

There are three interfaces defined in TMF for this group, including:
* clearPerformanceMonitoringData: This interface shall allow MDSC to clear or reset the performance register on TPs or NEs. This clearing operation means to reset the data value to the current value. The monitoring job is still running on.
* disablePerformanceMonitoringData: This interface shall allow MDSC disable PM data collection on a list of TPs or NEs.
* enablePerformanceMonitoringData: This interface shall allow MDSC enable PM data collection on a list of TPs or NEs.

### clearPerformanceMonitoringData

Because the performance data can be changed frequently, so the current and history performance data are not suitable to define in a data model. Clearing performance monitoring data is more suitable to define an RPC to perform this operation. Especially according  to the MTOSI interface, this clearing operation can be operated on multiple objects in one request. Currently there is not such an approach to support this kind of flexible method if using data API. Therefore we define a RPC operation to support this function.

~~~~ ascii-art
rpcs:
   +---x clear-performance-monitoring-data
      +--ro input
      |  +--ro resources*   leafref
      +--ro output
         +--ro failed-resources*   leafref
~~~~

If there are any resources failed to clear performance monitoring data, their identifier should be returned by the failed-resources leaf list. An empty list indicates that this operation was performed successfully.

### enablePerformanceMonitoringData & disablePerformanceMonitoringData

For interfaces of enable/disable performance monitoring data, we introduce a monitor task to do this control. In the monitoring task, MDSC should be able to configure what period and what kind of performance data it want to collect. The disabling, enabling operation can be satisfy by changing the admin status which includes disabled, enabled. The change's result will affect the task status accordingly.

~~~~ ascii-art
module: ietf-optical-resource-pm
   +--rw performance-monitoring
      +--rw monitor-tasks
      |  +--rw monitor-task* [task-id]
      |     +--rw task-id          yang:uuid
      |     +--rw resource-id?     leafref
      |     +--ro resource-type?   identityref
      |     +--rw task-name?       string
      |     +--rw admin-status?    enumeration
      |     +--ro task-status?     enumeration
      |     +--rw task-cfg
      |        +--rw period?       identityref
      |        +--rw indicators
      |           +--rw indicator* [indicator-name]
      |              +--rw indicator-name          string
      |              +--rw indicator-value-unit?   string
~~~~

## PerformanceManagementRetrieval

There are six interfaces are defined in TMF for this group, including:
* getAllCurrentPerformanceMonitoringData: This interface should allow MDSC to retrieve performance data on a series of termination point.
* getHistoryPerformanceMonitoringData: This interface should allow MDSC to retrieve some historic performance data on a series of termination point in a time period through FTP.
* getAllPerformanceMonitoringPoints: This interface should allow MDSC to retrieve all the performance monitoring points in a specified termination point or NE.
* getHoldingTime: This interface should allow MDSC to retrieve how many hours PM data records are held in PNC.
* getMePerformanceMonitoringCapabilities: This interface should allow MDSC to retrieve what parameters are supported by a specified NE or termination point.
* getProfileAssociatedTerminationPoints： The Interface shall allow MDSC to retrieve the names of all the TPs known to the PNC that are associated with a specified Threshold Crossing Alert (TCA) Parameter Profile.

### getAllCurrentPerformanceMonitoringData & getHistoryPerformanceMonitoringData

For the retrieval of current/historic performance data, we consider these data are not suitable to define in a data model. Because performance data can be changed frequently and if we follow that approach, according to the requirement of on-change notification in YANG-push {{?RFC8641}}, once the performance data changes, the PNC should trigger a notification to the MDSC, there would be great amount of notifications reported in the big network. These great amount of notifications will bring great pressure to both PNC and MDSC. People don't use notification to get performance data in real network.

These two retrieval interfaces are usually invoked on-demand. It is also hard to support retrieving performance data of multiple resources by data model in one request. And for historic performance data retrieval, there could be a requirement to specify the start time and end time. It is not quite flexible to support this requirement by data model neither.

So we suggest to define two RPCs to accomplish these two interfaces' function.

~~~~ ascii-art
rpcs:
   +---x get-all-current-pm-data
   |  +--ro input
   |  |  +--ro resources*   leafref
   |  +--ro output
   |     +--ro pm-data
   |        +--ro pm-data-list* [resource]
   |           +--ro resource          leafref
   |           +--ro collect-time?     yang:date-and-time
   |           +--ro resource-type?    identityref
   |           +--ro indicator-data
   |              +--ro indicator-data-list* [indicator-name]
   |                 +--ro indicator-name          string
   |                 +--ro indicator-value?        string
   |                 +--ro indicator-value-unit?   string
   +---x get-historic-pm-data
      +--ro input
      |  +--ro resources*    leafref
      |  +--ro start-time?   yang:date-and-time
      |  +--ro end-time?     yang:date-and-time
      +--ro output
         +--ro pm-data
            +--ro pm-data-list* [resource]
               +--ro resource          leafref
               +--ro collect-time?     yang:date-and-time
               +--ro resource-type?    identityref
               +--ro indicator-data
                  +--ro indicator-data-list* [indicator-name]
                     +--ro indicator-name          string
                     +--ro indicator-value?        string
                     +--ro indicator-value-unit?   string
~~~~

### getAllPerformanceMonitoringPoints & getHoldingTime & getMePerformanceMonitoringCapabilities

These three interfaces allow MDSC to retrieve performance monitoring points, holding time, and performance monitoring capabilities (indicators) as per a specific resource. We consider that these information are all performance monitoring capabilities in a broad sense. These information should be defined under the resource node.

~~~~ ascii-art
module: ietf-optical-resource-pm
      +--rw resources
         +--rw resource-list* [resource]
            +--rw resource             union
            +--ro resource-type?       identityref
            +--rw holding-time?        uint8
            +--rw pm-parameter-list* [layer-rate]
            |  +--rw layer-rate        identityref
            |  +--rw indicator-name*   string
            +--rw sub-resources*       leafref
~~~~

The sub-resources is list of the performance monitoring points' identifier. And the pm-parameter-list is the indicators that can be supported by this specific resource.

### getProfileAssociatedTerminationPoints

For the TCA related definition, it can be found in the next session. A TCA profile can be associated with a lot of resources, so we don't defined a  resource list under the profile instance to avoid reporting some unnecessary  notifications while the resource instances in this list have been changed. We define a RPC operation to support this flexible retrieval request.

~~~~ ascii-art
rpcs:
   +---x get-profile-associated-termination-points
      +--ro input
      |  +--ro profile-id?   leafref
      +--ro output
         +--ro resource-list*   leafref
~~~~

## ThresholdCrossingAlertControl

There are nine interfaces are defined in TMF for this group, including:
* createTcaParameterProfile: This interface should allow MDSC to create a new TCA parameter profile. This profile can be applied to the resources.
* deleteTcaParameterProfile: This interface should allow MDSC to delete an existing TCA parameter profile which should not have been used by any resources.
* enableThresholdCrossingAlert: This interface should allow MDSC to turn on TCA reporting on a list of TPs and NEs if the reporting was turned off before.
* disableThresholdCrossingAlert: This interface should allow MDSC to turn off TCA reporting on a list of TPs and NEs.
* getAllTcaParameterProfiles: This interfaces should allow MDSC to retrieve all TCA parameter profile that are being managed by a specified NE.
* getTcaParameterProfile: This interface should allow MDSC to retrieve all the parameters of a specified TCA parameter profile.
* getTcaTpParameter: This interface should allow MDSC to retrieve the PM threshold values on a TP.
* setTcaParameterProfile: This interface should allow MDSC to configure all threshold values of a TCA parameter profile.
* setTcaTpParameter: This interface should allow MDSC to modify the values of TCA Parameters on a TP

To be summarized, there are four main requirements for Threshold Crossing Alert Control:
* Creation/retrieval/deletion/updating of TCA profile;
* Enabling/disabling TCA reporting on the resource;
* Configuring TCA on the resource by associating an existing profile;
* Configuring TCA on the resource by detailed parameters.

And for the TCA parameters, no matter it is configured directly on the resource or by a preset profile, there should not be any differences. The TCA parameter (indicator) should include:
* indicator-name: Name of indicator. This indicator should also include some data processing information, such if it is a maximum, or minimum, or average data .etc.
* threshold-type: This threshold type is used to indicate when the alert will be triggered. By exceeding the upper bound value, or by below the lower bound value.
* period: This period is used to indicate the frequency of the data collection.
* severity: This severity is used to indicate what level of alert would be triggered if cross the threshold.
* indicator-value: The value of threshold.
* indicator-value-unit: The unit of threshold value.

The tree structure for TCA profile:

~~~~ ascii-art
module: ietf-optical-resource-pm
   +--rw performance-monitoring
      +--rw monitor-tasks
      |  +--rw monitor-task* [task-id]
      |     +--rw task-id          yang:uuid
      |     +--......................
      +--rw tca-management
      |  +--rw profiles
      |  |  +--rw profile* [profile-id]
      |  |     +--rw profile-id      yang:uuid
      |  |     +--rw profile-name?   string
      |  |     +--rw tca-cfg
      |  |        +--rw tca-indicator* [indicator-name threshold-type period severity]
      |  |           +--rw indicator-name          string
      |  |           +--rw indicator-value         string
      |  |           +--rw indicator-value-unit    string
      |  |           +--rw threshold-type          enumeration
      |  |           +--rw period                  identityref
      |  |           +--rw severity                identityref
~~~~

The function  of enabling/disabling TCA on the resource can be controlled by an admin-status attributes in "tca" node:

~~~~ ascii-art
module: ietf-optical-resource-pm
   +--rw performance-monitoring
      +--rw monitor-tasks
      |  +--rw monitor-task* [task-id]
      |     +--rw task-id          yang:uuid
      |     +--......................
      +--rw tca-management
      |     +--rw tca* [resource-id]
      |        +--rw resource-id         leafref
      |        +--ro resource-type?      identityref
      |        +--rw admin-status?       enumeration
      |        +--......................
~~~~

If MDSC wants to configure TCA by an existing profile, it can use this applied-profiles structure:

~~~~ ascii-art
module: ietf-optical-resource-pm
   +--rw performance-monitoring
      +--rw monitor-tasks
      |  +--rw monitor-task* [task-id]
      |     +--rw task-id          yang:uuid
      |     +--......................
      +--rw tca-management
      |     +--rw tca* [resource-id]
      |        +--rw resource-id         leafref
      |        +--ro resource-type?      identityref
      |        +--rw applied-profiles
      |        |  +--rw profile* [profile-id]
      |        |     +--rw profile-id    leafref
      |        +--......................
~~~~

MDSC can also configure TCA value directly by the "tca-cfg" structure:

~~~~ ascii-art
module: ietf-optical-resource-pm
   +--rw performance-monitoring
      +--rw monitor-tasks
      |  +--rw monitor-task* [task-id]
      |     +--rw task-id          yang:uuid
      |     +--......................
      +--rw tca-management
      |     +--rw tca* [resource-id]
      |        +--rw resource-id         leafref
      |        +--ro resource-type?      identityref
      |        +--......................
      |        +--rw tca-cfg
      |           +--rw tca-indicator* [indicator-name threshold-type period severity]
      |              +--rw indicator-name          string
      |              +--rw indicator-value         string
      |              +--rw indicator-value-unit    string
      |              +--rw threshold-type          enumeration
      |              +--rw period                  identityref
      |              +--rw severity                identityref
~~~~

# Performance Indicator Introduction

In the session10.2 of {{ITU-T_G.874}}, a performance management information table lists all the PM current data and history data collected on the EMF(Equipment Management Function). Since it is quite mature, this document would not like to do a duplicated research and would like to reference this recommendation. It is suggested to use the existing PM indicator listed in this table only if it is missing.

It is impossible to list all the PM indicator exhaustively, even if ITU-T has done a lot of work. Some new performance indicators would be raised once there are some new requirements and technologies. So in this document we would like to provide String type rather than an explicit type for performance indicator, to have a better compatibility for future extension. Then if there are some new indicators, there is no need to revise this document or create a branch of documents to standardize the PM indicators.

Although this approach seems too flexible, the integration will not be affected if the MDSC and PNC make a good agreement in advanced.


# Optical Performance Monitoring Tree Diagram

~~~~ ascii-art
{::include ./ietf-optical-resource-pm.tree}
~~~~
{: #fig-rpm-tree title="Optical Resource Performance Monitoring tree diagram"
artwork-name="ietf-optical-resource-pm.tree"}

# YANG Model for Optical Performance Monitoring

~~~~ yang
{::include ./ietf-optical-resource-pm.yang}
~~~~
{: #fig-rpm-yang title="Optical Resource Performance Monitoring YANG module"
sourcecode-markers="true" sourcecode-name="ietf-optical-resource-pm@2023-07-04.yang"}

# YANG Model for Optical Performance Monitoring Types

~~~~ yang
{::include ./ietf-optical-resource-pm-types.yang}
~~~~
{: #fig-rpm-type-yang title="Optical Resource Performance Monitoring Types YANG module"
sourcecode-markers="true" sourcecode-name="ietf-optical-resource-pm-types@2023-07-04.yang"}

# Manageability Considerations

  \<Add any manageability considerations>

# Security Considerations

The YANG module specified in this document defines a schema for data that is designed to be accessed via network management protocols such as NETCONF {{!RFC6241}} or RESTCONF {{!RFC8040}}. The lowest NETCONF layer is the secure transport layer, and the mandatory-to-implement secure transport is Secure Shell (SSH) {{!RFC6242}}. The lowest RESTCONF layer is HTTPS, and the mandatory-to-implement secure transport is TLS {{!RFC8446}}.

The NETCONF access control model {{!RFC8341}} provides the means to restrict access for particular NETCONF or RESTCONF users to a preconfigured subset of all available NETCONF or RESTCONF protocol operations and content.

There are a number of data nodes defined in this YANG module that are writable/creatable/deletable (i.e., config true, which is the default). These data nodes may be considered sensitive or vulnerable in some network environments. Write operations (e.g., edit-config) to these data nodes without proper protection can have a negative effect on network operations. Considerations in Section 8 of {{!RFC8795}}are also applicable to their subtrees in the module defined in this document.

Some of the readable data nodes in this YANG module may be considered sensitive or vulnerable in some network environments. It is thus important to control read access (e.g., via get, get-config, or notification) to these data nodes. Considerations in Section 8 of {{!RFC8795}} are also applicable to their subtrees in the module defined in this document.

# IANA Considerations

This document registers following YANG modules in the YANG Module Names registry {{!RFC6020}}.


   name:         ietf-optical-resource-pm
   namespace:    urn:ietf:params:xml:ns:yang:ietf-optical-resource-pm
   prefix:       optrpm
   reference:    RFC XXXX: A YANG Data Model for Optical Performance Monitoring

   name:         ietf-optical-resource-pm-types
   namespace:    urn:ietf:params:xml:ns:yang:ietf-optical-resource-pm-types
   prefix:       optrpm-types
   reference:    RFC XXXX: A YANG Data Model for Optical Performance Monitoring


--- back

# Acknowledgments
{: numbered="false"}

This document was prepared using kramdown.

