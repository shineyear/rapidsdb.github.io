---
layout: default
title: Security
parent: Architecture
nav_order: 7
---

# Security

---

RapidsDB requires authentications of all client connections before any commands can be issued.  Similar to the federation of disparate data sources, RapidsDB can federate multiple authentication sources.  User configuration can be used as a source for the authentication process. The current version supports password-based (internal authentication) and Kerberos-based user authentications.

RapidsDB requires all client-end connections to authenticate user identities before issuing commands. Similar to how RapidsDB federates different data sources, it can federate multiple different authentication sources. Users can be configured and authenticated through a specific source, that is, an authenticator.
