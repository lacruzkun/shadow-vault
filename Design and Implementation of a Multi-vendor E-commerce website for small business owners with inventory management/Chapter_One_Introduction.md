**CHAPTER ONE:**

**INTRODUCTION**

# 1.1 Background of the Study

Electronic commerce has evolved from a relatively narrow means of conducting transactions over the internet into an important component of contemporary retail and service delivery. The growth of internet connectivity, smartphones, digital payment infrastructure and web technologies has made it possible for consumers to discover, compare and purchase products without being physically present in a conventional store. UN Trade and Development (UNCTAD, 2024) describes digitalisation as an important force reshaping the way businesses operate and consumers interact with markets. This development creates opportunities for small businesses because an online presence can extend their market reach beyond the immediate geographic area in which a physical shop operates.

Within this environment, e-commerce platforms may be implemented using different business models. A conventional single-vendor store generally represents one business and its products, whereas a multi-vendor e-commerce platform provides a common digital marketplace in which independent vendors can offer products to a shared customer base. The platform consequently performs two related functions: it provides a customer-facing shopping environment and it provides management facilities through which vendors and administrators can control the marketplace. This structure can reduce the need for each small business to build and maintain a separate e-commerce system while giving customers a single interface through which products from different vendors can be discovered and purchased.

Product discovery in such a marketplace involves not only finding a product but also deciding which vendor to buy from. Where several vendors list similar items, buyers can compare prices and, if the platform shows it, the location of each vendor. Research on trade on eBay and MercadoLibre found that distance remained an important deterrent to trade between buyers and sellers even online, and that buyers showed a strong tendency to trade with sellers in the same city (Hortaçsu et al., 2009). For a small-business marketplace in Nigeria, where physical delivery is arranged outside the platform, showing each vendor’s location beside the price allows a buyer to prefer a nearby vendor. The same applies to vendors themselves: a business that sells one kind of product may need to buy others from fellow vendors, and so may use the marketplace as a buyer as well as a seller.

The practical value of such systems extends beyond product listing. Small businesses must manage product information, prices, stock quantities, customer orders, payments and post-purchase communication. When these activities are maintained through disconnected channels, the business may have difficulty maintaining accurate stock information and keeping a consistent record of transactions. E-commerce technologies can therefore support the integration of these activities into a common information system. Research on e-business adoption among Nigerian SMEs has identified perceived usefulness, ease of use and privacy among factors relevant to e-commerce implementation, while organisational and technical conditions also influence adoption (International Journal of E-Business Research, 2023).

Payment is another central component of an online marketplace. A customer-facing system must provide a mechanism through which an order can be paid for and the payment state can be associated with the corresponding order. In Nigeria, electronic payment usage has expanded considerably. The Central Bank of Nigeria (2024) reported substantial growth in internet, mobile, point-of-sale and instant-payment transactions, illustrating the importance of electronic channels in contemporary commercial activity. For an e-commerce system serving Nigerian users, this makes the integration of an electronic payment mechanism an important part of the overall transaction process.

However, the success of an online marketplace is not determined by functionality alone. Trust, security and usability are particularly important because online transactions reduce the direct human contact normally present in physical shopping. Gefen, Karahanna and Straub (2003) found that trust is an important component of online shopping intentions alongside perceived usefulness and ease of use. Nigerian-focused research has similarly identified security, trust and usability as important considerations in the adoption and experience of e-commerce websites (Abraham et al., 2021). These considerations imply that a useful marketplace should not only provide transaction functions but should also present clear interfaces, transparent status information and mechanisms for managing customer and vendor relationships.

Inventory management is especially important in a multi-vendor environment because products offered by different vendors are stored and managed independently while being presented through the same marketplace. A system that does not maintain current stock information can permit customers to purchase unavailable items, create inaccurate vendor records and increase the likelihood of fulfilment problems. An integrated inventory mechanism can instead associate stock quantities with products, deduct inventory after successful purchases and provide vendors with alerts when stock reaches a defined low-stock threshold. This allows the marketplace to connect the customer transaction cycle with the vendor's operational responsibilities.

