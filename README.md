
# Starve-Free-Reader-Writer-Problem

The Reader-Writer Problem is a classical problem in Computer Science in which a data structure like database, storage area, etc is being accessed simultaneously by multiple processes concurrently.In the problem there are two types of processes :

Reader:They can only read the shared resources .

 Writer:They can modify the shared resources.
 
 The critical section is implemented such that it can either be accessed by a single writer or multiple Reader's simultaneously.This is implemented using semaphore's and has the following trivial solution:

 Writer-Starve: The solution is implemented such that no reader should wait for other readers to finish simply because a writer is waiting,In this soluton the writer starves.

Reader-Starve:The solution is implemented such that  if a writer is waiting to access the object, no new readers may start reading.In this soluton the reader starves.


Presenting the solution for Reader-Writer Problem so that neither 
the Reader nor the Writer starves 

## Semaphore's used 
entry_mx-Semaphore(1)

read_mx-Semaphore(1)

wrt_mx-Semaphore(1)

reader_ct-Integer(0)


- reader_ct:This is a count variable and is used to store the number of readers active in the critical section.

- read_mx:This is used as a write-access to modfiy the reader_ct variable.Any reader that enters/exits the critical section must first acquire it before leaving/entering .

- wrt_mx:This is used to give access to the critical section to both reader and the Writer,has to be obtained by every writer before entering the critical section and signalled every time it exits the section, but only the first reader has to acquire this semaphore to enter the critcal section and only the last reader to exit the critical section has to signal it 

- entry_mx:This has to obtained before anyone(reader/writer) gets the access to wrt_mx  variable to access the critical section or if a reader access the critical section direclty .This mutex ensured bounded waiting for the writer,let's assume a writer comes in between 2 reader so in order to get access to the critical section the writer as it comes first would acquire the entry_mx and the reader that comes after would be blocked,even if there are some reader's present in the critical section after they exit the writer would get the priority to get access to the wrt_mx as it would be on the top of the blocked queue of the wrt_mx .
Thus readers and writers are now at equal priority and none would starve ,this is an effiecient and starve-free solution to the Reader-Writer problem.

## writer's pseudocode 
```bash
 wait(entry_mx);//first have to access the entry mutex 
 wait(wrt_mx);// access the wrt_mx to ensure mutual exclusion between Reader and writer
 [critical section]
 signal(wrt_mx);//relase the wrt mutex 
 signal(entry_mx);//allow other Reader or writer to enter the critical section 
```

## Reader's pseudocode
```bash
wait(entry_mx);//first have to access the entry mutex 
wait(read_mx);//to get mutually exclusive access to modify Reader_ct
Reader_ct+=1;
if(Reader_ct==1)wait(wrt_mx);//if the first Reader to access the critical section has to access wrt_mx 
signal(read_mx);// allow other reader's to modify the Reader_ct and enter the critical section
signal(entry_mx);// allow other writer or Reader to get access to the entry mutex
[critical section]
wait(read_mx);//to get mutually exclusive access to modify Reader_ct
Reader_ct-=1;
if(Reader_ct==0)signal(wrt_mx);//if the last Reader to exit the critical section so signal wrt_mx so that waiting writer can get access to the critical section
signal(read_mx);allow other writer to modify the Reader_ct and enter the critical section
``` 
