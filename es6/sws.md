 SWF
 	Makes it easy to coordinate work across distributed application components.
 		Ex: Use cases suh as media processing, web app. back-ends, business process workflows, analytics pipelines can be designed to be a coordination of tasks

	 	Tasks represent invocations of various processing steps in an application which can be performed by executable code, web service calls, human actions, and scripts.

	E - SQS is automated, where SWF can utilize HUMAN actions
		I.e. Go to isle 6 in wharehouse and pick up item 4 and return it

	E - SWF offers a task-oriented API, whereas SQS offers a message-oriented API

	Service gaurantees that a task is assigned only once and is never duplicated.  With SQS, you need to handle duplicated messages and may also need to ensure that a message is processed only once.