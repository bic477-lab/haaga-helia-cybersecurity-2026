# H1 – CyberScience


## A. Science Hunt

### 1. Search Process

**Search information:**
- Search engine: Google Scholar
- Search keywords: `From 5G to 6G cyber security`
- Search date: 24 October 2026
- Result: The selected article appeared as the first search result.
- Article type: Survey / Review Article
- Publication year: 2026

**Selected article:**

Yang, M., et al. (2026). *From 5G to 6G: A Survey on Security, Privacy, and Standardization Pathways*. ACM Computing Surveys, 58(8), Article 193.

DOI: https://doi.org/10.1145/3785467

**Journal verification:**
- Journal: ACM Computing Surveys
- JUFO ID: 50067
- JUFO Level: 3 (Top Level)
- Source: Finnish Publication Forum (JUFO Portal)


- It is related to my professional experience with 4G and 5G networks.
- It is relevant to my master's thesis about the evolution of enterprise connectivity services from 5G to 6G.
- It examines existing and emerging cybersecurity risks.
- It discusses not only technical security solutions but also risk management, standardization and industry developments.

## B. Review the Review

### 1. Key Research Areas


- **Evolution of network security:** How security architectures and protocols developed from previous mobile generations toward 6G.
- **Emerging threats:** Cybersecurity risks associated with AI, autonomous network functions, edge computing and distributed networks.
- **Privacy protection:** Challenges concerning large-scale data collection, AI models, IoT devices and sensing technologies.
- **Risk assessment:** Whether existing frameworks can effectively assess the risks of future 6G networks.
- **Standardization:** The role of 3GPP, ITU, GSMA and O-RAN in developing common security requirements.

### 2. Widely Agreed Findings

- Many cybersecurity threats already present in 4G and 5G are expected to continue in future networks.
- Existing security mechanisms provide an important foundation for 6G, but they require further development.
- AI and automation offer opportunities to improve network monitoring and threat detection while introducing new security risks.
- The growing complexity and distribution of network functions create additional challenges for end-to-end security.
- Security by Design and collaboration among operators, technology providers, researchers and regulators remain important.

**Critical observation:**

These are recurring findings in the reviewed literature. The article does not provide a statistical measurement of scientific agreement, so they should not all be treated as proven facts about future 6G deployments.

### 3. Open Research Questions

Important questions that remain open:

- How can AI-driven network decisions be protected against manipulated data or compromised AI models?
- How can operators accurately estimate cybersecurity risks when 6G technologies have not yet been widely deployed and tested?
- How can existing security risk assessment frameworks be adapted to distributed, autonomous and AI-driven networks?
- How can post-quantum cryptography be introduced without excessive performance and energy costs?
- How can end-to-end security be assured across different vendors, cloud environments and enterprise applications?

**Our critical perspective:**

One important limitation is the lack of large-scale operational evidence from real 6G networks.

Existing knowledge from 5G and 5G-Advanced can help identify possible threats. However, accurate estimation of future risks remains difficult because their likelihood and business impact are not yet well established.

## C. Threat Modeling Quickrun

### 1. Company

**Hypothetical Finnish Mobile Network Operator**

The company provides 5G-Advanced enterprise connectivity services, including private networks, network slicing and connectivity for industrial customers.

It has started using AI-assisted network optimization in parts of its infrastructure.

The company supports customers whose business operations depend on reliable connectivity.

### 2. Crown Jewels

The most important assets are:

- Network availability and business continuity.
- Enterprise customer data and network configurations.
- Integrity of network management and AI optimization systems.
- Customer trust and company reputation.
- Revenue from enterprise connectivity contracts and SLAs.

### 3. Threat Actors – COI

**Capability:** Attackers may have technical knowledge of networks, stolen credentials, access to compromised devices or the ability to manipulate data sources.

**Opportunity:** Increasing automation, shared infrastructure, network interfaces and distributed computing may create opportunities for attacks.

**Intent:** Attackers may aim to disrupt services, demand money, steal information or damage the operator's reputation.

### 4. Risk Assessment

**Assessment period:** One year.

The following numbers are hypothetical estimates for learning purposes. They are not measured probabilities or actual financial figures from a Finnish telecom operator.

| Risk | Annual likelihood | Financial impact if it occurs | Estimated annual risk |
|---|---:|---:|---:|
| R1 – Compromised AI network optimization | 15% | €600,000 | €90,000 |
| R2 – Network slice resource exhaustion | 30% | €200,000 | €60,000 |

**Risk 1 – Compromised AI Network Optimization**

Scenario:
- An attacker compromises a source of information used by an AI-assisted optimization system.
- The manipulated data causes incorrect network resource allocation or configuration decisions.
- Enterprise customers experience service degradation or outages.

Possible consequences:
- Service interruption and recovery costs.
- SLA penalties or customer compensation.
- Investigation and restoration expenses.
- Commercial losses and reduced customer confidence.

Calculation:

15% × €600,000 = **€90,000 expected annual loss**

**Risk 2 – Network Slice Resource Exhaustion**

Scenario:
- An attacker generates malicious traffic against an enterprise network slice.
- Automatic resource scaling consumes shared computing or network capacity.
- Other enterprise slices experience degraded performance or service interruptions.

Possible consequences:
- Reduced service availability.
- SLA violations.
- Additional operational expenses.
- Customer dissatisfaction.

Calculation:

30% × €200,000 = **€60,000 expected annual loss**

### 5. META Decision

**Highest estimated risk: R1 – Compromised AI Network Optimization**

Proposed strategy: **Mitigate**

Possible controls:
- Validate and monitor data used by AI optimization systems.
- Restrict access to AI models and network management functions.
- Require human approval for high-impact configuration changes.
- Monitor abnormal AI decisions and network performance.
- Maintain rollback and recovery procedures.
- Test automated functions in controlled environments before wider deployment.

The purpose is to reduce both the probability of incorrect autonomous decisions and their possible business impact.

## References

1. Yang, M., et al. (2026). *From 5G to 6G: A Survey on Security, Privacy, and Standardization Pathways*. ACM Computing Surveys, 58(8). https://doi.org/10.1145/3785467
2. Karvinen, T. (2026). *Cyber Security – H1 CyberScience*. https://terokarvinen.com/cyber-security/
3. Publication Forum (JUFO). https://jfp.csc.fi/jufoportal

