**Installation:**

1. Architecture Overview
* The Main App (bigfix): Installed on the Search Head. Contains the UI, dashboards, navigation, and custom icons.
* The Technology Add-ons (TAs): Installed on the BigFix endpoint servers via a Splunk Universal Forwarder. Data collection rules (inputs.conf) and parsing instructions (props.conf, app.conf) are split into targeted packages to ensure servers only collect data for the specific components installed on them:

  * TA-bigfix\_Root
  * TA-bigfix\_BFI (Inventory)
  * TA-bigfix\_BFC (Compliance)
  * TA-bigfix\_TRC (Remote Control)
  * TA-bigfix\_webreports
  * TA-bigfix\_Relay
  * TA-bigfix-webui

NOTE: In the inputs.conf file, verify the path to the log files are correct. Currently they are pointing to "C:\\Program files (x86)".

2. Prerequisites
* Splunk Enterprise (Version 10.2 or higher)
* Splunk Universal Forwarder installed on target BigFix servers
* Admin access to Splunk Web (Search Head)
* Admin access to the Splunk Deployment Server (Agent Management)
* If enabling TA-bigfix\_webreports, ensure you have webreports logging enabled
3. Installation Step 1: Create the Index
* Action: Create a new index on your Indexers.
* Required Name: bigfix
4. Installation Step 2: Install the Main App (Search Head)
* Navigate to Manage Apps -> Install app from file in Splunk Web.
* Upload the bigfix.tar file.
* Restart Splunk Web if prompted.
5. Installation Step 3: Configure and Deploy the TAs (Deployment Server)
* Extract the provided TA folders (TA-bigfix\_Root, TA-bigfix\_BFI, etc.) into the /etc/deployment-apps/ directory on your Deployment Server. These folders can be found in Releases in the Source Code.zip file.
* Enable Data Collection: By default, all data collection is disabled in the default/inputs.conf files. For each TA you intend to deploy:

  * Create a local directory (e.g., TA-bigfix\_Root/local/inputs.conf).
  * Copy the contents of default/inputs.conf into your new local/inputs.conf.
  * Change disabled = 1 to disabled = 0.
  * NOTE: Verify the path to the monitored log files are correct. Currently they are pointing to "C:\\Program files (x86)".
* Map in Agent Management:

  * Log into Splunk Web on your Deployment Server and navigate to Settings -> Agent Management (formerly Forwarder Management).
  * Under the Apps tab, locate your specific TAs.
  * Edit the Server Classes for each TA, mapping them strictly to their corresponding BigFix server groups (e.g., map TA-bigfix\_BFI only to your BigFix Inventory Server Class).

    * Crucial: Ensure the Restart Splunkd option is checked for each mapping so the Universal Forwarders restart and begin reading the logs.
6. Verification
* Run the following search to verify data is flowing and parsing time extractions correctly: index=bigfix | stats count by sourcetype
* Navigate to the BigFix app from the Splunk top navigation bar to confirm dashboards are populating.

