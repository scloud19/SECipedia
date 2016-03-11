! - MS Directive 

Audit Log
  Typical Entry Size
    1.3KB
      This is for
        a short, GET request without a body
        no logging of the response body

  Structure
    Serial Audit Logging
      All enteries will be placed within one file

    Concurrent Audit Logging
      Each audit log entry is a single file
      See concurrent logging below

    Serial Single Audit Log Entry Ex
      Note: The actual entry wont have blank lines
        Did this for readability

      --6b253045-A--
      [18/Aug/2009:08:25:15 +0100] SopXW38EAAE9YbLQ 192.168.3.1 2387 192.168.3.111 8080

      --6b253045-B--
      POST /index.html?a=test HTTP/1.1
      Host: 192.168.3.111:8080
      User-Agent: Mozilla/5.0
      Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
      Accept-Language: en-us,en;q=0.5
      Accept-Encoding: gzip,deflate
      Accept-Charset: ISO-8859-1,utf-8;q=0.7,*;q=0.7
      Keep-Alive: 300
      Connection: keep-alive
      Referer: http://192.168.3.111:8080/index.html?a=test
      Content-Type: application/x-www-form-urlencoded
      Content-Length: 6

      --6b253045-C--
      b=test

      --6b253045-F--
      HTTP/1.1 200 OK
      Last-Modified: Tue, 18 Aug 2009 07:17:44 GMT
      ETag: "6eccf-99-4716550995f20"
      Accept-Ranges: bytes
      Content-Length: 159
      Keep-Alive: timeout=5, max=100
      Connection: Keep-Alive
      Content-Type: text/html

      --6b253045-E--
      <html><body><h1>It works!</h1></body></html>

      <form action="index.html?a=test" method="POST">
      <textarea name="b">test</textarea>
      <input type=submit>
      </form>

      --6b253045-H--
      Stopwatch: 1250580315933960 1902 (551* 580 978)
      Response-Body-Transformed: Dechunked
      Producer: ModSecurity for Apache/2.5.9 (http://www.modsecurity.org/).
      Server: Apache/2.2.11 (Unix) DAV/2

      --6b253045-K--
      SecAction "phase:2,auditlog,log,pass,msg:'Matching test'"

      --6b253045-Z--

      Explained
        Consists of multiple segments
          Controlled by
            ! SecAuditLogParts
        Only parts A and Z are mandatory


        A
          Audit log header (mandatory)
          Time, UID, source IP/Port, destination IP/Port

        B
          Request headers
        C
          Request body
            Usually contains a POST request
        D 
          Reserved
        E 
          Response body
        F
          Response headers
        G
          Reserved
        H 
          Audit log trailer, which contains additional data
        I 
          Compact request body alternative (to part C), which excludes files
        J
          Information on uploaded files

        K 
          Contains a list of all rules that matched for the transaction
            Audit logs that record transactions where there are warnings (or those that were blocked) will contain atleast one rule here.
        Z 
          Final boundary (mandatory)

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

  Concurrent Logging
    To enable
      @@ SecAuditLogType Concurrent
        Restart Apache
        Don't activate mlogc
        @@ I want this for zhost

    Folder structure
      ex: ./20090822/20090822-1324/20090822-132420-SojdH8AAQEAAAugAQEAAAAA
      
      A hierarchy that uses subfolders
        These subfolders are marked with the date.
          YYYYMMDD-HHMMSS-UID

    Currently At
      In addition to each entry getting its own file, the format of the main audit log file will change when concurrent logging is activated. The file that previously stored the entries themselves will now be used as a record of all generated audit log files.
      https://www.feistyduck.com/library/modsecurity-handbook/online/ch04-logging.html