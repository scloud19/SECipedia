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

	Information is stored for 2 weeks

	You can set alarms based on certain metrics that are being monitored
		Ex: Send me an email or text every time the CPU Utilization goes over 80%
			When the alarm occurs, you can define different actions.
				Notification Action
				AutoScaling Action
					You can scale/down-scale
				EC2 Action
					Stop this instance or terminate it
						Ex: If CPU utilization is less than 10%

			Send an email based on Load Balancer Latency
				http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/US_AlarmAtThresholdELB.html

			Send email based on CPU usage
				http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/US_AlarmAtThresholdEC2.html

			Send email based on EBS Throughput (KiB/s)
				http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/US_AlarmAtThresholdEBS.html

	EC2 Monitoring
		Can create alarms that automatically stop, terminate, reboot, or recover your EC2 instance.
			You can add these actions to any alarm that is set on an Ec2 per-instance metric.

			STOP/TERMINATE
				Can help you save money.
			REBOOT/RECOVER
				Automatically reboot instances or recover them onto new hardware if a system impairment occurs.

				EC2ActionsAccess
					IAM role enables AWS to perform alarm actions on your behalf.

		Certain metrics aren't provided through CloudWatch.  AWS has provided perl scripts that allow you to monitor memory, swap, disk space metrics.
			http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/mon-scripts.html

		Common alarm actions
			http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/UsingAlarmActions.html
				Stop Idle Instance
				Send email about servers with unusually high traffic
				Stop servers with unusually high traffic
				Stop an instance experiencing a memory leak
				stop an impaired instance
				terminate instances when batch processing jobs are complete





		For instances on which you've enabled detailed monitoring, you can also get aggregated data across groups of similar instances.
			Each data point covers the instance's previous 1 minute of activity. (If non-detailed monitoring is enabled, each data point covers the instance's previous 5 minutes of activity.)

			Metrics are completely separate across regions, AWS can't aggregate metrics across regions through CloudWatch

			When aggregating, you can further refine the metrics via dimensions

			You can also aggregate statistics by
				auto scaling group
				AMI ID

			You can also monitor an individual instance in the EC2 Console


