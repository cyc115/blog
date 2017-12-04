# Advanced Go: using WaitGroup and channel to restart process on configuration change 

var wg sync.WaitGroup can be used to synchronize between gorutines 
`#Add(n int)` : specify n# of rutins to wait for
`#Done()` : each child rutine calls `Done()` to signal completion 
`#Wait()` : blocks the main routine until `Done` is called n times by child rutines