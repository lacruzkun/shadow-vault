**CHAPTER FOUR:**

**SYSTEM IMPLEMENTATION AND TESTING**

# 4.1 Introduction

This chapter describes how the design in Chapter Three was turned into a working application, which the project calls LocalMarket, and how it was tested. It covers the implementation environment, each module in turn, the automated tests, and a discussion of how far the objectives in Section 1.3 were met.

# 4.2 Implementation Environment

| Item | Detail |
| --- | --- |
| Language and framework | Python 3.12 with Flask 3 |
| Database | SQLite by default (a file for development, memory for tests); PostgreSQL through the `DATABASE_URL` setting |
| Main libraries | Flask-SQLAlchemy, Flask-Migrate, Flask-Login, Flask-WTF, WTForms, Pillow, requests |
| Front end | Jinja2 templates, Bootstrap 5, JavaScript |
| Payment | Mock mode (default) or Flutterwave, selected by the `PAYMENT_PROVIDER` setting |
| Testing | pytest |
| Packaging | Dockerfile and docker-compose file that publish the application on port 5000 |

The source code is organised in the same layers as the design. The `app` package holds one folder for each blueprint (`auth`, `customer`, `vendor`, `admin`, `payments`, `notifications`), a `models` package with one file for each group of tables, a `services` package with the business rules, a `utils` package with access decorators and image handling, and the `templates` and `static` folders. Database changes are managed with migrations. The `flask seed` command initialises the roles and creates the administrator account, and `app/seed.py` also defines the demonstration vendors, products, orders and product images used during development.

Configuration is held in three classes. The testing class turns off CSRF checking and uses an in-memory database. The production class marks cookies as secure and strict about cross-site use. Secrets and payment keys are read from environment variables, so none is stored in the code.

# 4.3 Implementation of the Modules

## 4.3.1 Authentication and role-based access

Registration collects the full name, email, optional phone number, account type, password and, for vendor accounts, an optional business address, city and state. The form requires a password of at least eight characters, a valid email address and an email that is not already registered. A customer account receives the customer role and a customer profile. A vendor account receives both the vendor and customer roles and a vendor profile in the pending state, with the supplied location stored on that profile.

Access is enforced by four decorators placed on the routes: one for customers, one for any vendor, one for approved vendors only, and one for administrators. A vendor who is not yet approved can sign in and see a dashboard but receives an access-denied response on product and inventory pages. Because vendors also hold the customer role, the customer decorator admits them, which is how vendor-as-buyer is supported. After login, the dashboard route sends each user to the administrator, vendor or customer dashboard according to role.

## 4.3.2 Vendor approval

The administrator's vendor page lists vendors by status and shows each vendor's declared location, so the administrator can review it during approval. Four actions are available: approve, reject with a reason, suspend and reactivate. Each action is carried out by the vendor service, which changes the status, stamps the approval or suspension time, writes a row in the status history with the administrator's identity and any note, and sends the vendor a notification. Only vendors in the approved state appear in the public vendor filter and on vendor store pages.

## 4.3.3 Product and category management

Approved vendors create, edit, activate, deactivate and delete their own products through a product form that takes the name, categories, description, price in naira, stock quantity, low-stock threshold, SKU, status and an image. The category field accepts a comma-separated list; each name is matched to an existing category or created, so one product can sit in several categories. Every edit and delete route compares the product's vendor with the logged-in vendor and returns an access-denied response if they differ.

Images are accepted only with the extensions png, jpg, jpeg, gif and webp. Each upload is renamed with a random identifier, stored under a folder for the product, and given a thumbnail. Uploads are limited to 5 MB. Administrators manage the category list, including adding, editing and removing categories.

## 4.3.4 Vendor location and location-aware search

The vendor profile stores the business location in three optional fields: the street `address`, which already existed, and two new columns, `city` and `state`. Both new columns are nullable strings of up to 100 characters. They are created by migration `9c1f4a7d2b3e_add_vendor_location_fields.py`, so an existing database can be upgraded without losing its vendor records. Two read-only properties on `VendorProfile` produce the display value. `locality` joins the city and state, and `location` combines the street address with that pair. When the address already contains the locality, the property returns the address once instead of repeating it, and when a vendor supplies only a city and state, the locality is returned on its own. This keeps the old `address` value useful while giving the application structured fields for searching.

