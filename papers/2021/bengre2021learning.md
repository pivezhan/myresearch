# A learning-based scheduler for high volume processing in data warehouse using graph neural networks

**Citation Key**: `bengre2021learning`

**Year**: 2021

**Type**: inproceedings

**Authors**: Bengre, Vivek and HoseinyFarahabady, M Reza and Pivezhandi, Mohammad and Zomaya, Albert Y and Jannesari, Ali

**Venue**: International Conference on Parallel and Distributed Computing: Applications and Technologies

---

## Key Points

- GNN-based scheduler for ETL operations in data warehouse systems (Apache Spark)
- 15% improvement in job completion time vs Decima (state-of-the-art ML scheduler)
- 20% improvement vs tuned heuristic-based scheduler (dynamic partitioning)
- GraphSAGE with reverse message passing (leaf-to-root) for DAG embedding
- Double Deep Q-Networks (DDQN) with hybrid heuristic-assisted training
- Tested on Apache Spark simulator with TPC-H dataset
- **Mohammad Pivezhandi: Co-author** (3rd of 5 authors, Iowa State University)

## Abstract

The process of extracting, transforming, and loading (ETL) a high volume of data plays an essential role in data integration strategies in data warehouse systems. In almost all distributed ETL systems currently used in both industrial and academic contexts, simple heuristic-based scheduling policies are employed. Such heuristic policies try to process a stream of jobs in a best-effort fashion, however, they can result in under-utilization of computing resources in most practical scenarios. Inefficient resource allocation strategies can result in unwanted increases in total completion time of data processing jobs.

This paper develops an efficient reinforcement learning technique that uses a Graph Neural Network (GNN) model to combine all submitted task graphs into a single graph to simplify the representation of states within the environment and efficiently make parallel applications for processing submitted jobs. To positively augment the embedding features in each leaf node, messages are passed from leaf to root so nodes can collaboratively represent actions within the environment.

The method uses GraphSAGE convolutions with reversed edge directions, allowing leaf node embeddings to have influence from higher nodes in the DAG. A Double Deep Q-Network (DDQN) tunes the parameters of the graph neural networks to set efficient embedding for DAG features. To address time-consuming convergence in policy search, a hybrid approach uses heuristic-based scheduler assistance by executing for the first few episodes.

Performance results show up to 15% improvement in job completion time compared to the state-of-the-art machine learning scheduler (Decima) and up to 20% enhancement compared to a tuned heuristic-based scheduler (dynamic partitioning). The architecture limits stages to one executor per node, allowing the RL agent to select the execution order while maximizing executor accessibility.

## Contributions

1. **GraphSAGE with reverse message passing**: Implements message passing from leaf to root (reversed edge directions) allowing leaf node embeddings to incorporate information from parent nodes several generations up the dependency hierarchy
2. **Direct Q-value representation via node embeddings**: Makes training significantly faster by representing Q-values directly through node feature embeddings in the RL agent, avoiding separate value function approximation
3. **Single aggregated DAG structure**: Combines all submitted job DAGs into one large graph structure to enhance RL parallelism and provide descriptive state representation, enabling efficient addition of new jobs by appending to existing graph
4. **DDQN training for continuous job arrival**: Uses Double Deep Q-Networks for handling continuous job arrival scenarios, reducing overestimation bias in Q-value learning
5. **Hybrid heuristic-assisted training**: Proposes initial heuristic-based scheduler assistance for first few episodes (90-100% assist) to accelerate policy exploration and reduce convergence time
6. **One executor per stage constraint**: Limits each stage to one executor and allows agent to select execution order, maximizing executor availability for high-load environments

## Methodology

**Problem Context**:
ETL (Extract, Transform, Load) operations in data warehouse systems (Amazon Redshift, Azure Data Warehouse, Google BigQuery) for business intelligence applications. Jobs represented as Directed Acyclic Graphs (DAGs) where nodes are processing stages and edges represent dependencies.

