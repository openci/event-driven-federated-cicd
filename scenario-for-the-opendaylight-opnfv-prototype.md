# Scenario for the OpenDaylight/OPNFV Prototype

Open Platform for NFV \(OPNFV\) facilitates the development and evolution of NFV components across various open source ecosystems. \[[3](https://www.opnfv.org/)\]  


OpenDaylight \(ODL\) is a modular open platform for customizing and automating networks of any size and scale. \[[4](https://www.opendaylight.org/)\]  


As part of the system level integration and testing efforts of OPNFV, different compositions of the reference platform \(scenarios\) are created. One of these scenarios is OpenStack with ODL.  


Very high level CI Flow which starts from a change in ODL Gerrit and goes till the OPNFV system level testing can be seen on the diagram below.

![](https://lh3.googleusercontent.com/WsIavrws54gPhJ-uYa8522OCblrSuD6tWa1j0gzyVy1un1gJWmvtsCNlOkq6M2QRLJgh8EdWxtXYauJSfmCgXtF-RBZAVBw3z2YGn-AoarD30B3nhbqMCxOmXyeWOejIFgnOyspf)

Please note that some details are neglected for the sake of simplicity and things might not reflect the reality in entirely correct way due to differences in how things are done by various projects in OPNFV.  


Event driven CI/CD addresses some of the issues with the current CI/CD between ODL and OPNFV as seen on the diagram above. The prototype attempts to demonstrate how these issues can be addressed via event driven CI/CD as shown on the diagram below.  


![](https://lh3.googleusercontent.com/LVuXODCT4gYNm3lbu9hrfP33-_vsNteDL71pa2vmxC_q7XaUb2Onk2MqnDSzztEYRIecEizWWxgB0opn5FYgwQufhJt75pngu1wDd6KGHNZfKOYV73yQFlLUog34Ei_PR0KHeXkZ)

### Scope of the Prototype

The prototype will use a very limited cross community CI Flow to demonstrate event driven CI/CD and Federation. The 3 main activities that happen in this limited flow are  


* artifact creation by ODL upon successful completion of ODL AutoRelease
* creation of the composition by OPNFV for testing the integrated platform
* applying confidence level upon \(successful\) completion of OPNFV acceptance test

![](https://lh4.googleusercontent.com/McdUfigbvmQGmvp_e421EP9rCBSouuRsM_7W8DUsM43PwoSgOYvlXpabGX_1L8yWYV3o9X92vqxytLrSJWmLsiP83Y0ZRW-pRelnd4RbSJWdekcaUNfEejuPQREcr-f1Y9G5g5fr)

### Activities to be Demonstrated

#### Artifact Creation: ODL CI

ODL RelEng/Autorelease project builds every ODL project from source for every active branch, including master using the corresponding Jenkins jobs. Each of those jobs, when the build is successful, produces build artifacts that include an OpenDaylight distribution. \[[5](http://docs.opendaylight.org/projects/integration-packaging/en/latest/autorelease-builds.html)\] As explained on the documentation, it is not straightforward to find and fetch these artifacts programmatically by the users, for example by OPNFV CI.  


In Event Driven CI, the ODL RelEng/Autorelease project Jenkins jobs can publish events upon the successful completion of the builds and whoever is interested in those events and if they are subscribed, they can consume those events to extract the metadata, locating the latest and greatest version of the ODL Distribution for the branch they may be interested in.

#### Creation of Composition: OPNFV CI

OPNFV needs to ensure the latest ODL Distribution built and made available by ODL RelEng/Autorelease project works fine before they are taken into the main OPNFV CI Flow. In order to achieve that, a simple mechanism to create candidate composition which consists of latest verified version of the OpenStack and the latest ODL Distribution that has been made available by ODL RelEng/Autorelease.  


This can be achieved by subscribing artifact created events published by ODL RelEng/Autorelease for the branch OPNFV is interested in, extracting the metadata such as artifact version, location, etc. and generating/publishing a new event that contains the created composition to be tested which consists of the metadata of verified version of OpenStack and the latest ODL distribution.  


It is important to mention here is that the composition can consist of artifacts, other compositions and artifacts and compositions depending on use case.

#### Confidence Level: OPNFV CI

The jobs in OPNFV CI can subscribe to composition defined events published by OPNFV CI itself this time. By doing this, the jobs on OPNFV CI that are interested in new compositions get triggered, deploying and testing the composed platform using the verified version of OpenStack and the latest version of ODL Distribution.  


Upon completion of the jobs, an event stating the new confidence level the composition gained can be published. Confidence level in this context is a quality stamp that is applied to candidate composition upon successful completion of the corresponding activity in CI. As well as compositions, standalone artifacts could gain \(multiple\) confidence levels while they traverse through different stages within CI Flow which may well be distributed across different communities, passing through multiple community boundaries.  


In this prototype, the confidence level that gets applied to the latest composition containing the latest ODL Distribution built by ODL RelEng/Autorelease means that this version of the distribution is good to use for the next stages within OPNFV CI. If desired, ODL CI can also subscribe to this event and apply a quality stamp on their side stating that the corresponding version of the ODL Distribution successfully passed OPNFV testing and other users can get this version with relatively higher confidence.

### Event Types and Event Metadata to Use for Prototyping

In order to bring up this prototype and have something realistic enough, 3 different event types

to be used during this work are defined. These event types are  


* ArtifactPublished
* CompositionDefined
* ConfidenceLevelModified

Apart from the event types, additional metadata that is common for all events are defined as well.  


* id: unique ID of the event
  * "id": "D10EFFE9-2BC0-4C58-AC38-3D331A195C47"
* time: The time which the event was generated to be published
  * "time": "12:34:56UTC"
* buildUrl: The URL to build job that published this event
  * "buildUrl": "[https://url/to/build/\#](https://url/to/build/#)"
* branch: The branch the artifact or composition is built/defined for.
  * "branch": "master"

In addition to common metadata, the events have additional metadata depending on their types as seen below.  


#### ArtifactPublished event

* artifactLocation: The url pointing to the location on artifact repository
  * "artifactLocation": "https://url/to/artifact"
* confidenceLevel: The confidence level \(quality stamp\) the artifact gained
  * "autoRelease": "SUCCESS"

#### CompositionDefined event

* compositionName: The name of the composition that's defined.
  * "compositionName": "os-odl-nofeature"
* compositionMetadataUrl: The prototype will keep the composition metadata in text files and this url points to that location
  * "compositionMetadataUrl": "https://url/to/compositionMetadata"

#### ConfidenceLevelModified event

* compositionName: The name of the composition that's defined.
  * "compositionName": "os-odl-nofeature"
* compositionMetadataUrl: The prototype will keep the composition metadata in text files and this url points to that location
  * "compositionMetadataUrl": "https://url/to/compositionMetadata"
* confidenceLevel: The confidence level \(quality stamp\) the composition gained
  * "OPNFV\_ACCEPTANCE": "SUCCESS"

Example events can be seen in OpenCI Gitlab Repo. \[[6](https://gitlab.openci.io/openci/prototypes/tree/master/federated-cicd/examples)\]  


Please note that the events types and the metadata documented in this section is solely for prototyping purposes and work is required to create a messaging protocol if Event Driven/Federated CI/CD proposal gets accepted by the community.

### Tooling to Use for Prototyping

The prototype will use openly available/already existing tooling to demonstrate the ideas.  


OPNFV and ODL projects are hosted by Linux Foundation and the projects use Jenkins as part

of their CI/CD Systems so the prototype will also use Jenkins as CI Engine component.  


Jenkins community developed JMS Messaging Plugin \[[7](https://plugins.jenkins.io/jms-messaging)\] which triggers builds using messages published towards topics/queues on message brokers. Apart from consuming messages, it can also publish messages as well.  


The 2 message brokers supported by JMS Messaging Plugin are ActiveMQ and FedMsg. ActiveMQ is chosen to be used due to difficulty bringing up FedMsg during the preparation

of this document. \[[8](http://activemq.apache.org/)\]  


Fetching artifacts will be done using Nexus REST API within a bash script. \[[9](https://blog.sonatype.com/2011/01/downloading-artifacts-from-nexus-with-bash/)\]  


An important thing to highlight here is that the tooling selected for the prototyping work does not mean this is the tooling proposed to be used. They are chosen to get things off the ground and demonstrate the ideas. The important thing to focus on this prototype is to see if the event driven CI/CD helps us address some of the challenges we are facing. If the community forms a consensus around what this prototype demonstrates, further discussions and actions can be taken to clarify the details with regards to tooling, which may or may not be necessary due to loosely coupled nature of the event driven CI/CD.

### Prototype Phases

Prototype will be developed in 2 phases.  


* Phase 1: Hello World: This phase aims to establish communication between ODL and OPNFV CI/CD Systems on sandbox Jenkins instances.
* Phase 2: Real Deal: This phase aims to move the pieces developed during the previous phase to production Jenkins instances and attempt to fetch the artifacts, define compositions, run testing and publish confidence level events.

Please see the backlog on Gitlab. \[[10](https://gitlab.openci.io/openci/prototypes/issues)\] The backlog of the Phase 2 is not developed yet as it depends on the outcome of the Phase 1.  


The community is encouraged to involve in developing the prototype in order to bring even more ideas and try them out for real. The community will be kept up to date regarding the progress with the prototype and everything will be available openly.

