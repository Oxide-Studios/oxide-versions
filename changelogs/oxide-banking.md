# Changelog

All notable changes to Oxide Banking will be documented in this file.

## [2.2.1] - 2026-10-23

### Changed
- The `ProcessWireTransfer` export takes whole dollars from $1 up to `Config.Transactions.MaxTransferAmount`, like transfers on the bank screen (see `docs/EXPORTS.md`).

### Fixed
- Reliability fixes for deposits, withdrawals, `/givecash` and wire transfers. Banking admin commands now also work from the server console.

## [2.2.0] - 2026-10-04

### Added
- **Phone app** - When `oxide-phone` is running, players can install a Banking app from the phone's App Store: Home (total balance and account cards), Activity (search and filters), Payments (send to a payment ID or recent payee, request money as a bill, move money between your own accounts including savings, pay bills, cancel scheduled transfers), Insights (six months of spending by category) and More (payment ID, shared-account members, cards, loan instalments). Turn it off with `Config.Phone.UsePhone`. Deposits and withdrawals are never offered on the phone.
- **Balance widget** - A small or medium phone home-screen widget with the player's balance. Players can hide the amount.
- **Phone notifications** - Money received, scheduled transfers delivered and failed recurring payments, plus the existing bill, loan and investment notices. Opening the matching screen clears them.
- **ox_inventory item template** - `itemimages/ox_inventory_items.lua` holds a paste-ready `bank_card` entry, with `itemimages/bank_card.png`.

### Changed
- **Whole-dollar amounts** - Every amount is whole dollars, at least $1, and fees are charged in whole dollars too. An amount with cents or under $1 is refused with a message saying so, before any money moves.
- **Recurring payments follow the transfer rules** - They now check frozen accounts, `MaxTransferAmount` and the daily transfer limit, and ask for the security PIN at `TwoFactorThreshold` or more when a payment is set up or its amount is raised.
- **Failed recurring payments** - When a recurring payment cannot be made, no money moves and the payer is told why if they are online. The next payment date still moves on.
- **Business reports** - Expenses, reports and job activity on the Business screen cover only your current job's account, and only for that job's boss. Business-tier players and players listed on a job or gang account no longer see them.
- **Transfer notes** - Notes are limited to 60 characters.
- **Refused transfers** - The bank screen now shows why a transfer was refused.

### Fixed
- **Business screen privacy** - A job boss could see other players' personal account activity from the Business screen.
- **Transfers** - The recipient is paid only after the money has left the sender's account, and the sender is refunded, fee included, if it cannot be delivered.
- **Recurring payments** - A recurring payment no longer pays the recipient when the payer's money could not be taken, and each payment runs only once per due date.
- **Scheduled transfers** - A scheduled transfer that cannot be delivered is now marked failed and refunded. It used to be marked completed with the money gone. A transfer can no longer be both delivered and refunded.
- **Failed refunds** - When a payment cannot be delivered and returning the money also fails, the player is no longer told the money is back. Every admin online is alerted and the case is written to `bank_audit_log` as `refund_failed` (see `docs/ADMIN.md`).
- **Amounts with cents** - A deposit, withdrawal or transfer with cents could record one amount and move another. Amounts are now whole dollars from start to finish.
- **Notifications** - Paying a bill no longer clears the notice for an unrelated investment, and an investment notice no longer replaces a bill notice.
- **Job invoices** - Invoices to a job account whose name differs only in capital letters (for example `LSPD` and `lspd`) can be paid again after a restart.

### Documentation
- `docs/CONFIGURATION.md` - Added `Config.Phone.UsePhone` and the whole-dollar rule.
- `docs/FEATURES.md` - Added the phone app, the whole-dollar rule, the transfer note limit, the recurring payment rules and failed refunds.
- `docs/ADMIN.md` - New "Failed Refunds" section: what admins see and how to find the cases in `bank_audit_log`.
- `docs/INSTALLATION.md` - Added the phone app section and its `o-link` 1.11.0 requirement, and the ox_inventory step now uses `itemimages/ox_inventory_items.lua`.

### Migration Guide
1. No database changes.
2. Copy the new `Config.Phone` block from `config/config.lua` into your own config. Without it, the phone app is on.
3. The phone app needs `o-link` 1.11.0 or newer and `oxide-phone`. On an older `o-link` everything else works and the app does not appear.
4. Restart the whole server, not only the resource. This version adds new files.

## [2.1.0] - 2026-09-21

