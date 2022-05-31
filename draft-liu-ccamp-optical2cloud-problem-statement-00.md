---
coding: utf-8

title: Problem Statement and Gap Analyis for Connecting to Cloud DCs via Optical Networks

abbrev: Cloud Optical Problem Statement
docname: draft-liu-ccamp-optical2cloud-problem-statement-00
workgroup: CCAMP Working Group
category: std
ipr: trust200902

stand_alone: yes
pi: [toc, sortrefs, symrefs, comments]

author:
  -
    name: Sheng Liu
    org: China Mobile
    email: liushengwl@chinamobile.com
  -
    name: Haomian Zheng
    org: Huawei Technologies
    email: zhenghaomian@huawei.com
  -
    name: Aihua Guo
    org: Futurewei Technologies
    email: aihuaguo.ietf@gmail.com
  -
    name: Yang Zhao
    org: China Mobile
    email: zhaoyangyjy@chinamobile.com

--- abstract

   Many applications, including optical leased line, cloud VR and computing cloud, benefit from the network
   scenario where the data traffic to cloud data centers (DCs) is carried over optical network end to end.
   This document describes the problem statement and requirements, and presents a gap analysis on existing
   control plane protocols for supporting this network scenario.

--- middle

# Introduction

   Cloud applications are becoming more popular and widely
   deployed in enterprises and vertical industries.  Organizations with
   multiple campuses are interconnected together with the remote cloud
   for storage and computing.  Such cloud services demands the underlying
   network to provide high quality experiences, such as high availability, 
   low latency, on-demand bandwidth adjustments, and so on.

   Cloud services have been carried over IP/Ethernet-based aggregated networks for years. MPLS-based
   VPNs with traffic engineering (TE) are usually used to achieve desired service quality. 
   Provisioning and management of MPLS VPNs is known to be complicated and typically involves manual
   TE configurations across the network.

   To improve the performance and flexibility of the aggregated networks, OTN (Optical Transport Network)
   networks are introduced to complement the IP/Ethernet-based aggregation networks to enable full-fiber
   connections. This scenario is described in the Fifth Generation Fixed Network Architecture 
   by the ETSI F5G ISG []. OTN can be used to provide high quality carrier services in addition to
   the traditional MPLS VPN services. OTN provides TDM-based connections with no queueing or scheduling
   needed, with an access bandwidth granularity of 1.25Gbps, i.e. ODU0 (Optical Data Unit) and above.
   This bandwidth granularity is typically more than what a single application would demand. 
   Therefore, user traffic usually needs to be aggregated before being carried forward through the network. However,
   advanced OTN techonologies developed in ITU-T work items have aimed to enhance OTN to support services
   of small granularity as low as 2Mbps with the introduction of the Optical Service Unit (OSU). 
   This enhancement makes OTN an even more suitable solution for bearing cloud traffic with high quality and bandwidth granularity close
   to what an IP/Ethernet-based network could offer.

   Many cloud-based services that require high bandwdith, deterministic service quality and flexible access could potentially
   benefit from the network scenario of using OTN-based aggregation networks to interconnect cloud DCs. For example, intra-city Data Center Interconnects (DCIs), which communicate with each other to supports public and/or private cloud services, can use OTN for via intra-city DCI networks to ensure ultra-low latency and on-demand provisioning of large bandwidth connections for its Virtual Machine (VM) migration services. Another example is the high quality private line, which can be provided over OTN dedicated connections with high security and reliability for large enterprises such asfinancial, medical centers and education customers. Yet another example is the Cloud Virtual Reality (VR) services, which typcially requires high bandwidth (e.g. over 1Gbps for 4K or 8k VR) links with low latency (e.g. 10ms or less) and low jitter (e.g. 5ms or less) for rendering with satisfactory user experience. These network properties required for cloud VR services can be typically offered by OTN networks in high quality comparing with IP/Ethernet based networks.

   {{?I-D.ietf-rtgwg-net2cloud-problem-statement}} and
   {{?I-D.ietf-rtgwg-net2cloud-gap-analysis}} present a detailed analysis on
   the coordination requirements between IP-based networks and cloud DCs. This document complements the analysis by further
   examining the requirements and gaps when accessing cloud DCs through OTN networks, from the control plane perspective.
   Data plane requirements are out of the scope of this document.

## Requirements Language
   The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT",
   "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and
   "OPTIONAL" in this document are to be interpreted as described in BCP
   14 {{!RFC2119}} {{!RFC8174}} when, and only when, they appear in all
   capitals, as shown here.

# Requirements and Gap Analysis

