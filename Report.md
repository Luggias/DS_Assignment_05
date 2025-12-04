Assignment 5 Report
---------------------

# Team Members


# GitHub link to your (forked) repository

...

# Task 1

Note: Some questions require you to take screenshots. In that case, please join the screenshots and indicate in your answer which image refer to which screenshot.

1. What happens when Raft starts? Explain the process of electing a leader in the first
term.

Ans: When Raft starts, all servers begin in the Follower state, and there is no leader yet. Each follower has its own random election timeout. 
     Since no leader exists to send heartbeats, one follower’s timeout will eventually expire. That follower becomes a Candidate, 
     increases the current term, and sends RequestVote messages to all other servers. 
     If it receives a majority of votes, it is elected the Leader for that term. Once elected, it immediately starts sending heartbeat messages to all followers to prevent new elections and to stabilize the cluster.

2. Perform one request on the leader, wait until the leader is committed by all servers. Pause the simulation.
Then perform a new request on the leader. Take a screenshot, stop the leader and then resume the simulation.
Once, there is a new leader, perform a new request and then resume the previous leader. Once, this new request is committed by all servers, pause the simulation and take a screenshot. Explain what happened?

Ans: When the first request was committed, all nodes had the same log entry. 
     After pausing the simulation, the leader received another request, but this second entry was not committed yet. 
     When the leader was stopped, a new leader was elected based only on the last committed entry, so the uncommitted entry from the old leader was ignored.
     The new leader accepted a new request, replicated it, and committed it. When the old leader rejoined, it discarded its uncommitted entry 
     and updated its log to match the new leader. In the end, all nodes converged to the same committed log again.

3. Using the same visualization, stop the current leader and two additional servers. After a few increments, pause the simulation and take a screenshot. Then resume all servers and restart the simulation. After the leader election, pause the simulation and take a screenshot. How do you explain the behavior of the system during the above exercise?

Ans: When the leader and two additional servers were stopped, only two nodes remained active. With only two nodes running, the system could no longer reach a majority. 
    Since Raft requires a majority to elect a leader, no leader could be chosen, and the two active nodes repeatedly increased their term numbers without making progress. 
    As a result, the cluster stayed in a state without a leader and could not commit any log entries.
    After all servers were resumed, a majority became available again. 
     The system immediately performed a new leader election, successfully selected a leader, and synchronized the logs across all nodes. 

# Task 2

1. Which server is the leader? Can there be multiple leaders? Justify your answer using the statuses from the different servers.
Ans: 
There is exactly one leader at any time. 
This can be seen in the /admin/status output of each node, where only one node reports state: 
leader and the others report state: follower. 
Raft guarantees that there cannot be multiple leaders in the same term because a leader must receive a majority of votes.

2. Perform a PUT operation for the key "a" on the leader. Check the status of the different nodes. What changes have occurred and why (if any)?

Ans:
Performing a PUT for key "a" updates the value on the leader and then replicates it to the followers.
After replication, all nodes show the same value in their internal state.
The commit index increases because the operation has been committed by a majority

3. Perform an APPEND operation for the key "a" on the leader. Check the status of the different nodes. What changes have occurred and why (if any)?

Ans:
The APPEND operation adds "mouse" to the existing list.
Again, the leader appends the log entry and replicates it to the followers.
After commit, all nodes return: ["cat","dog","mouse"]


4. Perform a GET operation for the key "a" on the leader. Check the status of the different nodes. What changes have occured and why (if any)?

Ans:
A GET request on any node returns the same value.
This shows that Raft has replicated the state correctly across all nodes.


# Task 3

1. Shut down the server that acts as a leader. Report the status changes that you get from the servers that remain active after shutting down the leader. What is the new leader (if any)?

Ans:

1. Perform a PUT operation for the key "a" on the new leader. Then, restart the previous leader, and indicate the changes in status for the three servers. Indicate the result of a GET operation for the key "a" to the previous leader.

Ans:

3. Has the PUT operation been replicated? Indicate which steps lead to a new election and which ones do not. Justify your answer using the statuses returned by the servers.

Ans:

4. Shut down two servers: first shut down a server that is not the leader, then shut down the leader. Report the status changes of the remaining server and explain what happened.

Ans:

5. Can you perform GET, PUT, or APPEND operations in this system state? Justify your answer.

Ans:

6. Restart the servers and note down the changes in status. Describe what happened.

Ans:

## Network Partition

For the first experiment, create a network partition with 2 servers (including the leader)
on the first partition and the 3 other servers on the other one. Indicate the changes that occur in the status of a server on the first partition and a server on the second partition. Reconnect the partitions and indicate what happens. What are the similarities and differences between the implementation of Raft used by your key/value service (based on the PySyncObj library) and the one shown in the Secret Lives of Data illustration from Task 1? How do you justify the differences?

Ans:

For the second experiment, create a network partition with 3 servers (including the
leader) on the first partition and the 2 other servers on the other one. Indicate the changes that occur in the status of a server on the first partition and a server on the second partition. Reconnect the partitions and indicate what happens. How does the implementation of Raft used by your key/value service (based on the PySyncObj library) compare to the one shown in the Secret Lives of Data illustration from Task 1?

Ans: 


# Task 4

1. Raft uses a leader election algorithm based on randomized timeouts and majority voting, but other leader election algorithms exist. One of them is the bully algorithm, which is described in Section 5.4.1 of the Distributed Systems book by van Steen and Tanenbaum. Imagine you update the PySyncObject library to use the bully algorithm for Raft (as described in the Distributed Systems book) instead of randomized timeouts and majority voting. What would happen in the first network partition from Task 3?

Ans: 

2. Why is it that Raft cannot handle Byzantine failure? Explain in your own words.

Ans: 
