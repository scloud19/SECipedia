CloudFront = CF

Delivery Methods
  Web Distribution
    Utilize if you want to
      Speed up the distribution of static and dynamic content
      Distribute media files using HTTP/HTTPS
      Add, update, or delete objects, and submit data from web forms.
      Use live streaming to stream an event in real time.
  
  RTMP Distribution
    To speed up distribution of you streaming media files that you Flash Media Server's RTMP protocol.

    This distribution allows an end user to begin playing a media file before the file has finished downloading from a Cloudfront edge location.
      You must store the media files in an s3 bucket.
      To utilize live streaming, use a web distribution

You can put CF in front of an S3 bucket or Load Balancer

When setting up
