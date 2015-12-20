SNS
  Simple Notification Service

  Sending notifications from the cloud.
    Can publish messages from an application and immediately deliver them to subscribers or other apps.

  Follows the "publish-subscribe" (pub-sub) messaging paradigm.
    Notifications are delivered to clients using a "push" mechanism that eliminates the need to "poll" for new information.

  With SQS, you have to poll.  But with SNS, you don't; it's "push" based.

  These push notifications can be a text message, email, to SQS queues, or to any HTTP endpoint.
    Can also send push notifications to Apple, Google, Fire OS, and Window's devices; as well as Android devices in China with Baidu Cloud Push.

    Ex: You can push messages from SNS to a SQS.  The SQS can then be polled via an EC2 instance. 

  To prevent messages from being lost, all messages published to SNS are stored redundantly across multiple AZs.

  Topics
    SNS is arranged by topics.

    You can group multiple recipients using topics.
      Ex: Subscribe all of IT to a topic and when a critical service goes down, push an email notification to that topic to notify IT of the lapse.

    A topic is an "access point" for allowing recipients to dynamically subscribe for identical copies of the same notification.

    One topic can support deliveries to multiple endpoint types
      Ex: You can group together iOS, Android, and SMS recipients.
        When you publish once to a topic, SNS delivers appropriately formatted copies of your message to each subscriber.






