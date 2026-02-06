# High-Level Design (HLD): Loan Origination System

## 1. System Architecture Overview
The **Loan Origination System (LOS)** architecture is structured around a set of integrated platforms and services, orchestrated to deliver a seamless end-to-end loan application, assessment, and fulfilment experience. 

At its core, the LOS manages the customer journey, interfacing with both internal and external systems via an **integration layer**. The architecture ensures modularity, clear separation of concerns, and robust integration points.

### Component Interaction Matrix
| Component | Interaction Targets | Protocols/Data Formats |
| :--- | :--- | :--- |
| **Loan Origination System** | Converge BankingSuite, Applicant | REST/HTTPS, JSON |
| **Integration Layer** | LOS, Core Banking System, External Services | REST/HTTPS, JSON |
| **Core Banking System** | Converge BankingSuite | REST/HTTPS, JSON |
| **Equifax** | Converge BankingSuite | REST/HTTPS, JWT, JSON |
| **Cotality** | Converge BankingSuite | REST/HTTPS, JSON |
| **Docmosis** | Converge BankingSuite | REST/HTTPS, JSON/PDF |

---

## 2. System Component Descriptions

### Loan Origination System (LOS)
The primary system within the bank’s boundary, handling loan application intake, workflow management, and coordination of all activities required for loan approval. It delegates specialised tasks to **Converge BankingSuite**, abstracting underlying service complexity from end-users.

### Integration Layer
Serves as the **Integration and Orchestration Layer**. It receives requests from the LOS and routes them to the appropriate downstream systems. Key responsibilities include:
* **Data Aggregation:** Pulling data from **Equifax** (Credit) and **Cotality** (Property).
* **Document Generation:** Triggering **Docmosis** for contracts.
* **Transaction Management:** Integrating with the **Core Banking System** for final loan booking.

### Core Banking System
The system of record for all loan accounts and financial transactions. It is invoked by Converge BankingSuite to create and maintain loan accounts once the origination process is complete.

### External Services
* **Equifax:** Provides credit scores. Secured by **JWT authentication**.
* **Cotality:** Supplies property valuations (AVM data) in JSON format.
* **Docmosis:** Generates dynamic loan offers and contracts as **PDF** files.

---

## 3. Data Flows and Communication Protocols
Data flows originate with the applicant submitting an application through the LOS front-end (over HTTPS). 

1. **Intake:** LOS interacts with Integration Layer for all backend logic. 
2. **Third-Party Integration:** Integration Layer issues synchronous REST/HTTPS requests to external providers. 
3. **Fulfillment:** Upon approval, Integration Layer  triggers a REST call to the Core Banking System for account creation.

> **Note on Security:** All API integrations use REST over HTTPS. JSON is the standard payload format. JWT is specifically applied for the Equifax integration to ensure secure, token-based access.

---

## 4. Key Design Considerations

* **Scalability:** Both LOS and Converge are stateless, supporting horizontal scaling.
* **Security:** Role-based access controls (RBAC) are enforced within the LOS. Data at rest is encrypted in all core systems.
* **Fault Tolerance:** Retry logic and circuit breakers are applied at integration points to handle third-party service downtime.
* **Compliance:** All data exchanges comply with **APRA** and local privacy regulations. Audit logs are maintained for all actions.

---

## 5. Visual Representation
The following diagram illustrates the high-level system flow:

