
FULL-STACK DEVELOPMENT PROMPT

Project Title

Design and Implementation of a Multi-Vendor E-Commerce Website for Small Business Owners With Inventory Management

---

1. ROLE

Act as a senior full-stack software engineer, software architect, database designer, UI/UX designer, security engineer, and testing engineer.

Build a complete, maintainable, production-structured web application based on the requirements in this document.

This is an academic software engineering project intended to provide a practical e-commerce platform for small business owners, particularly in a local/rural community context.

Do not build a superficial prototype consisting of disconnected CRUD pages.

The application must have:

- a coherent architecture
- proper authentication
- role-based authorization
- relational database design
- transactional business logic
- realistic marketplace workflows
- inventory management
- simulated online payment
- order management
- delivery tracking
- notifications
- dispute management
- validation
- error handling
- security protections
- automated testing
- responsive UI
- documentation

The final result must be a runnable Flask application.

---

2. CORE TECHNOLOGY STACK

Backend

Use:

- Python 3.12+
- Flask
- SQLAlchemy 2.x / Flask-SQLAlchemy
- Flask-Migrate / Alembic
- Flask-Login or an equivalent secure authentication mechanism
- WTForms or another robust server-side validation system
- PostgreSQL as the primary production database
- SQLite may be supported for local development and automated testing
- python-dotenv
- Werkzeug security utilities
- pytest

Do not use:

- Django
- FastAPI
- another backend framework

Flask must remain the backend framework.

---

3. FRONTEND

Use:

- Jinja2
- Bootstrap 5
- HTML5
- CSS3
- JavaScript
- HTMX where useful
- Alpine.js where useful

Do not use React, Vue, Angular, or another SPA framework.

The application should primarily be server-rendered.

Use JavaScript only where it improves the user experience.

---

4. DATABASE

Use PostgreSQL as the primary database.

Use proper relational database design.

The database should make appropriate use of:

- primary keys
- foreign keys
- indexes
- unique constraints
- check constraints
- timestamps
- cascading rules
- transactional updates

Do not store core relational data in arbitrary JSON blobs.

Use migrations.

Do not rely on manually creating tables.

---

5. PROJECT GOAL

Build a multi-vendor e-commerce platform where:

- customers can browse products from multiple vendors
- customers can search and filter products
- customers can add products to a shopping cart
- customers can purchase products
- customers can make simulated online payments
- vendors can register
- administrators can approve vendors
- approved vendors can manage products
- vendors can manage inventory
- vendors can process orders up to dispatch
- administrators can manage delivery after dispatch
- customers can track order progress
- the system automatically updates inventory after successful payment
- the system generates low-stock alerts
- the system provides notifications
- administrators can resolve customer/vendor disputes

The application must use Nigerian Naira (NGN) as the project currency.

---

6. USER ROLES

The system must provide three primary roles:

1. Customer
2. Vendor
3. Administrator

Role permissions must be enforced server-side.

Hiding buttons in the frontend is not sufficient authorization.

---

7. CUSTOMER FUNCTIONALITY

Customers must be able to:

- register
- log in
- log out
- manage their profile
- manage delivery addresses
- browse products
- search products
- filter products
- sort products
- view categories
- view vendor information
- view product details
- add items to cart
- update cart quantities
- remove cart items
- clear cart
- checkout
- make simulated payments
- view order history
- view individual order details
- track delivery status
- view notifications
- submit disputes
- view dispute status

Customers must never be able to:

- access administrator functions
- access vendor management functions
- edit vendor products
- change inventory
- modify payment records
- modify order status
- modify another customer's orders

---

8. VENDOR REGISTRATION AND APPROVAL

A user who registers as a vendor must initially receive:

PENDING_APPROVAL

Vendor statuses:

PENDING_APPROVAL
APPROVED
REJECTED
SUSPENDED

Workflow:

1. User registers as a vendor.
2. Vendor account is created.
3. Vendor cannot sell products while pending.
4. Administrator sees the pending vendor.
5. Administrator reviews the vendor.
6. Administrator approves or rejects the vendor.
7. The vendor receives a notification.
8. Approved vendors gain selling privileges.
9. Rejected vendors cannot sell.
10. Suspended vendors cannot sell until reactivated.

---

9. VENDOR FUNCTIONALITY

Approved vendors can:

- access a vendor dashboard
- manage their profile
- create products
- edit products
- deactivate products
- delete products where appropriate
- upload product images
- assign products to categories
- set prices
- set stock quantity
- set low-stock threshold
- view inventory
- view inventory history
- receive low-stock notifications
- view incoming orders
- confirm orders
- prepare orders
- mark orders ready for dispatch
- mark orders dispatched
- view their order history
- receive notifications
- participate in dispute resolution

A vendor must only be able to access:

- their own products
- their own inventory
- the vendor-specific portions of orders belonging to them

A vendor must never be able to:

