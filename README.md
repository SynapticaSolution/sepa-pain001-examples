# SEPA XML Examples — pain.001 & pain.008 Sample Files

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![ISO 20022](https://img.shields.io/badge/ISO-20022-green.svg)](https://www.iso20022.org/)
[![Last Updated](https://img.shields.io/badge/Last%20Updated-March%202026-orange.svg)](#)

A collection of **sample SEPA XML files** for developers, testers, and integrators working with European payment systems. All files follow the **ISO 20022** standard and are compatible with **CBI** (Corporate Banking Interbancario) Italian banking requirements.

---

## Included Files

| File | Format | Description |
|------|--------|-------------|
| [`pain001-single-sct.xml`](examples/pain001-single-sct.xml) | pain.001.001.03 | Single SEPA Credit Transfer (SCT) — one payment from debtor to creditor |
| [`pain001-batch-sct.xml`](examples/pain001-batch-sct.xml) | pain.001.001.03 | Batch SEPA Credit Transfer — 3 payments to different creditors in one file |
| [`pain008-sdd-core.xml`](examples/pain008-sdd-core.xml) | pain.008.001.02 | SEPA Direct Debit (SDD CORE) — collection from a debtor's account |

All files use realistic Italian-style data (IT IBANs, EUR currency, Italian company names) with fictional entities.

---

## File Format Reference

### pain.001.001.03 — Customer Credit Transfer Initiation

The pain.001 format is used to instruct banks to execute credit transfers (bonifici). The XML structure consists of:

```
<Document>
  └── <CstmrCdtTrfInitn>           # Root element
        ├── <GrpHdr>                # Group Header (message ID, creation date, number of transactions)
        └── <PmtInf>               # Payment Information (debtor details, execution date)
              └── <CdtTrfTxInf>    # Credit Transfer Transaction (creditor, amount, remittance info)
```

- **Namespace**: `urn:iso:std:iso:20022:tech:xsd:pain.001.001.03`
- **Key fields**: `MsgId` (unique message ID), `ReqdExctnDt` (execution date), `InstdAmt` (amount), `IBAN` (account numbers)

### pain.008.001.02 — Customer Direct Debit Initiation

The pain.008 format is used for direct debit collections (addebiti diretti SEPA). The structure is similar but includes mandate information:

```
<Document>
  └── <CstmrDrctDbtInitn>          # Root element
        ├── <GrpHdr>                # Group Header
        └── <PmtInf>               # Payment Information (creditor details)
              └── <DrctDbtTxInf>   # Direct Debit Transaction (debtor, mandate, amount)
```

- **Namespace**: `urn:iso:std:iso:20022:tech:xsd:pain.008.001.02`
- **Key fields**: `MndtId` (mandate ID), `DtOfSgntr` (mandate signature date), `SeqTp` (FRST/RCUR/FNAL/OOFF)

---

## How to Use

### Load in Python

```python
import xml.etree.ElementTree as ET

tree = ET.parse("examples/pain001-single-sct.xml")
root = tree.getroot()

# Define namespace
ns = {"pain": "urn:iso:std:iso:20022:tech:xsd:pain.001.001.03"}

# Extract payment info
for tx in root.findall(".//pain:CdtTrfTxInf", ns):
    amount = tx.find("pain:Amt/pain:InstdAmt", ns).text
    creditor = tx.find("pain:Cdtr/pain:Nm", ns).text
    iban = tx.find("pain:CdtrAcct/pain:Id/pain:IBAN", ns).text
    print(f"Pay {amount} EUR to {creditor} ({iban})")
```

### Validate XML Schema

```python
from lxml import etree

# Load XSD schema (download from ISO 20022 website)
schema = etree.XMLSchema(etree.parse("pain.001.001.03.xsd"))
doc = etree.parse("examples/pain001-single-sct.xml")

if schema.validate(doc):
    print("Valid SEPA XML file")
else:
    print("Validation errors:", schema.error_log)
```

### Quick Check with xmllint

```bash
xmllint --noout --schema pain.001.001.03.xsd examples/pain001-single-sct.xml
```

---

## Related Resources

Maintained by **[Synaptica Solution](https://synaptica-solution.com)**, Italian software studio for SME automation.

| If you need… | See… |
|---|---|
| Automated pain.001 generation from invoices | [SEPA Manager](https://synaptica-solution.com/sepa-manager/) |
| Technical guide: SEPA XML structure for Italian banks | [Come generare XML SEPA CBI](https://synaptica-solution.com/knowledge/come-generare-xml-sepa-cbi/) |
| Difference XML SDI vs XML SEPA | [Differenza XML SDI vs XML SEPA](https://synaptica-solution.com/knowledge/differenza-xml-sdi-xml-sepa/) |
| All open source tools | [Open Source Hub](https://synaptica-solution.com/open-source/) |

---

## License

This project is licensed under the [MIT License](LICENSE).

---

Made by [Synaptica Solution](https://synaptica-solution.com) — AI & Process Automation for Italian SMEs