### Added
- **Redesigned Services screen** - Loans, bills, savings, investments, cards, and credit each have their own tab, plus an Overview tab showing what needs attention and what is due in the next two weeks.
- **Tablet app** - When `oxide-tablet` is running, the bank appears as a tablet app with a badge for bills and loan payments due soon, tray notifications, and an **Upcoming payments** home-screen widget. Bank locations still open the bank's own window. Turn it off with `Config.Tablet.UseTablet`. Deposits and withdrawals are hidden on the tablet unless `Config.Tablet.AllowCashActions` is `true`.
- **Savings notice period** - Money withdrawn from savings soon after it was paid in is charged `earlyWithdrawalPenalty`. Money that has sat for `withdrawalNoticeDays` (default 7) comes out free. The savings screen shows how much is free to withdraw, and the fee, before a withdrawal is confirmed.
- **Invoice late fees and collection** - Late fees grow for each day an invoice stays unpaid (`InvoiceLateFeeDailyPercent`). Overdue invoices are collected from the debtor's bank account after `InvoiceAutoDebitAfterDays`. Turn collection off with `InvoiceAutoDebitEnabled = false`. Unpaid invoices also lower the debtor's credit score.
- **Invoice limits** - `InvoiceMaxAmount` and `MaxOutstandingInvoicesPerRecipient` cap invoices players write from the banking screen. Invoices from other resources (fines, medical bills, vehicle finance) are not affected.
- **Invoice from the business** - Bosses can send an invoice from the business account on the Business screen.
- **Total borrowing limit** - `Config.Loans.exposure` caps how much one player can owe across all their loans.
- **Position limits** - `maxPositionsPerProduct` and `maxTotalPositions` for investments, `maxPerProduct` and `maxTotal` for certificates of deposit.
- **Early cash-out for investments** - Investments can be withdrawn before they mature. Each product's `earlyWithdrawalPenalty` is taken from the earnings only, never from the amount invested.
- **Credit score settings** - `Config.CreditScore` now sets the score range, how far credit events can move the score, how quickly that fades, and the points for each event (`eventDeltas`).
- **Command suggestions** - Every banking command now shows chat suggestions, and command help text and chat replies can be translated in `locales/en.json`.
- **`/forceinvoices collect`** - Marks overdue invoices and also collects them.

### Changed
- **Banking days follow the calendar** - A day the server was off at midnight, such as during a nightly restart, is now counted when the server starts again.
- **Loan repayment** - Loans are repaid in instalments every `LoanPaymentInterval` days, taken automatically even when the borrower is offline. Paying off early refunds the interest for instalments that never ran. Loans in collections can be paid from the bank screen.
- **Loan applications** - Applications waiting for approval count toward `maxActiveLoans`, and the application shows an eligibility check before it is sent. `Config.LoanApproval.processingTime` is in real hours. The Emergency Loan now needs an account at least 1 day old.
- **Rates rebalanced** - Savings pays 1.25x the tier rate (was 2x). Certificate of deposit rates are annual: 4%, 6%, and 8%. Expected investment returns: savings bond 9%, stock index 12%, crypto fund 15%, real estate 11%.
- **Investments** - A position holds its value once it matures until the player cashes it out. Investing follows each tier's `investments` setting, so Basic accounts cannot invest by default.
- **Savings minimum balance** - A savings account can be emptied, but a withdrawal cannot leave it below `minimumBalance`.
- **Fee settings trimmed** - `config/fees.lua` now lists only the fees the bank charges. ATM and external transfer fees stay available to other resources through the `CalculateATMFee` and `CalculateTransactionFee` exports.
- **`WithdrawFromSavings` export** - The `applyPenalty` argument has been removed. The notice period decides the penalty.

### Fixed
- Reliability fixes across loans, savings, investments, certificates of deposit, invoices, cards, and credit scores.
- **Overdraft** - An overdraft withdrawal now empties the bank balance and records the rest as owed. The amount owed shows on the banking screen and is paid back from the next money into the account.
- **Security PIN** - Players who turn on the security PIN are asked for it before withdrawals and transfers of `TwoFactorThreshold` or more.
- **ATM PIN lockout** - Too many wrong ATM PINs now lock PIN entry (`MaxLoginAttempts`, `LockoutDuration`).
- **Fraud alerts** - Large withdrawals, deposits and transfers, and bursts of transactions, now alert online admins (`EnableFraudDetection`).
- **ESX transfers** - Transfers to other players now work on ESX.
- **Tier upgrades** - An upgrade no longer takes the first month's fee when the player cannot also pay the upgrade fee.
- **`GetPlayerStatements` export** - `statement_type` and `date` are now filled in.

