# Creating and Applying an ACL

## Project Overview

The lab focuses on creating and applying an Access Control List (ACL) in a Cisco networking environment. ACLs are used to control traffic according to defined rules, allowing network administrators to permit or deny specified traffic flows.

The practical exercise provides experience with configuring network access policies through Cisco IOS commands and verifying their behavior using connectivity tests and configuration inspection.

## Objectives

The objectives of this lab are to:

- Understand the purpose of Access Control Lists in network administration.
- Create an ACL using the required Cisco IOS syntax.
- Apply the ACL to the appropriate interface or configuration context.
- Understand how ACL rule order affects traffic filtering.
- Verify ACL configuration and application using IOS show commands.
- Test permitted and denied traffic to evaluate the access policy.
- Troubleshoot configuration issues using a structured approach.

## Technologies and Tools

| Technology | Purpose |
|---|---|
| Cisco Packet Tracer | Network simulation and configuration |
| Cisco IOS CLI | Router and network-device configuration |
| IPv4 | Network addressing and traffic identification |
| Access Control Lists (ACLs) | Traffic filtering and access control |
| ICMP / Ping | Connectivity testing, where applicable |

## Implementation

### 1. Review the Network

Before creating an ACL, review the supplied topology and identify:

- The relevant routers and switches.
- The source network or host whose traffic must be controlled.
- The intended destination network or host.
- The interface through which the relevant traffic travels.
- The specific traffic that should be permitted or denied.

Confirm basic connectivity before applying filtering rules so that later test results can be compared against the original network behavior.

### 2. Create the ACL

Create the ACL according to the requirements in the lab instructions.

Cisco IOS supports different ACL types, including standard and extended ACLs. The correct type depends on the criteria specified by the exercise.

A standard ACL primarily filters according to source IPv4 addresses. An extended ACL can filter using source and destination addresses, protocols, and other supported traffic criteria.

The exact ACL type, name or number, and entries should match the configuration used in the submitted Packet Tracer file.

### 3. Apply the ACL

An ACL must be applied at the correct location and in the appropriate direction to affect traffic as intended.

The configuration should identify:

- The interface or configuration context where the ACL is applied.
- Whether filtering is inbound or outbound, where interface application is used.
- The relationship between the traffic flow and the selected interface.
- Any additional permit statements needed to preserve required connectivity.

ACL entries are evaluated in order, from top to bottom. Traffic that does not match an explicit permit entry is subject to the ACL's implicit deny behavior.

### 4. Verify the Configuration

Use the relevant Cisco IOS verification commands to confirm that the ACL exists and is applied correctly.

Common verification commands include:

```text
show access-lists
show ip interface
show running-config
```

Use the commands supported by the device model and IOS version in Packet Tracer.

The verification process should confirm both the ACL entries and their application point. Seeing an ACL in the configuration alone does not prove that it is filtering the intended traffic.

## Testing and Verification

Test the network according to the traffic requirements in the course activity.

| Test | Expected behavior | Actual result |
|---|---|---|
| Required permitted traffic | Communication succeeds | Record the observed result |
| Required denied traffic | Communication is blocked | Record the observed result |
| Unaffected traffic | Required communication remains available | Record the observed result |
| ACL verification | Correct entries and application are visible | Record the observed result |

Use the actual source and destination addresses specified in the lab when conducting tests. Do not mark a test as successful until the observed result has been checked.

### Evidence

Add screenshots from the completed lab to an `images/` directory.

Recommended evidence includes:

- The Packet Tracer topology.
- The ACL configuration.
- The interface or configuration context where the ACL is applied.
- Relevant connectivity tests.
- ACL verification output.

Suggested directory structure:

```text
creating-and-applying-an-acl/
├── README.md
├── Creating-and-Applying-an-ACL.pkt
└── images/
    ├── topology.png
    ├── acl-configuration.png
    ├── acl-application.png
    └── verification.png
```

Adjust the filenames to match the files included in the repository.

## Key Concepts Learned

### ACL Processing

ACL entries are processed sequentially. Rule ordering is important because an earlier matching entry determines how the packet is handled.

### Interface and Direction

The placement and direction of an ACL determine which traffic is examined. An ACL applied inbound evaluates traffic as it enters an interface, while an outbound ACL evaluates traffic as it leaves an interface.

### Implicit Deny

Cisco IOS ACLs include an implicit deny at the end of the list. Required traffic must therefore be explicitly permitted where appropriate; otherwise, it may be blocked.

### Verification and Troubleshooting

Effective troubleshooting requires checking the ACL entries, confirming where the ACL is applied, and testing the relevant traffic flows. Configuration inspection and connectivity testing provide complementary evidence.

## Challenges and Troubleshooting

When troubleshooting an ACL, use a methodical process:

1. Verify that the source and destination addresses are correct.
2. Confirm that the relevant interfaces are operational.
3. Check the ACL entries and their order.
4. Verify that the ACL is applied to the correct interface or context.
5. Confirm the filtering direction.
6. Repeat the required permitted and denied traffic tests.
7. Inspect ACL match counters or other available verification output, where supported.

This process helps distinguish an ACL problem from an addressing, routing, or interface issue.

## Conclusion

This lab provides practical experience with configuring and verifying network access controls in a simulated Cisco environment. It reinforces the importance of understanding traffic flow, ACL processing order, application location, and the need to validate security rules through testing.

These skills are relevant to network support, infrastructure administration, and troubleshooting tasks where access policies must be implemented without unintentionally disrupting legitimate communication.

## Skills Demonstrated

- Cisco Packet Tracer
- Cisco IOS CLI
- IPv4 networking
- Access Control Lists
- Network traffic filtering
- Interface configuration
- Connectivity testing
- Network troubleshooting
- Technical documentation

## Project Files

- **Packet Tracer file:** The lab topology and configuration used for the exercise.
- **README.md:** Project overview, implementation notes, and verification documentation.
- **images/:** Screenshots supporting the documented configuration and test results.

