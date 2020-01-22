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
`src/aqm-eval-suite/examples` directory provides a set of programs, each corresponding to a specific scenario listed in RFC 7928. Each program can be run individually. Alternatively, `aqm-eval-suite-runner.cc` allows the user to run all scenarios at once.
# User interaction with the suite
Users can learn about the list of scenarios available in the suite from `src/aqm-eval/examples` directory. The programs can be run in a usual way. For example, `aggressive-transport-sender.cc` is equivalent to the scenario described in Section 5.2 of the RFC. Assuming examples have been enabled during configure, the following commands would run `aggressive-transport-sender.cc`

    ./waf --run "aqm-eval-suite-runner --number=5.2"

or

    ./waf --run "aqm-eval-suite-runner --name=AggressiveTransportSender"

To run all scenarios at once, the following command could be used:

    ./waf --run "aqm-eval-suite-runner --name=All"
 
## Simulating additional AQM algorithms using this suite

* By default, the suite evaluates AQM algorithms implemented in |ns3|. To
  simulate additional AQM algorithms, such as the ones designed by the user,
  the ``addAQM`` method of ``ScenarioImpl`` can be used in the scenarios
  available in ``src/aqm-eval-suite/examples``. For example, to add a new AQM
  of typeId ``ns3::ExampleQueueDisc`` in ``aggressive-transport-sender.cc``,
  ``CreateScenario`` method can be modified as shown in the code below:


      EvaluationTopology
      AggressiveTransportSender::CreateScenario (std::string aqm)
      {
        .
        .
        addAQM ("ns3::ExampleQueueDisc");
        EvaluationTopology et ("AggressiveTransportSender", nflow, pointToPoint, aqm, 1460);
        .
        .
      }

## Scope and limitations of the suite

* All scenarios described in Section 5, 6 and 8 of RFC 7928 are supported.

* Scenarios listed in Section 7 and 9 are not yet supported.

* Currently, the suite cannot be used to study the interaction of queue disciplines
  with Explicit Congestion Notification (ECN) and Scheduling Algorithms.

* Multi-AQM scenarios are not yet supported.

## Packages Required for Processing Metrics and Graphing

Following are the packages required for the suite and their installation instruction in Ubuntu

* python-pip: apt-get install python-pip

* python numpy: pip install numpy

* gnuplot: apt-get install gnuplot-qt

* imagemagick (optional package for ns-3): apt-get install imagemagick


# The Network Simulator, Version 3

Much more substantial information about **ns-3** can be found [here](http://www.nsnam.org)

## 1) An Open Source project

**NS-3** is a free open source project aiming to build a discrete-event network simulator targeted for simulation research and education.   
This is a collaborative project; we hope that the missing pieces of the models we have not yet implemented will be contributed by the community in an open collaboration process.
 
The process of contributing to the ns-3 project varies with the people involved, the amount of time they can invest and the type of model they want to work on, but the current process that the project tries to follow is described [here](http://www.nsnam.org/developers/contributing-code/)

This README excerpts some details from a more extensive tutorial that is maintained [here](http://www.nsnam.org/documentation/latest/)

## 2) Building ns-3

The code for the framework and the default models provided by **ns-3** is built as a set of libraries. User simulations are expected to be written as simple programs that make use of these **ns-3** libraries.
 
To build the set of default libraries and the example programs included in this package, you need to use the tool **waf**. Detailed information on how to use waf is included in the file *doc/build.txt*

However, the real quick and dirty way to get started is to type the command

    ./waf configure --enable-examples     

followed by
  
    ./waf 
  
in the directory which contains this README file. The files built will be copied in the *build/* directory.

The current codebase is expected to build and run on the set of platforms listed in the RELEASE_NOTES file.
 
Other platforms may or may not work: we welcome patches to improve the portability of the code to these other platforms. 

## 3) Running ns-3
 
On recent Linux systems, once you have built **ns-3** (with examples enabled), it should be easy to run the sample programs with the following command, such as:

    ./waf --run simple-global-routing

That program should generate a *simple-global-routing.tr* text trace file and a set of simple-global-routing-xx-xx.pcap binary pcap trace files, which can be read by *tcpdump -tt -r filename.pcap* The program source can be found in the *examples/routing* directory.

## 4) Getting access to the ns-3 documentation

Once you have verified that your build of ns-3 works by running the simple-point-to-point example as outlined in **3)** above, it is quite likely that you will want to get started on reading some **ns-3** documentation. 

All of that documentation should always be available from
the **ns-3** [website](http://www.nsnam.org/documentation/).

This documentation includes:

  - a tutorial
 
  - a reference manual

  - models in the ns-3 model library

  - a wiki for user-contributed tips [here](http://www.nsnam.org/wiki/)

  - API documentation generated using doxygen: this is
    a reference manual, most likely not very well suited 
    as introductory [text](http://www.nsnam.org/doxygen/index.html)
