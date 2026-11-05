**CHAPTER FIVE:**

**SUMMARY, CONCLUSION AND RECOMMENDATIONS**

# 5.1 Introduction

This chapter closes the study. It summarises the work reported in the previous four chapters, states what was found when the system was compared with the objectives in Section 1.3, draws conclusions, and makes recommendations for improving the system and for further research.

# 5.2 Summary of the Study

The study set out to design and implement a web-based multi-vendor e-commerce platform with integrated inventory management for small business owners in the Nigerian market. Chapter One described the problem: small vendors often keep product records, stock, orders and payments in separate places, which gives them little visibility over their business, while buyers have difficulty comparing vendors and finding sellers close to them. The chapter set an aim, seven specific objectives, the scope and the limitations of the work.

Chapter Two reviewed the literature on multi-vendor marketplaces, established platforms such as Amazon, Taobao, Jumia, Etsy and Jiji, the role of location in online trade, trust and security, electronic payment, inventory accuracy, usability and software architecture. It concluded that these concerns are usually studied one at a time, whereas a working marketplace must make them operate together. The study therefore located its contribution in the integration of established ideas within a bounded platform for Nigerian small businesses.

Chapter Three analysed existing ways of selling online, identified the users, and defined 18 functional and six non-functional requirements. It then presented the design: a role-based interface, a three-tier architecture with a service layer, a checkout and payment process, security measures and a relational database of 21 tables.

Chapter Four described the implementation, called LocalMarket, built with Python and Flask on a SQLite or PostgreSQL database, with a Bootstrap 5 front end. It covered authentication and role-based access, vendor approval, product and category management, vendor location capture and location-aware search, a multi-vendor cart and checkout, payment in mock and Flutterwave modes, inventory control, order processing, notifications and disputes. It also set out thirteen automated test cases and ten manual test scenarios and compared the result with the objectives.

# 5.3 Summary of Findings

The findings below follow the specific objectives in Section 1.3.

**User management and access control.** The system supports customer, vendor and administrator accounts. Access is enforced by decorators on the routes, so a pending vendor, a customer or another vendor cannot reach pages that belong to someone else. Because roles are stored as data, a vendor also holds the customer role and can buy from other vendors without any extra code. A vendor's business address, city and state are recorded at registration and stored on the vendor profile.

**Administrative management.** The administrator can approve, reject, suspend and reactivate vendors, with each action recorded in a status history and notified to the vendor. The administrator also manages categories, orders, delivery statuses and disputes.

**Vendor dashboard.** Approved vendors create, edit, activate, deactivate and delete their own products, with images and several categories per product, and manage stock on an inventory page. The business location supplied at registration is shown on the vendor's storefront and product listings, and a business profile page lets the vendor update the business name, description, contact details, address, city and state after registration.

**Marketplace, cart and checkout.** Customers, and vendors acting as buyers, can search by text, category, price, vendor, location and availability, sort and page through results, keep a cart containing products from several vendors, and check out. The server recalculates totals and splits each order into one vendor order per vendor. Each product card shows the vendor's location alongside the product's price, vendor and category, and the same location can be used as a search filter.

**Payment.** One completion routine serves both the demonstration mode and the Flutterwave mode. It rejects repeated payments and payments whose amount or currency does not match the order, and in Flutterwave mode it acts only on a response verified with the provider (Flutterwave, n.d.). Live payments were not tested.

**Inventory.** Stock is deducted inside the payment transaction under a condition that prevents overselling. Every change is written to a ledger, a low-stock alert is sent once when stock crosses the threshold, and a product at zero is marked out of stock. This supports the stock-accuracy concern raised in the literature (DeHoratius & Raman, 2008).

**Notifications.** Users receive internal notifications for vendor status changes, payments, order progress, delivery updates, low stock and disputes, with an unread count shown on every page.

**Design and architecture.** Keeping the business rules in a service layer meant that the demonstration payment exercises the same order and stock logic as a live payment, which is the kind of separation of concerns recommended by Fowler (2002).

*Note on testing: when the automated suite was run for this report, `pytest` reported thirteen passed tests and no failures. The manual scenarios M1 to M10 had not been run when this chapter was drafted, so no results are claimed for them.*

# 5.4 Conclusion

The study aimed to design and implement a multi-vendor e-commerce platform with integrated inventory management that lets small business owners sell and manage their products and gives buyers a convenient and secure way to shop. On the evidence of Chapter Four, the aim has been largely achieved. The platform provides role-based accounts, administrator control over vendor onboarding, vendor product and inventory management, a unified multi-vendor cart and checkout, server-verified payment, automatic stock deduction with low-stock alerts, notifications and dispute handling, all through a responsive web interface that works on desktop and mobile browsers.