## Multi-cloud Access

   Cloud services are deployed in geographically distributed means for scalability and resilliency 
   considerations, and they are usually hosted by multiple interconnected data
   centers (DCs). DCs are traditionally interconnected through Layer 2/3 switches or routers
   with full mesh connectivity. To improve interaction
   efficiency as well as service experience, OTN is also evaluated as an
   option to be used for DC interconnection. This network scenario is illustrated in 
   {{fig-cloud-access-optical}}.

~~~~
      //------\\                                               /----\
    ||Enterprise|\\                                          |Vertical|
    ||   CPE    || \\        ------------          +-----+   /|Cloud |
      \\------//     \ +---*/            \*---+    |Cloud| //  \----/
                       |O-A|              |O-E|----+ GW  |/
                       +---+              +---+    +-----+
                      |      OTN Networks      |
      //-----\\       ++---+              +---+    +-----+     /-----\
    || Vertical||-----+ O-A|              |O-E|----+Cloud|---||Private||
     |   CPE   |      +----*\            /*---+    | GW  |    | Cloud |
      \\-----//              ------------          +-----+     \-----/

~~~~
{: #fig-cloud-access-optical title="Multi-cloud access through OTN Network"}

   A customer application is connected to the cloud via one of the Customer Premises
   Equipment (CPE), and access cloud services that are typically hosted in multiple clouds
   attached to different cloud gateways. L2VPN or L3VPN are used as overlay services
   on OTN network to support multi-cloud access. Serving as an overlay, the OTN networks should
   provide the capability of creating different types of connections, including 
   point-to-point(P2P), point-to-multipoint(P2MP) and multipoint-to-multipoint (MP2MP) connections to support
   diverse L2VPN or L3VPN services.

   OTN connections are point to point by nature on the data plane. To support P2MP and MP2MP services, multiple 
   P2P OTN connections can be established between each source and destination pair. The routing and signaling protocols for OTN need to coordinate these OTN connections to ensure they are routed with proper diverse paths to meet resilliency and path quality constraints.

   {{!RFC4461}} defines the requirements for establishing P2MP MPLS label switched paths (LSPs). {{!RFC6388}} describes
   extensions to the Label Distribution Protocol (LDP) for the setup of P2MP and MP2MP LSPs in MPLS Networks. The generic
   rules introduced by these work also apply to OTN networks, however, proper protocol extensions are still required for establishing P2MP and MP2MP connetctions with OTN resources, i.e. time slots.
 
## Service-awareness
   
   Service packets are encapsulated into L2VPN or L3VPN and routed to cloud DCs without being aware of 
   the existence of underlying OTN networks. To support the routing protocol to choose the best path to 
   cloud DCs, it requires the underlying OTN edge nodes to be capable of understanding the incoming traffic type 
   and quality-of-service (QoS) requirements, such as bandwidth and delay, and convey the routing protocol information to the
   right outgoing cloud gateway following the best feasible connection path in the OTN network. This allows the 
   routing protocol for packets to make the best routing choice which is reflected onto the best possible underlay path.
   
   At the OTN edge node, packets can be classified by their 



## Deterministic performance

   Accessing cloud-based services requires deterministic performance from
   the underlay optical networks in order to achieve good user experience. 
   Connections built on optical networks need to be deterministic in many 
   quality factors, such as end-to-end latency, delay jitter, bandwidth,
   and availaility supported by end-to-end protection and restoration. This
   deterministic performance are hard to reach on shared resources
   but can be achieved relatively easier on TDM-based optical networks.
   
   Traditionally in an optical network, connections are pre-configured and
   the speed of dynamic restoration and reconfiguration of connections are
   in the order of several hundred milliseconds to several minutes. The control
   and management plane of the optical network should be enhanced to significantly
   improve the speed of connection operations and be able to convey accurate
   estimate of the performance to the upper layer to achieve end-to-end
   deterministic performance. Extensions to existing control plane and 
   management interfaces are likely needed to support this capability.

   
## High-performance and high-reliability

   To support the above-mentioned applications some of the network
   properties are critical to promise the Quality of Services (QoS).
   For instance, high bandwidth (e.g. larger than 1 Gbps), low latency
   (e.g. no more than 10 ms) and low jitter (e.g. no more than 5 ms),
   are required for Cloud VR.  In addition, small-granularity container
   is required to improve the efficiency of the networks.

   It is also critical to support highly reliable DCI for cloud
   services.  With advanced optical transport network protection and
   automatic recovery technologies, services can still run properly even
   fiber cuts occur in the DCI network.  Specific protection and
   restoration schemes are required, to provide high reliability for the
   networks.

# Manageability Considerations

   TBD

# Security Considerations

   TBD

# IANA Considerations

   This document requires no IANA actions.

--- back

{: numbered="false"}

# Acknowledgments

   TBD

{: numbered="false"}
