# Errors


The Nifty API uses the following error codes:


Error Code | Meaning
---------- | -------
400 | Bad Request -- Your request was incorrect
401 | Unauthorized -- Your API key is wrong or you did not provide one
403 | Forbidden -- The API call cannot be requested by you.
404 | Not Found -- The specified resource was not found.
405 | Method Not Allowed -- The HTTP method is not allowed on this resource.
406 | Not Acceptable -- You requested a format that isn't json
429 | Too Many Requests -- You are being throttled! Slow down!
500 | Internal Server Error -- We had a problem with our server. Try again later.
503 | Service Unavailable -- We're temporarily offline for maintenance. Please try again later.
