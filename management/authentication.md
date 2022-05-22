---
layout: default
title: Authentication
parent: Management
nav_order: 6
---

# Authentication

---

Authenticators are dynamically pluggable modules, which control the authentication of a RapidsDB user. Each user of the system can only be associated with one authenticator instance. When a user attempts to be authenticated, the request will be processed and executed by the authenticator instance.
  
RapidsDB currently supports the internal password-based authentication and the third-party Kerberos authentication protocol. Except for password-based authenticators, which are automatically created by the system, an authenticator type instance needs to be created before the authenticator type can be used. Different authenticator types may require different information and configurations when they are created.

It is possible to drop an authenticator while keeping the user, but the users will not be able to login. The default behavior will be to drop all user accounts associated with the authenticator being removed. Similarly, if an authenticator is disabled, all authentication requests it receives will be denied to prevent all users that are associated with this authenticator instance from being able to login until that authenticator is enabled again. User accounts can also be individually disabled and enabled to prevent them from logging in.

Unlike the Kerberos authentication protocol, the internal password-based authenticator cannot be deleted or disabled. The password is salted and then hashed with a computationally-intensive algorithm such that it takes a relatively long time to compute. This makes it extremely difficult to brute-force guess the password.

Refer to [RapidsDB Installation and Management Guide](../downloads/RapidsDB_Installation_and_Management_Guide_Release_v4.3.3.pdf):

* Chapter 4 Users and Authentication
* Chapter 7.3 Adding Authenticators
* Chapter 7.4 Dropping Authenticators
* Chapter 7.5 Altering Authenticators