### Documentation
- All docs checked against the code and corrected.
- `docs/CONFIGURATION.md` - Covers the savings notice period, loan repayment, the borrowing limit, investments, certificates of deposit, credit score settings, invoice late fees and collection, and the trimmed fee settings.
- `docs/FEATURES.md` - Added the Services screen and the tablet app.
- `docs/EXPORTS.md` - Updated the savings, loan, invoice, and credit exports, and added `SettleInvoice`.
- `docs/ADMIN.md` - Added `/forceinvoices collect`.
- `docs/INSTALLATION.md` - Added the update step for `sql/migrations/`.

### Migration Guide
1. Back up your database.
2. Before you start 2.1.0, run the three files in `sql/migrations/` once:
   - `credit_hybrid_and_market_drift.sql`
   - `savings_notice_period_and_invoice_fees.sql`
   - `overdraft_owed.sql`

   All three are safe to run more than once. They are written for MariaDB. On MySQL 8, remove `IF NOT EXISTS` from each `ADD COLUMN` and `ADD INDEX` line, and skip any column that already exists.
3. Copy the new settings from `config/` into your own config files. Several unused settings were removed from `config/fees.lua`.
4. Restart the whole server, not only the resource. This version adds new files.

Existing savings balances are dated from the day each account was opened, so money in an account older than the notice period comes out free. In an account opened less than `withdrawalNoticeDays` ago, the existing balance becomes free once the account reaches that age. Open loans move onto the new repayment schedule automatically the first time the server starts.


## [2.0.2] - 2026-09-01

### Fixed
- **Debit card limit counted every card on the server** - Personal checking cards were counted server-wide instead of per character, so once the server reached the card limit no one could order a new debit card. Card limits and card lists are now per character. Shared, job, and gang accounts are unaffected.
- **Paid invoices could be left unpaid** - A failure while writing statement history no longer undoes a payment that already went through. The money moves, the invoice is marked paid, and the failure is written to the server log instead.
- **Invoice actions failed silently** - Creating, paying, or cancelling an invoice now shows a clear error message instead of leaving the banking screen unresponsive when the server hits a problem.
- **Blank invoice confirmations** - Success messages now show the correct invoice number and paid amount.

### Changed
- **Admin commands use your framework's admin check** - Every banking admin command (listed in `docs/ADMIN.md`) now uses the standard admin check instead of requiring a separate `group.admin` ACE grant in `server.cfg`. Existing framework admins keep access with no config changes, and the server console can still run every command.

### Documentation
- `docs/EXPORTS.md` - Documented the new optional `citizenid` argument on `GetCardsForAccount` and `GetAccountCardCount`.


## [2.0.0] - 2026-04-15

### Added
- **Cross-framework support via `o-link`** - QBCore, QBX, and ESX are all supported with zero configuration. Framework is auto-detected at runtime.
- **`o-link` bridge layer** - `bridge/_loader.lua` exposes the `olink` global plus shared `FormatNumber` / `FormatMoney` helpers used by every server module.

### Changed
- **NUI Updated** - NUI style has been updated along with polishing out previously incomplete features. 
- **Server modules split for clarity** - `server/credit.lua`, `server/security.lua`, `server/business.lua`, `server/interest.lua`, `server/scheduler.lua`, and `server/compat.lua` are now standalone modules (previously folded into fewer files).
- **Config split into five files** - `config/config.lua`, `config/accounts.lua`, `config/loans.lua`, `config/interest.lua`, `config/fees.lua` are loaded as `shared_scripts` for cleaner separation of banking, tier, loan, interest, and fee settings.
- **`provides { 'qb-banking' }`** is now declared in `fxmanifest.lua` so legacy resources requesting `qb-banking` resolve to oxide-banking.
- **Inventory access standardized on `o-link.inventory`** - bank card inventory checks and item metadata reads now go through the bridge regardless of the active inventory resource.
- **ATM and branch interaction routed through `o-link.target`** when `Config.UseTarget = true`, with proximity fallback when `false`.

### Database Migration
Re-run `sql/install.sql` on existing installations. All statements use `IF NOT EXISTS` and are safe to re-run — existing data is not touched.


## [1.3.0] - 2026-01-27

### Added
- **Transaction Limits** - Configurable limits for deposits, withdrawals, and transfers to prevent excessive transaction amounts
- **Debug Mode Security** - New security settings for debug mode in production environments
  - Auto-timeout for debug sessions
  - Debug action logging
  - Restricted debug access controls
- **Database Connection Resilience** - Retry logic with exponential backoff during resource startup
- **SafeExecute Wrapper** - Error handling wrapper for scheduled tasks ensures failures don't interrupt execution flow
- **Global FormatNumber Function** - Consistent number formatting across all banking operations
- **ox_inventory Support** - Item metadata retrieval now supports both `qb-inventory` and `ox_inventory`
  - New `Config.UseOxInventory` option

