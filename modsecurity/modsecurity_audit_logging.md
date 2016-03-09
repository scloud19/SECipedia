Audit Log

  Directives
    SecAuditEngine On/Off/RelevantOnly
      Controls the audit log engine
    SecAuditLog
      Path to an audit log file
    SecAuditLog2
      Path to another audit log file (copy)
    SecAuditLogParts
      Specifies which part of a transaction will be logged
    SecAuditLogRelevantStatus
      Specifies which response statuses will be considered relevant
    SecAuditLogType
      The type of audit log to use: Serial or Concurrent
    SecAuditLogStorageDir
      Path where concurrent audit log files will be stored

      Currently At
        A typical audit log entry (short, GET request without a body and no logging of the response body) consumes around 1.3 KB. Requests with bodies will increase the amount of data that needs to be logged, as well as the logging of response bodies.
          https://www.feistyduck.com/library/modsecurity-handbook/online/ch04-logging.html