**Overall Approach**:
Reinforcement learning-based scheduler using Graph Neural Networks to embed DAG structures and Double Deep Q-Networks to learn scheduling policies. System targets Apache Spark ETL workloads with goal of minimizing job completion time.

**State Representation (Aggregated Single Graph)**:

Environment state contains all unexecuted job DAGs combined into one large graph:
- Traditional approach: Collection of separate sparse adjacency matrices + feature matrices per job
- Proposed approach: Single unified graph containing all DAGs
- Node numbering: 0 to node_count (total nodes across all jobs)
- "Soft delete": Completed nodes marked with flag but retained to preserve numbering
- New job arrival: Append new nodes to existing graph structure
- Repeated node numbers: Represent new job arrivals
- Feature matrix: Computed just before training/inference (inexpensive operation)
- Edge direction reversal: All edges reversed for leaf-to-root message passing

**Advantages of single graph representation**:
- Fast new job addition (append operation)
- Efficient parallelization of embedding computation
- Simplified state management
- Enables batch processing of multiple jobs simultaneously

**Graph Embedding with GraphSAGE**:

**Architecture**: Three GraphSAGE layers [Hamilton et al., NeurIPS 2017]
- Layer 1: 5 input features → 5 output features
- Layer 2: 5 input features → 5 output features
- Layer 3: 5 input features → 1 output feature (Q-value/logit per node)

**Message Passing** (Reversed Direction):
- Edges reversed: Parents become neighbors instead of children
- Leaf nodes influenced by nodes several generations up dependency chain
- Three rounds of message passing: Ensures reasonable influence span from higher nodes

**Node Embedding Update**:
```
x^l_parent(u) = agg( { x^(l-1)_v for all v in parent(u) } )  (Eq. 5)
x^l_u = σ( W · concat(x^(l-1)_u, x^l_parent(u)) )           (Eq. 6)
```

Where:
- x^l_u: Node u embedding at layer l
- parent(u): Parent nodes (dependencies) of node u in DAG
- agg(): Aggregation function (mean or pooling)
- σ(): Activation function
- W: Learnable weight matrix

**Aggregator Functions Tested**:
1. **Mean aggregator**: Element-wise mean of neighbor embeddings
2. **Pooling aggregator**: Max/mean pooling over neighbor embeddings

**Reinforcement Learning Framework**:

**Agent**: DDQN (Double Deep Q-Network) [Van Hasselt et al., AAAI 2016]
- State: Aggregated DAG graph with node features
- Action: Select which leaf node (stage) to execute next
- Reward: Based on job completion time reduction
- Policy π: Maps states to actions, π(action|state) = P(action|state)

**Value Function**:
```
v_π(s) = E[ G_t | S_t, A_t → π(s) ]                        (Eq. 2)
G_t = R_{t+1} + γR_{t+2} + γ^2R_{t+3} + ...                (Eq. 3)
```

Where:
- G_t: Total expected reward from state S_t
- γ: Discount factor (0.9)
- R_t: Reward at time t

**Optimal Policy**:
```
π* = max( Σ_{t>0} γ^t R_t )                                (Eq. 4)
```

**Training Process**:

**Hybrid Heuristic-Assisted Training** (Novel contribution):
- **Burning period**: 1000 episodes with heuristic guidance
- **Stage 1** (Pooling or Mean Stage 1): 90-100% heuristic assist, 10-0% random exploration
- **Stage 2** (Mean only): 0% heuristic assist, 100% RL exploration
- Episodes: Pooling (1 episode), Mean Stage 1 (15 episodes), Mean Stage 2 (30 episodes)
- Exploration decay: 0.9998-0.9999 per episode

**Training Parameters** (Table 2):
- Learning rate: 0.001
- Gamma (discount): 0.9
- Exploration decay: Exponential decay to balance exploration vs exploitation

