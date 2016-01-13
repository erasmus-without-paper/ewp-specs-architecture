EWP Architecture
================

This document describes EWP Network components, establishes some common
security measures, features, data types and vocabulary, and explains how
partners communicate with themselves. It also describes one of the components
in detail - **the EWP Registry**.

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


How to join the EWP Network?
----------------------------

### Quick start

In order to join our network you'll need to:

 * Publish a valid [Discovery Manifest][discovery-api] somewhere on your
   servers.

 * Send the URL of your manifest to the EWP Registry maintainers. It will be
   stored in the EWP Partners Registry, allowing other partners to identify and
   verify your future requests.


### Implementing APIs

Having completed the two steps described above, you will become a member of the
EWP Network, and you will be able to issue requests to other EWP members
(retrieve information from **other** institutions). You will NOT however share
any of **your** data yet - you'll need to implement some more APIs to do that.

For a complete list of APIs - see the [Discovery API documentation]
[discovery-api].


<a name="registry"></a>

The EWP Registry
----------------

### Summary

The registry is the only centralised part of the EWP architecture. It allows
all EWP hosts to access the list of other EWP hosts. (It MAY also be used for
projects unrelated to EWP, as long as these projects will be mentioned in the
Discovery API documentation.)


### How is it updated?

The registry is being updated **automatically**. It MUSTs periodically read all
the information which all the hosts provide within their [Discovery Manifest
files][discovery-api], and these changes MUST be reflected in the registry
response.

The major advantage of such automatic updating is that the partners *do not
need* to contact the registry maintainer when they want to change some of their
Registry entries. Most changes in the registry can performed simply by updating
the manifest on **the partner's** server (and the Registry will fetch these
changes automatically).


### Requirements for the Clients

 * All clients SHOULD cache the registry responses. (Note, that all APIs SHOULD
   be backward compatible after they are deployed onto production systems, so
   caching API version numbers is quite safe).

 * The clients MAY use the HTTP headers returned in the Registry response to
   determine the amount of time the Registry response should be cached. They
   also MAY choose their own constant value for such expiry, but it MUST NOT be
   greater than 12 hours.

 * The clients MAY keep the cached copy of the Registry as a backup. E.g. if
   the Registry Server cannot be contacted for some reason, a stale cached copy
   may be used instead.


### Requirements for the Server

 * The Registry response MUST be accompanied by a proper HTTP `Cache-Control`
   and `Expires` headers.

 * The Registry Server MUST verify the SSL server certificates when it fetches
   the Manifest files from EWP Hosts.

 * The Registry Server MUST validate the manifest files before importing them.
   If the manifest fails validation, the server MUST NOT import any new changes
   and it SHOULD notify the administrator of the Host which serves the invalid
   manifest OR a human maintainer of the Registry Server, so that the problem
   will be noticed.

 * The Registry MUST recognise and support the latest version of the
   Manifest file (immediately after it is released).


### Response format

The Registry service takes no parameters. It simply returns a "static"
response at the proper URL.

The response MUST conform to the XML Schema provided in the attached
[registry.xsd](registry.xsd) file. See [registry-example.xml]
(registry-example.xml) for an example of a valid registry response.


### Location

*WRTODO: To be determined.*

<!-- WRTODO: Backup domain? -->


Hosts, APIs and other components
--------------------------------

### HEIs

HEI is an acronym for **Higher Education Institution**. The EWP Project aims on
allowing communication between them.

![Scattered HEIs](images/diagram-step1.png)


### EWP Hosts

In order for the HEI to benefit from the EWP Project, it needs to be placed
within an EWP Host.

 * EWP Hosts are *groups of HEIs*. Host 1 **covers** institutions A, B and C,
   while institution D is **being covered** by Host 2. Institution E is not
   covered by any host within the EWP network.

 * One HEI SHOULD be covered by no more than one EWP Host.

 * In some countries, there will be only a single EWP Host per country. In
   other countries every HEI will run its own EWP Host.

