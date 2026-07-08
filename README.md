# paysim-transaction-analysis
Excel analysis of PaySim mobile money transactions — investigating volume, value, and balance data integrity across transaction types
Background Story

I spent almost four years in merchant onboarding and client success roles at a payments company, so transaction data and balance reconciliation issues aren't new to me. I've just never looked at them through an analyst's lens before. PaySim is synthetic data, but it's modeled on real mobile money transactions from an African context, which felt a lot closer to home than a random US bank dataset.

This is my first proper portfolio project, and honestly, it took longer than I expected, mostly because I kept running into formatting issues (more on that below) before I even got to the actual analysis. I'm leaving that part in because I think it's a fair reflection of what learning this stuff for real actually looks like.

The data

A 10,000-row sample of PaySim, covering five transaction types: PAYMENT, TRANSFER, CASH_IN, CASH_OUT, and DEBIT. Each row has the transaction amount, sender and recipient balances before and after, and fraud flags.

What I actually did


Cleaned the raw CSV in Excel (this took way longer than it should have — regional settings kept messing with my decimals and formula syntax, and I had to redo the row-splitting a few times before it stuck)
Added a few of my own columns: day and hour of the transaction, and a check to see whether the balances actually added up
Built pivot tables to compare transaction types by volume, value, and how often the balances didn't reconcile
When something looked off, I tried to figure out why rather than just reporting the number


What I found

1. Volume and value aren't the same thing
TRANSFER moves the most total money (R400M+) but happens far less often than PAYMENT. PAYMENT is high-frequency, low-value. TRANSFER is the opposite. If I were building monitoring for this in a real ops context, I'd treat these two very differently.

2. I flagged something as a data problem — it was actually my formula
My first balance check said 100% of CASH_IN transactions were "wrong." Turns out my formula assumed money always leaves the sender's account, but CASH_IN adds money in. Once I fixed the formula to match how that transaction type actually works, the CASH_IN mismatch rate dropped to about 25%. Small thing, but it taught me to double check my own logic before trusting a scary-looking result.

3. CASH_OUT and TRANSFER still had way more mismatches than the rest
Even after that fix, CASH_OUT (~91%) and TRANSFER (~89%) had much higher mismatch rates than PAYMENT (~41%) or DEBIT (~18%).

4. I could explain one of those — not the other, yet
Digging further, CASH_OUT's high mismatch rate turned out to be because the destination balance fields are basically never filled in for that transaction type (100% empty). Makes sense once you see it. But TRANSFER doesn't have that excuse — 96% of TRANSFER rows do have destination balance data, and it's still mismatching 89% of the time. I don't have an answer for that one yet, and I'd rather say so than force an explanation that isn't solid.

What I'd do with more time


Actually figure out what's going on with TRANSFER
Move this into SQL or Power BI so I can work with the full 24 million rows instead of a sample
Check whether the mismatches line up with the fraud flag at all, or if they're just a data quirk


Tools

Excel — Text to Columns, formulas, pivot tables, charts. (Next project, I'm hoping to bring in SQL.)

Dataset

PaySim - Synthetic Financial Datasets For Fraud Detection, Kaggle