### Changed
- **Rebranded to Oxide Banking** - All references updated from QB-Banking-Plus to Oxide Banking
- **Simplified Low Balance Alert** - Removed threshold parameter for cleaner API
- **In-Game Day Increment** - Now triggers all scheduled daily tasks automatically
- **Enhanced Startup Stability** - Player data retrieval during resource restarts handled gracefully

### Documentation
- Added new admin commands to README: `/forcecardcleanup`, `/forcedividends`, `/pendingloans`, `/approveloan`, `/denyloan`
- Updated ADMIN.md with clarifications on day increment behavior
- Clarified `bank_cards` export includes hashed PINs for security

## [1.2.1] - 2026-01-22

### Added
- **Transfer Delays with Expedited Option** - External transfers now have configurable processing delays
  - `Config.TransferDelays` - Configure delay hours for internal/external/wire transfers
  - Expedited transfers available for instant processing (+$25 fee)
  - Pending transfers UI with cancel option
  - New `bank_pending_transfers` table for tracking delayed transfers
- **Loan Processing Time** - Non-instant loans now have configurable processing time
  - `Config.LoanApproval.processingTime` - Hours before loan is auto-approved (default: 2)
  - New `processing` status for loans being processed
  - Scheduler processes pending loans hourly
- **Account Audit Trail** - Comprehensive audit logging system
  - `LogAuditEvent()` function for tracking all account operations
  - `GetAuditLog()` function for querying audit history
  - New `bank_audit_log` table with category-based filtering
  - Audit logging added to deposits, withdrawals, loan applications, card operations
- **Investment Dividends** - Dividend payments for eligible investment products
  - `dividendRate` and `dividendFrequency` config options for investment products
  - `ProcessDividends()` function runs daily via scheduler
  - Real Estate Trust now pays 3% annual dividends every 14 days
  - `/forcedividends` admin command
  - `TriggerDividends` export
- **Daily Loan Limit Tracking** - Added `loans_today` column to track daily loan applications
- **New Locale Keys** - Added 40+ new localization keys for all new features

### Fixed
- **Card Daily Limit Time Inconsistency** - Card daily limits now use in-game day counter instead of server time
  - Changed `last_reset` (DATE) to `last_reset_day` (INT) in `bank_cards` table
  - Limits reset properly with in-game day changes
- **Loan Cache/DB Atomicity** - Loan payments now wrapped in MySQL transactions
  - Prevents race conditions in concurrent payment scenarios
  - Cache only updates after successful database transaction
  - Automatic refund on transaction failure
- **DB ENUM Mismatch** - Added `external` to `bank_loan_payments.payment_type` ENUM

### Removed
- **ExportTransactionHistory** - Removed stub function (not implemented)
- **Foreign Transaction Fees** - Removed unused `foreignTransaction` config from fees