- approve another vendor
- modify another vendor's products
- access administrator functionality
- change administrator-controlled delivery statuses
- manually mark payments as successful
- modify payment records

---

10. ADMINISTRATOR FUNCTIONALITY

Administrators have platform-wide access.

Vendor Management

Administrators can:

- view pending vendors
- inspect vendor details
- approve vendors
- reject vendors
- suspend vendors
- reactivate vendors
- view vendor status

User Management

Administrators can:

- view customers
- view vendors
- activate/deactivate accounts
- inspect user information

Product Management

Administrators can:

- view all products
- deactivate inappropriate products
- remove products when necessary
- manage categories

Order Management

Administrators can:

- view all orders
- inspect order details
- monitor vendor order processing
- oversee delivery-stage order statuses

Delivery Management

After a vendor dispatches an order, the administrator controls delivery-stage status updates.

Possible delivery statuses:

DISPATCHED
IN_TRANSIT
OUT_FOR_DELIVERY
DELIVERED
DELIVERY_FAILED
RETURNED

Dispute Management

Customers can create disputes associated with orders.

Administrators can:

- view disputes
- inspect related orders
- inspect involved users
- add internal notes
- update dispute status
- resolve disputes
- close disputes

Dispute statuses:

OPEN
UNDER_REVIEW
RESOLVED
CLOSED

---

11. PRODUCT MANAGEMENT

Each product should support:

- id
- vendor_id
- category_id
- name
- slug
- description
- SKU
- price
- stock_quantity
- low_stock_threshold
- primary image
- status
- created_at
- updated_at

Product statuses:

DRAFT
ACTIVE
INACTIVE
OUT_OF_STOCK

A vendor must only manage products that belong to that vendor.

---

12. PRODUCT CATEGORIES

Provide categories such as:

- Electronics
- Clothing
- Food
- Household
- Health and Beauty
- Building Materials
- Agriculture
- Other

Administrators can:

- create categories
- edit categories
- deactivate categories
- safely delete categories where possible

Do not allow deletion of a category if it would violate database integrity without handling its dependent products correctly.

---

13. PRODUCT IMAGES

Allow vendors to upload product images.

Requirements:

- validate MIME type
- restrict file extensions
- limit file size
- sanitize filenames
- prevent executable file uploads
- store uploaded files securely
- use generated filenames
- provide image previews
- generate thumbnails where practical
- provide alt text
- allow image replacement/deletion

Never trust a filename supplied by a user.

---

14. MARKETPLACE

Create a customer-facing marketplace.

Homepage should include:

- site branding
- search
- categories
- featured/recent products
- product cards
- vendor information
- cart indicator
- login/register controls

Customers must be able to:

- browse products
- browse by category
- search products
- filter by price
- filter by category
- filter by vendor
- filter by availability
- sort products
- view product details
- view the vendor associated with a product

Search should support:

- product name
- description
- SKU

Use database queries.

Do not fetch the entire product catalog into JavaScript just to perform filtering.

Use pagination.

---

15. SHOPPING CART

The cart must support products from multiple vendors.

Customers can:

- add products
- change quantities
- remove products
- clear the cart
- view subtotal
- proceed to checkout

Each cart item should contain:

- product
- vendor
- quantity
- current/relevant unit price
- subtotal

Before checkout:

- verify the product still exists
- verify it is active
- verify sufficient stock
- recalculate all totals on the server

Never trust cart totals supplied by the browser.

---

16. MULTI-VENDOR ORDER ARCHITECTURE

A customer may purchase products from multiple vendors in a single checkout.

Recommended structure:

Order
 ├── OrderItem
 ├── OrderItem
 ├── OrderItem
 │
 ├── VendorOrder
 │    └── Vendor A items
 │
 └── VendorOrder
      └── Vendor B items

The customer sees one overall order.

Each vendor sees only their relevant "VendorOrder".

The administrator sees the complete order.

This design must support independent vendor-side processing within a single customer order.

---

17. CHECKOUT

Checkout must collect:

- customer information
- delivery address
- phone number
- order items
- payment method
- total amount

The customer must be shown an order summary before payment.

The server must recalculate:

- product prices
- item quantities
- item subtotals
- order subtotal
- total

Never trust the browser to determine the final amount.

Use proper currency handling.

Do not use floating-point arithmetic for financial values.

Use:

- integer minor units, or
- Decimal with appropriate database precision

---

18. ORDER CREATION

When creating an order:

1. validate customer
2. validate all products
3. validate product availability
4. validate stock
5. calculate prices server-side
6. create the order
7. create order items
8. create vendor-order records where applicable
9. create a pending payment
10. commit safely

Use a database transaction.

Do not leave partially-created orders.

---

19. ORDER STATUS WORKFLOW

Vendor-side status workflow:

PENDING_PAYMENT
        ↓
PAID
        ↓
PROCESSING
        ↓
READY_FOR_DISPATCH
        ↓
DISPATCHED

