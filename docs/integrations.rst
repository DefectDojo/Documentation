Integrations
============

DefectDojo has the ability to import reports from other security tools.

Arachni Scanner
---------------
Arachni JSON report format.

AppSpider (Rapid7)
------------------
Use the VulnerabilitiesSummary.xml file found in the zipped report download.

Bandit
------
JSON report format

Burp XML
--------
When the Burp report is generated, the recommended option is Base64 encoding both the request and response fields. These fields will be processed and made available in the 'Finding View' page.

Clair
-----
'Vulnerabilities' section of the JSON report format.

Contrast Scanner
----------------
CSV Report

Checkmarx
---------
Detailed XML Report

Dependency Check
----------------
OWASP Dependency Check output can be imported in Xml format.


Generic Findings Import
-----------------------
Import Generic findings in CSV format.

Nessus (Tenable)
----------------
Reports can be imported in the CSV, and .nessus (XML) report formats.

Nexpose XML 2.0 (Rapid7)
------------------------
Use the full XML export template from Nexpose.

Nikto
-----
XML output

Nmap
----
XML output (use -oX)

Node Security Platform
----------------------
Node Security Platform (NSP) output file can be imported in JSON format.

NPM Audit
---------
Node Package Manager (NPM) Audit plugin output file can be imported in JSON format. Only imports the 'advisories' subtree.

OpenVAS CSV
-----------
Import OpenVAS Scan in CSV format. Export as CSV Results on OpenVAS.

Qualys
------
Qualys output files can be imported in XML format.
Qualys WebScan - Qualys WebScan output files can be imported in XML format.

Retire.js
---------
Retire.js JavaScript scan (--js) output file can be imported in JSON format.

SKF Scan
--------
Output of SKF Sprint summary export.

Snyk
----
Snyk output file (snyk test --json > snyk.json) can be imported in JSON format.

SSL Labs
--------
JSON Output of ssllabs-scan cli.

Trufflehog
----------
JSON Output of Trufflehog.

Visual Code Grepper (VCG)
-------------------------
VCG output can be imported in CSV or Xml formats.

Veracode
--------
Detailed XML Report

Zed Attack Proxy
----------------
ZAP XML report format.

The importers analyze each report and create new Findings for each item reported.  DefectDojo collapses duplicate
Findings by capturing the individual hosts vulnerable.

.. image:: /_static/imp_1.png
    :alt: Import Form

Additionally, DefectDojo allows for re-imports of previously uploaded reports.  DefectDojo will attempt to capture the deltas between the original and new import and automatically add or mitigate findings as appropriate.

.. image:: /_static/imp_2.png
    :alt: Re-Import Form

Bulk import of findings can be done using a CSV file with the following column headers:

Date: ::
    Date of the finding in mm/dd/yyyy format.

Title: ::
    Title of the finding

CweId: ::
    Cwe identifier, must be an integer value.

Url: ::
    Url associated with the finding.

Severity: ::
    Severity of the finding.  Must be one of Info, Low, Medium, High, or Critical.

Description: ::
    Description of the finding.  Can be multiple lines if enclosed in double quotes.

Mitigation: ::
    Possible Mitigations for the finding.  Can be multiple lines if enclosed in double quotes.

Impact: ::
    Detailed impact of the finding.  Can be multiple lines if enclosed in double quotes.

References: ::
    References associated with the finding.  Can be multiple lines if enclosed in double quotes.

Active: ::
    Indicator if the finding is active.  Must be empty, True or False

Verified: ::
    Indicator if the finding has been verified.  Must be empty, True, or False

FalsePositive: ::
    Indicator if the finding is a false positive.  Must be True, or False. 

Duplicate: ::
    Indicator if the finding is a duplicate.  Must be True, or False.
