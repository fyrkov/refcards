## Taxation of investments in Germany

### Inputs for calculating taxes

The following inputs directly impact the calculation of investment taxes in Germany:

1. **Person State** (The "Who")
    - Includes personal information relevant for taxation

2. **Securities State** (The "What")
    - Asset Class
    - Instrument Jurisdiction & Currency
    - Corporate Actions: Payment dates for coupons and dividends. Splits, mergers, or spin-offs that may trigger "
      fictional sales."

3. **Securities x Person State** (The "Personal History")
    - FIFO Backlog
    - Acquisition Prices
    - Loss Pots
    - Prior VOPA Payouts

4. **Universal State** (The "Environment")
    - Withholding taxes in other jurisdictions
    - Double Taxation Agreements
    - Fund Classifications for Teilfreistellung
    - VOPA Parameters

### Person state

A person can have the following attributes that affect taxation:

1. Tax residency or residencies
2. Kirchensteuer relation
3. FSA exemption order status
4. NV (non-assessment) Certificates and their status
5. Losspots statuses
6. US taxes (FATCA) relation

### General info about taxes

Investment income in Germany is taxed with:

| Tax Component                                         | Rate       | Description                                                  |
|:------------------------------------------------------|:-----------|:-------------------------------------------------------------|
| **Abgeltungstuer, Capital Gains Tax**                 | 25%        | Base rate for dividends, interest, and asset sales.          |
| **Solidaritätszuschlag (Soli), Solidarity Surcharge** | +5.5%      | Calculated on the tax amount (effectively 1.375% of profit). |
| **Kirchensteuer, Church Tax**                         | +8% or +9% | Optional (depending on your registered church status).       |

Normally effective total rate without church tax is 26.375%.

Taxation is applied immediately upon the realization of capital gains in most of the cases.
It means that every taxable event is taxes without defer. One exception is VOPA.

Capital gains are calculated using the FIFO principle: 
the sale value minus the original buy value(s) of the oldest shares in chronological order.

### Freistellungsauftrag (FSA), Sparer-Pauschbetrag, Exemption Order

A person is granted 1000 tax free allowance (Sparer-Pauschbetrag) per year or 2000 as a couple per year.
This can be used to deduct from taxable base.

The Freistellungsauftrag (FSA) is specifically the instruction to a bank to use that allowance.

Cutoff date is 01 Jan of every year.

### Loss pots

There are 3 types of loss pots that accumulate losses by types.
Loss pots can later offset the taxable base by deducting the accumulated losses.

| Loss Pot Name             | Covered Assets                 | Offset Against (Gains)                            |
|:--------------------------|:-------------------------------|:--------------------------------------------------|
| **Aktienverlusttopf**     | Individual stocks only.        | **Only** gains from individual stocks.            |
| **Sonstiger Verlusttopf** | ETFs, funds, bonds, etc.       | **Any** capital income (ETFs, dividends, stocks). |
| **Quellensteuertopf**     | Foreign taxes paid (e.g., US). | Reduces the German tax liability.                 |

:exclamation: Main rule: What a loss pot can offset depends on its type and the income category.

Losspots are automatically transferred to the new fiscal year if not fully utilized (except Quellensteuertop)

### Crypto assets
In Germany, cryptocurrencies are not capital assets; they are "Private Economic Assets" (Privates Veräußerungsgeschäft).

| Asset Held | Timeframe | Tax Rate |
| :--- | :--- | :--- |
| **Crypto** | < 12 Months | Personal Income Rate (14%–45%) |
| **Crypto** | > 12 Months | **0% (Tax-Free)** |
| **Staking Rewards** | On Receipt | Personal Income Rate (14%–45%) |

### 📈 Vorabpauschale (VOPA) - Advance Flat-Rate Tax.

The Vorabpauschale is a "pre-paid" tax on the expected gains of investment funds (ETFs).
Collected once in a year and applied only if ETFs increased in price.
VOPA contributions decrease future tax if ETF is sold.

**Calculation Logic:**

```
If ETF grew: `Tax = (0.7 * Basiszins * (Value of the fund on Jan 1st) - Dividends) * 70% (if Equity ETF) * 26.375%`
else: `Tax = 0`
```

Brokers calculate VOPA on the 2 Jan (legal accrual date) for the previous year.\
Effective payout can happen later.

Brokers are obliged to track VOPA contributions and credit them against final sell of the ETF.\
Usually for that purpose acquisition cost of the corresponding ETF position is increased.

### Teilfreistellung (Partial Exemption)

Some funds have a partial exemption.\
For example, Finanzamt allows to tax only 70% of gains on equity ETFs.\
This also applies to VOPA.\
This also applies to losspots, e.g. only 70% of losses can be recorded to the loss pot.

| Asset Type        | Requirement  | Taxable Portion |
|:------------------|:-------------|:----------------|
| **Equity ETF**    | > 51% Stocks | **70%**         |
| **Mixed ETF**     | > 25% Stocks | **85%**         |
| **Bond/Gold ETF** | < 25% Stocks | **100%**        |


