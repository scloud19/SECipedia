When you attempt to modify server responses coming to the client, you might get this HTTP message in your proxy

HTTP/1.1 304 Not Modified
Date: Wed, 6 Jul 2011 22:40:20 GMT
Etag: “6c7-5fcc0900”
Expires: Thu, 7 Jul 2011 00:40:20 GMT
Cache-Control: max-age=7200

Thus, there isn't any "payload" portion of the message to adulterate.