The proposed system is therefore conceived as a web-based multi-vendor e-commerce platform with integrated inventory management for small business owners operating within the Nigerian market. The implementation provides differentiated interfaces and privileges for customers, vendors and administrators. Customers can browse products, search and filter the marketplace, see each vendor’s price and location in the search results, maintain a multi-vendor cart, complete checkout and view orders. Vendors provide their business location when they register, can manage products and inventory, process their vendor-specific orders and purchase from other vendors as buyers, while administrators can oversee vendor approval, categories, orders, delivery status and disputes. The platform also provides internal notifications and payment processing capabilities. Its responsive web interface is intended to provide a coherent experience across desktop and mobile browsers rather than requiring a separate native mobile application.

# 1.2 Statement of the Problem

Despite the opportunities provided by e-commerce, small businesses may face operational difficulties when selling through disconnected online and offline channels. Product information, inventory records, orders, payment confirmations and customer communication may be maintained in separate places, making it difficult to obtain a single reliable view of business activity. In a multi-vendor setting, these challenges become more complex because several independent sellers must operate within the same customer-facing marketplace while their products, stock and order responsibilities remain distinct.

The study addresses the following problems:

**Fragmented product and sales management.** Small vendors may need to maintain product listings, customer enquiries and transaction records through several channels. The absence of a unified marketplace can increase duplication of work and reduce visibility over product and sales information.

**Limited inventory visibility.** When stock quantities are not linked directly to product sales, vendors may not receive timely information about decreasing inventory. This creates the possibility of inaccurate stock records and customer purchases of products that are no longer available.

**Difficulty coordinating multiple vendors.** A shared marketplace needs to distinguish vendor ownership of products and orders. Without role-specific management functions, it becomes difficult to separate vendor responsibilities while maintaining a consistent customer experience.

**Difficulty finding nearby vendors.** When several vendors list similar products, a buyer has no easy way to see where each vendor is located. A buyer, including a vendor who needs to purchase a product from another vendor, may therefore be unable to tell which listings come from vendors close to them without contacting each one separately.

**Insufficient marketplace oversight and trust mechanisms.** A multi-vendor environment requires controls for vendor onboarding, account status, product administration and dispute handling. Without administrative oversight, inappropriate vendors or unresolved customer-vendor conflicts may reduce confidence in the platform. Trust and security are recognised concerns in online shopping, particularly in the Nigerian context (Abraham et al., 2021; Gefen et al., 2003).

**Poor continuity of customer communication.** Customers and vendors need timely information when an order is created, payment is confirmed or an order status changes. Without a central notification mechanism, users may not have a consistent record of relevant updates.

**Limited integration of the customer purchase cycle.** Product discovery, cart management, checkout, payment, inventory deduction and order processing are interdependent activities. Treating them as isolated processes makes it harder to maintain consistent transaction records and operational state.

# 1.3 Aim and Objectives of the Study

The aim of this study is to design and implement a web-based multi-vendor e-commerce platform with integrated inventory management that enables small business owners to market and manage their products efficiently, helps buyers find products from vendors located close to them, and provides customers with a secure and convenient online shopping experience.

The main objective of the study is to develop and implement a multi-vendor e-commerce website for small business owners with integrated inventory management and location-aware product discovery. The specific objectives are to:

- Develop a user management system that supports customer, vendor and administrator accounts with appropriate role-based access control, and that records each vendor’s business location when the vendor creates a profile.

- Create an administrative management module that enables the administrator to approve vendor registrations, manage users and products, monitor platform activities, and manually resolve disputes between customers and vendors.

- Implement a vendor dashboard that enables approved vendors to add, edit, delete and manage their products, inventory and business profile, including their location.

