.. _socket_addr_func:

Socket address functions
########################

The :cpp:func:`nrf_getaddrinfo` function blocks until the Domain Name System (DNS) lookup is either returning a response or if the query did not succeed.
The DNS lookup result will be returned through the addr_info response parameter.

Example::

   int socket_handle;

   socket_handle = nrf_socket(NRF_AF_INET, NRF_SOCK_STREAM, 0);

   if (socket_handle != -1)
   {
       // ERROR.
   }

   struct nrf_sockaddr_in client_addr; // IPv4 socket address type.

   memset(&client_addr, sizeof(client_addr));
   portno = 9999;

   client_addr.sin_family      = NRF_AF_INET;
   client_addr.sin_addr.s_addr = INADDR_ANY;
   client_addr.sin_port        = nrf_htons(portno);

   if (nrf_bind(socket_handle, (struct nrf_sockaddr *) &client_addr, sizeof(client_addr)) < 0)
   {
       // ERROR.
   }

   char                  port[15];
   char                  server_name[] = "www.nordicsemi.no";
   struct nrf_addrinfo   hints;
   struct nrf_addrinfo * server_info;
   struct nrf_addrinfo * p;
   int                   rv;

   snprintf(port, 15, "%d", 80);

   memset(&hints, 0, sizeof(hints));

   hints.ai_family   = NRF_AF_INET;
   hints.ai_socktype = NRF_SOCK_STREAM;

   if ((rv = nrf_getaddrinfo(server_name, port, &hints, &server_info)) != 0)
   {
       // ERROR.
   }

   for (p = server_info; p != NULL; p = p->ai_next)
   {
       if (nrf_connect(socket_handle, p->ai_addr, p->ai_addrlen) < 0)
       {
           // ERROR.
       }

       break;
   }


There is also possible to perform the query on a given PDN connection.
To do this, the PDN must be set up and a second hint has to be provided to guide the query to use the specific PDN connection.

Example::

   int  socket_handle;
   int  socket_handle_pdn;
   char apn_name[] = "MYAPN";

   socket_handle = nrf_socket(NRF_AF_INET, NRF_SOCK_STREAM, 0);

   if (socket_handle == -1)
   {
       // ERROR.
   }

   struct nrf_sockaddr_in client_addr; // IPv4 socket address type.

   memset(&client_addr, sizeof(client_addr));
   portno = 9999;

   client_addr.sin_family      = NRF_AF_INET;
   client_addr.sin_addr.s_addr = INADDR_ANY;
   client_addr.sin_port        = nrf_htons(portno);

   if (nrf_bind(socket_handle, (struct nrf_sockaddr *) &client_addr, sizeof(client_addr)) < 0)
   {
       // ERROR.
   }

   socket_handle_pdn = nrf_socket(NRF_AF_LTE, NRF_SOCK_MGMT, NRF_PROTO_PDN);

   if (socket_handle_pdn != -1)
   {
       nrf_connect(socket_handle_pdn, apn_name, sizeof(apn_name));
   }

   char                  port[15];
   char                  server_name[] = "www.nordicsemi.no";
   struct nrf_addrinfo   hints;
   struct nrf_addrinfo   pdn_hints;
   struct nrf_addrinfo * server_info;
   struct nrf_addrinfo * p;
   int                   rv;

   snprintf(port, 15, "%d", 80);

   memset(&pdn_hints, 0, sizeof(pdn_hints));

   pdn_hints.ai_family    = NRF_AF_LTE;
   pdn_hints.ai_socktype  = NRF_SOCK_MGMT;
   pdn_hints.ai_protocol  = NRF_PROTO_PDN;
   pdn_hints.ai_canonname = apn_name;

   memset(&hints, 0, sizeof(hints));

   hints.ai_family   = NRF_AF_INET;
   hints.ai_socktype = NRF_SOCK_STREAM;
   hints.ai_next     = pdn_hints;

   if ((rv = nrf_getaddrinfo(server_name, port, &hints, &server_info)) != 0)
   {
       // ERROR.
   }

   for (p = server_info; p != NULL; p = p->ai_next)
   {
       if (nrf_connect(socket_handle, p->ai_addr, p->ai_addrlen) < 0)
       {
           // ERROR.
       }

       break;
   }



.. doxygengroup:: nrf_socket_addr_func
   :project: nrfxlib
   :members:
