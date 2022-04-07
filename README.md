# COSC 315 Project 1

## Sources

[IMB waitpid() — Wait for a specific child process to end](https://www.ibm.com/docs/en/zos/2.1.0?topic=functions-waitpid-wait-specific-child-process-end)

## Design Choices

### Launch the Command n-times
In order to run the given command n times, we are running a for-loop that spawns a child process each iteration.

### Keep track of all child processes
In order to keep track of all the child processes, we are storing their ids (returned by fork) in an array. This is especially relevant for the parallel case because we are there spawning all n process before waiting for them to terminate. In the sequential case we wait for the termination each iteration of the for-loop. In this case a simple variable would be sufficient, but we decided to use the array in both cases to increase the readability.

### Keep track of  time while waiting for childs to terminate
Our first idea to keep track of the termination time was to have a time within the main processes. But for this case, we could not figure out a way to continue the for-loop if the process terminated faster than the timeout. Our second idea, we decided to implement an seperate timer process, which we are running in a second child process. We are then waiting for any child process to terminate and check afterwards which child returned, so we can act accordingly by killing the timer or the child. Specifically, we have implemented this as follows:

#### Sequential Case
If in the sequential mode, we are starting a new timer each iteration. At the end of the for-loop, we are waiting in the main process in a while-loop for either the child or the timer to return. We are doing this in a while-loop, because we are waiting in mode -1, which is waiting for any child to return. If the timer terminates first, we kill the child process excuting the command otherwise we kill the timer.

#### Parallel Case
For the parallel mode, we are starting only one timer in the last iteration, as we are waiting outside of the for-loop, so the commands can run in parallel. The challenge in the waiting process was any child or timer can terminate in any order. Also we had to keep track of which child already arrived. 

We decided to implement a while-loop which listened for any child to terminate. We then run over the child array to see if was a child that terminated. If so we increase a counter, counting the terminated childs and set the id in the array to zero. If either the counter counts up to the inputted number of childs, or the terminated process is the timer process, we exit the while-loop and either kill the timer.

---
## Code Explanation
1. Run a for-loop based on `count`
2. Set timer_id, so we can determine if we are in the timer process
3. fork() process child and store the child id in an array (0 in the child process)
4. determine whether timer is needed in this iteration and process
5. Start timer if required
Next, the path breaks into 3 parts:
    - if in command child process, execute child's command using `execvp()`
    - if in the timer child process, use `sleep(timeout)` to check if process is taking too long
    - if in main process and sequential mode, wait for either response from child using `waitpid()` or timer ID. Kill child process if timer ID arrives first or kill timer ID if child process arrivces first.
6. after the for loop, if operating in parallel mode, listen for child process to arrive using `waitpid()`. Check if all have child processes have arrived. If timer ID completed first, then kill all child processes. Otherwise, kill the timer.

## Team

The team members of project are as follows:

* Alex Rogov
* Niklas Tecklenburg 

### Contributions - Alex

- Discussion on code creation
- Write code of one version of a solution
- Write the README.md

### Contributions - Niklas
- Discussion on code creation
- Write final code solution
- Write the README.md