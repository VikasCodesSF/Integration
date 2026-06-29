# Integration

## 1. Salesforce Cloud Computing Classification
1. **SAAS** — Ready-made applications (e.g., Salesforce CRM).
2. **PAAS** — Force.com Platform: a cloud-based development environment that lets us:
   1. Customize the existing Salesforce CRM application.
   2. Leverage/extend features with additional enhancements.
   3. Build our own custom applications (Banking, Finance, Manufacturing, Life Sciences, Pharma, etc.).
   4. Integrate Salesforce applications with any external system.
   - 2 Programming Languages on Force.com: **Apex** & **Visualforce**.

### Example flow (SuperMarket business, illustrating multi-system integration)
Salesforce App (Force.com) → SuperMarket Online Facility → Select Items (Cart) → Make Payment → Banking Application (Payment Gateway) → Payment Done → Sales Order → Sales.

## 2. What is Integration?
- An enterprise uses many applications to manage different parts of its business; these are often built on different platforms.
- **Integration** = combining parts so they work together / form a whole.

## 3. Benefits of Integration
- **Offers all-in-one access** — employees don't switch systems; one login gets everything done.
- **Increases productivity** — avoids manual cut/paste between systems (error-prone, time-wasting); integrated systems auto-sync data, freeing employee time.
- **Provides effortless access to data** — data accessible across all integrated systems.
- **Enhances communication** — teams can use their preferred system while still sharing info, since the systems are connected.
- **Automates workflows without code** — point-and-click automation across integrated systems.
- **Provides more insightful reporting** — more detailed, relevant reports.

## 4. Ways to Integrate
1. User Interface Integration
2. Business Logic Integration
3. Data Integration

## 5. Ways to Implement Integration (Common Approaches)
1. Outbound Messaging
2. Email Services (Inbound)
3. Call-Ins
4. Call-Outs

## 6. Two Ways to Integrate Salesforce with External Systems

### A. Declarative Integration (Without Coding)
1. **Integration Tools (EAI Tools)**: Tibco, WebMethods, SeeBeyond, MS-BizTalk, Dell Boomi, Informatica Cloud, MuleSoft (Salesforce), JitterBit, CastIron, AppExchange Products.
2. **Salesforce-to-Salesforce Integration** (detailed below).
3. **Heroku Platform**.

### B. Programmatic Integration (With Coding)
1. **Email Services** (Email Integration)
2. **REST API** — Representational State Transfer (JSON format). Example: MongoDB → JSON.
3. **SOAP API** — XML, WSDL (Web Service Description Language) — Simple Object Access Protocol.
4. **Outbound Messaging** — XML (SOAP).

---

## Quick Recap — Programmatic Integration Methods (cross-reference)
| Method | Format |
|---|---|
| Email Services | Plain text / attachments |
| REST API | JSON |
| SOAP API | XML / WSDL |
| Outbound Messaging | XML (SOAP) |