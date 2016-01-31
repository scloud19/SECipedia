HTTP
  Hypertext transfer protocol (HTTP) is the core communications protocol used to access the WWW.

  Originally developed for retrieving static text-based resources.

  Uses a message-based model in which a client sends a request message and the server returns a response message.

  The protocol is basically connectionless: however, HTTP uses the stateful TCP protocol as its transport mechanism.  Each exchanged of request/response is an autonomous transaction and may use a different TCP connection.

  REST (Representational State Transfer)
    Style of architecture where requests/responses contain representations of the current state of the system's resources.

    HTTP and the format of URLs, conform to REST

    While URLs containing parameters within the query string do technically conform to REST; a "REST-style URL" is often used to denote a URL that contains it's parameters within the URL file path (rather than the query string)
      Ex: http://wahh-app.com/search?make=ford&model=pinto

      Rest-style URL (with REST-style parameters)
        http://wahh-app.com/search/ford/pinto/delete
          Sometimes, the action is at the end.



        