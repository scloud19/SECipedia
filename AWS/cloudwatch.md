CloudWatch
	Is enabled by default and is free (for polling every 5 mins)
	For exam:  Look into how many free alarms you get per month, and overall pricing
	Real time monitoring
		Ex: What has happened over the past 24 hours, etc?
	vs Cloud Trail
		This has to do with log auditing/management, it ISN'T the same as CloudWatch

	Can be utilized on EC2 instances, as well as DynamoDB, RDS, and custom items.
	Standard Monitoring
		Polls every 5 mins
	Detailed Monitoring
		Polls every minute

	You can set alarms based on certain metrics that are being monitored
		Ex: Send me an email or text every time the CPU Utilization goes over 80%
			When the alarm occurs, you can define different actions.
				Notification Action
				AutoScaling Action
					You can scale/down-scale
				EC2 Action
					Stop this instance or terminate it
						Ex: If CPU utilization is less than 10%