Vendors control order processing until "DISPATCHED".

Administrator delivery workflow:

DISPATCHED
        ↓
IN_TRANSIT
        ↓
OUT_FOR_DELIVERY
        ↓
DELIVERED

Alternative outcomes:

DELIVERY_FAILED
RETURNED

Vendors must not control administrator delivery-stage statuses.

Customers may view the full status timeline but cannot modify statuses.

---

20. DELIVERY

The software tracks delivery status only.

Physical logistics are outside the system.

Assume:

- vendors prepare orders
- vendors dispatch orders
- external logistics services or transport arrangements handle physical movement
- administrators update delivery status after dispatch

Do not implement:

- GPS fleet tracking
- route optimization
- driver management
- vehicle management
- courier dispatch algorithms
- warehouse automation

The system is an order/delivery tracking platform, not a logistics management suite.

---

21. INVENTORY MANAGEMENT

Inventory management is a core system feature.

Each product must have:

- stock quantity
- low-stock threshold

Inventory must automatically update after successful payment.

Rules:

1. Product cannot be purchased when stock is zero.
2. Customer cannot purchase more than available stock.
3. Stock decreases after confirmed successful payment.
4. Failed payment does not permanently reduce stock.
5. Cancelled payment does not permanently reduce stock.
6. Inventory cannot become negative.
7. Low-stock threshold triggers an alert.
8. Restocking is recorded.
9. Manual adjustments are recorded.
10. Returns that restore inventory are recorded.

---

22. CONCURRENCY AND OVERSELLING

Prevent overselling during simultaneous purchases.

Do not implement unsafe logic such as:

stock = product.stock_quantity
stock -= quantity
product.stock_quantity = stock

without transaction protection.

Use appropriate transaction handling and row-level locking where PostgreSQL supports it.

The inventory update and successful payment processing must be atomic.

---

23. INVENTORY HISTORY

Create an inventory transaction/history system.

Record:

- product
- transaction type
- quantity change
- previous quantity
- new quantity
- reason
- related order
- actor
- timestamp

Possible transaction types:

SALE
RESTOCK
MANUAL_ADJUSTMENT
RETURN
CANCELLATION

---

24. LOW-STOCK ALERTS

Each product has a configurable low-stock threshold.

Example:

Stock = 10
Threshold = 3

No alert.

When stock reaches:

3

create a low-stock alert.

Avoid generating duplicate alerts continuously.

If stock is replenished above the threshold and later drops to/below it again, a new alert may be generated.

---

25. NOTIFICATION SYSTEM

Implement an internal notification system.

Notification fields should include:

- id
- user_id
- type
- title
- message
- read status
- created_at
- related entity where useful

Users should have:

- notification dropdown
- notification page
- unread count
- mark as read
- mark all as read if practical

Customer Notifications

Examples:

- account-related confirmation
- payment confirmation
- order confirmation
- order status changed
- dispatch notification
- delivery notification
- dispute update

Vendor Notifications

Examples:

- vendor approval
- vendor rejection
- new order
- low-stock alert
- order status changes
- dispute notifications

Administrator Notifications

Examples:

- new vendor registration
- new dispute
- other important system events

Design the notification service so additional delivery channels such as email can be added later.

Do not build SMS infrastructure for the current project.

---

26. DEMO PAYMENT SYSTEM

IMPORTANT

The current project must NOT integrate Flutterwave or any external payment gateway.

The payment system must be a fully simulated local payment system.

No real money should be processed.

No external payment account should be required.

No payment API credentials should be required.

The application must be completely testable without a Flutterwave account.

---

27. DEMO PAYMENT GOAL

From the perspective of the rest of the application, the demo payment system should behave like a real payment gateway.

Workflow:

Customer checkout
       ↓
Payment page
       ↓
Select Card or Bank Transfer
       ↓
Demo transaction
       ↓
Server verifies demo payment
       ↓
Payment becomes successful
       ↓
Order becomes PAID
       ↓
Inventory updates
       ↓
Notifications created

---

28. DEMO PAYMENT METHODS

Support:

- Demo Card
- Demo Bank Transfer

These are simulations.

Clearly inform the customer:

DEMO PAYMENT

This is a simulated payment for demonstration purposes.
No real money will be charged.

---

29. DEMO CARD PAYMENT

Create a demo card payment page containing:

- cardholder name
- card number
- expiry date
- CVV

Display clearly marked fake test information.

Example:

Card Number:
4242 4242 4242 4242

Expiry:
12/30

CVV:
123

These are test values only.

Do not send them to any external provider.

Do not store CVV.

Do not store raw card details.

The card form exists only to simulate a payment process.

---

30. DEMO BANK TRANSFER

Create a simulated bank transfer page.

Display fictional payment information such as:

Bank:
Demo Bank

Account Name:
Demo Marketplace

Account Number:
1234567890

Amount:
₦XX,XXX

Reference:
DEMO-XXXXXX

