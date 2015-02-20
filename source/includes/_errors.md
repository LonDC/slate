# Errors

<aside class="notice">If an operation fails you will receive an error code, and a message in the response from our server</aside>

The LDC Via API uses the following error codes:


Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request sucks
401 | Unauthorized -- Your API key is wrong
403 | Forbidden -- You're trying to do something that you are not allowed to
404 | Not Found -- The specified request could not be found
405 | Method Not Allowed -- You tried to access a URL with an invalid method
418 | I'm a teapot
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.