Registration now collects the optional business address, city and state in `RegistrationForm`. The registration route passes the three values to `create_user`, which trims them and writes them to the vendor profile only for a vendor account. The fields remain optional, so customer registration and existing vendor records are unaffected. On the registration page the location block is marked with `data-vendor-location`; the function `initVendorLocationFields` in `app/static/js/app.js` shows the block when the account type is vendor and hides and disables its inputs for a customer. If JavaScript is unavailable, the fields are still validated on the server and ignored for a customer account.

The saved location is displayed throughout the marketplace. The shared product card in `app/templates/main/_product_card.html` renders `product.vendor.location` below the vendor and category line, so each search result shows the product's price, vendor and location together. The same value appears on the product detail page, the vendor storefront, the featured products on the home page and the administrator's vendor list.

Vendors can correct the location after registration. The route `vendor.profile` in `app/vendor/routes.py` renders `VendorProfileForm` from `app/vendor/forms.py` and saves the business name, description, contact details, address, city and state on the logged-in vendor's profile. The page is linked from the vendor sidebar and dashboard, so any vendor account can update its details, and the new values immediately appear wherever `vendor.location` is rendered.

Search and filtering use the new fields. In `app/main/routes.py`, the text search (`q`) now matches the product name, description and SKU as before, and also the vendor's address, city and state. A separate location filter accepts `location`, `city` or `state` and applies the same vendor-field match. The filter value is carried through category links, pagination and the filter form, so a search is not lost when the user changes page or category. The demonstration data in `app/seed.py` defines a city and state for each of its vendors, drawn from a list of Nigerian cities.

| Implementation area | Main code location | Behaviour |
| --- | --- | --- |
| Data model | `app/models/user.py` | `city` and `state` columns, plus `locality` and `location` display properties |
| Database migration | `migrations/versions/9c1f4a7d2b3e_add_vendor_location_fields.py` | Adds the two nullable columns to `vendor_profiles` |
| Registration form | `app/auth/forms.py` | Optional address, city and state with length validation |
| Registration service | `app/auth/services.py` | Trims and stores the location for vendor accounts |
| Registration interface | `app/templates/auth/register.html`, `app/static/js/app.js` | Vendor-only location block, hidden for customers |
| Profile editing | `app/vendor/routes.py`, `app/vendor/forms.py`, `app/templates/vendor/profile.html` | Vendor can update business details, address, city and state after registration |
| Marketplace display | `app/templates/main/_product_card.html` and related templates | Location shown on product cards, detail page and storefront |
| Search and filter | `app/main/routes.py`, `app/templates/main/product_list.html` | `q` searches vendor location; location filter and pagination preserve it |
| Seed data | `app/seed.py` | Each demonstration vendor is defined with a city and state |

The implementation does not calculate distance or rank vendors by proximity. The buyer reads the declared location and decides whether the vendor is close enough, which is the scope set in Section 1.5.

## 4.3.5 Product search and browsing

The public product page builds one database query from the parameters in the address. It supports a text search across the product name, description and SKU, and across the vendor's address, city and state; a category filter; a minimum and maximum price; a vendor filter; a location filter; an availability filter; four sort orders (newest, price low to high, price high to low, name); and pagination of 12 products a page. Only active and out-of-stock products are shown; draft and inactive products are hidden. Filtering is done in the database, so the page loads only the products being displayed.

## 4.3.6 Shopping cart and checkout

Each user has one cart, created when first needed. Adding a product checks that it is available and that the requested quantity, including any quantity already in the cart, does not exceed stock. The unit price is saved in the cart item when it is added and refreshed when the quantity is updated. A cart can hold products from several vendors. Users can change quantities, remove an item or clear the cart, and each of these actions checks that the cart item belongs to the logged-in user.

The checkout form collects name, phone, address, city, state, postal code and optional notes. On submission, the order service validates every item again and groups the items by vendor. It then creates the order with the total calculated on the server, one vendor order for each vendor, the order items, and a pending payment record with a unique reference, and finally empties the cart. All of this is committed together. The customer then lands on the order page to pay.

## 4.3.7 Payment

Two providers are implemented behind one order process.

