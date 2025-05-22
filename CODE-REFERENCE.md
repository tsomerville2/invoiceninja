# Invoice Ninja Code References

This document maps the features listed in `FEATURES.md` to their corresponding code locations within the Invoice Ninja application. This helps in understanding where each feature is implemented.

## I. Invoicing & Billing

*   **Create & Manage Professional Invoices:**
    *   Controller: `app/Http/Controllers/InvoiceController.php` (Class: `InvoiceController`, Methods: `store`, `update`, `show`, `edit`, `destroy`)
    *   Factory: `app/Factory/InvoiceFactory.php`
    *   Model: `app/Models/Invoice.php`
    *   Repository: `app/Repositories/InvoiceRepository.php`
    *   Service: `app/Services/Invoice/InvoiceService.php`
*   *Design with Pre-built Templates:*
    *   Templates Directory: `resources/views/pdf-designs/`
    *   Service: `app/Services/PdfMaker/Design.php` (manages design selection/application)
    *   Model: `app/Models/Design.php`
    *   Controller: `app/Http/Controllers/DesignController.php`
*   *Customize with Your Logo & Branding:*
    *   Controller: `app/Http/Controllers/CompanyController.php` (Method: `upload` for logo, general settings update)
    *   Model: `app/Models/Company.php` (Stores `company_logo` and branding settings)
    *   PDF Services: `app/Services/Pdf/*`, `app/Services/PdfMaker/*` (utilize company settings)
*   *Support for Multiple Currencies:*
    *   Model: `app/Models/Currency.php`
    *   Company Settings: `app/Models/Company.php` (stores default and client-specific currency settings)
    *   Utility: `app/Utils/Number.php` (potential currency formatting)
    *   Seeder: `database/seeders/CurrenciesSeeder.php`
*   *Automatic Tax Calculation:*
    *   Model: `app/Models/TaxRate.php`
    *   Controller: `app/Http/Controllers/TaxRateController.php`
    *   Service: `app/Services/Invoice/Taxer.php` (applies taxes)
    *   Models: `app/Models/Invoice.php`, `app/Models/InvoiceItem.php` (store applied taxes)
*   *Line Item Discounts & Totals:*
    *   Model: `app/Models/InvoiceItem.php` (Fields: `discount`, `quantity`, `cost`)
    *   Services: `app/Services/Invoice/InvoiceSum.php`, `app/Services/Invoice/InvoiceItemSum.php` (calculate totals)
*   *Due Dates & Payment Terms:*
    *   Model: `app/Models/Invoice.php` (Fields: `due_date`, `terms`)
    *   Model: `app/Models/PaymentTerm.php`
    *   Controller: `app/Http/Controllers/PaymentTermController.php`
*   **Automate Billing with Recurring Invoices:**
    *   Model: `app/Models/RecurringInvoice.php` (Properties: `frequency_id`, `next_send_date`, `auto_bill`; Trait: `App\Utils\Traits\Recurring\HasRecurrence`)
    *   Service: `app/Services/Recurring/RecurringService.php` (Handles starting, stopping, sending)
    *   Controller: `app/Http/Controllers/RecurringInvoiceController.php`
    *   Cron Job: `app/Jobs/Cron/RecurringInvoicesCron.php` (dispatches `app/Jobs/RecurringInvoice/SendRecurring.php`)
    *   Events: `app/Events/RecurringInvoice/` (Directory for lifecycle events)
    *   *Flexible Scheduling:*
        *   Model: `app/Models/RecurringInvoice.php` (Property: `frequency_id` with constants like `FREQUENCY_DAILY`, `FREQUENCY_MONTHLY`; Methods: `nextSendDate()`, `recurringDates()`)
    *   *Auto-Bill & Auto-Pay Options:*
        *   Model: `app/Models/RecurringInvoice.php` (Properties: `auto_bill`, `auto_bill_enabled`)
        *   Service: `app/Services/Invoice/AutoBillInvoice.php` (Manages auto-billing process)
        *   Related Model: `app/Models/ClientGatewayToken.php` (for stored payment methods)
        *   Payment Drivers: `app/PaymentDrivers/` (Directory for payment processing)
        *   Cron Job Logic: `app/Jobs/Cron/RecurringInvoicesCron.php` (conditional stopping of auto-billing)
*   **Send Professional Quotes & Proposals:**
    *   Controller: `app/Http/Controllers/QuoteController.php` (Class: `QuoteController`, Methods: `store`, `update`, `show`, `action` for sending)
    *   Factory: `app/Factory/QuoteFactory.php`
    *   Model: `app/Models/Quote.php`
    *   Repository: `app/Repositories/QuoteRepository.php`
    *   Service: `app/Services/Quote/QuoteService.php` (e.g., Method: `sendEmail`)
    *   Invitations Model: `app/Models/QuoteInvitation.php`
    *   PDF Service: `app/Services/Quote/GetQuotePdf.php`
    *   Email Job: `app/Jobs/Entity/EmailEntity.php` (handles general entity emailing)
    *   *Convert Quotes to Invoices:*
        *   Controller Logic: `app/Http/Controllers/QuoteController.php` (within `action()` method, case `convert_to_invoice`)
        *   Service: `app/Services/Quote/ConvertQuote.php`
        *   Factory: `app/Factory/CloneQuoteToInvoiceFactory.php`
        *   Model Update: `app/Models/Quote.php` (status changes, e.g., `Quote::STATUS_CONVERTED`)
    *   *Quote Expiration Tracking:*
        *   Model: `app/Models/Quote.php` (Field: `valid_until`)
        *   Job: `app/Jobs/Quote/QuoteCheckExpired.php` (updates status of expired quotes)
        *   Controller/Repository: Logic for setting `valid_until` during quote creation/update.
*   **Issue Credit Notes & Manage Refunds:**
    *   Credit Notes:
        *   Controller: `app/Http/Controllers/CreditController.php` (Class: `CreditController`, Methods: `store`, `update`, `show`, `action`)
        *   Factory: `app/Factory/CreditFactory.php`
        *   Model: `app/Models/Credit.php`
        *   Repository: `app/Repositories/CreditRepository.php`
        *   Service: `app/Services/Credit/CreditService.php` (e.g., Methods: `sendEmail`, `applyPayment`)
        *   PDF Service: `app/Services/Credit/GetCreditPdf.php`
        *   Invitations Model: `app/Models/CreditInvitation.php`
    *   Refunds (Payment Related):
        *   Controller: `app/Http/Controllers/PaymentController.php` (Method: `refund`)
        *   Service: `app/Services/Payment/RefundPayment.php`
        *   Payment Drivers: `app/PaymentDrivers/` (Directory containing specific gateway refund logic)
        *   Model: `app/Models/Payment.php` (Status updates or negative entries for refunds)
        *   Event: `app/Events/Payment/PaymentWasRefunded.php`