Provide a button:

Simulate Transfer

Clicking the button simulates successful payment.

No actual bank transfer is created.

---

31. DEMO PAYMENT RESULTS

Support:

Successful Payment

When the customer chooses successful payment:

1. create/record payment transaction
2. verify the transaction server-side
3. mark payment successful
4. mark order paid
5. update inventory
6. create notifications
7. show order confirmation

Failed Payment

When a payment fails:

- payment becomes "FAILED"
- order remains unpaid
- inventory remains unchanged
- customer is informed
- customer can retry

Cancelled Payment

When the customer cancels:

- payment becomes "CANCELLED"
- order remains unpaid
- inventory remains unchanged

Possible payment statuses:

PENDING
SUCCESSFUL
FAILED
CANCELLED

---

32. PAYMENT MODEL

Create a "Payment" model containing appropriate fields such as:

- id
- order_id
- transaction_reference
- payment_method
- amount
- currency
- status
- created_at
- updated_at

Use:

NGN

for the currency.

Every payment attempt must have a unique transaction reference.

Example:

DEMO-PAY-8F4D91A2

---

33. PAYMENT ABSTRACTION

Implement a payment gateway abstraction.

For example:

class PaymentGateway:
    def create_payment(self, order):
        raise NotImplementedError

    def verify_payment(self, reference):
        raise NotImplementedError

Then implement:

class DemoPaymentGateway(PaymentGateway):
    ...

Do not implement Flutterwave yet.

The architecture should make future Flutterwave integration possible without redesigning the core system.

Future architecture:

PaymentGateway
    ├── DemoPaymentGateway
    └── FlutterwavePaymentGateway

Current implementation:

PaymentGateway
    └── DemoPaymentGateway

---

34. PAYMENT SERVICE

Create a dedicated payment service instead of putting payment logic in route functions.

Suggested methods:

create_payment(...)
process_demo_payment(...)
verify_demo_payment(...)

Keep provider-specific logic isolated.

The order system should not care whether the payment provider is:

- demo
- Flutterwave
- another future provider

---

35. PAYMENT SECURITY

Even though this is a simulated system:

- never store CVV
- do not store raw card numbers
- do not expose secret credentials
- do not pretend to process real cards
- do not call external payment APIs
- do not hard-code real financial credentials

The application must never claim that a real financial transaction occurred.

---

36. PAYMENT VERIFICATION

Do not mark an order as paid simply because the browser visits a "success" page.

Even in demo mode, simulate the architecture of real verification:

Customer initiates payment
        ↓
Demo transaction created
        ↓
Server verifies transaction
        ↓
Payment marked successful
        ↓
Order marked PAID

This will make future real gateway integration easier.

---

37. PAYMENT IDEMPOTENCY

Prevent duplicate payment processing.

If the same successful payment confirmation is submitted more than once:

- do not deduct inventory twice
- do not create duplicate payment success records
- do not create duplicate order notifications
- do not alter the order incorrectly

Use unique transaction references and database constraints.

---

38. FUTURE FLUTTERWAVE INTEGRATION

Flutterwave is intentionally excluded from the current implementation because payment credentials are not currently available.

Structure the project so that Flutterwave can be integrated later.

When Flutterwave is eventually added, its implementation should handle:

- payment initialization
- payment verification
- provider authentication
- webhook handling
- provider-specific failures

Do not couple those details to:

- cart logic
- order creation
- inventory
- delivery tracking
- notifications
- disputes

Do not implement Flutterwave now.

Do not create placeholder code pretending that Flutterwave works.

---

39. VENDOR PAYOUTS

Vendor payout automation is out of scope.

Do not implement:

- vendor withdrawals
- automatic vendor settlement
- vendor wallet
- Flutterwave Transfers
- payout scheduling

Customers can make simulated payments to the platform, but automatic disbursement to vendors must not be implemented.

This is a deliberate project limitation.

---

40. ORDER CANCELLATION

Implement sensible cancellation rules.

For example:

- unpaid orders may be cancelled
- pending/processing orders may have restricted cancellation
- dispatched orders cannot normally be cancelled
- delivered orders cannot be cancelled through the normal cancellation action

If cancellation restores inventory:

- restore the appropriate stock
- create an inventory transaction

Do not invent automatic real-money refunds.

---

41. DISPUTE SYSTEM

Customers can create a dispute attached to an order.

A dispute should contain:

- id
- order_id
- customer_id
- vendor_id where relevant
- title
- description
- status
- created_at
- updated_at

Possible statuses:

OPEN
UNDER_REVIEW
RESOLVED
CLOSED

Administrators can:

- review disputes
- add notes
- update status
- resolve disputes
- close disputes

Keep the system simple and manual.

---

42. USER AUTHENTICATION

Implement:

- registration
- login
- logout
- password hashing
- password confirmation
- authentication-protected routes
- account status
- role assignment

Passwords must be hashed using secure password hashing.