The location-aware objective is now met at the registration, profile editing and display stages: vendors give their address, city and state at registration, can correct them from a business profile page, and the marketplace shows the location on product cards and permits location search, so buyers can prefer sellers near them. The system does not calculate distance or rank by proximity; the buyer judges this from the declared location, as the scope in Section 1.5 states. This matters because the literature found that buyers favour sellers near them (Hortaçsu et al., 2009) and that platforms such as Etsy and Jiji attach seller location to listings. All seven specific objectives are therefore represented in the implementation.

The study also supports the position taken in Chapter Two that the value of such a system lies in connecting functions that are usually treated separately, such as payment with inventory and governance with trust (Hagiu & Wright, 2015). Orders, payments and stock are updated together in one transaction, which keeps the vendor's stock record consistent with what customers have bought.

Finally, the results come from a demonstration environment. The payment provider was not used for live transactions and the system has not been tested with real vendors or real order volumes, so conclusions about its commercial effectiveness remain provisional.

*Update the manual test results when scenarios M1 to M10 have been run.*

# 5.5 Contribution of the Study

The study contributes a working example of a small-business marketplace built for the Nigerian setting. It shows how vendor approval, multi-vendor order splitting, atomic stock deduction, server-verified payment, notifications and dispute handling can be combined in one modular web application. For students and researchers it is a case study in requirements analysis, role-based web development, database design and payment integration. For small businesses it demonstrates a way to sell online and manage stock without building a separate system for each business.

# 5.6 Recommendations

The following recommendations arise from the findings and from the points noted in Section 4.5.

**Correct the issues found in the code review.**

- Disable the demonstration-payment route on the server whenever Flutterwave mode is configured, rather than only hiding the button.
- Mark a multi-vendor parent order as dispatched only when every vendor has dispatched its part.
- Add a route for reducing stock manually, so that damaged or lost goods can be recorded.

**Extend the automated tests.** Add tests for payment verification, the low-stock alert and the order-status rules, which are currently checked only by manual scenarios.

**Test live payment before deployment.** Run the Flutterwave integration with the provider's test environment and then with small real transactions, covering failed payments, repeated callbacks and provider outages.

**Pilot with real vendors.** Deploy the system for a small group of vendors and buyers, and collect feedback on usability, speed on mobile connections and the usefulness of the location display, before wider release.

**Plan vendor payouts.** Automated settlement of funds to vendors was outside this study, but a marketplace in real use needs it. A payment provider that supports split payments could be considered.

# 5.7 Suggestions for Further Research

Future work could extend the study in several directions:

- calculating the distance between buyer and vendor and ranking results by proximity, with the buyer's consent to share their location;
- integrating delivery providers so that order tracking reflects actual movement of goods;
- adding vendor sales and stock analytics, and demand forecasting for restocking;
- measuring the effect of showing vendor location on buyers' choices and on small vendors' sales;
- building a native or installable mobile application, with offline support for areas with unreliable connectivity; and
- studying trust mechanisms such as vendor ratings and reviews in the Nigerian small-business context.

# 5.8 Chapter Summary

This chapter summarised the study, set out its findings against the objectives, and concluded that the platform meets the objectives for user management, administration, vendor and inventory management, marketplace purchase, location-aware discovery, payment and notifications. It recommended correcting the issues noted in the code review, extending the tests, testing live payment and piloting with real vendors, and it proposed directions for further research.

# References

DeHoratius, N., & Raman, A. (2008). Inventory record inaccuracy: An empirical analysis. *Management Science, 54*(4), 627–641. https://doi.org/10.1287/mnsc.1070.0789

Flutterwave. (n.d.). *Transaction verification*. Flutterwave Developer Documentation. https://developer.flutterwave.com/docs/transaction-verification

Fowler, M. (2002). *Patterns of enterprise application architecture*. Addison-Wesley.

Hagiu, A., & Wright, J. (2015). Multi-sided platforms. *International Journal of Industrial Organization, 43*, 162–174. https://doi.org/10.1016/j.ijindorg.2015.03.003

Hortaçsu, A., Martínez-Jerez, F. A., & Douglas, J. (2009). The geography of trade in online transactions: Evidence from eBay and MercadoLibre. *American Economic Journal: Microeconomics, 1*(1), 53–74. https://doi.org/10.1257/mic.1.1.53
