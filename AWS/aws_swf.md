(E)xam

SQS vs SWF
  Whereas SQS is automated, SWF can utilize human inputs/actions.
    Ex: Go to section 34e and grab item.  Place that item back in the packing area.

  SWF presents a task-oriented API, whereas SQS offers a message-oriented API

  SWF ensures that a task is assigned only once and is never duplicated.  With SQS, you need to handle diplicated messages and may also need to ensure that a message is processed only once.

  SWF keeps track of all the tasks and events in an application.  With SQS, you need to implement you own application-level tracking, especially if your application uses multiple queues.

  Visibility Timeout
    Max: 12 months

    See SQS.md for more information on what a visibility timeout is.

  SWF Actiors (E)
    Workflow Starters
      An application that can initiate (start) a workflow

    Deciders
      Control the flow of activity tasks in a workflow execution.

      If something has finished in a workflow (or fails), a Decider will figure out what to do next.

    Activity Workers
      Carry out the activity tasks