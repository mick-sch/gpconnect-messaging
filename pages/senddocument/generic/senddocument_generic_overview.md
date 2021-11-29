---
title: Overview
keywords: document, use-case
tags: [mesh, itk3, use-case, send-document]
sidebar: senddocument_sidebar
permalink: senddocument_generic_overview.html
summary: "Overview of a potential use of the generic payload"
---

## Introduction ##

When a patient completes an online consultation it is necessary for information from that online consultation to be sent to the appropriate care provider, for example, this could be the patient’s registered GP practice, or a community pharmacy. This is so the online consultation information can be available for the care provider to give the appropriate care to the patient.

The use case listed below are indicative of how Send Document could be used and are not exhaustive.

## Online Consultation ##

###  Patient survey ###

This use case follows the following process:

1.	the patient completes the online consultation, in this case a patient survey (this can be initiated by the patient or requested by the patient's GP)
2.	the Online Consultation (OC) system triages the online consultation based on the content of the consultation and either sends the message to the patient's registered GP practice, or to an alternative care provider, for example a community pharmacy.
3.	the OC system sends the message (containing a PDF of the online consultation) to the care provider
4.  the care provider receives the message and takes the appropriate action


### Admin items ####

This use case follows the following process:

1.	the patient completes the online consultation, in this case a patient wishes to inform their GP that they have a certain type of family medical history
2.	the Online Consultation (OC) system triages the online consultation based on the content of the consultation and sends the message to the patient's registered GP practice
3.	the OC system sends the message (containing a PDF of the online consultation) to the patient's registered GP practice
4.  the patient's registered GP practice receives the message and takes the appropriate action


## Message flows in Online Consultation Report sent to a GP ##

The following diagram illustrates how a message can flow between the sending (originating) system and the receiving care provider via MESH to fulfil this use case:

<div style="max-width:100%;max-height:100%;display:block;margin: 0 auto;" >
	{% include images/senddocument/sequence_oc_gp.svg %}
</div>
<br/>


The diagram above depicts a successful message flow where the receiving care provider (in this example a GP) validates the message successfully and consumes. This involves the following steps:

1.  **MESH Address Lookup**

    1. After the online consultation is complete, the OC system initiates a MESH Address Lookup request for the patient's registered GP practice.
    2. The OC system receives a response containing the MESH mailbox ID for the patient's registered GP practice.

2.  **Online Consultation Report message**

    1. A trigger at the OC system results in a FHIR Message being constructed which includes a PDF describing the online consultation (as a minimum).
    2. The MESH API Send Message function is used to send the message to the mailbox acquired in step 1.
    3. The MESH server places the message in the GP practice's MESH mailbox awaiting collection.
    4. The GP practice uses the MESH API Download Message function to retrieve the message from the MESH mailbox.
    5. The message is processed at the GP practice.

3.  **Infrastructure acknowledgement ITK Response**

    1. When the Online Consultation Report message is passed from MESH for processing at the GP practice, the message is first validated to ensure that its structure is correct. An ITK3 Response message is generated which indicates the success of message processing at a technical level - this is known as an "infrastructure acknowledgement". 
    2. The GP practice uses the MESH API Send Message function to send the acknowledgement message to the MESH server where it awaits collection by the OC system.
    3. The OC system uses the MESH API Download Message function to collect the acknowledgement message. The ITK Response is then available to the OC system components for onward processing.
    4. The acknowledgement message is processed as appropriate by the OC system.

4.  **Business acknowledgement ITK Response**

    1. When the Online Consultation Report message is passed from MESH for processing at the GP practice, after successful message validation, the subject of the message contents are landed in the workflow of the GP practice. An ITK3 Response message is generated which indicates the success of consuming the message - this is known as a "business acknowledgement". 
    2. The GP practice uses the MESH API Send Message function to send the acknowledgement message to the MESH server where it awaits collection by the OC system.
    3. The OC system uses the MESH API Download Message function to collect the acknowledgement message. The ITK Response is then available to the OC system components for onward processing.
    4. The acknowledgement message is processed as appropriate by the OC system.

