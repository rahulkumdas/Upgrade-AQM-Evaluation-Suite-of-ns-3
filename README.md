# Upgrade-AQM-Evaluation-Suite-of-ns-3
**AQM Evaluation Suite** is an automated framework to evaluate the **AQM** algorithms implemented in **ns-3**. Although it works with the latest **ns-3-dev**, there is a lot of redundant code that can be now removed from the suite to make it lightweight and optimized. The aim of this project is to optimize the suite and add additional functionality in the suite like adding the **COBALT** algorithm which was not there in the previous version of the suite and publish it on **ns-3 app store**.
# Introducing the AQM Evaluation Suite
**AQM Evaluation Suite** automates the cycle of simulation setup to results collection in ns-3. It closely follows the recommendations provided in **RFC 7928** to setup the simulation scenarios and collects results in the suggested formats. A user can choose to either run an individual scenario from the set of scenarios provided in the suite, or run all scenarios at once. Results for each scenario are systematically stored in text and graphical formats.
# Architecture of AQM Evaluation Suite
The suite is implemented in [src/aqm-eval-suite] directory.
