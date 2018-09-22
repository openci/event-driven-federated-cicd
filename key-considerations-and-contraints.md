---
description: >-
  Some key considerations and constraints that are taken into account by the
  prototype are detailed below.
---

# Key Considerations and Contraints

### Complex/E2E CI/CD Flows {#docs-internal-guid-b4d1d709-7fff-bc00-74b4-45475daed9ca}

Majority of open source projects work pretty near to SCM and do not go far from where the development happens. This results in difficulty when it comes to E2E Integration and Testing since the CI/CD Systems are not built for this type of scenarios.  


Recently, communities started to think about and work on system level integration and testing. This naturally requires evolution of the CI/CD Systems developed and used by these communities and within open source ecosystem in general to support CI/CD flows that span across multiple communities and pass through multiple community boundaries.  


It is important for open source communities to work on these challenges together in order to establish common understanding and methodology so the efforts to bring up complex CI/CD Flows can be reduced significantly.

### Loose Coupling

Messaging makes applications loosely coupled by communicating asynchronously, which also makes the communication more reliable because the two applications do not have to be running at the same time. Messaging makes the messaging system responsible for transferring data from one application to another, so that applications can focus on what data they need to share as opposed to how to share it. \[[1](http://www.enterpriseintegrationpatterns.com/)\]

### Event Broadcasting

In software architecture, publish-subscribe is a messaging pattern where senders of messages do not program the messages to be sent directly to specific receivers but instead categorize published messages into classes without knowledge of which subscribers, if any, there may be. Similarly, subscribers express interest in one or more classes and only receive messages that are of interest, without knowledge of which publishers, if any, there are. \[[2](https://en.wikipedia.org/wiki/Publish%E2%80%93subscribe_pattern)\]

### Being Agnostic to the Technology and Processes

Communities use various SCM Systems \(Gerrit, Gitlab, Github\), CI Tooling \(Jenkins, Zuul, Gitlab CI\) and Artifact Repositories \(Nexus, Google Cloud Storage\).  


Due to this, it is important to have an agnostic approach to the underlying technologies used by the community CI/CD Systems. As long as these systems adhere to the \(yet to be defined\) messaging protocol, the underlying technology used by them is not a concern in event driven CI/CD since it is up to the communities to use the tooling they have in a way that benefits them most.  


The only requirement from the tooling perspective is that the tools used by the communities are able to publish and consume events.  


Apart from the tooling used to construct the CI pipelines, the processes between the communities differ. It is important for CI/CD Systems to be agnostic to the processes followed by the communities as well in order to achieve event driven end-to-end integration flows.  


In the end, the machines communicate via events that conform to the protocol and take appropriate actions.

### Scalability

It is important to keep the scalability aspects in mind as well and treat the CI/CD Systems as decentralized systems.  


This can be achieved by the event broadcasting, enabling the distribution and management of CI activities in a scalable manner and removing the bottlenecks that might be introduced by the use of CI tooling and/or the processes and the way of working followed by the communities that are not capable of providing decentralization out of the box.  


Scalability in this case mean both horizontal and vertical which enables the use of event driven

CI/CD both in a single community context or cross community context.

### Flexibility

As mentioned above, it is important to stress the importance of consumers not being aware of other entities who might be interested in what is developed by originating communities. There could be 0..n number of consumers out there but the only requirement on the communities who publish information is to adhere to the messaging protocol and make the information and artifacts that could be consumed by the interested communities publicly accessible.  


Apart from the number of consumers, the consumers may be interested in subset of activities that are happening in other CI/CD flows so they can subscribe to the events published upon the completion of those certain activities. Again, this is not something that requires the knowledge on publishers' side.  


As highlighted above, the main responsibility is on the consumers since apart from adhering the same messaging protocol, they will need to use the information provided by the originating communities in order to extract the metadata to retrieve the artifacts with the desired confidence level/quality stamp from their origin and any other information they may require.  


In summary, the community CI Systems do not tell each other what to do via hooks or RPCs but instead they tell what happened and the necessary actions are taken by the consumers \(prescriptive vs descriptive\). This gives great flexibility to construct CI Pipelines that span across multiple communities.

### Other Considerations and Concerns

On top of what is listed above, there are other considerations the communities need to take into account when it comes to the event driven CI/CD Systems, such as:  


* traceability
* reproducibility
* high availability
* performance
* security

Since this prototype is limited in its scope, the details of these topics are not covered here.

