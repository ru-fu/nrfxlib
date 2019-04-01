.. _socket_options:

Socket options
##############

.. list-table::
   :header-rows: 1

   * - Name
     - Value
     - :cpp:func:`Set <nrf_setsockopt>`
     - :cpp:func:`Get <nrf_getsockopt>`
     - Socket level
     - Description
   * - :c:data:`NRF_SO_BINDTODEVICE`
     - timeval
     - x
     - x
     - :c:data:`NRF_SOL_SOCKET`
     - Set socket to use specified interface (only valid for NRF_AF_INET/NRF_AF_INET6).
   * - :c:data:`NRF_SO_RCVTIMEO`
     - int32_t
     - x
     - x
     - :c:data:`NRF_SOL_SOCKET`
     - Maximum time to wait for a blocking receive operation to complete. (0 = Infinite)
   * - :c:data:`NRF_SO_ERROR`
     - int32_t
     -
     - x
     - :c:data:`NRF_SOL_SOCKET`
     - Read out error code of a failing set/getsockopt operation.
   * - :c:data:`NRF_SO_PDN_AF`
     - nrf_sa_family_t[]
     - x
     - x
     - :c:data:`NRF_SOL_PDN`
     - Set and get supported address families on the PDN.
   * - :c:data:`NRF_SO_SEC_ROLE`
     - nrf_sec_role_t
     - x
     - x
     - :c:data:`NRF_SOL_SECURE`
     - Get and/or set the TLS role on the socket.
   * - :c:data:`NRF_SO_SEC_TAG_LIST`
     - nrf_sec_tag_t
     - x
     - x
     - :c:data:`NRF_SOL_SECURE`
     - Get and/or set the security tags to be used on the TLS socket.
   * - :c:data:`NRF_SO_SEC_SESSION_CACHE`
     - nrf_sec_session_cache_t
     - x
     - x
     - :c:data:`NRF_SOL_SECURE`
     - Get and/or set the choice of session caching used on the TLS socket.
   * - :c:data:`NRF_SO_SEC_PEER_VERIFY`
     - nrf_sec_peer_verify_t
     - x
     - x
     - :c:data:`NRF_SOL_SECURE`
     - Get and/or set the choice of peer verification on the TLS socket.
   * - :c:data:`NRF_SO_HOSTNAME`
     - string
     - x
     - x
     - :c:data:`NRF_SOL_SECURE`
     - Get and/or set the host name of the peer used for peer verification.
   * - :c:data:`NRF_SO_CIPHERSUITE_LIST`
     - nrf_sec_cipher_t
     - x
     - x
     - :c:data:`NRF_SOL_SECURE`
     - Get and/or set the TLS cipher suite on the socket.
   * - :c:data:`NRF_SO_CIPHER_IN_USE`
     - nrf_sec_cipher_t
     -
     - x
     - :c:data:`NRF_SOL_SECURE`
     - Get the TLS cipher selected for the session on the socket.
   * - :c:data:`NRF_SO_DFU_FW_VERSION`
     - nrf_dfu_fw_version_t
     -
     - x
     - :c:data:`NRF_SOL_DFU`
     - Get firmware version.
   * - :c:data:`NRF_SO_DFU_RESOURCE`
     - nrf_dfu_fw_resource_t
     -
     - x
     - :c:data:`NRF_SOL_DFU`
     - Get resources available for DFU.
   * - :c:data:`NRF_SO_DFU_TIMEO`
     - uint32_t
     - x
     - x
     - :c:data:`NRF_SOL_DFU`
     - Get and/or set the timeout to send a DFU fragment.
   * - :c:data:`NRF_SO_DFU_APPLY`
     -
     - x
     -
     - :c:data:`NRF_SOL_DFU`
     - Set (execute) firmware upgrade.
   * - :c:data:`NRF_SO_DFU_REVERT`
     -
     - x
     -
     - :c:data:`NRF_SOL_DFU`
     - Set (execute) revert the upgraded firmware to the old one.
   * - :c:data:`NRF_SO_DFU_BACKUP_DELETE`
     -
     - x
     -
     - :c:data:`NRF_SOL_DFU`
     - Set (execute) delete any backup firmware.
   * - :c:data:`NRF_SO_DFU_OFFSET`
     - uint32_t
     - x
     - x
     - :c:data:`NRF_SOL_DFU`
     - Get and/or set the offset of the downloaded firmware.


Access functions
****************

.. doxygengroup:: nrf_socket_options
   :project: nrfxlib
   :members:

Option levels
*************

.. doxygengroup:: nrf_socket_options_levels
   :project: nrfxlib
   :members:

Options
*******

.. doxygengroup:: nrf_socket_options_sockets
   :project: nrfxlib
   :members:

.. doxygengroup:: nrf_socket_options_pdn_sockets
   :project: nrfxlib
   :members:

.. doxygengroup:: nrf_socket_options_secure_sockets
   :project: nrfxlib
   :members:

.. doxygengroup:: nrf_socket_options_dfu_sockets
   :project: nrfxlib
   :members:
