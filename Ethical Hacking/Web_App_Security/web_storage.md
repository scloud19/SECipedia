Web Storage (aka DOM storage)
  Was a part of the HTML5 specification but no longer is.

  Similarities in Cookies and Storage
    Like cookies, two main types of storage exist (one that persists locally and one that is available during the session)

  Local Storage
    Persists over multiple visits from the user

  Session Storage
    Is only available in the tab that created it

  Differences between Cookies and Storage
    Web storage is created only by JS, whereas cookies can be created with HTTP headers.

    Cookies are transmitted to the server in every request, whereas storage obviously isn't.

    Important difference:  There is no concept of path restrictions with local storage.

    Web storage permits much greater sizes than cookies.
      Usually this is at least 5 megabytes.

    The Some Origin Policy (SOP) applies to local storage with each origin being contained.
      In other words, other origins can't access the local storage, nor can subdomains access it.


