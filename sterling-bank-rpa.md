# Sterling Bank: RPA that had to survive a dropped connection

At Sterling Bank I worked on automation for core banking operations. The work sat between Python RPA scripts, the SeaBass Core Banking system, ATM telemetry, and the support processes that kept branch operations moving.

## The constraint

Banking batches are not a place for optimistic retries. A script that moves financial data has to know whether a transaction actually happened before it tries again. If the network drops between the request and the response, a naive retry can post the same operation twice.

The second constraint was visibility. ATM and branch operations generate a lot of telemetry. The useful signal is not a wall of logs. It is the transaction that is stuck, the terminal that is out of sync, or the queue that is backing up.

## The decision

I wrote the automation around a simple rule: a timeout is an unknown outcome, not a clean failure.

- Each operation was identified so a retry could be checked instead of blindly re-posted.
- The batch flow kept a state record the operations team could inspect.
- The RPA layer treated a timeout as an unknown outcome, not a failure that could be retried blindly.
- ATM telemetry and database feeds were monitored for bottlenecks and synchronization failures rather than only for process liveness.

The alternative was a linear script that assumed every call returned cleanly. It would have worked in a demo and failed in a branch with a flaky link.

## The implementation

The work included Python automation for batch data transfer into SeaBass Core Banking and monitoring for ATM telemetry and database feeds. The automation reduced manual processing time by 65% against the previous process.

## The result

The manual processing time for the automated batch flow dropped by 65%. The retry path was designed to avoid duplicate postings, and the support team had a state record to inspect when a link failed.

## What I would change

I would make the state of each batch visible to the operations team from day one, with a simple screen showing pending, completed, and needs-review transactions. The state handling was the part I cared about; the human handoff could have been clearer.

## What stays private

Bank customer data, internal system details, production credentials, and the bank's source code are not included here. The 65% figure comes from my CV and the work record available to me.