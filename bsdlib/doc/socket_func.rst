.. _socket_func:

Socket functions
################

.. list-table::
   :header-rows: 1

   * - Function
     - Description
   * - :ref:`func_socket`
     - Create a new socket instance.
   * - :ref:`func_close`
     - Close a socket.
   * - :ref:`func_bind`
     - Bind the socket to a name or address so that other socket can connect to it.
   * - :ref:`func_listen`
     - Configure the socket to allow other sockets to connect to it.
   * - :ref:`func_accept`
     - Wait for connection.
   * - :ref:`func_connect`
     - Connect to a socket that is in accept mode.
   * - :ref:`func_send`
     - Send data on the socket.
   * - :ref:`func_recv`
     - Receive data on the socket.
   * - :ref:`func_sendto`
     - Send data to a connection-less socket.
   * - :ref:`func_recvfrom`
     - Receive data from a connection-less socket.


Functions
*********


.. _func_socket:

:cpp:func:`nrf_socket`
======================

This function creates a socket instance that is associated with a handle value.
The returned handle value is the unique indentificator in subsequent socket operations.
As :cpp:func:`nrf_fcntl` functions are not provided to set the socket to be blocking or non-blocking, the *type* field could also be used to indicate whether the socket should be non-blocking.
By default, all sockets are blocking.

Example::

   int socket_handle;

   socket_handle = nrf_socket(NRF_AF_INET6, NRF_SOCK_STREAM, NRF_IPPROTO_TCP);

   if (socket_handle == -1)
   {
       // ERROR.
   }


.. _func_close:

:cpp:func:`nrf_close`
=====================

This function close a socket instance. The function requires the handle value as parameter in order to close the associated socket.

Example::

   int socket_handle;

   socket_handle = nrf_socket(NRF_AF_INET6, NRF_SOCK_DGRAM, NRF_IPPROTO_UDP);

   if (socket_handle != -1)
   {
       int result;

       result = nrf_close(socket_handle);

       if (result != 0)
       {
           // ERROR.
       }
   }


.. _func_bind:

:cpp:func:`nrf_bind`
====================

Bind a socket to an address in order to let other sockets connect to it.
The port or the IP address in the sockaddr structure may be wildcarded.

* To wildcard the port number the sin_port field of the address can be set to 0.
* To wildcard the IPv4 address, set the sin_addr.s_addr field of the address to INADDR_ANY.
* To wildcard the IPv6 address, set the sin6_addr.s6_addr field of the address to IN6ADDR_ANY.

Note that the second argument to bind() is casted to the generic :cpp:type:`nrf_sockaddr`.

Example::

   int socket_handle;

   socket_handle = nrf_socket(NRF_AF_INET, NRF_SOCK_STREAM, 0);

   if (socket_handle != -1)
   {
       // ERROR.
   }

   struct sockaddr_in server_addr; // IPv4 socket address type.

   memset(&server_addr, sizeof(server_addr));
   portno = 5683;

   server_addr.sin_family      = NRF_AF_INET;
   server_addr.sin_addr.s_addr = INADDR_ANY;
   server_addr.sin_port        = nrf_htons(portno);

   if (nrf_bind(socket_handle, (struct nrf_sockaddr *) &server_addr, sizeof(server_addr)) < 0)
   {
       // ERROR.
   }

.. _func_listen:

:cpp:func:`nrf_listen`
======================

The function listen marks the socket for accepting connections and sets an outstanding queue limit.
If a ``backlog`` argument is set to 0 or less, it will be backlog value will be set by the implementation.

Example::

   int socket_handle;

   socket_handle = nrf_socket(NRF_AF_INET, NRF_SOCK_STREAM, 0);

   if (socket_handle != -1)
   {
       // ERROR.
   }

   struct nrf_sockaddr_in server_addr; // IPv4 socket address type.

   memset(&server_addr, sizeof(server_addr));
   portno = 5683;

   server_addr.sin_family      = NRF_AF_INET;
   server_addr.sin_addr.s_addr = INADDR_ANY;
   server_addr.sin_port        = nrf_htons(portno);

   if (nrf_bind(socket_handle, (struct nrf_sockaddr *) &server_addr, sizeof(server_addr)) < 0)
   {
       // ERROR.
   }

   nrf_listen(socket_handle, 5); // Allow 5 clients to connect at the same time on the socket.

.. _func_accept:

:cpp:func:`nrf_accept`
======================

The accept function normally blocks until a connection request arrives, trigged by the remote issuing *connect*.
A new socket handle is returned from the function call unless O_NONBLOCK option is set.
If the socket is configured to be non-blocking, it will return a socket handle or -1 if no connection request has arrived.
The second paramter, ``sa``, can be used to return the socket address of the client.
The third parameter, ``sa_len``, indicates the size of the storage that *sa* is pointing to.

