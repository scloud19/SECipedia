Apps may serialize data/objects before transmitting them within HTTP
  If this is the case, you need to unpack the serialized content, edit it, and reserialize it correctly

Each browser ext technology comes with its own scheme for serializing data within HTTP messages

Java Serialization
  Can be usually seen via
    Content-Type: application/x-java-serialized-object

    Once the raw serialized data is intercepted
      The Burp plug-in DSer
        Provides a framework for viewing/manipulating serialized Java objs
        Will convert the data into XML for simple editing


Flash Serialization
  is provided by Action Message Format (AMF)
    Ex: Content-Type: application/x-amf

    Burp natively supports AMF format
      When it ids at HTTP request/response containing AMF, it is unpacked

Silverlight Serialization
  Silverlight apps make use of the Windows Communication Foundation remoting framework that is built into .NET