- Build a customer-facing marketplace that enables buyers, including customers and approved vendors purchasing from other vendors, to browse, search for and purchase products from multiple vendors through a unified shopping cart and checkout system. Search results shall show each matching product’s price and the vendor’s location, so that the buyer can choose a vendor close to them or continue browsing.

- Implement an online payment module that enables customers to complete purchases using supported electronic payment methods.

- Develop an inventory management system that automatically updates product stock levels after successful purchases and generates low-stock alerts for vendors.

- Implement a notification system that delivers relevant updates to customers and vendors, including order confirmations, payment confirmations and changes in order status.

# 1.4 Significance of the Study

The study is significant to the major stakeholders involved in the operation and use of a multi-vendor e-commerce platform.

**To small business owners and vendors.** The platform provides an organised online storefront through which vendors can publish products, maintain inventory records and manage their own orders. This can reduce the need to construct separate e-commerce systems and provides vendors with a central place for routine marketplace operations. Vendors also become visible to nearby buyers through their displayed location, and can buy from other vendors through the same marketplace.

**To customers.** Customers can access products from multiple vendors through a single marketplace, maintain a unified cart, complete checkout and view order information. Showing each vendor’s location beside the price lets customers prefer vendors close to them. Clear transaction states, responsive interfaces and internal notifications are intended to make the purchasing process easier to understand and use.

**To marketplace administrators.** The administrative module provides tools for vendor approval, category management, product and order oversight, delivery-status administration and dispute resolution. These controls help the administrator maintain platform governance and respond to issues that cannot be handled directly between a customer and a vendor.

**To the Nigerian e-commerce ecosystem.** The project demonstrates how a locally oriented multi-vendor marketplace can combine product management, inventory control, electronic payment and customer-management functions within one web-based system. This is relevant in a context where electronic payment channels and online commerce continue to develop (Central Bank of Nigeria, 2024; UNCTAD, 2024).

**To researchers and students.** The project provides a practical case study in requirements analysis, role-based web application development, database design, service-oriented business logic, payment integration, inventory management and responsive user-interface design. It may also serve as a basis for later research into marketplace optimisation, analytics, trust, logistics and other advanced e-commerce capabilities.

# 1.5 Scope of the Study

This study is limited to the design and implementation of a web-based multi-vendor e-commerce platform for small business owners operating within the Nigerian market. The system provides distinct interfaces and permissions for customers, vendors and administrators and supports the core software processes necessary for basic multi-vendor e-commerce transactions.

The functional scope of the system comprises:

- user registration, authentication and role-based authorization for customers, vendors and administrators;

- vendor registration and an administrative approval, rejection and suspension workflow;

- vendor product creation, editing, activation/deactivation and image management;

- product browsing, searching, filtering, sorting and pagination for customers;

- capture of each vendor’s business location at registration and display of that location, with the price, for each product in search results;

- use of the marketplace, cart and checkout by approved vendors as buyers;

- persistent shopping carts that can contain products from multiple vendors;

- checkout and server-side order-total calculation with shipping information;

- payment processing through the supported payment configuration, including the project's demonstration/mock payment mode and integration pathway for Flutterwave;

- multi-vendor order processing in which a customer order can be associated with vendor-specific sub-orders;

- vendor inventory management, automatic stock deduction after successful purchases and low-stock notifications;

- vendor-side order processing and administrator management of delivery-related order statuses;

- internal notifications concerning order, payment and status events;

- customer dispute creation and administrator dispute review and resolution;

- administrative management of categories, vendors, orders and other marketplace activities; and

- a responsive web interface designed for desktop, tablet and mobile browsers.

The scope deliberately excludes management of physical delivery logistics. Although the application records and manages software-level order and delivery status information, the actual transportation and physical fulfilment of goods are assumed to be carried out by external delivery providers. The study also excludes automated distance calculation and route planning, since proximity is judged by the buyer from the displayed vendor location, as well as automated disbursement or payout of funds to vendors, advanced enterprise features such as multi-branch inventory synchronisation and warehouse management, sophisticated business intelligence and analytics, customer loyalty programmes, and the development of a separate native Android or iOS application. The responsive browser interface is therefore the mobile-access strategy for the system.

