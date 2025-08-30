- [Transport_Layer](transport_layer)
- [Network_Layer](network_layer)

| status_code | description                                                                                                               |
| ----------- | ------------------------------------------------------------------------------------------------------------------------- |
| 301         | permanent redirect, `post` request may be changed to `get` request by the browser                                         |
| 302         | temporary redirect, `post` request may be changed to `get` request by the browser                                         |
| 429         | too many requests                                                                                                         |
| 500         | internal server error                                                                                                     |
| 502         | bad gateway, a server acting as a gateway or proxy received an invalid response from an upstream server                   |
| 503         | service unavailable, server is temporarily unable to handle the request due to being overloaded or undergoing maintanence |
| 504         | gateway timeout, a server acting as a gateway or proxy didn't receive a timely response from upstream server              |