**Workflow** (Figure 1):

1. **Environment State**: Aggregated DAG graph with all unexecuted jobs
2. **Reverse Messaging**: Reverse edge directions for leaf-to-root message passing
3. **GraphSAGE-2 Convolutions**: Two rounds of GraphSAGE message passing
4. **Leaf Node Mask**: Mask non-leaf nodes (only executable leaf nodes selectable)
5. **Argmax Q-values**: Select leaf node with highest Q-value
6. **Node Limit = 1**: One executor per stage constraint
7. **Execute Action**: Assign selected stage to free executor
8. **New State & Reward**: Environment transitions, reward computed
9. **Temporal Difference Update**: θ_new = θ_prev - α∇(TD_e - TD_t)

**Executor Allocation Constraint**:
- Each stage limited to one executor
- Agent selects execution order (which leaf node to execute)
- Simplifies action space complexity
- Maximizes executor availability in high-load scenarios
- Avoids resource contention issues

**Implementation**:

**Platform**: Apache Spark simulator (based on Decima simulator [Mao et al., SIGCOMM 2019])
- Cluster management: YARN, Mesos, Kubernetes, or Spark Standalone
- SparkContext: Coordinates executors on worker nodes
- Tasks: Sent from SparkContext to executors for execution

**Dataset**: TPC-H benchmark [TPC-H v2/v3]
- Standard database benchmark for decision support systems
- Jobs generated randomly based on TPC-H patterns
- Variable job arrival rates tested

**Framework**: Deep Graph Library (DGL) [Wang et al., arXiv 2019]
- Graph-centric, high-performance package for GNNs
- Efficient message passing implementations
- Supports various GNN architectures (GraphSAGE, GCN, GAT, etc.)

**Comparison Baselines**:
1. **FIFO** (First In First Out): Spark default scheduler
2. **Dynamic Partitioning**: Tuned heuristic-based scheduler
3. **Decima**: State-of-the-art RL-based scheduler [Mao et al., SIGCOMM 2019]

**Evaluation Metrics**:
- Average job completion time
- Executor usage (resource utilization)
- Cumulative distribution of rewards
- Training time
- Inference time

## Results

**Job Completion Time Improvement**:

**vs Decima (State-of-the-Art ML Scheduler)**:
- **15% improvement** in average job completion time
- Decima uses separate DAG processing without aggregated graph structure
- Decima uses standard GNN message passing (root-to-leaf)

**vs Dynamic Partitioning (Tuned Heuristic)**:
- **20% improvement** in average job completion time
- Dynamic partitioning uses rule-based resource allocation
- Heuristic-based approaches inefficient for complex, high-frequency job arrivals

**vs FIFO (Spark Default)**:
- Significant improvement (exact percentage not specified)
- FIFO processes jobs in arrival order without considering dependencies or resource optimization

**Executor Usage (Figure 2)**:

**Decima Executor Usage**:
- Executor usage: 0.9895741710286684
- Average completion time: 1118995.3024828587
- Shows fluctuating executor usage over time (0-2,500,000 time units)
- Number of executors varies between 0-50

**Dynamic Scheduling Executor Usage**:
- Executor usage: 0.798885354637495
- Average completion time: 1130313.9169152423
- More stable executor usage pattern
- Fewer executors active simultaneously (0-40 range)

**Key Finding**: Proposed method achieves better completion time with efficient executor utilization

**Training Convergence Analysis** (Figure 3):

**Pooling Aggregator**:
- Loss: Converges fairly fast initially, then stabilizes around 3000+ episodes
- Q-value: Steady increase from 0.01 to 0.04 over 100 episodes
- Convergence: Slower than mean aggregator
- Final policy efficiency: Comparable to mean aggregator

**Mean Aggregator**:
- **Stage 1** (Heuristic-assisted):
  - Loss: Rapid decrease from 0.90 to near 0
  - Q-value: Rapid increase from 0.36 to 0.42 in 15 episodes
  - Convergence: Much faster due to heuristic guidance