- **Mock mode.** The customer presses a demonstration payment button. The server builds a successful transaction record for the exact order amount and passes it to the same completion routine that real payments use. This lets the whole purchase cycle be tested without real money.
- **Flutterwave mode.** The server sends the order reference, amount, currency and customer details to the Flutterwave payments endpoint and redirects the customer to the returned checkout page. When the customer returns, the server calls Flutterwave's verification endpoint with the transaction identifier and acts only on that response. A webhook route also accepts provider notifications, but only when the signature header matches the configured secret, and it verifies the transaction again before acting.

The completion routine is the same in both modes. It ignores a payment that has already succeeded, rejects a payment whose amount or currency differs from the order, and then updates the payment, order and vendor orders inside one transaction, as described in Section 3.8.2. A failed payment leaves the order pending and notifies the customer.

## 4.3.8 Inventory management

Stock deduction happens inside the completion routine. For each order item the database is updated with a condition that the stock must be at least the quantity bought, so the update affects no row, and the whole transaction fails, if another purchase has taken the stock first. This prevents overselling. A ledger row records the sale with the previous and new quantity, the order and the payment reference. A low-stock notification is sent to the vendor when the new stock is at or below the threshold and the previous stock was above it, so one crossing produces one alert. A product that reaches zero is set to out of stock, and returns to active when restocked.

On the vendor inventory page, products are listed with their stock, and those at or below their threshold are shown separately. A vendor can restock or make a manual adjustment; both add to stock and each writes a ledger row.

## 4.3.9 Order processing and delivery tracking

The vendor's order page lists only that vendor's orders. A vendor moves an order from paid to processing, ready for dispatch and dispatched; any other move is refused, and a vendor cannot change another vendor's order. The customer is notified at each step. When an order has been dispatched, the administrator can record in transit, out for delivery, delivered, delivery failed or returned, and the customer is notified of each.

## 4.3.10 Notifications

A notification service writes a record for a user with a type, title, message and a link to the related order, product or dispute. Notifications are created for vendor status changes, payments, order progress, delivery updates, low stock and disputes. A small count of unread notifications appears in the navigation bar on every page, and users can read the list, mark one as read or mark all as read.

## 4.3.11 Disputes

From an order, a customer opens a dispute with a subject and description. Only the owner of the order can do so, and all administrators are notified. Customer and administrator can add notes to the dispute thread. The administrator changes the status among open, under review, resolved and closed; resolution records the time, and the customer is notified of each change.

## 4.3.12 User interface

All pages extend one base template that loads Bootstrap 5 and sets the viewport for mobile screens, so the same pages adapt to desktop, tablet and phone widths. Customers, vendors and administrators each have their own dashboard and navigation. Shared page elements such as the product card, pagination and status badges are kept in reusable template files, which keeps the look consistent across roles. The product card also carries the vendor location, so the marketplace and the home page present it in the same way, and the registration page reveals the location fields only when the visitor chooses a vendor account.

# 4.4 System Testing

## 4.4.1 Testing strategy

Testing combined automated and manual checks at three levels. Unit and integration tests exercise the routes and database together through Flask's test client, using an in-memory database that is created and dropped for every test, so tests cannot affect one another. System tests follow a complete user journey from registration to paid order. Security tests confirm that one user cannot act on another user's records.

## 4.4.2 Automated test cases

The suite contains thirteen tests in six files.

| No. | Test | What it checks | Expected result |
| --- | --- | --- | --- |
| 1 | Register customer | A new customer account can be created | Success message shown |
| 2 | Register vendor | A new vendor account can be created | Success message shown |
| 3 | Register vendor with location | A vendor supplies an address, city and state during registration | Values saved on the vendor profile and combined for display |
| 4 | Login and logout | A registered user can sign in and out | Welcome and sign-out messages shown |
| 5 | Pending vendor access | A vendor awaiting approval opens the product page | Access denied (403) |
| 6 | Admin approves vendor | The administrator approves a pending vendor | Vendor status becomes approved |
| 7 | Vendor creates product | An approved vendor adds a product with three categories | Product saved and linked to all three categories |
| 8 | Cross-vendor edit | One vendor tries to change another vendor's product | Access denied (403) |
| 9 | Product list shows location | A product from a vendor in Ikeja, Lagos is listed and searched by city, state or location | Location appears on the card; matching product returned and non-matching location excluded |
| 10 | Cart and checkout | A customer adds a product to the cart and checks out | Order placed message shown |
| 11 | Payment reduces stock | A demonstration payment on an order of 2 units from a stock of 10 | Stock falls to 8 |
| 12 | Customer opens dispute | A customer raises a dispute on an order | Dispute submitted message shown |
| 13 | Vendor updates profile | An approved vendor changes the business name, address, city and state | New values saved and reflected in the vendor location |

