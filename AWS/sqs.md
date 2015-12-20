Know all pricing for each service.  Double check pricing information,
it might of changed.

(E)xam point.  I just started denoting this at this point in my studies.  I should go back and denote this for all of the previous lectures.

SQS
	Can't prioritize your messages
		They can be delivered entirely at random

		If you're building a service that needs to prioritize messages
			Create 2 queues, one would be a high priority queue.
				The EC2 instances would poll the high priority queue.  If it's empty, then they'd start to poll the lower priority queue.

				But for the exam, SQS by ITSELF doesn't offer any kind of priority mechanism.

	Longest running service on AWS

	Visibility Time Out
		Default: 30 seconds
		Maximum 12 hours

		Ex:
			You have an SQS queue for watermarking images.  An EC2 instance polls that queue and takes a message.  When this occurs, the message is still in the queue, but it isn't directly visible to other EC2 instances.
				In this scenario, the Ec2 instance has a default time frame of 30 seconds to finish the processing, unless the message reappears in the queue.
					This occurs for fault tolerance.  What if that Ec2 instance goes down in the middle of processing an image? 

			If you need a longer time frame, SWF offers a max of 12 months visibility timeout.


	Provides you access to a message queue that can be used to store messages while waiting for a computer to process them.

	Billed at 64kb "chunks"
		Each 64kb chunk of the payload is billed as 1 request.
			Ex: A single API call with a 256kb payload will be billed as four requests

	A single request to the SQS api can have from 1 to 10 messges, up to a max total payload of 256kb

	(E) Anytime you see the word "decouple" think SQS


	Ensured delivery of each message at least once, and supports multiple readers and writers interacting with the same queue.
		A single queue can be used simultaneously by many distributed application components, with no need for those components to coordinate with each other to share the queue.

		(E) While most of the time each message will be delivered to your application exactly once, you should design your system so that processing a message more then once doesn't create any problems

	Aren't guaranteed FIFO delivery (E)
		If you need this, you can place sequencing information in each message, so that you can reorder the messages when the queue returns them.

	A way of decoupling parts of your applications.
		You can have a queue which sits between you applications, so if one part fails before the other part can process the message, there is no data loss.

		A queue is a temporary repository for messages that are awaiting processessing.
			Ex: A user uploads an image to S3 that needs additional processessing.  Once the user starts an upload, a message can be sent to SQS to denote that the image needs additional processessing.  At this point, the processing could occur and if it finishes, the message will then be taken out of the queue.
				So if something happened where the image couldnt recieve additional processing (say a server failed), then the message would still be in the queue and it could then be picked up by another server.
	You can retrieve the messages programmatically using the SQS API
		Messages can contain 256 KB of text in any format (E)

	The queue acts as a buffer between the component producing and saving data, and the component recieving the data for processing.
		The queue handles cases where the producer is producing work faster than the consumer can process it, or if the producer or consumer are only intermittently connected to the network.
			In other words, you're dealing with situations where you need failover or auto-scaling.

	Ex: This is a workflow that you could utilize

		You have a number of image files to encode.  In an AWS SQS worker queue, you create an SQS message for each file specifying the command (jpeg-encode) and the location of the file in S3.  A pool of EC2 incances running the needed image processing software does the following
			1) Async. pull (NOT PUSH architecture) the task messages from the queue.
				(E)- The instances must poll for messages.  The messages are never pushed to an reciever.  This is a key difference between SQS and SNS
			2) Retrieves the named file from S3
			3) Processes the image conversion
			4) Writes the image back to S3
			5) Writes a "task complete" message to another queue
			6) Deletes the original task message
			7) Checks for more messages in the worker queue

	General Workflow for SQS
		(C)omponent
		1) Component 1 (say our Web Server )sends Message A to the queue
			There's a SQS visibility timeout clock on the message
				12 hour visibility time out by default (E)
		2) Component 2 (say our Applicaiton Server ) retrieves Message A from the queue and the visibility timeout period starts
		2) C2 processes Message A and then deletes it from the queue during the visibility timeout period
			If the app can't finish the task within the visibility timeout(server crashed) and the timeout expires.  Another server will see that the timeout has expired and it will try to process the message

	Autoscaling
		User -- Web Server --- Request Queue (and seperate Response Queue) -- Processing Servers (Auto scaling group)
			If a queue is getting above a certain size, we can then triggle another server instantiation
				We can also scale-down on a certain queue size



