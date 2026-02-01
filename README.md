# High-Level Design/ System Design (HLD) for self-practice
High-Level Design diagrams for practicing various problems, learning Domain Driven Design, understanding backend concepts, understanding functional and non-functional requirements, and an overview of the components involved.
Includes
# Notification Service / Design Brevo - Notification Service
  ## Functional Requirements
  1. The service should be able to send all kinds of notifications - be in SMS/Emails/ In-App/ Transactions/ Promotions
  2. It should be pluggable - meaning that notifications can be sent via SMS/Email/In-App or Telephone
  3. This service can be considered as a Software as a Service(SaaS) product - meaning it's shippable to various companies 
  4. This service should be able to prioritize among which type of notifications to be shared

  ## Non Functional Requirements
  1. This service should be available
  2. This service should be able to send notifications to multiple users
<img width="1196" height="694" alt="image" src="https://github.com/user-attachments/assets/c544b67e-ddb7-4d20-893e-177d0f66cebd" />

  ## Core Architecture Flow
    1. Clients send requests to a Notification Service, which validates inputs and queues them to Kafka. Messages then flow through:
    2. Notification Validator & Prioritizer: Assigns priority (high/medium/low) based on type (e.g., OTP high, promo low) and routes to priority-specific Kafka topics.
    3. Rate Limiter: Uses Redis for client/user counters (e.g., increments keys by time window like day/second; drops or bills excess).
    4. Notification Handler & User Preferences: Checks user DB/service for prefs (e.g., no SMS, unsubscribe promo); fetches contact info (email/phone) from User Service.
    5. Channel Handlers (SMS, Email, In-App, IVRS): Separate services consume from channel-specific Kafka topics, integrate with vendors (e.g., regional SMS providers, SMTP, Firebase), and scale independently.
    6. Notification Tracker: Logs all sends to Cassandra for auditing.
    7. Bulk Notifications UI → Bulk Notification Service: Takes filter criteria (e.g., "users who ordered milk 3 days ago") and message.
    8. Leverages User Transaction Data (parsed from external Kafkas into Elasticsearch/MongoDB via Transaction Data Parser).
    9. Query Engine runs complex filters/aggregations to get user lists, then feeds to main Notification Service
