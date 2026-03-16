# 📦 Shopify Order & Delay Tracking System

An automated Google Apps Script solution to sync Shopify orders via webhooks, track fulfillment status, and automatically flag shipping delays.

## 🚀 Features
- **Real-time Sync:** Uses `doPost` webhooks to capture order updates instantly.
- **Duplicate Prevention:** Matches orders by Primary Key (Order ID) to update existing rows instead of creating duplicates.
- **Smart Tracking Logic:** Automatically detects and labels "Success" fulfillments that are missing tracking numbers as `'undefined'`.
- **Automated Delay Scanner:** A background process that flags orders based on age:
  - 🟡 **4 Days:** Standard Delay
  - 🟠 **5 Days:** Urgent Follow-up
  - 🔴 **6+ Days / Critical:** Severe Delay or missing tracking info.
- **Visual Dashboard:** Color-coded flags and automated sorting for the `Delay_Order` sheet.

## 📋 Sheet Structure
The system expects an `Orders` sheet with the following 17-column structure:
1. Order ID | 2. Flags | 3. Order Date | 4. Last Updated | 5. Customer Name | 6. Email | 7. Phone | 8. Channel | 9. Total | 10. Payment Status | 11. Fulfillment Status | 12. Items | 13. Tags | 14. City | 15. Shipment Status | 16. Tracking Company | 17. Tracking Number

## ⚙️ Installation

1. **Setup Google Sheet:**
   - Create a new Google Sheet.
   - Open **Extensions > Apps Script**.
   - Paste the provided `code.gs` into the editor.
   
2. **Initialize:**
   - Run the `setupSheetHeaders` function once to create the headers.

3. **Deploy Webhook:**
   - Click **Deploy > New Deployment**.
   - Select **Web App**.
   - Set "Execute as" to **Me**.
   - Set "Who has access" to **Anyone**.
   - Copy the **Web App URL** and paste it into your Shopify Webhook settings (Orders/Create & Orders/Updated).

4. **Set Automation Trigger:**
   - In Apps Script, click the **Triggers (Clock icon)**.
   - Add a new trigger for `updateDelayOrderSheet`.
   - Set it to run on a **Time-driven** event (e.g., Every hour).

## 🔍 Filtering for Pickups
To view only the orders stuck without tracking for more than 3 days, use this formula in a new sheet:
```excel
=IFERROR(FILTER(Orders!A:Q, Orders!K:K="success", Orders!O:O="N/A", Orders!Q:Q="'undefined", INT(Orders!C:C) <= TODAY()-3), "No Pickup Delays Found")