# 1.6 Limitations of the Study

The study is subject to limitations arising from the operational environment, the chosen technology scope and the resources available for development.

- Internet dependence. The platform is web-based and requires a functional internet connection for users to access marketplace, account, order, payment and notification features. Poor connectivity or service interruptions may affect availability and transaction completion.

- Payment environment. The implementation includes a demonstration/mock payment mode and a pathway for Flutterwave integration. Behaviour observed in demonstration mode cannot by itself represent every characteristic of live financial transactions, settlement procedures or payment-provider outages.

- External delivery dependency. The application can record and update order-related delivery statuses, but it does not control the physical movement of products. Delivery time, transport quality, route availability and other logistics outcomes therefore remain dependent on external providers.

- No automated vendor payout mechanism. The system records customer payments and marketplace orders but does not implement automated distribution or settlement of funds to individual vendors.

- Vendor-declared location. Vendor location is entered by the vendor and displayed as supplied. The system does not calculate distances between buyer and vendor or independently verify declared locations, so accuracy depends on vendor input and on any check made when vendors are approved.

- Limited enterprise inventory scope. Inventory is managed at the product level within the marketplace; advanced warehouse management, multi-branch synchronisation, procurement forecasting and other enterprise inventory capabilities are outside the project scope.

- Platform-specific design context. The system is designed around the requirements of a Nigerian small-business marketplace. Deployment in another country or a substantially different business environment may require changes to payment, regulatory, currency, delivery and user-interface assumptions.

- Resource and project-time constraints. The study focuses on the core marketplace functions required for a functional multi-vendor platform. Advanced analytics, recommendation engines, loyalty programmes, artificial-intelligence features and other enhancements are therefore reserved for future work.

- Operational evaluation. The system's effectiveness in a larger commercial environment will depend on factors such as real user volume, infrastructure capacity, payment-provider availability and the operating practices of participating vendors. These factors may require further evaluation after deployment.

# References

Abraham, G. T., Osaisai, E. F., Dienagha, N. S., & Ineyekineye, A. (2021). Usability, security and trust of e-commerce websites: The effect on the Nigerian e-shopper. Asian Journal of Research in Computer Science, 10(4), 58–68. https://doi.org/10.9734/AJRCOS/2021/v10i430250

Central Bank of Nigeria. (2024). Payment modes in Nigeria. Central Bank of Nigeria Payment System. https://www.cbn.gov.ng/PaymentsSystem/modes.html

Gefen, D., Karahanna, E., & Straub, D. W. (2003). Trust and TAM in online shopping: An integrated model. MIS Quarterly, 27(1), 51–90. https://doi.org/10.2307/30036519

Hortaçsu, A., Martínez-Jerez, F. A., & Douglas, J. (2009). The geography of trade in online transactions: Evidence from eBay and MercadoLibre. American Economic Journal: Microeconomics, 1(1), 53–74. https://doi.org/10.1257/mic.1.1.53

Igudia, P. O. (2016). An integrated model of the factors influencing the adoption and extent of use of e-payment systems by SMEs in Nigeria. Information and Knowledge Management, 6(12), 7–32.

UN Trade and Development. (2024). Digital Economy Report 2024: Shaping an environmentally sustainable and inclusive digital future. United Nations Conference on Trade and Development. https://unctad.org/publication/digital-economy-report-2024

Online shopping: A systematic review of customers’ perceived benefits and challenges during COVID-19 pandemic. (2024). Global Knowledge, Memory and Communication, 74(7–8), 2655–2672. https://doi.org/10.1108/GKMC-04-2023-0129

Factors influencing the usage of e-business to improve SME performance. (2023). International Journal of E-Business Research, 19(1). https://doi.org/10.4018/IJEBR.324065
