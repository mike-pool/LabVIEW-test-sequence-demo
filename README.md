Seen here is a simple test sequencer with simulated hardware IO. The sequencer constists of three steps. Each generates a waveform (configurable below) which outputted to a modeled device under test (DUT). The DUT is modeled by a multiplication factor with added noise and its "Actual" output value is then compared against an ideal "Target" to evaluate a pass or fail at each step. Also for simulation purposes, the chance of a fault occurring (DUT outputs 0) at each step can be adjusted with the Fault Probability slider.

If the actual deviates from the target by more than 10%, the step is marked as "fail" (red).

The overall result of the test is indicated by the large LED.

DUT amplifies stimulus by 5x .

Sample rate slow (~50 ms) and inconsistent due to browser.

The framework behind this sequencer operates by handling instructions in a manager loop. Instructions are initiated by sending an "Instruction" message via the manager's queue (marked with ******************) and checked against a list of valid start states.  In each sequence element in the instruction, a manager state name is defined ("____ Start") and the program status is updated upon reaching that element. The sequence steps are then executed sequentially by sending a message to the corresponding loop ("Waiting on ___ to ___..."), with the manager then expecting a response to indicate each step's completion via the Report Step Complete VI ("Completed ___ ___").  This framework allows for easily reconfigurable program execution with the order of execution being defined as structured overview in a single location. Adding additional step types requires simply adding a corresponding step to a desired loop.

Note:
- The array nested in program sequences contains the individual steps to be executed, each in turn containing a message queue/ loop name and a message defining the action to be completed.
- The final emtpy sequence in the array is used to set the final state of the manager.
- The UI loop receives a "___ Complete" upon the conclusion of all steps in any given sequence, with the ___ being the state name.
- The names of the step messages do not have to match the name of the manager state for that sequence.
- Instructions cannot be queued. There can only be one active at a time.
- Instruction names, start states and corresponding sequences are defined and stored in the Define Program Instructions VI, being made available to the manager at program start.


Run Test Sequence example

State: Idle

Start Pressed

Initialize
	>> To UI ---Initialize
	Waiting on UI
	>> To IO ---Initialize
	Waiting on IO
Execute
	>> To UI ---Execute
	Waiting on UI
	>> To IO ---Execute
	Waiting on IO
Finalize
	>> To IO ---Finalize
	Waiting on IO
	>> To UI ---Finalize
	Waiting on UI
Idle


---todo
change status text so instruction, sequence and steps are clearly identifiable
	sequence not to be confused with test sequence (maybe change one to routine?)
create/ configure simulation cases/ flags
clean up
	UI loop
	IO loop
	save all text log messages in same place
	
---feature_list
Hardware fault
	be able to clear error with corresponding case in IO
	handle during initialize or execute
Abort
Pause
Pass initial instruction data to individual steps
Display test history/ data
Select between historical test data for display
	Historical analysis/ comparison
Splining/ smooth transitions between waveforms
	Force sawtooth to return to 0 before waveform transition?
Unit tests
Check for empty test sequence