![HEIs have been covered by two EWP Hosts](images/diagram-step2.png)


### Registry

We'll try to imagine an example request within the EWP Network. Let's say that
HEI B wants to fetch some student data from the EWP Network.

 * HEI B **knows** the ID of the student, and it knows the ID of the home
   institution of this student. So it knows which HEI it wants to fetch the
   data *from* (let's say it's HEI D).

 * HEI B asks *its own* EWP Host (Host 1) to fetch the data it needs from the
   EWP Network.

 * Host 1 **does not know** which of the EWP Hosts **covers** HEI D. It doesn't
   even know if HEI D is *being covered* in the EWP Network.

 * Host 1 calls the EWP Registry Service. The Registry Service keeps track of
   all the EWP Hosts and of all the HEIs covered by them. Given the Registry
   response, Host 1 is now able to determine that it needs to contact Host 2 in
   order to get the data.

![Registry connects EWP Hosts with each other](images/diagram-step3.png)


### APIs

EWP Hosts are **not required** to implement *all* features of the EWP Network.
Host 2 covers HEI D, but this **does not necessarily mean** that Host 2 has
implemented the API needed (or specific API *version* needed) by Host 1 in
order to fetch the student data it needs.

In order to avoid unnecessary requests, The Registry Service keeps track of all
the implemented APIs too. Host 1 will be able to verify if the specific API has
been implemented, and retrieve its URL from the Registry.

