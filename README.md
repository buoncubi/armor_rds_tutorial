# ARMOR RDS Tutorial

This repository contains the code used in our demo at the [ROS Developer Conference 2018](http://www.theconstructsim.com/ros-developers-online-conference-2018-rdc-worldwide/) which took place on July 8th. A full set-up [instance](??) of the [ROS Developer Studio (RDS)](http://www.theconstructsim.com/rds-ros-development-studio/) was shared with the participants on that date. This repository is geared towards both conference attendees and other robotics enthusiasts that want to have a second look a the demo material outside of the RDS environment.

**ARMOR** - *a ROS Multi-Ontology Reference* is a software that allows developers to access and modify ontological databases in robotic applications easily. If you do not know what an ontology is or why you should care for robotic applications, don't worry! We provided motivations in our tutorial [abstract](??), as well as a short primer later in these notes. 
For now, let's focus on the installation process.

## Installation

To install ARMOR, you will need the following packages:

#### ROS Java bridge

[Rosjava](http://wiki.ros.org/rosjava/Tutorials/kinetic/Installation) is a ROS package that allows the use of nodes written in Java. It was originally meant to let ROS nodes run on Android, but we are using it here to interface with ontologies. Indeed, ontologies were developed for the semantic web and the only APIs available are written in Java; therefore we will need to run JVM instances in our ROS architecture. 
For [ROS kinetic](http://wiki.ros.org/kinetic), Rosjava can be acquired from the official repositories as:

```sh
    sudo apt-get install ros-kinetic-rosjava
```

In RDS though, you will have to install it from [source](http://wiki.ros.org/rosjava/Tutorials/kinetic/Source%20Installation). Thus, if you are relying on such a development environment, you should use the following commands instead of the previous one.

```sh 
    mkdir -p ~/catkin_ws/src
    cd ~/catkin_ws/src
    wstool init -j4 https://raw.githubusercontent.com/<USERNAME>/rosinstalls/kinetic-devel/myjava.rosinstall
    cd ~/catkin_ws
    catkin_make
```

#### A Multi-Ontology Reference (AMOR)

[AMOR](https://github.com/EmaroLab/multi_ontology_reference) is the core of ARMOR, and it is a Java library that simplifies the use of the [Ontology Web Language (OWL) API](http://owlapi.sourceforge.net/). Indeed, the OWL API is designed to develop semantic data structures respecting World Wide Web Consortium (W3C) standards. With cloud computing purposes, the OWL API relies on the [Factory pattern](https://en.wikipedia.org/wiki/Factory_(object-oriented_programming)) to instancing semantic objects identified by URI addresses. On the one hand, this simplifies the development of complex semantics structures (e.g., exploited in the [Protégé ontology editor](https://protege.stanford.edu/)). On the other, it makes difficult the management of defined semantics at run-time. Typically, in robotics, we perform reasoning by design the semantics of sensors streams and, at run-time, we wont to manage data instances with current information and used them for update the reasoner outcomes dynamically. 
AMOR allows performing most of those operations in only one line of code, and it assures thread-safe operations among multiple ontologies and reasoners. It also comes with a simple debugging GUI that shows the states of the ontologies at run-time, and we mainly use it for having a more maintainable and extendible code as far as robotic applications are concerned.
The above AMOR repository contains the library [JAR release](https://github.com/EmaroLab/multi_ontology_reference/releases).
Of course, you can use this library all the times you can interface your programs with a JVM (this is what we have done for a long time!). 

For a simple installation of the AMOR core, clone its repostiroy on your workspace: 

```sh
    git clone https://github.com/EmaroLab/multi_ontology_reference
```

Then, since specified as a compile dependence in its [Gradle building set-up](https://github.com/EmaroLab/armor/blob/master/amor_interface_service/build.gradle), ARMOR will use the [`it.emarolab.amor:amor:1.0.0`](https://github.com/EmaroLab/multi_ontology_reference/tree/master/files) JAR library.


#### ARMOR services
[ARMOR](https://github.com/EmaroLab/armor) is a ROS service that uses AMOR to create, open, query, and modify multiple ontological knowledge bases. Therefore, all the nodes of a ROS architecture can access this knowledge by interacting with ARMOR through service requests. You can delve into the details by reading the extensive documentation on the repository, for now, just clone it in your workspace:

```sh
    git clone https://github.com/EmaroLab/armor
```

#### ARMOR messages interface
[armor_msgs](https://github.com/EmaroLab/armor_msgs) is a package (designed following the Rosjava recomandations) that defines all the messages used for ARMOR requests and responses. In order to interface your nodes with ARMOR, just clone it in your workspace:

```sh
    git clone https://github.com/EmaroLab/armor_msgs
```

##### ARMOR testers
[armor_py_api](https://github.com/EmaroLab/armor_py_api) is a WIP python utility that allows you to perform calls to ARMOR without the need to fill in the messages, but just calling some very intuitive functions to query and modify a given ontology. We will use some parts of this package as an example in the tutorial. Also, it offers a nice self-test script to check if the installation of the framework was successful. Clone this repo in your workspace too:

```sh
    git clone https://github.com/EmaroLab/armor_py_api
```

#### ARMOR tutorial
The very last step is cloning the demo repository in your worskpace. 

```sh
    git clone https://github.com/EmaroLab/armor_rds_tutorial
```

This demo will use ontological databases to work with two turtlebots in a domestic environment simulated in Gazebo. If you are working in RDS, remember to move the ```turtlebot3_simulations``` folder inside the ```simulation_ws``` folder. Else, remember to launch gazebo with the ```turtlebot3_house.launch``` before running the tutorial code!


#### Building
Now that we have everything ready, build your workspace!

```sh
    cd ~/catkin_ws
    catkin_make
```

 Two little notes before moving on:

+ Rosjava nodes are a bit tedious to launch with both ```rosrun``` and ```roslaunch``` since the syntax required is pretty verbose. To simplify the process for ARMOR, run the following commands. They will create an executable that can be launched in a much easier way. Usually, this must be done only the first time after build, but in RDS you **must** do it every time you boot a new session.

```sh
    roscd armor (??)
    ./gradlew deployApp
```

+ In the latest version of RDS, please remember also to source your workspace in every terminal you open.

```sh
    source  ~/catkin_ws/devel/setup.bash (??)
```

## What are Ontologies?

Ontology is a term borrowed from Greek philosophy, used to describe *the philosophical study of the nature of being, becoming, existence, or reality, as well as the basic categories of being and their relations*. In computer science and information technology, we usually refer to a database as an ontology when it allows storing information about classes of objects, subclasses, instances of said classes and hierarchical properties among them. 

We are going to focus here to the **OWL standard** (an *XML schema*) which supports a broad set of *reasoning* algorithms that exploit the [Description Logic (DL)](https://en.wikipedia.org/wiki/Description_logic) formalism to infer the state of the data in our ontology.
Remarkably, DL is not only used for reasoning, but it is also a state-of-art formalism that supports natural language. Therefore, it is suitable for Human-Robot Interaction and developing purposes, since the structured data inferred by the reasoner is completely accessible.

### Reasoning

Let's assume we define a very simple ontology where there exist a class *Fruit*, which is defined as the set of all instances difed with the property to have some *seeds* (e.g., if `I hasSeeds(S)` it implies that `Fruit(I)`):

+ What happens if I add to the ontology an object with some seeds? It will automatically be classified as a fruit, even if that was not explicitly stated. This is the result of the instance checking perfomed by the reasoner.

+ What if we do the same, but we explicitly states that the object we add is not a fruit (i.e., it is *disjoint* from the class fruit)? Then this event throws the ontology in an *inconsistent* state. This is called ***consistence checking***. 

Both these checks are performed by the OWL **reasoner**: a little piece of software that is programmed to interface with an OWL ontology and infers new knowledge. There are [several reasoners](http://owl.cs.manchester.ac.uk/tools/list-of-reasoners/) available. But here we are going to use only [Pellet](https://github.com/stardog-union/pellet), which supports also the [Semantic Web Rule Language (SWRL)](https://www.w3.org/Submission/SWRL/).

### Defining an ontology

Writing an ontology in a text editor is technically possible, but extremely tedious and error-prone. As a consequence, the logical skeleton of an ontology is usually implemented and inspected using a dedicated graphical tool. The most common, and the one we are using in this demo, is the [Protégé](https://protege.stanford.edu/) editor. 
You can download it for free from the website (or use online) and play with it; it is very intuitive and simplifies a lot the process of designing OWL structures and reasoning paradigms. 
For a further understanding of OWL capabilities, use such an editor to follow [this popular tutorial](http://mowl-power.cs.man.ac.uk/protegeowltutorial/resources/ProtegeOWLTutorialP4_v1_3.pdf).

### Ontologies in robotics

Ontologies have great potential in robotics as they allow semantic, user-friendly data storage and powerful reasoning capabilities that other AI tools (such as neural networks) still lack. It has been proved that ontologies and robots work well together, even in ROS, like in the case of the well-known [KnowRob project](http://knowrob.org/home). The main issue is that there is not a standard framework to ease the use of such tools like MoveIt does for motion planning or ROSPlan does for task planning.

Surprisingly, we are not saying we did or even that we should! In the end, a framework would be intrinsically useless, as there are infinitely many ways one can structure an ontology and exploit its reasoning capabilities. **What we did achieve though, is an ontology management service that takes out all of the aforementioned complexities, so that you can go quickly from sketching an ontology, to writing some code that employs it, to testing your system**. We aim to greatly reduce the entry barriers for both beginners and expert roboticists alike, as well as providing a tool that allows much faster iterations in AI software for robotics.

Have a look at the ARMOR Github [page](https://github.com/EmaroLab/armor), as well as to the list of supported [ontological operations](https://github.com/EmaroLab/armor/blob/master/commands.md), and glance through how ARMOR can help you.
For instance, we used it to perform:
 + task planning to manipulate cluttered objects,
 + task planning to manipulate articulated objects,
 + spatial perception and reasoning, and
 + natural human supervisions.

## Tutorial

Now that you have an idea of what is an ontology and how it can help you, let's delve into the tutorial. This demo will follow a top down approach: first we will check that our installation was successful, then we will try to communicate with ARMOR from terminal, and later we will go and check in the self test utility which commands are called and to which ARMOR messages and commands they correspond. Once the basis is layed down, we will have a look at a concrete example with two turtlebot and three ontologies interacting together.

Please note that this tutorial was meant for RDS so there may be some specific jargon involved, but everything should work the same on a regular ROS installation.

### Running the self-test

Let's check everything works! First of all try to manually launch ARMOR:

```sh
rosrun armor execute it.emarolab.armor.ARMORMainService
```

If it does not run, check that you built your workspace and run ```gradlew deployApp```. If you did not get any error, try to launch the self test utility:

```sh
rosrun armor_py_api client_test.py
```

If you get any error an error here, most pobably you forgot to source the workspace. Else, you should see some operations going on the terminal and a success message.

Before going on, take your time to inspect the test ontologies in Protégé. You can run Protégé from terminal and it will appear in the RDS graphical tools view.

```sh
/catkin_ws/src/armor_rds_tutorial/Protege-5.2.0/run.sh
```


### Communicating with ARMOR

Great! Now let's do something similar manually using ```rosservice call```. You can find the complete ARMOR commands documentation at this [page](https://github.com/EmaroLab/armor/blob/master/commands.md). Make sure ARMOR is still running, then:

+ Load the test ontology:

```sh
rosservice call /armor_interface_srv "armor_request:
  client_name: 'terminal'
  reference_name: 'ref'
  command: 'LOAD'
  primary_command_spec: 'FILE'
  secondary_command_spec: ''
  args: ['/home/user/catkin_ws/src/armor_py_apy/test/test.owl', 'http://www.semanticweb.org/emarolab/pyarmor/test', 'false', 'PELLET', 'true']"  
```

+ Add an individual of individuals:

```sh
rosservice call /armor_interface_srv "armor_request:
  client_name: 'terminal'
  reference_name: 'ref'
  command: 'ADD'
  primary_command_spec: 'IND'
  secondary_command_spec: ''
  args: ['example_ind']"  
```

+ Reason and save:

```sh
rosservice call /armor_interface_srv "armor_request:
  client_name: 'terminal'
  reference_name: 'ref'
  command: 'REASON'
  primary_command_spec: ''
  secondary_command_spec: ''
  args: ['']"  
```

```sh
rosservice call /armor_interface_srv "armor_request:
  client_name: 'terminal'
  reference_name: 'ref'
  command: 'SAVE'
  primary_command_spec: 'INFERENCE'
  secondary_command_spec: ''
  args: ['/home/user/test_inferred.owl']"  
```

You can now inspect the saved ontology with Protégé. If you have doubts about buffered manipulation and buffered reasoning, check it out on the ARMOR documentation page linked before.

There exist also a legacy GUI in ARMOR to visualize an ontology online, mainly designed for debugging purposes. Its graphic may be glitchy, but the data shown is reliable. We are not going to cover it in the tutorial, but you can check how to enable it in the documentation. 

### The self-test tool and armor_py_api

Now, it is time to go back to the self-test tool. Open it in the RDS IDE and look at the commands, then let's check together their implementation in ```armor_py_api```. This is a good example of how to call ARMOR from inside you code. Note that since we are communicating with service calls, calling ARMOR from C++ or Java nodes is fundamentally the same. An example:

```python
res = client.call('ADD', 'IND', 'CLASS', [ind_name, class_name])
```

Note that at the moment this library does not implement all the functions of ARMOR, but more are added as needed by the developing team or the students in our lab. Please feel free to contribute if you'll ever use it!

### Testing with robots

Alright, let's get to the real thing (or to Gazebo at least!). We are going to simulate two turtlebots navigating a smart house. Each robot will have its own ontology representing its knowledge of the house topology and its beliefs. Whenever a new axiom such that ```isGoingTo(someRobot, someRoom)``` is added, the robot will start moving to that room and update its topological position (i.e. in which room it currently is). A third ontology will be used to represent the house as a whole, with its sensors and logical capabilities. In this case, we will detect when two robots are in the same room, and send one away to avoid conflicts.

If you are asking yourself why we use multiple ontologies, this is for two reasons: the first one is that reasoning, like many algorithms in AI, tends towards *combinatorial explosion*, hence multiple smaller ontologies will reason faster than an equivalent bigger one; the second reason is that it feels like the logical thing to do (no pun intended!), with each agent having his own belief system, sometimes physically located far from the others.

Let's start by opening the RDS Gazebo view and launching ```turtlebot3_house.launch```, then input the following command in a terminal to launch the framework:

```sh
roslaunch armor_rds_tutorial rds_tutorial.launch
```

The second step consists in opening Protégé and checking out the the two onologies to familiarize with the classes we are going to poll later. In the house ontology pay particular attention to the SWRL rules. SWRL rule are logic rules of the type *if this than that* that can be used to expand the inference capabilities of a reasoner. Not all reasoners supports them, but the default one does! You can add the SWRL view in Protégé from the window menu.

Now move a robot by modifing its knowledge base!

```sh
rosservice call /armor_interface_srv "armor_request:
  client_name: 'robot1'
  reference_name: 'robot1_reference'
  command: 'ADD'
  primary_command_spec: 'OBJECTPROP'
  secondary_command_spec: 'IND'
  args: ['isGoingTo', 'robot1', 'bedroom1']"  
```

Pay attention to the client name field. Since we ***mounted*** our node on the ontology, it is currently the only one allowed to modify it! If we want to do it from terminal, we have to identify us in the same way. This flexible system is convenient when multiple nodes have to acte together, but others must be locked out (i.e. data acquisition phase).

Now, let's keep track of its position by performing our first query to the ontology:

```sh
rosservice call /armor_interface_srv "armor_request:
  client_name: ''
  reference_name: 'robot1_reference'
  command: 'QUERY'
  primary_command_spec: 'OBJECTPROP'
  secondary_command_spec: 'IND'
  args: ['isGoingTo', 'robot1']"  
```

This whill return all the values for that property. In this case we have to make sure there is always one. Note that since query operations do not modify the ontology, they are allowed even by clients with different ids.

We can move the second robot too. Let's try to push them in the same room.

```sh
rosservice call /armor_interface_srv "armor_request:
  client_name: 'robot1'
  reference_name: 'robot1_reference'
  command: 'ADD'
  primary_command_spec: 'OBJECTPROP'
  secondary_command_spec: 'IND'
  args: ['isGoingTo', 'robot1', 'kitchen']"  
```

```sh
rosservice call /armor_interface_srv "armor_request:
  client_name: 'robot2'
  reference_name: 'robot2_reference'
  command: 'ADD'
  primary_command_spec: 'OBJECTPROP'
  secondary_command_spec: 'IND'
  args: ['isGoingTo', 'robot2', 'bedroom1']"  
```

Ouch! One of the robot stopped and is moving to the living room! This is because a potential collision event was detected by the house ontology! Don't worry, the robot will go back to its orginal destination soon. In the meanwhile, pay attention to some new command:

```python
res = self._client.call('ADD', 'DATAPROP', 'IND', [dataprop_name, ind_name, "BOOLEAN", "true"])
```

This command modifies a data property. You may have already seen them in Protégé. They are similar to regular properties, but instead of linking to another individual, they link an individual and a data value, such as an integer or a boolean.

## Tips and remarks!

1. In robotics, there is generally no need to create an ontology from scratch online. Get a prototype in Protégé, populate it with ARMOR using the data from your sensors, reason and act. Don't be afraid to iterate and go back to design phase.

2. ARMOR implements all the functions necessary for this kind of use. If you are more interested in using the full potential of ontologies, you may want to give AMOR a try instead.

3. ARMOR is the preferred choice for robotics applications because it allows sharing the ontology across the whole architecture. Also, it's nice that you do not have to know Java, as many roboticists are mainly skilled in C++ or Python.

4. **Keep it fast!** In robotics, you want your reasoning loops to be quick enough not to slow down the rest of the architecture and lose responsiveness. This requires some tricks compared to the classic way to use ontologies:

    + Split your ontology in multiple ontologies and organize them in hierarchies.
    + Use buffered reasoning whenever possible so to know exactly when the long reasoning process starts. Buffered manipulations may help too.
    + Use an incremental reasoner if available and suitable for your application (i.e. a reasoner that can update their previous result rather than recomputing all from scratch).
    + Avoid inconsistencies and re-reasoning as an event trigger/action-reasoning loop. Try as much as you can to control the logic of the ontology and poll specific entities (i.e. classes, individuals, properties).
    
## That's it!
We know this demo may have been way too simple for you to grasp the full potential of logic reasoning if you are not acquainted with. Still, we hope to have piqued your interest and that our software can help you explore this field.

**Thank you for attention!**

## Publication
More details at:
- [AROW ROMAN 2017](https://www.researchgate.net/publication/318107154_A_ROS_multi-ontology_references_services_OWL_reasoners_and_application_prototyping_issues)
- [and related presentation](https://www.researchgate.net/publication/319483418_A_ROS_Multi-Ontology_References_Service_OWL_Reasoners_and_Application_Prototyping_Issues)
- More to come!

## Contacts
For comments, discussions or support refer to online documentation or contact us at:
 - [alessio.capitanelli@dibris.unige.it](mailto:alessio.capitanelli@dibris.unige.it),
 - [luca.buoncompagni@edu.unige.it](mailto:luca.buoncompagni@edu.unige.it).