- **Stage 2** (Pure RL exploration):
  - Loss: Continues decreasing gradually
  - Q-value: Continues increasing to 0.43+
  - Refinement of policy learned in Stage 1

**Key Finding**: Mean aggregator with heuristic assistance converges **significantly faster** than pooling aggregator

**Aggregator Comparison**:

| Aggregator | Training Episodes | Convergence Speed | Final Performance |
|------------|------------------|-------------------|-------------------|
| Pooling | ~100+ | Slower (gradual) | Comparable |
| Mean Stage 1+2 | 15 + 30 = 45 | **Much faster** | Comparable |

**Recommendation**: Mean aggregator with 2-stage hybrid training preferred for faster convergence

**Training Time Reduction**:

**Hybrid Heuristic Assistance Benefits**:
- **Stage 1**: 90-100% heuristic guidance accelerates initial policy exploration
- Reduces wasted exploration in suboptimal regions of state space
- Burning period (1000 episodes) establishes baseline performance
- **Stage 2**: Pure RL refines policy beyond heuristic limitations

**Comparison**: Training time significantly reduced compared to pure RL exploration (exact speedup not quantified)

**Resource Utilization Patterns**:

**Proposed Method**:
- Efficient executor allocation through RL-learned policy
- One executor per stage maximizes stage parallelism
- Better handling of high-frequency job arrivals

**Baseline Methods**:
- FIFO: Simple but suboptimal resource usage
- Dynamic Partitioning: Better than FIFO but lacks learning capability
- Decima: Good performance but slower convergence, no aggregated graph representation

**Limitations Observed**:

**One Executor Per Stage Constraint**:
- **Advantage**: Works well for high-load environments (more stages can execute in parallel)
- **Disadvantage**: Limits overall response time (makespan) for individual jobs
- **Trade-off**: Job completion time vs individual job latency

**Proposed Solution** (Future Work):
- Manually tune algorithm for lower loads
- Increase maximum executors per stage when cluster load is low
- Adaptive executor allocation based on current cluster state

**Scalability**:
- Single aggregated graph representation scales well with number of jobs
- Append-only operation for new job arrivals (O(1) complexity)
- Parallel embedding computation across all nodes

**Generalization**:
- GraphSAGE inductive learning generalizes to unseen job DAGs
- No matrix factorization required (unlike GCN)
- Learns from node features, applicable to new DAG structures

**Benchmark Variability**:
- Results based on TPC-H dataset randomness
- Multiple runs used for averaging (handles stochasticity)
- Rewards may increase with extended job sets

## Notes

