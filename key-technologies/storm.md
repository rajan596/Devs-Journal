# Apache Storm

- It is open source software which has below capabilities.
    - real-time
    - distributed
    - fault-tolerant
    - stream data processing system
    - Storm will automatically reassign any failed tasks. 
    - Storm guarantees that there will be no data loss, even if machines go down and messages are dropped.

- Implementation languages:
    - Closure (Mostly)
    - Java
- Open sources in late 2011

## Storm Internal

### Nimbus
There are two kinds of nodes on a Storm cluster: the master node and the worker nodes. 

The master node runs a daemon called "Nimbus" that is similar to Hadoop's "JobTracker". 

Nimbus is responsible for 
1. distributing code around the cluster, 
2. assigning tasks to machines, and 
3. monitoring for failures.

### Supervisor
Each worker node runs a daemon called the "Supervisor". 

The supervisor 
1. listens for work assigned to its machine and starts and stops worker processes as necessary based on what Nimbus has assigned to it. 

Each worker process executes a subset of a topology; a running topology consists of many worker processes spread across many machines.

### Zookeeper
- Co-ordinator between nimbus and supervisor.
- All states information is stored in zookeeper. So Storm becomes stateless.
- You can kill nimbus and storm and it will start from where it left. It makes storm fail-fast.

### Topology
The complete computation processing graph is called topology. It can have
- spouts : block of code which ingests data for processing
- bolts : block of code which does some subset of work/processing.
- streams: links between spout and bolts.

Networks of spouts and bolts are packaged into a "topology" which is the top-level abstraction that you submit to Storm clusters for execution. 

A topology is a graph of stream transformations where each node is a spout or bolt. 

Edges in the graph indicate which bolts are subscribing to which streams. 
When a spout or bolt emits a tuple to a stream, it sends the tuple to every bolt that subscribed to that stream.

- topology runs forever until killed.

### Workers, Executors, and Tasks
Apache Storm processes, called workers, run on predefined ports on the machine that hosts Storm.

Each worker process can run one or more executors, or threads, where each executor is a thread spawned by the worker process.

Each executor runs one or more tasks from the same component, where a component is a spout or bolt from a topology.

### Streams
Apache Storm processes, called workers, run on predefined ports on the machine that hosts Storm.

Each worker process can run one or more executors, or threads, where each executor is a thread spawned by the worker process.

Each executor runs one or more tasks from the same component, where a component is a spout or bolt from a topology.

### Tuples
- Data model of storm. Storm components understand tuples.
- It is list of values. Like numbers, strings, Object (must impmenent serialized)
- Topology must declare output fields. 


_collector.emit(input, new Values(val*2, val*3));
declarer.declare(new Fields("double", "triple"));

So, 
val*2 will be mapped to double and
val*3 will be mapped to tripple.

### How to run storm topology ?
1. Bundle code in a single jar file and then execute storm jar command.

Run with class name:
storm jar <jar> <class-name> <list of args>
storm jar all-my-code.jar org.apache.storm.MyTopology arg1 arg2

Run with flux file:


What it does ?
Uploads jar to Nimbus. Then nimbus distributes the work to supervisors.

Nimbus is a Thrift service.

### Groupings in storm
1. "shuffle grouping" means that tuples should be randomly distributed from the input tasks to the bolt's tasks.

### Configurations
- Parallelism: How many threads needed for each bolt/spout. Default: 1

- The setting topology.max.spout.pending limits the number of un-ACKed tuples at the spout level. Once a spout reaches this limit, the spout's nextTuple() method will not be called until some ACKs are received for the outstanding emits.

- numTasks: No. of tasks assigned to executor(thread). Running more than one task per executor does not increase the level of parallelism -- an executor always has one thread that it uses for all of its tasks, which means that tasks run serially on an executor. The number of executor threads can be changed after the topology has been started (see storm rebalance command). The number of tasks of a topology is static. And by definition there is the invariant of #executors <= #tasks. (Ref: https://storm.apache.org/releases/2.2.0/Understanding-the-parallelism-of-a-Storm-topology.html)