It's worth noting that API implementations can be hosted across multiple
servers (which might be convenient, especially if you work with multiple
developer teams, each one of which might want to use their favourite languages
and environments.


![Registry keeps track of implemented APIs](images/diagram-step4.png)


### Push notifications (and broadcasting)

What if HEI B wanted to **notify** HEI D about something, instead of fetching
the data from it? E.g. a student has just failed an exam at B and we suspect
that D would want to know it?

Some of the APIs are **event listener APIs**. If Host 2 wants to receive such
notifications from other hosts, he indicates that he has implemented a specific
event listener API in its Manifest file. Now, if Host 1 is able to broadcast
such notifications, then it asks the Registry which hosts have implemented the
proper listeners, and posts the proper notifications at the listener URLs.

It's worth noting that Host 1 is NOT REQUIRED to be able to broadcast such
notifications (even if all other hosts implement listeners). Implementers MAY
choose to implement only a subset of push notifications, or even not implement
them at all. You will find more information on this in the documentation of
specific event listener APIs.

<!-- WRTODO: Examples of such APIs. -->


Security
--------

### SSL Certificates

There are two types of certificates which all implementers must be aware of:

 * **Server certificates** are used by the Host when it **responds** to API
   requests.
 * **Client certificates** are used to **issue requests** within the EWP
   Network.

Implementers MAY use the same certificate for both purposes, but it is NOT
REQUIRED.


### Server certificates

These are just "regular" SSL certificates, bound to a domain.

All our APIs MUST be served via the HTTPS protocol and protected by a
certificate bound their domain. This allows the clients to verify that the
responses come from the proper source.

Note, that APIs can be spread across multiple domains.


### Client certificates

Each EWP Host declares a list certificates it will use for making requests to
other hosts. It does so by including acceptable domain names and/or specific
X.509 certificates in its Manifest file (review the Manifest's XSD file for
details). This list will often contain just one or two domain entries, but it
MAY also contain an unlimited number **self-signed** certificates.

This setup has the following advantages:

 * During development stages, it allows developers to generate their own
   certificates and install them in their browsers for debugging purposes.

 * In production environments, it facilitates the usage of EWP Network, its
   Registry and Manifest files in **other** projects (unrelated to EWP). Such
   projects will most probably serve their APIs (and clients) on other domains
   (so we need to support more than a single domain or certificate).
 
 * Using domain names allows client certificates to be easily replaced by newer
   versions when they expire. The manifest will not need to be updated.

The list of allowed domains and certificates in use can be acquired from the
EWP Registry.


### Should I verify *all* requests?

No. Just some.

Every API defines its own security requirements. In most cases it *will* be
REQUIRED for the EWP Host to verify its requester, but in some other cases
(e.g. the Discovery Manifest API) it will be the opposite - some requests MUST
be allowed to be performed by **anonymous** requesters (with no client
certificate).

When verifying client certificates, you MUST match them against all the domains
and certificates published in the EWP Registry (review the Registry's XSD for
details).


### Should I verify *all* responses?

Yes.

It is RECOMMENDED for all EWP Clients to verify the SSL server certificates
when retrieving responses from other EWP Hosts. Use the "regular" SSL
verification - you do not need to compare certificates with the lists
provided by the Registry as EWP Servers do - you simply need to check if the
server's certificate was signed by a trusted CA.


### Example Security Policies

Every API specifies its own security policy. Here are some examples of common
policies.

 * **Anonymous access**: The request can be made by anyone. The client does not
   need to use any SSL certificate.

 * **Access to anyone from within the EWP Network**: Client certificate is
   required. The server needs to verify if the client's request was signed by
   *any* of the certificates *or* domains listed in the EWP Registry.

 * **Access to a single EWP Host which covers a specific HEI**: Requests
   MUST be signed by a certificate of a single, specific EWP Host. Other EWP
   Hosts MUST NOT be able to access the resource. (This will probably be the
   most common policy across EWP APIs, as the project focuses on exchanging
   data between HEIs.)


<a name='backward-compatibility-rules'></a>

Backward-compatibility rules
----------------------------

All implementers and API designers MUST follow the following basic rules when
designing, developing and accessing API methods:

 * Adding a new **optional** element to an XML file is backward-compatible.
   Client implementers MUST ignore unknown elements. If an element has not
   existed in version `1.0.0`, but you want it to exist in the next version,
   then you can tag the next version as `1.1.0`.

 * Adding a new **required** element to an XML file is NOT backward-compatible.
   All hosts which implement this API will need to be upgraded, and designers
   MUST bump the API version to `2.0.0`.

 * Changing an existing element is *usually* NOT backward-compatible. For
   example, if the element contained an integer in version `1.0.0`, but API
   designer wants it to be able to contain a float from now on, then he MUST
   increase the version number to `2.0.0`.

 * Adding an **optional** parameter to the API method interface is
   backward-compatible, as long as the default value for the newly added
   element doesn't change the method output which the older clients expect to
   receive.

 * It is agreed that after an API is deployed onto production systems, all
   changes to this API SHOULD be kept backward-compatible - new major versions
   of such APIs SHOULD NOT be released. However, such APIs MAY become
   [deprecated][statuses] (and new APIs MAY be released in their place).


<a name="schac-identifiers"></a>

SCHAC identifiers
-----------------

One of the fundamental features required of the EWP Network is the ability to
uniquely identify individual HEIs. A couple of HEI identifier types are already
in use in Europe, but only one of them - SCHAC identifier - is universal
enough.

SCHAC identifiers are quite ingenious in their simplicity. They identify HEIs
by Internet Domain Names registered for them. E.g. `uw.edu.pl` is the SCHAC ID
for University of Warsaw.

SCHAC identifiers are obviously easy to be acquired by *a human*. We do
acknowledge however, that its not so easy for machines. Some student
information systems will identify external HEIs by their PIC or Erasmus codes,
and they won't be capable to acquire SCHAC identifiers "on the fly". For this
reason, the EWP Registry will provide a **mapping**.

You will be able to use the Registry to map between PIC identifiers, Erasmus
codes, and SCHAC IDs. The Registry will hold a database of these identifiers.
Initially, this database will be based on Manifest files, but other sources MAY
be used later on.


[discovery-api]: https://github.com/erasmus-without-paper/ewp-specs-api-discovery/blob/master/README.md
[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management/blob/master/README.md#statuses
