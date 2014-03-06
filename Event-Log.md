# Mailpile's Event Log

*This document is a work in progress*

## Problems to solve:

1. For debugging and troubleshooting, an event log is a necessity
2. We need a standard way to tell the user what Mailpile is doing
3. A queue of unfinished or pending ops may simplify the design of
   certain more complex workflows, especially ones which require
   user input.
4. If sufficiently detailed, an event log may allow one mailpile to
   synchronize itself with another.
5. It would be nice to be able to undo certain actions.

The question remains whether it is realistic to solve all of these
different problems using the same internal data structure.  See below
for further discussion.


## What does each problem imply?

#### 1. Debugging

This requires timestamps, arguments and result summaries for commands
invoked.  Human readable messages are preferable.

#### 2. Visibility

This requires timestamps, and human readable descriptions of what is
going on.  These human readable descriptions are probably one and the
same as the status messages the commands should be returning.

Updates and progress should update the visible entry (not create a new
one).

#### 3. Workflows

A command that runs in the background needs to be able to post events
which trigger foreground user input, which then gets sent to a 2nd stage
command.  Informing the user what action he should take requires a human
readable description.  Describing the action itself may be complex as
we might need to present the user with multiple choices.

A command that fails due to network trouble, needs to be able to post
events which trigger a retry at some later date. Users should be informed
that operations were incomplete and potentially given the option to cancel
or force an immediate retry.

#### 4. Synchronization

For synchronization to work, any modification of underlying data needs
to be recorded in sufficient detail that it can be duplicated perfectly
by the log reader. Timestamps and unique IDs are required for ordering
operations.

#### 5. Undo

For undo, any modification to underlying data needs to be recorded in
sufficient detail that it can be undone.  This may require grouping
together and reversing multiple actions, as what the user perceives as
a single operation may involve multiple transformations under the hood.

This also implies that operations need to log what actually happened,
not what the user requested.


## Summary of functional requirements

* Timestamps
* Unique IDs
* Human readable descriptions
* Info about finished commands, how they were invoked and their results
* Info about what changes were made to underlying data structures
* Info about commands to run, and how to invoke them
* Scheduling information "run this event after X minutes"
* The ability to change/update an event's status (unique IDs)
* A flexible way to render events as user visible notifications, so
  questions can be asked and structured data rendered nicely.
* Events must be written to disk, encrypted for privacy reasons
* On app restart, still pending events need to be loaded from disk



