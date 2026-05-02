# Assignment 3 - Complete Documentation

**Student Name**: [Faris Abdulrahman Alobaid]  
**Student ID**: [445050115]  
**Date Submitted**: [2026/5/2]

---

## 🎥 VIDEO DEMONSTRATION LINK (REQUIRED)

> **⚠️ IMPORTANT: This section is REQUIRED for grading!**
> 
> Upload your 3-5 minute video to your **PERSONAL Gmail Google Drive** (NOT university email).
> Set sharing to "Anyone with the link can view".
> Test the link in incognito/private mode before submitting.

**Video Link**: [(https://drive.google.com/drive/folders/1MaVkm79ScSMZJLaIkUlGCa26Z7dombdd)]

**Video filename**: `[YourStudentID]_Assignment3_Synchronization.mp4`

**Verification**:
- [ ] Link is accessible (tested in incognito mode)
- [ ] Video is 3-5 minutes long
- [ ] Video shows code walkthrough and commits
- [ ] Video has clear audio
- [ ] Uploaded to PERSONAL Gmail (not @std.psau.edu.sa)

---

## Part 1: Development Log (1 mark)

Document your development process with **minimum 3 entries** showing progression:

### Entry 1 - [2026/5/2, 8:09PM]
**What I implemented**:
First, I entered my student ID and learned the structure of the scheduler simulation code with threads, shared resources, and process execution. 

**Challenges encountered**: 
Understanding how multiple threads access shared variables simultaneously and identifying potential race conditions was initially confusing

**How I solved it**: 
I looked at the lecture notes and textbook concepts on race conditions and critical sections

**Testing approach**: 
I ran the program to observe its behavior without synchronization

**Time spent**: 
about 13min
---

### Entry 2 - [2026/5/2, 8:22PM]
**What I implemented**:
I added ReentrantLock to protect shared counters such as contextSwitchCount and completedProcessCount and totalWaitingTime 

**Challenges encountered**: 
Ensuring that locks are always released correctly to avoid deadlocks

**How I solved it**: 
I used try-finally blocks so that unlock() is always executed

**Testing approach**: 
Tested the program multiple times to confirm that counters increase properly

**Time spent**: 
about 18min
---

### Entry 3 - [2026/5/2, 8:40PM]
**What I implemented**: 
I added a separate ReentrantLock to protect the executionLog ArrayList

**Challenges encountered**: 
Understanding why ArrayList is not thread safe and how concurrent modification exceptions happen

**How I solved it**:
I put the executionLog.add() in a lock 

**Testing approach**:
Made sure a ConcurrentModificationException does not happen 

**Time spent**:
about 8min 

---

### Entry 4 - [2026/5/2, 8:48PM]
**What I implemented**:
I implemented a Semaphore to control access to the CPU and to make sure that only one process runs at a time

**Challenges encountered**:
Knowing how to get and release the semaphore in the right places 

**How I solved it**:
I placed acquire() at the start of run() and release() in the finally block 

**Testing approach**:
Verified that processes execute sequentially without overlap 

**Time spent**: 
about 10min
---

### Entry 5 - [2026/5/2, 8:59PM]
**What I implemented**:
Final testing and debugging and verification of consistent results

**Challenges encountered**:
make sure that all synchronization mechanisms work together without errors

**How I solved it**:
Reviewed all critical sections carefully and did multiple tests

**Testing approach**:
Executed the program multiple times and compared outputs

**Time spent**: 
about 10min
---

## Part 2: Technical Questions (1 mark)

### Question 1: Race Conditions
**Q**: Identify and explain TWO race conditions in the original code. For each:
- What shared resource is affected?
- Why is concurrent access a problem?
- What incorrect behavior could occur?

**Your Answer**:

[Two race conditions exist in the original code the first comes in the shared counters such as contextSwitchCount multiple threads may read the same value simultaneously and increment it causing lost updates and incorrect final values the second race condition comes in the executionLog ArrayList Since ArrayList is not thread-safe concurrent modifications may lead to inconsistent data or runtime exceptions like ConcurrentModificationException without synchronization these shared resources may produce unpredictable results]

---

### Question 2: Locks vs Semaphores
**Q**: Explain the difference between ReentrantLock and Semaphore. Where did you use each in your code and why?

**Your Answer**:

[ReentrantLock provides mutual exclusion allowing only one thread to access a critical section at a time it is used to protect shared variables such as counters and the execution log semaphore on the other hand controls access to a limited number of resources In this implementation a binary semaphore 1 permit is used to simulate cpu access ensuring only one process executes at a time locks were used for data protection while the semaphore was used for resource control]

---

### Question 3: Deadlock Prevention
**Q**: What is deadlock? Explain TWO prevention techniques and what you did to prevent deadlocks in your code.

**Your Answer**:

[Deadlock occurs when two or more threads wait indefinitely for each other to release resources one prevention technique is using try-finally blocks to ensure locks are always released another technique is avoiding nested locks or maintaining a consistent lock ordering in my code i prevented deadlocks by always releasing locks and semaphores inside finally blocks ensuring no resource remains locked if an exception occurs]

---

### Question 4: Lock Granularity Design Decision 
**Q**: For Task 1 (protecting the three counters), explain your lock design choice:
- Did you use ONE lock for all three counters (coarse-grained) OR separate locks for each counter (fine-grained)?
- Explain WHY you made this choice
- What are the trade-offs between the two approaches?
- Given that the three counters are independent, which approach provides better concurrency and why?

**Your Answer**:

[ used a single lock coarse-grained locking to protect all three counters this simplifies the implementation and reduces the risk of deadlocks it reduces concurrency because only one thread can update any counter at a time fine-grained locking would allow separate locks for each counter increasing concurrency but adding complexity since the counters are independent fine-grained locking could provide better performance i chose coarse-grained locking for simplicity and reliability]

---

## Part 3: Synchronization Analysis (1 mark)

### Critical Section #1: Counter Variables

**Which variables**:
contextSwitchCount and completedProcessCount and totalWaitingTime

**Why they need protection**:
Multiple threads update them concurrently causing race conditions 

**Synchronization mechanism used**:
 ReentrantLock

**Code snippet**:
```java
// Paste your implementation here
counterLock.lock();
try 
{
    contextSwitchCount++;
} 
finally 
{
    counterLock.unlock();
}
```

**Justification**:
Ensures only one thread updates shared counters at a time

---

### Critical Section #2: Execution Log

**What resource**:
executionLog (ArrayList) 

**Why it needs protection**:
ArrayList is not thread-safe and concurrent access can cause exceptions 

**Synchronization mechanism used**:
ReentrantLock 

**Code snippet**:
```java
// Paste your implementation here
logLock.lock();
try 
{
    executionLog.add(message);
} 
finally 
{
    logLock.unlock();
}
```

**Justification**:
Prevents concurrent modification and ensures data consistency 

---

### Critical Section #3: CPU Semaphore

**Purpose of semaphore**:
Control cpu access 

**Number of permits and why**:
1 permit to allow only one process at a time

**Where implemented**:
In run() and runToCompletion() 

**Code snippet**:
```java
// Paste your implementation here
        try 
        {
            SharedResources.cpuSemaphore.acquire();
        } 
        catch (InterruptedException e) 
        {
            e.printStackTrace();
        }
        
        try {
            if (startTime == -1) {
                startTime = System.currentTimeMillis();
            }
            
            // Increment context switch counter
            SharedResources.incrementContextSwitch();
            
            int runTime = Math.min(timeQuantum, remainingTime);
            
            String quantumBar = createProgressBar(0, 15);
            String message = "  ▶ " + name + " (Priority: " + priority + ") executing quantum [" + runTime + "ms]";
            System.out.println(Colors.BRIGHT_GREEN + message + Colors.RESET);
            
            // Log execution
            SharedResources.logExecution(name + " started quantum execution");
            
            try {
                int steps = 5;
                int stepTime = runTime / steps;
                
                for (int i = 1; i <= steps; i++) {
                    Thread.sleep(stepTime);
                    int quantumProgress = (i * 100) / steps;
                    quantumBar = createProgressBar(quantumProgress, 15);
                    System.out.print("\r  " + Colors.YELLOW + "⚡" + Colors.RESET + 
                                    " Quantum progress: " + quantumBar);
                }
                System.out.println();
                
            } catch (InterruptedException e) {
                System.out.println(Colors.RED + "\n  ✗ " + name + " was interrupted." + Colors.RESET);
            }
            
            remainingTime -= runTime;
            int overallProgress = (int) (((double)(burstTime - remainingTime) / burstTime) * 100);
            String overallProgressBar = createProgressBar(overallProgress, 20);
            
            System.out.println(Colors.YELLOW + "  ⏸ " + Colors.CYAN + name + Colors.RESET + 
                              " completed quantum " + Colors.BRIGHT_YELLOW + runTime + "ms" + Colors.RESET + 
                              " │ Overall progress: " + overallProgressBar);
            System.out.println(Colors.MAGENTA + "     Remaining time: " + remainingTime + "ms" + Colors.RESET);
            
            if (remainingTime > 0) {
                System.out.println(Colors.BLUE + "  ↻ " + Colors.CYAN + name + Colors.RESET + 
                                  " yields CPU for context switch" + Colors.RESET);
                SharedResources.logExecution(name + " yielded CPU");
            } else {
                completionTime = System.currentTimeMillis();
                long waitingTime = (completionTime - creationTime) - burstTime;
                SharedResources.addWaitingTime(waitingTime);
                SharedResources.incrementCompletedProcess();
                SharedResources.logExecution(name + " completed execution");
                System.out.println(Colors.BRIGHT_GREEN + "  ✓ " + Colors.BOLD + Colors.CYAN + name + 
                                  Colors.RESET + Colors.BRIGHT_GREEN + " finished execution!" + 
                                  Colors.RESET);
            }
            System.out.println();
            
        } finally 
        {
            // TODO #4: Release CPU semaphore here
            // Always release in finally block to prevent deadlocks!
            // Release cpu access after execution
            SharedResources.cpuSemaphore.release();
        }
```

**Effect on program behavior**:
Ensures processes do not execute simultaneously preventing conflicts 

---

## Part 4: Testing and Verification (2 marks)

### Test 1: Consistency Check
**What I tested**:
Running program multiple times

**Testing procedure**:
```bash
javac SchedulerSimulationSync.java
java SchedulerSimulationSync
```

**Results**: 
(The results were consistent across multiple runs with correct counts)

**Why synchronization is necessary**: 
(Without synchronization counters could produce incorrect values due to race conditions)

**Conclusion**:
Synchronization ensures deterministic and correct output 

---

### Test 2: Exception Testing
**What I tested**: Checking for ConcurrentModificationException

**Testing procedure**:
Ran program with multiple threads accessing executionLog

**Results**:
No exceptions occurred 

**What this proves**:
The execution log is properly synchronized 

---

### Test 3: Correctness Verification
**What I tested**: Verifying correct final values (total burst time, context switches, etc.)

**Expected values**:
Correct number of completed processes and accurate waiting time 

**Actual values**:
Matched expected values 

**Analysis**:
Synchronization ensured accuracy

---

### Test 4: Different Scenarios
**Scenario tested**: [e.g., different time quantum, more processes, etc.]

**Purpose**:
Verify scalability

**Results**:
Program behaved correctly

**What I learned**:
Synchronization works under different loads 

---

## Part 5: Reflection and Learning

### What I learned about synchronization:

[Synchronization is essential in multithreaded systems to prevent race conditions and ensure data consistency i learned that when multiple threads access shared resources the results can become unpredictable without proper protection using ReentrantLock helped me understand how mutual exclusion works by allowing only one thread to enter a critical section at a time also i learned how semaphores control access to limited resources like allowing only one process to use the cpu one of the main challenges was ensuring that locks are always released that i solved using try-finally blocks and i realized the importance of identifying critical sections correctly before applying synchronization, this assignment helped me connect theoretical concepts from the textbook to real implementation it improved my understanding of concurrency and thread safety in operating systems]

---

### Real-world applications:

Give TWO examples where synchronization is critical:

**Example 1**:
Banking systems use synchronization when multiple users access the same account without synchronization two transactions could update the balance incorrectly leading to data inconsistency

**Example 2**:
Operating systems use synchronization to manage access to shared resources such as printers or files for example only one process should print at a time to avoid mixed outputs 

---

### How I would explain synchronization to others:

[Synchronization is like a traffic control system Imagine multiple cars "threads" trying to enter a one-lane road "shared resource" without traffic lights cars may crash "race condition" a lock works like a traffic signal that allows only one car to pass at a time a semaphore is like allowing a limited number of cars depending on the road capacity in simple terms synchronization ensures that threads take turns safely when accessing shared data preventing errors]

---

## Part 6: GitHub Repository Information

**Repository URL**: https://github.com/Faris-Alobaid/OS-Assignment3-Faris-Alobaid

**Number of commits**: 7 without this one

**Commit messages**: 
1. int studentID = 445050115; 
2. Added synchronization libraries ReentrantLock and Semaphore
3. Added ReentrantLock and Semaphore for synchronization
4. Protected shared counters
5. Protected execution log
6. Implemented semaphore to control cpu access
7. Applied semaphore synchronization to runToCompletion method

---

## Summary

**Total time spent on assignment**:
about 2h

**Key takeaways**: 
1. Synchronization is necessary to prevent race conditions in multithreaded programs
2. Locks ensure mutual exclusion while semaphores manage resource access
3. Proper use of try-finally blocks is essential to avoid deadlocks

**Most challenging aspect**:
The most challenging aspect was understanding how race conditions occur and determining which parts of the code require synchronization It was also challenging to ensure that locks and semaphores were used correctly without introducing deadlocks

**What I'm most proud of**:
I am proud of successfully implementing synchronization mechanisms and ensuring that the program produces consistent and correct results across multiple runs and i feel confident in explaining synchronization concepts both theoretically and practically 

---

**End of Documentation**