*Test outcome: when the suite was run for this chapter, `pytest` reported thirteen passed tests and no failures. The warnings were deprecation notices and did not affect the results.*

## 4.4.3 Manual and system test scenarios

Vendor location capture, display and later editing are covered by test cases 3, 9 and 13. The automated suite does not yet cover the payment-verification rules, the low-stock alert or the order-status rules. These were planned as manual scenarios, to be run in mock mode with demonstration data.

| No. | Scenario | Expected result |
| --- | --- | --- |
| M1 | Pay for an order, then submit the payment again | The second attempt is ignored; stock is deducted once |
| M2 | Two customers buy the last units of a product; the second pays after the first | The second payment fails and the order stays pending |
| M3 | Stock falls to the threshold after a sale | Vendor receives one low-stock notification and the product shows as low |
| M4 | Stock reaches zero | Product shows as out of stock and cannot be added to a cart; restocking makes it available again |
| M5 | A vendor tries to skip a status, for example paid to dispatched | Move refused with an invalid-transition message |
| M6 | A customer opens an administrator page | Access denied (403) |
| M7 | A vendor opens another vendor's order | Access denied (403) |
| M8 | A vendor uploads a file that is not an image | Upload rejected |
| M9 | A customer's cart holds items from two vendors and the order is paid | Two vendor orders appear, each only to its own vendor; the customer sees one order |
| M10 | An approved vendor searches, adds another vendor's product and checks out | Order is created as for any customer |

*Record the actual result and date for each scenario when it is run.*

# 4.5 Discussion of Results

The table below compares the specific objectives in Section 1.3 with the implementation.

| Objective | Implemented in | Status |
| --- | --- | --- |
| User management with role-based access and vendor location at registration | Sections 4.3.1, 4.3.4 | Complete; roles and access control enforced, and location captured at registration |
| Administrative module for vendor approval, users, products, activity and disputes | Sections 4.3.2, 4.3.3, 4.3.11 | Complete for vendors, categories, orders, delivery and disputes; vendor location shown during review |
| Vendor dashboard for products, inventory and business profile including location | Sections 4.3.3, 4.3.4, 4.3.8 | Complete; products, inventory and business profile editing, including location, are provided |
| Marketplace with unified cart and checkout, with price and vendor location in search results | Sections 4.3.4, 4.3.5, 4.3.6 | Complete; location displayed on product cards and location search and filtering available |
| Online payment module | Section 4.3.7 | Complete in mock mode and Flutterwave mode; live use not tested here |
| Inventory with automatic deduction and low-stock alerts | Section 4.3.8 | Complete |
| Notifications for orders, payments and status changes | Section 4.3.10 | Complete |

The design choices proved useful in three ways. Keeping business rules in services meant that the demonstration payment and the Flutterwave payment share one completion routine, so the mock mode exercises the same stock and order logic as a live payment. Treating roles as data let a vendor act as a buyer with no extra code. Recording every stock change in a ledger gives a traceable history that supports the stock-accuracy concern raised in Chapter Two (DeHoratius & Raman, 2008).

Review of the code also found points that should be corrected or reported as limitations:

- The demonstration-payment route is hidden from the page when Flutterwave mode is set, but the route itself does not check the configured provider. In a live deployment it should be disabled on the server.
- When any one vendor dispatches its part of a multi-vendor order, the parent order is marked dispatched, even if other vendors have not yet dispatched.
- Manual stock adjustment can only add stock; there is no route for reducing it other than a sale.
- Payment verification and the low-stock alert are not covered by automated tests.

# 4.6 Chapter Summary

This chapter described the implementation of the marketplace on Flask: role-based access, vendor approval, product and inventory management, vendor location capture and location-aware search, a multi-vendor cart, order splitting, server-verified payment with atomic stock deduction, notifications and disputes. It set out thirteen automated tests and ten manual scenarios, and compared the result with the objectives. All seven specific objectives are now represented in the implementation, including vendor location capture, display, search and later editing through the business profile page.