**Research Context**:
- **CO-AUTHORED PAPER**: Mohammad Pivezhandi is **3rd author of 5** (not first author)
- **First author**: Vivek Bengre (Iowa State University, SwAPP Lab)
- **Second author**: M. Reza HoseinyFarahabady (University of Sydney, Center for Distributed and High Performance Computing)
- **Third author**: **Mohammad Pivezhandi** (Iowa State University, SwAPP Lab)
- **Fourth author**: Albert Y. Zomaya (University of Sydney)
- **Fifth author**: Ali Jannesari (Iowa State University, SwAPP Lab - Mohammad's advisor)

**Mohammad's Affiliation**:
- Iowa State University, Department of Computer Science
- Laboratory for Software Analytics and Pervasive Parallelism (SwAPP)
- Email: mpvzhndi@iastate.edu

**Conference**: PDCAT 2021
- International Conference on Parallel and Distributed Computing: Applications and Technologies
- Published by Springer
- Pages: 175-186

**Application Domains**:
- Data warehouse systems (Amazon Redshift, Azure Data Warehouse, Google BigQuery)
- ETL (Extract, Transform, Load) operations
- Business intelligence platforms
- Distributed data processing (Apache Spark)
- Stream processing systems
- Real-time query processing

**Technical Innovation**:
- **Reverse message passing**: Key novelty enabling leaf nodes to incorporate parent information
- **Single aggregated graph**: Simplifies state representation, enables efficient parallelization
- **Hybrid training**: Heuristic assistance dramatically accelerates convergence
- **Direct Q-value embedding**: Eliminates separate value network, speeds training
- **One executor per stage**: Simplifies action space, works well for high loads

**Graph Neural Networks Background**:
- **GNNs**: Deep learning for graph-structured data [Scarselli et al., IEEE TNN 2008]
- **Applications**: Social networks, node classification, drug delivery, protein-protein interaction
- **Graph embedding**: Compresses graph structure while preserving information
- **Message passing**: Aggregates features from neighboring nodes
- **GraphSAGE**: Inductive learning on large graphs [Hamilton et al., NeurIPS 2017]
  - Mean aggregator: Average neighbor embeddings
  - Pooling aggregator: Max/mean pooling over neighbors
  - Generalizes to unseen nodes (no matrix factorization)

**Reinforcement Learning Background**:
- **Markov Decision Process**: Environment assumed Markovian (current state summarizes history)
- **Agent components**: State, policy, value function, model
- **Policy π**: Maps state to action distribution
- **Value function**: Expected cumulative reward following policy
- **Model**: Approximation of environment dynamics
- **Goal**: Find optimal policy π* maximizing expected discounted reward

**DDQN (Double Deep Q-Network)**:
- **Advantage**: Reduces overestimation bias of DQN [Van Hasselt et al., AAAI 2016]
- **Mechanism**: Separate networks for action selection and value estimation
- **Temporal Difference Learning**: θ_new = θ_prev - α∇(TD_error)
- **Replay Buffer**: Stores past experiences for training stability

**Apache Spark Architecture**:
- **SparkContext**: Main program coordinator
- **Cluster Manager**: YARN, Mesos, Kubernetes, Standalone
- **Executors**: Worker processes running computations and storing data
- **Tasks**: Units of work sent to executors
- **DAG Scheduler**: Breaks jobs into stages based on dependencies

**Job Scheduling Challenges**:

**1. NP-Hard Complexity**: DAG scheduling with dependencies is NP-hard
**2. Unknown Arrival Patterns**: Job arrival rate and characteristics unknown a priori
**3. Resource Contention**: Multiple jobs compete for limited executors
**4. Dependency Management**: Must respect DAG dependencies (topological ordering)
**5. High-Frequency Arrivals**: Must handle bursts of job submissions efficiently

**Comparison with Prior Work**:

**Decima [Mao et al., SIGCOMM 2019]**:
- State-of-the-art RL scheduler for data processing clusters
- Uses GNN for DAG embedding
- Separate DAG processing (not aggregated)
- Standard message passing (root-to-leaf)
- This work improves by 15% with aggregated graph + reverse messaging

**Heuristic Schedulers**:
- **FIFO**: First Come First Serve - simple but suboptimal
- **Round Robin**: Cyclic executor allocation
- **SJF**: Shortest Job First - requires job length knowledge
- **Dynamic Partitioning**: Rule-based resource allocation
- This work improves by 20% over tuned dynamic partitioning

**Domain-Specific Schedulers** [Isard et al. 2007, Peng et al. 2018]:
- Focus on specific workload characteristics
- Not generalizable to diverse job types
- This work uses general RL approach applicable to varied workloads

**Control-Based Approaches** [HoseinyFarahabady et al. 2017-2018]:
- Model Predictive Control for QoS guarantees
- Priority channels for stream processing
- Lambda platform CPU usage control
- Complementary to RL-based scheduling (could be combined)

**Dependency-Aware Schedulers** [Grandl et al., OSDI 2016]:
- GRAPHENE: Packing and dependency-aware scheduling
- Considers DAG structure for resource allocation
- This work extends with RL for dynamic learning

**Hybrid Learning Approaches** [Kumar & Vidyarthi 2016]:
- PSO-GA meta-heuristic for DAG scheduling with communication
- This work uses RL instead of evolutionary algorithms

**Related GNN Work (Co-authors)**:

**SwAPP Lab Publications** (Ali Jannesari's group):
- **SpaFL** [Yu et al. 2021]: Salient parameter aggregation for federated learning
- **Auto Graph Encoder-Decoder** [Yu et al., ICCV 2021, arXiv 2020]: Neural network pruning, model compression, network acceleration
- **Hierarchical Representations** [Liu et al., arXiv 2017]: Efficient architecture search

**Acknowledgments**:
- **Iowa State Research IT (RIT)**: HPC cluster access for experiments
- **Australian Research Council**: Discovery scheme DP190103710 (Albert Y. Zomaya)
- **University of Sydney**: Center for Distributed and High Performance Computing facilities, ML/analytic infrastructure

**Limitations**:
- One executor per stage may limit individual job performance in low-load scenarios
- Training requires simulator (difficult to test on production systems directly)
- Heuristic assistance requires good initial heuristic (domain knowledge)
- TPC-H dataset may not represent all real-world ETL workload patterns
- Makespan (overall response time) trade-off with completion time

**Future Directions** (from Conclusion):
- Adaptive executor allocation: Tune max executors per stage based on load
- Hardware optimization: Limited memory, CPU, storage constraints
- Extended learning: Continued training on real-life cluster loads for performance improvement
- Multi-objective optimization: Balance completion time, resource utilization, energy

**Connection to Mohammad's Research Arc**:
- Follows pivezhandi2020parahist (2020, FPGA optical flow - hardware acceleration)
- Precedes pivezhandi2023toward (2023, master's thesis on RL for resource scheduling)
- **Establishes expertise in**: Graph neural networks, reinforcement learning, resource scheduling
- **Collaboration**: International collaboration with University of Sydney researchers
- **SwAPP Lab**: Laboratory for Software Analytics and Pervasive Parallelism focus
- **Foundation for later DVFS work**: RL-based resource allocation techniques

**Related Work**:
- Scarselli et al. (IEEE TNN 2008): Graph neural network model
- Van Hasselt et al. (AAAI 2016): Double deep Q-learning
- Mnih et al. (arXiv 2013): Playing Atari with deep RL (DQN)
- Mao et al. (SIGCOMM 2019): Decima scheduler for data processing clusters
- Hamilton et al. (NeurIPS 2017): GraphSAGE inductive learning on large graphs
- Wu et al. (IEEE TNNLS 2020): Comprehensive survey on GNNs
- Wang et al. (arXiv 2019): Deep Graph Library
- Grandl et al. (OSDI 2016): GRAPHENE dependency-aware scheduling
- HoseinyFarahabady et al. (2017-2018): QoS-aware resource provisioning

**Code/Data Availability**:
- Not mentioned in paper
- Based on Decima simulator (publicly available)
- Implementation details suggest custom DGL + PyTorch implementation

**Performance Highlights**:
- **15% faster** than Decima (SOTA ML scheduler)
- **20% faster** than dynamic partitioning heuristic
- **Significantly faster training** with hybrid heuristic assistance
- **Mean aggregator** converges much faster than pooling aggregator
- **Scales well** with number of jobs (aggregated graph representation)

## Bibdata

```bibtex
% Row 5
@inproceedings{bengre2021learning,
  title={A learning-based scheduler for high volume processing in data warehouse using graph neural networks},
  author={Bengre, Vivek and HoseinyFarahabady, M Reza and Pivezhandi, Mohammad and Zomaya, Albert Y and Jannesari, Ali},
  booktitle={International Conference on Parallel and Distributed Computing: Applications and Technologies},
  pages={175--186},
  year={2021},
  organization={Springer}
}
```