### Database Migration
Run the following SQL on existing installations:
```sql
-- Add loans_today column
ALTER TABLE `bank_player_accounts`
ADD COLUMN IF NOT EXISTS `loans_today` INT NOT NULL DEFAULT 0 AFTER `daily_transferred`;

-- Update loan status ENUM
ALTER TABLE `bank_loans`
MODIFY COLUMN `status` ENUM('pending', 'pending_approval', 'processing', 'approved', 'active', 'paid', 'defaulted', 'collections', 'denied', 'cancelled') NOT NULL DEFAULT 'pending';

-- Add processing_until column
ALTER TABLE `bank_loans`
ADD COLUMN IF NOT EXISTS `processing_until` DATETIME DEFAULT NULL AFTER `status`;

-- Update payment_type ENUM
ALTER TABLE `bank_loan_payments`
MODIFY COLUMN `payment_type` ENUM('scheduled', 'extra', 'garnishment', 'external') NOT NULL DEFAULT 'scheduled';

-- Add dividend columns
ALTER TABLE `bank_investments`
ADD COLUMN IF NOT EXISTS `last_dividend_day` INT NOT NULL DEFAULT 0 AFTER `current_value`,
ADD COLUMN IF NOT EXISTS `total_dividends` BIGINT NOT NULL DEFAULT 0 AFTER `last_dividend_day`;

-- Fix card daily limit column
ALTER TABLE `bank_cards` DROP COLUMN IF EXISTS `last_reset`;
ALTER TABLE `bank_cards` ADD COLUMN IF NOT EXISTS `last_reset_day` INT NOT NULL DEFAULT 0 AFTER `daily_used`;
UPDATE `bank_cards` SET `last_reset_day` = 0, `daily_used` = 0;

-- Create pending transfers table
CREATE TABLE IF NOT EXISTS `bank_pending_transfers` (
    `id` INT NOT NULL AUTO_INCREMENT,
    `from_citizenid` VARCHAR(50) NOT NULL,
    `to_citizenid` VARCHAR(50) NOT NULL,
    `amount` BIGINT NOT NULL,
    `fee` BIGINT NOT NULL DEFAULT 0,
    `transfer_type` ENUM('internal', 'external', 'wire') NOT NULL,
    `expedited` TINYINT(1) NOT NULL DEFAULT 0,
    `status` ENUM('pending', 'completed', 'cancelled', 'failed') NOT NULL DEFAULT 'pending',
    `scheduled_completion` DATETIME NOT NULL,
    `reason` VARCHAR(255) DEFAULT NULL,
    `created_at` TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    `completed_at` TIMESTAMP NULL,
    PRIMARY KEY (`id`),
    KEY `idx_status` (`status`),
    KEY `idx_scheduled` (`scheduled_completion`),
    KEY `idx_from` (`from_citizenid`),
    KEY `idx_to` (`to_citizenid`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Create audit log table
CREATE TABLE IF NOT EXISTS `bank_audit_log` (
    `id` BIGINT NOT NULL AUTO_INCREMENT,
    `timestamp` TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    `citizenid` VARCHAR(50) DEFAULT NULL,
    `account_name` VARCHAR(50) DEFAULT NULL,
    `action` VARCHAR(50) NOT NULL,
    `category` ENUM('account', 'transaction', 'loan', 'card', 'security', 'admin') NOT NULL,
    `performed_by` VARCHAR(50) NOT NULL,
    `details` JSON DEFAULT NULL,
    `success` TINYINT(1) NOT NULL DEFAULT 1,
    PRIMARY KEY (`id`),
    KEY `idx_timestamp` (`timestamp`),
    KEY `idx_citizenid` (`citizenid`),
    KEY `idx_account` (`account_name`),
    KEY `idx_action` (`action`),
    KEY `idx_performed_by` (`performed_by`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

## [1.2.0] - 2026-01-20

### Fixed
- **Config.DailyLimits was not wired** - Removed orphaned `Config.DailyLimits` section (ATMWithdraw, ATMDeposit, Transfer) that was defined but never used for actual transactions
- **Config.Collections wrong path** - Fixed `Config.Collections` references to use correct path `Config.Loans.collections`
- **Hardcoded fallback values** - Removed unnecessary `or` fallback defaults throughout server code where config values are guaranteed to exist

### Changed
- **Card daily limits now tier-based** - Bank cards now inherit the player's account tier `dailyWithdrawLimit` instead of using a separate config value
  - Basic tier: $5,000/day
  - Premium tier: $25,000/day
  - Business tier: $100,000/day
- **Interest calculations** - Now use `Config.TimeSettings.DaysPerYear` consistently without fallbacks
- **Loan rate calculations** - Now use `Config.Loans.minimumInterestRate` instead of hardcoded 1% floor

### Added
- `Config.Loans.minimumInterestRate` - Configurable minimum interest rate floor for loans (default: 0.01 / 1%)

### Documentation
- Updated `docs/CONFIGURATION.md` to reflect tier-based daily limits
- Fixed documentation for `Config.Loans.collections` path

## [1.1.0] - 2025-01-16

### Added
- **Vehicle Loan Export System** - External scripts (like qb-vehicleshop) can now register and track vehicle loans through the bank
  - `RegisterVehicleLoan()` - Register a new vehicle loan from external script
  - `RecordVehicleLoanPayment()` - Record payments made at dealership
  - `CloseVehicleLoan()` - Close loan as 'paid' or 'repossessed'
  - `GetVehicleLoan()` - Retrieve loan info by plate
- Vehicle loans display in bank UI with "Pay at Dealership" label
- New `docs/VEHICLESHOP_INTEGRATION.md` - Step-by-step guide for qb-vehicleshop integration
- Documentation section added to README.md listing all available guides
- External loan types configuration (`Config.Loans.externalTypes`)

### Changed
- Vehicle loans are now managed externally by dealership scripts rather than through bank applications
- Updated LoansTab.js to conditionally show payment buttons (hidden for externally managed loans)
- Added vehicle icon to loan type icons

### Removed
- Property loan product (not implementable without standardized housing script)
- Vehicle loan product from bank applications (now handled via exports)

### Database
- Requires `notes` column in `bank_loans` table:
  ```sql
  ALTER TABLE `bank_loans` ADD COLUMN `notes` TEXT NULL DEFAULT NULL AFTER `collateral_id`;
  ```
