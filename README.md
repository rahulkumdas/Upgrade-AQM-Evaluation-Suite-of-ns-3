# Upgrade-AQM-Evaluation-Suite-of-ns-3
**AQM Evaluation Suite** is an automated framework to evaluate the **AQM** algorithms implemented in **ns-3**. Although it works with the latest **ns-3-dev**, there is a lot of redundant code that can be now removed from the suite to make it lightweight and optimized. The aim of this project is to optimize the suite and add additional functionality in the suite like adding the **COBALT** algorithm which was not there in the previous version of the suite and publish it on **ns-3 app store**.
# Introducing the AQM Evaluation Suite
**AQM Evaluation Suite** automates the cycle of simulation setup to results collection in ns-3. It closely follows the recommendations provided in **RFC 7928** to setup the simulation scenarios and collects results in the suggested formats. A user can choose to either run an individual scenario from the set of scenarios provided in the suite, or run all scenarios at once. Results for each scenario are systematically stored in text and graphical formats.
# Architecture of AQM Evaluation Suite
The suite is implemented in `src/aqm-eval-suite` directory.
# Model
`src/aqm-eval-suite/model` contains an implementation of the following three primary classes:
- class :cpp:class: `EvaluationTopology` : This class has three major functionalities.
- Creating the topology: It sets up a point-to-point dumbbell topology by using :cpp:class:PointToPointDumbbellHelper: with required number of nodes, and configures the data rate and for all the links. It also installs the desired queue discipline on the router node.
- Installing application on nodes: it provides an API for configuration of applications. This API takes the application parameters such as data rate, packet size, transport protocol, initial congestion window in case of TCP, maximum bandwidth and one way delay of the channels.
- Getting the metrics of interest from the experiment: It uses the traces sources provided by different classes of ns-3 for the calculation of metrics. The metrics recommended in the RFC are queue-delay, goodput, throughput and number of drops.
- class :cpp:class:EvalApp: This class is based on :cpp:class:OnOffApplication: and is used for generating TCP and UDP traffic in the suite. The native class :cpp:class:OnOffApplication: in ns-3 creates sockets at the start time of an application. Thus, to configure different values for parameters like initial congestion window in TCP is non-trivial, since they cannot be configured before the socket is created and after the application starts. To overcome this, :cpp:class:EvalApp: is implemented on the same principles as that of the :cpp:class:OnOffApplication: in which a socket is created and the application is started only after its parameters are configured.
- class :cpp:class:EvalTimestampTag: This is a subclass of :cpp:class:Tag: and has been developed to fetch the queue delay information from :cpp:class:QueueDisc:. When a packet is enqueued by the QueueDisc, this tag is added with a timestamp (as the enqueue time) and when the packet is dequeued, the queue delay is computed as the difference between the dequeue time and the enqueue time.
# Helper
`src/aqm-eval-suite/helper` contains an implementation of the following class:
- class :cpp:class:`ScenarioImpl`: This class implements the following two methods:
 - `ScenarioImpl::CreateScenario ()`: This is a virtual function implemented by each scenario according to the topology and traffic profiles mentioned in the RFC.
 - `ScenarioImpl::RunSimulation ()`: This method takes the scenario created by each subclass and runs them with all the queue disciplines available in ns-3.
 # Utils
 `src/aqm-eval-suite/utils` directory provides four Python scripts that take performance metrics computed in the suite as input, and generate a graph with Queuing Delay as the X-axis against Goodput as the Y-axis. The graph depicts an ellipse which is plotted as per the guidelines mentioned in the RFC and [Remy]. The co-variance between the queuing delay and goodput is determined by the orientation of the ellipse, and helps to analyze the effect of traffic load on Goodput and Queuing Delay.
 # Examples