Never store plaintext passwords.

---

43. AUTHORIZATION

Create explicit permission rules.

Examples:

Customer:

/customer/*

Vendor:

/vendor/*

Admin:

/admin/*

But route naming alone is not authorization.

Always validate:

- current user
- current role
- ownership of requested resources
- account status

Examples:

- vendor cannot modify another vendor's product
- customer cannot access another customer's order
- vendor cannot access admin functions
- suspended vendor cannot sell
- customer cannot change delivery status

---

44. WEB SECURITY

Implement protection against:

- CSRF
- XSS
- SQL injection
- insecure direct object references
- unauthorized access
- malicious file uploads
- session abuse
- mass assignment

Use ORM parameterization.

Validate input server-side.

Escape output.

Use secure session configuration.

Use:

- HTTPOnly cookies
- appropriate SameSite settings
- secure cookies in production
- strong secret keys

---

45. ADMIN DASHBOARD

Create an administrator dashboard displaying useful metrics.

Include:

- total customers
- total vendors
- pending vendors
- total products
- low-stock products
- total orders
- pending orders
- delivered orders
- unresolved disputes
- payment totals where appropriate

Use simple visualizations where genuinely useful.

Do not over-engineer the dashboard.

---

46. VENDOR DASHBOARD

Display:

- total products
- active products
- low-stock products
- recent orders
- pending orders
- orders ready for dispatch
- unread notifications

Provide quick actions:

- add product
- manage products
- inventory
- orders

---

47. CUSTOMER DASHBOARD

Display:

- recent orders
- active orders
- unread notifications
- account information
- saved addresses

Provide shortcuts to:

- marketplace
- cart
- orders
- profile

---

48. MAIN PAGE STRUCTURE

Public Pages

- Home
- Marketplace
- Product listing
- Product detail
- Category page
- Vendor/store page
- Login
- Registration
- About

Customer Pages

- Dashboard
- Cart
- Checkout
- Demo Payment
- Orders
- Order Detail
- Notifications
- Profile
- Addresses
- Disputes

Vendor Pages

- Dashboard
- Products
- Add Product
- Edit Product
- Inventory
- Inventory History
- Orders
- Order Detail
- Notifications
- Profile

Admin Pages

- Dashboard
- Vendors
- Vendor Detail
- Users
- Products
- Categories
- Orders
- Delivery Management
- Disputes
- Audit Logs

---

49. UI/UX

Create a polished, modern marketplace interface.

Do not simply use default Bootstrap styles without customization.

Use:

- responsive layouts
- consistent spacing
- clear typography
- accessible forms
- useful empty states
- validation messages
- loading indicators where useful
- meaningful success/error messages
- clear navigation
- mobile-friendly tables/cards

The website should look like a real small-business marketplace.

Do not overcomplicate the visual design.

---

50. RESPONSIVE DESIGN

The application must work on:

- desktop
- tablet
- mobile

Check:

- navigation
- product cards
- marketplace filters
- checkout
- forms
- customer dashboard
- vendor dashboard
- admin dashboard
- tables

Where possible, use responsive cards or collapsible data on smaller screens instead of forcing large desktop tables.

---

51. ACCESSIBILITY

Use:

- semantic HTML
- proper form labels
- keyboard navigation
- descriptive buttons
- alt text
- useful focus states
- understandable validation messages
- accessible color contrast

---

52. SEARCH AND FILTERING

Support:

- keyword search
- category
- vendor
- price range
- stock availability

Sorting:

- newest
- price low to high
- price high to low
- name

Use indexed database fields where useful.

Use pagination.

---

53. DATABASE ENTITIES

At minimum, consider:

User
Role
VendorProfile
CustomerProfile
Address
Category
Product
ProductImage
Cart
CartItem
Order
OrderItem
VendorOrder
Payment
PaymentEvent / PaymentTransaction
InventoryTransaction
Notification
Dispute
DisputeNote
DeliveryStatusHistory
AuditLog

Use sensible relationships.

Do not create unnecessary entities merely for the sake of complexity.

---

54. DATABASE RELATIONSHIPS

Important relationships include:

User ─── CustomerProfile
User ─── VendorProfile

Vendor ─── Product

Category ─── Product

Customer ─── Cart
Cart ─── CartItem
CartItem ─── Product

Customer ─── Order
Order ─── OrderItem
Order ─── VendorOrder
VendorOrder ─── Vendor

Order ─── Payment

Product ─── InventoryTransaction

User ─── Notification

Order ─── Dispute

Order ─── DeliveryStatusHistory

User ─── AuditLog

Define foreign keys and constraints correctly.

---

55. AUDIT LOGGING

Record sensitive administrative actions.

Examples:

- vendor approval
- vendor rejection
- vendor suspension
- product deactivation
- category modification
- manual inventory changes
- delivery-status changes
- dispute resolution

Log:

- actor
- action
- target entity
- target ID
- timestamp
- optional metadata

Do not log passwords or sensitive payment credentials.

---

56. APPLICATION ARCHITECTURE

Use Flask's application factory pattern.

Suggested architecture:

project/
│
├── app/
│   ├── __init__.py
│   ├── config.py
│   ├── extensions.py
│   │
│   ├── models/
│   │
│   ├── auth/
│   ├── main/
│   ├── customer/
│   ├── vendor/
│   ├── admin/
│   ├── payments/
│   ├── notifications/
│   │
│   ├── services/
│   ├── utils/
│   │
│   ├── templates/
│   │   ├── base.html
│   │   ├── auth/
│   │   ├── main/
│   │   ├── customer/
│   │   ├── vendor/
│   │   └── admin/
│   │
│   └── static/
│       ├── css/
│       ├── js/
│       └── images/
│
├── migrations/
├── tests/
├── uploads/
├── instance/
├── .env.example
├── .gitignore
├── requirements.txt
├── run.py
├── README.md
└── docker-compose.yml

Improve the structure where useful without changing the required technology stack.

---

57. FLASK BLUEPRINTS

Use separate Blueprints for:

- authentication
- public marketplace
- customer
- vendor
- administrator
- payments
- notifications

Do not put every route into one file.

---

58. SERVICE LAYER

Complicated business logic must not be embedded directly in route handlers.

Create services for:

- order creation
- inventory management
- payment processing
- payment verification
- notifications
- vendor approval
- dispute resolution
- delivery status management

Examples:

create_order(...)
process_payment(...)
verify_payment(...)
update_inventory(...)
create_notification(...)
approve_vendor(...)
update_delivery_status(...)
resolve_dispute(...)

---

59. DATABASE TRANSACTIONS

Use transactions for:

- order creation
- successful payment processing
- inventory reduction
- cancellation/stock restoration
- delivery status changes when necessary
- manual inventory adjustments

Related operations must succeed or fail as a unit.

Do not permit inconsistent states such as:

Payment = SUCCESSFUL
Order = UNPAID
Inventory = unchanged

or:

Order = PAID
Inventory = deducted twice

---

60. ERROR HANDLING

Provide:

- 404 page
- 403 page
- 500 page
- validation errors
- payment errors
- database errors

Do not expose stack traces to users in production.

Log useful errors safely.

---

61. LOGGING

Configure application logging.

Log:

- application failures
- payment verification errors
- malformed demo payment attempts where useful
- webhook-related failures if future support is added
- authorization failures where useful
- administrator-sensitive operations

Never log:

- passwords
- CVV
- raw card numbers
- secret keys

---

62. ENVIRONMENT CONFIGURATION

Create environment variables for general application configuration.

Example:

SECRET_KEY=
DATABASE_URL=
FLASK_ENV=

Do not require Flutterwave variables for the current version.

Do not commit ".env".

Provide:

.env.example

---

63. CONFIGURATION MODES

Provide separate configuration for:

- development
- testing
- production

Use environment variables.

Do not commit production credentials.

---

64. TEST SEED DATA

Create a seed system that generates realistic development data.

Include:

- administrator
- customers
- approved vendors
- pending vendor
- categories
- products
- varying stock levels
- low-stock products
- sample orders
- sample notifications
- sample disputes

Use fake data only.

---

65. DEVELOPMENT ACCOUNTS

Provide development/demo accounts such as:

Admin:
admin@example.com

Vendor:
vendor@example.com

Customer:
customer@example.com

Use clearly documented fake development passwords.

Never use real credentials.

---

66. TESTING

Use pytest.

Write tests for:

Authentication

- registration
- login
- invalid password
- logout
- protected routes

Authorization

- customer cannot access vendor routes
- customer cannot access admin routes
- vendor cannot access admin routes
- vendor cannot modify another vendor's product
- customer cannot modify another customer's order
- suspended vendor cannot sell

Vendors

- vendor registration
- pending state
- approval
- rejection
- suspension
- reactivation

Products

- creation
- editing
- deactivation
- ownership
- invalid input

Marketplace

- search
- filtering
- pagination
- out-of-stock handling

Cart

- add
- update quantity
- remove
- invalid quantity
- insufficient stock

Orders

- create order
- multi-vendor order
- vendor-specific order visibility
- status transitions
- cancellation rules

Inventory

- successful payment decreases stock
- failed payment leaves stock unchanged
- cancelled payment leaves stock unchanged
- stock cannot become negative
- low-stock alerts
- inventory history

Demo Payment

Test:

- successful card payment
- successful bank transfer simulation
- failed payment
- cancelled payment
- duplicate payment confirmation
- invalid transaction reference
- mismatched transaction amount

Delivery

- vendor can dispatch
- vendor cannot update delivery-stage statuses after dispatch
- administrator can update delivery statuses
- customer can view delivery history

Notifications

- notification creation
- unread count
- mark as read
- correct recipient

Disputes

- create dispute
- administrator sees dispute
- status changes
- resolution

---

67. PAYMENT TESTING SCENARIO

Test this complete path:

Customer
   ↓
Adds product
   ↓
Checkout
   ↓
Demo payment
   ↓
Successful payment
   ↓
Server verification
   ↓
Order PAID
   ↓
Inventory reduced
   ↓
Vendor notified
   ↓
Customer notified

Also test:

Customer
   ↓
Checkout
   ↓
Demo payment
   ↓
Payment failed
   ↓
Order remains unpaid
   ↓
Inventory unchanged

---

68. COMPLETE END-TO-END TEST

Perform this exact scenario before considering the application complete:

1. Register customer.
2. Register vendor.
3. Administrator approves vendor.
4. Vendor creates a product.
5. Vendor sets stock to 10.
6. Vendor sets low-stock threshold to 3.
7. Customer finds the product.
8. Customer adds 2 units to cart.
9. Customer checks out.
10. Customer chooses demo payment.
11. Customer simulates successful payment.
12. Server verifies demo payment.
13. Payment becomes successful.
14. Order becomes paid.
15. Inventory changes from 10 to 8.
16. Vendor sees the order.
17. Vendor confirms the order.
18. Vendor prepares it.
19. Vendor marks it ready for dispatch.
20. Vendor dispatches it.
21. Administrator sees the dispatched order.
22. Administrator changes delivery status to "IN_TRANSIT".
23. Administrator changes it to "OUT_FOR_DELIVERY".
24. Administrator marks it "DELIVERED".
25. Customer sees the entire status history.
26. Relevant notifications appear.
27. Inventory remains correct.
28. Audit records exist for administrator status changes.

Then test:

- failed payment
- cancelled payment
- duplicate payment
- insufficient stock
- low-stock threshold
- unauthorized access
- vendor suspension
- dispute creation
- dispute resolution
- invalid payment amount
- invalid product ownership
- invalid order ownership

Fix all critical failures.

---

69. DOCUMENTATION

Create a comprehensive "README.md".

Include:

Project Overview

Explain the system.

Features

List major capabilities.

Technology Stack

List technologies.

Installation

Provide exact commands.

Environment Configuration

Explain ".env".

Database Setup

Explain migrations.

Seed Data

Explain how demo data is created.

Running the Application

Provide exact commands.

Running Tests

Provide exact commands.

Demo Payment

Explain that payment is simulated.

Explain demo card and transfer flows.

User Roles

Explain customer/vendor/admin functionality.

Project Structure

Explain folders.

Deployment

Provide basic production guidance.

---

70. ACADEMIC DOCUMENTATION

Generate supporting technical documentation:

- system architecture diagram
- database ER diagram
- use-case diagram
- sequence diagrams
- deployment diagram
- database schema documentation
- functional requirements
- non-functional requirements

Use Mermaid diagrams where appropriate.

---

71. FUNCTIONAL REQUIREMENTS

Document requirements such as:

FR-01 User Registration
FR-02 User Authentication
FR-03 Vendor Registration
FR-04 Vendor Approval
FR-05 Product Management
FR-06 Product Browsing
FR-07 Product Search
FR-08 Shopping Cart
FR-09 Checkout
FR-10 Demo Payment
FR-11 Inventory Management
FR-12 Order Management
FR-13 Delivery Tracking
FR-14 Notifications
FR-15 Dispute Management
FR-16 Administrative Management

Expand these requirements appropriately.

---

72. NON-FUNCTIONAL REQUIREMENTS

Include:

Security

Protect accounts and sensitive information.

Usability

The interface should be understandable for users with limited technical experience.

Performance

Normal operations should respond efficiently.

Reliability

Payment, inventory, and order operations must maintain database consistency.

Maintainability

Use modular code and clear naming.

Scalability

Architecture should permit growth in products, users, vendors, and orders.

Accessibility

Provide a usable interface for a broad range of users.

---

73. PROJECT LIMITATIONS

The implementation must explicitly respect these limitations:

1. Payments are simulated.
2. No real money is processed.
3. Flutterwave is not currently integrated.
4. Vendor payouts are not implemented.
5. Vendor payout through Flutterwave Transfers is outside the scope.
6. The platform uses NGN.
7. Multi-currency support is not implemented.
8. Physical delivery logistics are outside the software.
9. Mobile application development is outside the project.
10. Advanced enterprise functionality is outside the project.

Do not add these omitted features merely because they might be useful.

---

74. OUT-OF-SCOPE FEATURES

Do not implement:

- real Flutterwave payment processing
- real bank transfers
- automatic vendor payouts
- vendor wallet
- cryptocurrency
- multi-currency support
- mobile apps
- GPS fleet tracking
- route optimization
- driver management
- warehouse robotics
- machine learning
- AI recommendations
- advanced fraud detection
- enterprise ERP
- blockchain
- unnecessary microservices
- complicated loyalty systems
- real-time customer/vendor chat unless genuinely necessary

This project should remain focused and academically defensible.

---

75. CODE QUALITY

Follow:

- PEP 8
- meaningful variable names
- type hints where useful
- modular code
- separation of concerns
- reusable components
- error handling
- comments only where helpful
- docstrings for important services

Avoid:

- giant route files
- repeated business logic
- arbitrary globals
- hard-coded business values everywhere
- fake persistence
- placeholder "pass" statements for major functionality
- client-side-only validation
- client-side-only authorization
- fake payment success
- storing passwords in plaintext

---

76. DEVELOPMENT PHASES

Build the application incrementally.

Phase 1: Foundation

Implement:

- application factory
- configuration
- extensions
- database
- migrations
- models
- authentication
- roles

Phase 2: Administration and Vendors

Implement:

- vendor registration
- vendor approval
- administrator dashboard
- user management
- vendor management

Phase 3: Marketplace

Implement:

- categories
- products
- images
- marketplace
- search
- filters
- vendor pages

Phase 4: Cart and Orders

Implement:

- cart
- checkout
- orders
- multi-vendor order structure
- vendor order processing

Phase 5: Demo Payment

Implement:

- payment model
- payment gateway abstraction
- demo card payment
- demo bank transfer
- simulated success/failure/cancellation
- server-side verification
- idempotency

Phase 6: Inventory

Implement:

- automatic inventory reduction
- inventory transactions
- low-stock thresholds
- low-stock notifications
- overselling prevention

Phase 7: Notifications

Implement:

- notification model
- notification service
- notification UI
- unread counts

Phase 8: Delivery and Disputes

Implement:

- dispatch
- delivery status
- status history
- disputes
- audit logging

Phase 9: Testing and Hardening

Implement:

- automated tests
- validation review
- authorization review
- security review
- UI review
- performance review

Phase 10: Documentation

Generate:

- README
- architecture diagrams
- ERD
- requirements
- setup instructions
- test instructions

---

77. DEVELOPMENT RULE

Do not generate the entire application as one giant response.

Work in logical stages.

For each stage:

1. Briefly explain the stage.
2. Show which files are being created or modified.
3. Provide complete code for those files.
4. Explain how to run the stage.
5. Explain how to test it.
6. Verify compatibility with previous stages.
7. Fix problems before moving on.

Do not silently rewrite working architecture without explaining why.

Do not introduce new technologies without necessity.

---

78. CURRENT PAYMENT IMPLEMENTATION RULE

For the current project:

ONLY "DemoPaymentGateway" must be implemented.

Do not write a fake Flutterwave client.

Do not create fake Flutterwave endpoints.

Do not include Flutterwave credentials.

Do not make HTTP requests to Flutterwave.

Do not claim Flutterwave is integrated.

The application must function completely without external payment services.

---

79. FUTURE-READY PAYMENT ARCHITECTURE

The application must nevertheless be designed so that a future real payment provider can replace the demo implementation.

Core application:

Checkout
   ↓
Payment Service
   ↓
PaymentGateway interface
   ↓
DemoPaymentGateway

Future:

Checkout
   ↓
Payment Service
   ↓
PaymentGateway interface
   ↓
FlutterwavePaymentGateway

The following modules must not depend directly on Flutterwave:

- Order
- Inventory
- Cart
- Notifications
- Vendor dashboard
- Customer dashboard
- Delivery
- Disputes

---

80. FINAL ACCEPTANCE CRITERIA

The project is complete only when:

- the application starts successfully
- the database migrations work
- registration works
- login works
- role separation works
- vendor approval works
- product management works
- inventory management works
- marketplace works
- search works
- cart works
- checkout works
- demo card payment works
- demo bank transfer works
- payment failures work
- payment cancellation works
- duplicate payment processing is prevented
- successful payment updates the order
- successful payment updates inventory
- low-stock alerts work
- vendor order processing works
- vendor dispatch works
- administrator delivery tracking works
- customers can track delivery
- notifications work
- disputes work
- audit logging works
- unauthorized access is blocked
- automated tests pass
- the website is responsive
- the README is complete
- no real payment credentials are required
- no real financial transaction occurs

---

81. FINAL INSTRUCTION TO THE AI

Build this system as a real, coherent Flask application suitable for an undergraduate software engineering project.

Prioritize:

1. correctness
2. security
3. database consistency
4. clear architecture
5. maintainability
6. usability
7. realistic workflows
8. testability
9. academic defensibility

Do not optimize for the number of features.

Do not add unnecessary complexity.

Do not fake functionality.

Do not leave core functionality as placeholders.

When there is a choice between a complex implementation and a simpler implementation that satisfies the requirements correctly, prefer the simpler implementation.

The payment system must remain a local simulation until a future version integrates a real provider.

The final application should feel like a complete community-oriented multi-vendor marketplace rather than a generic tutorial project.
