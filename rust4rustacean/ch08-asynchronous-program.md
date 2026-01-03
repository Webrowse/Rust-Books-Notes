# Chapter 8: Asynchronous Programming. 

This chapter explores how Rust handles operations that run in the background, allowing a program to stay productive while waiting for slow events like network responses or disk writes.  
- ## 1. Synchronous vs. Asynchronous. 
- **Synchronous (Blocking)**: The application performs one task at a time and waits for it to finish. This is often called blocking because execution stops until the event occurs.  
- **Multithreading**: One way to handle multiple tasks is to give each its own thread, but switching between many threads is expensive and requires complex synchronization like Arc and Mutex.  
- **Asynchronous (Non-blocking)**: Instead of waiting, a function returns immediately with a Poll enum, telling the caller it is either Ready with a result or Pending and needs to be checked again later.  