Example::

   int socket_handle;

   socket_handle = nrf_socket(NRF_AF_INET, NRF_SOCK_STREAM, 0);

   if (socket_handle != -1)
   {
       // ERROR.
   }

   struct nrf_sockaddr_in server_addr; // IPv4 socket address type.

   memset(&server_addr, sizeof(server_addr));
   portno = 5683;

   server_addr.sin_family      = NRF_AF_INET;
   server_addr.sin_addr.s_addr = INADDR_ANY;
   server_addr.sin_port        = nrf_htons(portno);

   if (nrf_bind(socket_handle, (struct nrf_sockaddr *) &server_addr, sizeof(server_addr)) < 0)
   {
       // ERROR.
   }

   nrf_listen(socket_handle, 5); // Allow 5 clients to connect at the same time on the socket.

   int client_socket_handle;

   struct nrf_sockaddr_in client_addr; // IPv4 socket address type.

   int client_addr_len = sizeof(client_addr);

   while (1)
   {
       client_socket_handle = nrf_accept(socket_handle,
                                        (struct nrf_sockaddr *)&client_addr,
                                        &client_addr_len);
   }

.. _func_connect:

:cpp:func:`nrf_connect`
=======================

The connect function normally blocks until a connection request is accepted by the remote.
Unlike the accept, this function does not return any socket handle, but can as well operate in NRF_O_NONBLOCK mode if set.
If the socket is configured to be non-blocking, it will -1 and continue to request connection.
It is possible to use select or poll in order to wait for the connection to succeed.

If NRF_SOCK_DGRAM sockets has been configured, the function will only set the remote peer address and return and not initiating any connection.
Subsequent send operations will then use the remote peer address.

If NRF_SOCK_STREAM is configured, and NRF_O_NONBLOCK is not configured for the socket, the function will block until timeout occurs.
The timeout expiration time is implementation specific.

Note that if the socket is not yet bound to any local address, the address will be automatically populated by an unused local address by the implementation.

Example::

   int socket_handle;

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

.. _func_send:

:cpp:func:`nrf_send`
====================

This function behave exacly like file descriptor funtion *write*.
The function writes data to the socket from the a data buffer provided as the second parameter.
The third parameter indicates the size of the buffer that should be sent.
The last, fourth parameter, is used for additional flags to define the behaviour of the send function.
The various flags are described below:


.. list-table::
   :header-rows: 1

   * - Name
     - Description
   * - NRF_MSG_DONTWAIT
     - The function will not block for the buffers to become free.


.. _func_recv:

:cpp:func:`nrf_recv`
====================

This function behave exacly like file descriptor funtion *read*.
The function reads data from the socket into a data buffer provided as the second paramter.
The third parameter indicates the size of the data buffer to read into.
The last, fourth paramter, is used for additional flags to define the behaviour of the recv function.
The various flags are described below:

.. list-table::
   :header-rows: 1

   * - Name
     - Description
   * - NRF_MSG_PEEK
     - Return the message, but do not mark it as read. Subsequent read function can be used to consume the data.
   * - NRF_MSG_WAITALL
     - Block the read function until the entire requested length is available.

.. _func_sendto:

:cpp:func:`nrf_sendto`
======================

This function is for connection-less socket.
The function sends a message to a remote using the address parameters provided using the local senders socket handle.
If used with a connected socket the address provided will be ignored and use the connected socket instead.

The function will block until it will fit in the send queue.
If NRF_O_NONBLOCK is set on the socket and there is not room in the function will return -1 and nothing will be sent.

Current API does not provide any flags for the sendto function.


.. _func_recvfrom:

:cpp:func:`nrf_recvfrom`
========================

Function for connection-less socket.
The function receives a message from a remote and also providing the source address of the sender.
The ``sa`` and ``sa_len`` parameters provides a address buffer and a size of the buffer to the function.
Upon return, if the address pointer is non-NULL, the buffer will be filled with the remote source address.
The ``sa_len`` will be set to the actual number of bytes of the ``sa`` address output parameter.

The fourth paramter, is used for additional flags to define the behavior of the *recv* function.
The various flags are described below:

.. list-table::
   :header-rows: 1

   * - Name
     - Description
   * - NRF_MSG_PEEK
     - Return the message, but do not mark it as read. Subsequent read function can be used to consume the data.
   * - NRF_MSG_WAITALL
     - Block the read function until the entire requested length is available.


API
***

.. doxygengroup:: nrf_socket_functions
   :project: nrfxlib
   :members:
